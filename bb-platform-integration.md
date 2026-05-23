# BB Platform Integration — Apply on bb-platform machine

**Date:** May 2026  
**From:** bb-learn integration build  
**Apply at:** `C:\Users\robhi\bb-platform`

Apply these changes in order. Each section tells you exactly which file to edit and what to add/change.

---

## Step 1 — Cloud Function: `recordTrainingCompletion`

**File:** `functions/index.js`  
**Action:** Add the block below to the bottom of the file (before the last closing brace if any, or just append).

```js
// ── TRAINING COMPLETION ─────────────────────────────────────────────────────
// Called by BB Learn HTML modules when a staff member starts or completes
// a module. Accepts { uid, moduleId, moduleTitle, event } in the POST body.
// No Firebase auth required — validates that the uid exists in Firestore.

const { onRequest } = require('firebase-functions/v2/https');
const corsLib = require('cors');
const trainingCors = corsLib({
  origin: ['https://bblearns.netlify.app', 'https://bbplatform.netlify.app'],
  methods: ['POST', 'OPTIONS'],
});

exports.recordTrainingCompletion = onRequest(async (req, res) => {
  trainingCors(req, res, async () => {
    if (req.method === 'OPTIONS') return res.status(204).send('');
    if (req.method !== 'POST') return res.status(405).json({ error: 'Method not allowed' });

    const { uid, moduleId, moduleTitle, event } = req.body || {};

    if (!uid || !moduleId || !event) {
      return res.status(400).json({ error: 'uid, moduleId, and event are required' });
    }
    if (!['started', 'completed'].includes(event)) {
      return res.status(400).json({ error: 'event must be "started" or "completed"' });
    }

    const db = admin.firestore();
    const userRef = db.collection('users').doc(uid);
    const userSnap = await userRef.get();

    if (!userSnap.exists) {
      return res.status(404).json({ error: 'User not found' });
    }

    const trainingRef = userRef.collection('training').doc(moduleId);
    const trainingSnap = await trainingRef.get();
    const now = admin.firestore.FieldValue.serverTimestamp();

    if (event === 'started') {
      if (!trainingSnap.exists) {
        await trainingRef.set({
          moduleId,
          moduleTitle: moduleTitle || moduleId,
          startedAt: now,
          completedAt: null,
        });
      }
    } else if (event === 'completed') {
      if (trainingSnap.exists) {
        await trainingRef.update({ completedAt: now });
      } else {
        // Edge case: completed without a started event (e.g. page reload mid-module)
        await trainingRef.set({
          moduleId,
          moduleTitle: moduleTitle || moduleId,
          startedAt: now,
          completedAt: now,
        });
      }
    }

    return res.status(200).json({ ok: true });
  });
});
```

**Note on `cors`:** Check if `cors` is already in `functions/package.json`. If not, run:
```bash
cd functions
npm install cors
```

**After deploying:**
```bash
firebase deploy --only functions
```
Then go to **Firebase Console → Functions → recordTrainingCompletion** and copy the URL.
It will look like: `https://recordtrainingcompletion-XXXXXXXX-uc.a.run.app`

Then paste it into both bb-learn module files, replacing `'PASTE_FUNCTION_URL_HERE'`:
- `C:\Users\rober\bb-learn\bb-module1.html` — line ~1905
- `C:\Users\rober\bb-learn\bb-module2.html` — line ~1905

---

## Step 2 — Firestore Security Rules

**File:** `firestore.rules`  
**Action:** Add this block inside the `match /databases/{database}/documents {` block, alongside your existing rules.

```
// Training completions — written only via Cloud Function (admin SDK bypasses rules)
// Staff can read their own training records; directors/admins can read all
match /users/{uid}/training/{moduleId} {
  allow read: if request.auth != null && (
    request.auth.uid == uid ||
    get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role in ['director', 'admin']
  );
  allow write: if false; // Cloud Function (admin SDK) handles all writes
}
```

Deploy rules:
```bash
firebase deploy --only firestore:rules
```

---

## Step 3 — New Training page

### 3a. Create `src/pages/Training/Training.jsx`

Create this as a new file at that path:

```jsx
import { useEffect, useState, useCallback } from 'react';
import { collection, getDocs } from 'firebase/firestore';
import { db } from '../../firebase';
import { useAuth } from '../../context/AuthContext';
import styles from './Training.module.css';

const PLATFORM_URL = 'https://bblearns.netlify.app';

const MODULES = [
  {
    id: 'module-1',
    number: '01',
    title: 'Financial Literacy',
    description:
      'Understand your pay stub, the difference between PTO and RTO, how the Reliability Reward works, and exactly what to do if something looks wrong on payday.',
    duration: '25 min',
    url: `${PLATFORM_URL}/bb-module1.html`,
  },
  {
    id: 'module-2',
    number: '02',
    title: 'Child Safety & Mandated Reporting',
    description:
      'Recognize signs of concern, understand your legal role as a mandated reporter under Virginia law, and know exactly what to do — step by step — if a child needs protection.',
    duration: '30 min',
    url: `${PLATFORM_URL}/bb-module2.html`,
  },
];

function statusLabel(record) {
  if (!record) return 'not-started';
  if (record.completedAt) return 'completed';
  return 'in-progress';
}

export default function Training() {
  const { user } = useAuth();
  const [completions, setCompletions] = useState({});
  const [loading, setLoading] = useState(true);

  const fetchCompletions = useCallback(async () => {
    if (!user?.uid) return;
    try {
      const snap = await getDocs(collection(db, 'users', user.uid, 'training'));
      const map = {};
      snap.forEach(doc => { map[doc.id] = doc.data(); });
      setCompletions(map);
    } catch (e) {
      console.error('Training fetch error:', e);
    } finally {
      setLoading(false);
    }
  }, [user?.uid]);

  useEffect(() => {
    fetchCompletions();
  }, [fetchCompletions]);

  // Re-fetch when staff return to this tab after completing a module
  useEffect(() => {
    const onVisible = () => {
      if (document.visibilityState === 'visible') fetchCompletions();
    };
    document.addEventListener('visibilitychange', onVisible);
    return () => document.removeEventListener('visibilitychange', onVisible);
  }, [fetchCompletions]);

  function launchModule(mod) {
    window.open(`${mod.url}?uid=${user.uid}`, '_blank', 'noopener,noreferrer');
  }

  const totalComplete = MODULES.filter(m => completions[m.id]?.completedAt).length;

  return (
    <div className={styles.page}>
      <div className={styles.header}>
        <span className={styles.eyebrow}>My Training</span>
        <h1 className={styles.title}>Your learning path.</h1>
        <p className={styles.subtitle}>
          {totalComplete === 0
            ? 'Complete each module at your own pace. Your progress is saved automatically.'
            : totalComplete === MODULES.length
            ? 'All modules complete. You can review any module at any time.'
            : `${totalComplete} of ${MODULES.length} modules complete. Keep going.`}
        </p>
      </div>

      {loading ? (
        <div className={styles.loadingRow}>
          <span className={styles.loadingDot} />
          <span className={styles.loadingDot} />
          <span className={styles.loadingDot} />
        </div>
      ) : (
        <div className={styles.grid}>
          {MODULES.map(mod => {
            const status = statusLabel(completions[mod.id]);
            return (
              <div key={mod.id} className={`${styles.card} ${styles[status]}`}>
                <div className={styles.cardTop}>
                  <span className={styles.moduleNum}>{mod.number}</span>
                  {status === 'completed' && (
                    <span className={styles.badge}>Complete</span>
                  )}
                  {status === 'in-progress' && (
                    <span className={`${styles.badge} ${styles.badgeProgress}`}>In progress</span>
                  )}
                </div>
                <h2 className={styles.cardTitle}>{mod.title}</h2>
                <p className={styles.cardDesc}>{mod.description}</p>
                <div className={styles.cardFooter}>
                  <span className={styles.duration}>{mod.duration}</span>
                  <button
                    className={`${styles.btn} ${status === 'completed' ? styles.btnGhost : ''}`}
                    onClick={() => launchModule(mod)}
                  >
                    {status === 'not-started' && 'Start module →'}
                    {status === 'in-progress' && 'Continue →'}
                    {status === 'completed' && 'Review'}
                  </button>
                </div>
              </div>
            );
          })}
        </div>
      )}
    </div>
  );
}
```

### 3b. Create `src/pages/Training/Training.module.css`

```css
.page {
  max-width: 680px;
  margin: 0 auto;
  padding: 32px 20px 80px;
}

.header {
  margin-bottom: 36px;
}

.eyebrow {
  display: inline-block;
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--bb-coral);
  margin-bottom: 10px;
}

.title {
  font-family: 'Playfair Display', serif;
  font-size: 32px;
  font-weight: 700;
  color: var(--bb-charcoal);
  margin-bottom: 10px;
  line-height: 1.2;
}

.subtitle {
  font-size: 15px;
  color: var(--bb-dark-gray);
  line-height: 1.7;
}

/* Loading dots */
.loadingRow {
  display: flex;
  gap: 8px;
  justify-content: center;
  padding: 60px 0;
}
.loadingDot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: var(--bb-light-gray);
  animation: pulse 1.2s ease-in-out infinite;
}
.loadingDot:nth-child(2) { animation-delay: 0.2s; }
.loadingDot:nth-child(3) { animation-delay: 0.4s; }
@keyframes pulse {
  0%, 80%, 100% { opacity: 0.3; transform: scale(0.9); }
  40% { opacity: 1; transform: scale(1); }
}

/* Grid */
.grid {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

/* Cards */
.card {
  background: var(--bb-white);
  border-radius: 20px;
  border: 1.5px solid var(--bb-light-gray);
  padding: 24px;
  transition: border-color 200ms, box-shadow 200ms;
}
.card:hover {
  border-color: var(--bb-coral);
  box-shadow: 0 4px 20px rgba(240, 135, 130, 0.12);
}
.completed {
  background: var(--bb-yellow-soft);
  border-color: var(--bb-yellow, #FFD437);
}
.completed:hover {
  border-color: var(--bb-yellow, #FFD437);
  box-shadow: 0 4px 20px rgba(255, 212, 55, 0.20);
}
.in-progress {
  border-color: var(--bb-coral);
}

.cardTop {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 14px;
}

.moduleNum {
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.08em;
  color: var(--bb-coral);
  background: var(--bb-coral-soft);
  padding: 4px 12px;
  border-radius: 50px;
}

.badge {
  font-size: 11px;
  font-weight: 700;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  background: #FFD437;
  color: #6d4c00;
  padding: 4px 12px;
  border-radius: 50px;
}
.badgeProgress {
  background: var(--bb-coral-soft);
  color: var(--bb-coral);
}

.cardTitle {
  font-size: 18px;
  font-weight: 700;
  color: var(--bb-charcoal);
  margin-bottom: 8px;
  line-height: 1.3;
}

.cardDesc {
  font-size: 14px;
  color: var(--bb-dark-gray);
  line-height: 1.7;
  margin-bottom: 20px;
}

.cardFooter {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
}

.duration {
  font-size: 12px;
  font-weight: 600;
  color: var(--bb-mid-gray);
}

.btn {
  background: var(--bb-coral);
  color: #fff;
  font-family: 'Inter', sans-serif;
  font-size: 14px;
  font-weight: 700;
  padding: 10px 24px;
  border-radius: 50px;
  border: none;
  cursor: pointer;
  min-height: 44px;
  white-space: nowrap;
  transition: transform 200ms ease, box-shadow 200ms ease;
}
.btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(240, 135, 130, 0.40);
}
.btn:focus {
  outline: 2px solid var(--bb-coral);
  outline-offset: 2px;
}
.btnGhost {
  background: transparent;
  color: var(--bb-charcoal);
  border: 1.5px solid var(--bb-light-gray);
}
.btnGhost:hover {
  border-color: var(--bb-charcoal);
  box-shadow: none;
}

@media (max-width: 480px) {
  .title { font-size: 26px; }
  .cardFooter { flex-direction: column; align-items: flex-start; }
  .btn { width: 100%; text-align: center; }
}
```

---

## Step 4 — Add `/training` route to App.jsx

**File:** `src/App.jsx`  
**Action:** Add the import and route. Find the section where other page imports live and add:

```jsx
// Add with other page imports
import Training from './pages/Training/Training';
```

Find where the protected staff routes are defined (the `<Route>` block for `/dashboard`) and add alongside it:

```jsx
<Route path="/training" element={
  <ProtectedRoute allowedRoles={['teacher', 'assistant', 'floater', 'director', 'admin']}>
    <Training />
  </ProtectedRoute>
} />
```

---

## Step 5 — Update "My Training" tile in StaffDashboard

**File:** `src/pages/dashboards/StaffDashboard/` (the main component file)  
**Action:** Find the "My Training" tile. It currently opens olilearn externally — it will look something like:

```jsx
// BEFORE — find something like this:
href="https://olilearn.netlify.app/..."
// or
onClick={() => window.open('https://olilearn.netlify.app...')}
```

Replace with an internal navigation. At the top of the file, add the `useNavigate` import if not already there:

```jsx
import { useNavigate } from 'react-router-dom';
```

Inside the component, add:
```jsx
const navigate = useNavigate();
```

Then update the My Training tile's action to:
```jsx
onClick={() => navigate('/training')}
```

And update the tile label — remove "(external)" if it's there.

---

## Step 6 — Deploy everything

```bash
# On the bb-platform machine:
cd C:\Users\robhi\bb-platform

# 1. Deploy functions first (so the URL exists)
firebase deploy --only functions

# 2. Get the recordTrainingCompletion URL from Firebase Console
#    Console → Functions → recordTrainingCompletion → copy trigger URL

# 3. Paste that URL into both bb-learn module files on the other machine:
#    C:\Users\rober\bb-learn\bb-module1.html  — replace 'PASTE_FUNCTION_URL_HERE'
#    C:\Users\rober\bb-learn\bb-module2.html  — replace 'PASTE_FUNCTION_URL_HERE'
#    Then commit and push bb-learn — Netlify auto-deploys

# 4. Deploy Firestore rules
firebase deploy --only firestore:rules

# 5. Build and deploy the React app
npm run build
netlify deploy --prod --dir=dist
```

---

## What this unlocks

Once deployed:

- Staff open `bbplatform.netlify.app/training` (via the My Training tile)
- They see module cards with live status — Not started / In progress / Complete
- Clicking a card opens the module in a new tab with their UID in the URL
- On module start → Firestore records `startedAt`
- On module complete → Firestore records `completedAt`
- When they close the tab and return to the platform, status updates automatically
- Directors and admins can query `users/{uid}/training/` to see completion records

## Adding future modules

When Module 3 is built, add one entry to the `MODULES` array in `Training.jsx`:

```jsx
{
  id: 'module-3',
  number: '03',
  title: 'Module title here',
  description: 'Description here.',
  duration: 'X min',
  url: `${PLATFORM_URL}/bb-module3.html`,
},
```

And add the same two blocks to `bb-module3.html`:
- `const MODULE_ID = 'module-3';`
- `const MODULE_TITLE = 'Module title here';`
- The rest of the integration code is identical.
