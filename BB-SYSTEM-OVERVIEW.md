# Bright Beginnings — Training System Overview

> Reference document for anyone building or maintaining the BB training ecosystem.  
> Last updated: May 2026

---

## What Are These Two Apps?

### bb-platform — `bbplatform.netlify.app`
The internal staff platform for Bright Beginnings. This is the hub where staff log in, access their profile, manage tasks, and track training progress. It is a **full React application** with user authentication and a live database.

- **Stack:** React 19 + Vite, React Router v7, Firebase Auth, Cloud Firestore, CSS Modules
- **Repo:** `C:\Users\robhi\bb-platform` (separate machine)
- **Auth:** Firebase email/password — every staff member has a `uid`
- **Database:** Cloud Firestore (Firebase project: `bb-platform-5c296`)
- **Deployed via:** Netlify (manual `netlify deploy --prod` after `npm run build`)

### bb-learn — `bblearns.netlify.app`
The training content delivery system. Each module is a **single, self-contained HTML file** — no frameworks, no build step, no server. Modules are authored here and deployed to Netlify automatically when pushed to GitHub.

- **Stack:** Vanilla HTML/CSS/JavaScript, Google Fonts only
- **Repo:** `github.com/robhichens/bb-learn` / local: `C:\Users\rober\bb-learn`
- **Deployed via:** Netlify auto-deploy on push to `main` (publish directory: `.`)
- **Current modules:** `bb-module1.html`, `bb-module2.html`

---

## How They Connect

```
Staff logs into bb-platform
        ↓
Clicks "My Training" tile on StaffDashboard
        ↓
Training.jsx fetches their completion records from Firestore
        ↓
Module cards render with status: not-started / in-progress / completed
        ↓
Staff clicks a module card
        ↓
Opens bblearns.netlify.app/bb-moduleN.html?uid=FIREBASE_UID
        ↓
Module records "started" → Cloud Function → Firestore
        ↓
Staff completes module
        ↓
Module records "completed" → Cloud Function → Firestore
        ↓
Staff returns to bb-platform tab
        ↓
visibilitychange event re-fetches Firestore → card updates to "completed"
```

### The Cloud Function
**Endpoint:** `https://us-central1-bb-platform-5c296.cloudfunctions.net/recordTrainingCompletion`  
**Method:** POST  
**Body:** `{ uid, moduleId, moduleTitle, event }` — `event` must be `"started"` or `"completed"`  
**Writes to:** `users/{uid}/training/{moduleId}` in Firestore  

The function validates that the uid exists before writing. Modules call it silently — if it fails for any reason, the learner experience is unaffected.

### Firestore Data Model
```
users/
  {uid}/
    training/
      module-1/
        moduleId: "module-1"
        moduleTitle: "Financial Literacy"
        status: "completed"
        startedAt: Timestamp
        completedAt: Timestamp
      module-2/
        ...
```

### Adding a New Module to bb-platform
Open `src/pages/Training/Training.jsx` on the bb-platform machine and add one object to the `MODULES` array:

```javascript
{ 
  id: 'module-3', 
  number: '03', 
  title: 'Trauma-Informed Care', 
  duration: '25 min', 
  url: `${PLATFORM_URL}/bb-module3.html` 
}
```

That's it. The Training page handles card rendering, status fetching, and linking automatically.

---

## BB Design System

Every training module must use these tokens. They are defined as CSS custom properties at the top of each HTML file.

### Color Palette

| Token | Hex | Use |
|---|---|---|
| `--coral` | `#F08782` | Primary brand, CTAs, active states |
| `--coral-soft` | `#FAE8E7` | Correct answer backgrounds, positive feedback |
| `--coral-dk` | `#C45E59` | Hover states on coral elements |
| `--yellow` | `#FFD437` | Accent, highlights, progress fill |
| `--yellow-soft` | `#FFF8D6` | Correct/affirming feedback backgrounds |
| `--yellow-dk` | `#6d4c00` | Text on yellow backgrounds |
| `--sky` | `#AEDFE5` | Secondary accent, informational |
| `--sky-soft` | `#E8F7F9` | Guided/neutral feedback backgrounds |
| `--sky-dk` | `#2a7a82` | Text on sky backgrounds |
| `--charcoal` | `#545454` | Primary body text |
| `--cream` | `#FAFAF5` | Page background |
| `--white` | `#FFFFFF` | Card surfaces |

**❌ Never use red.** Not for errors, not for wrong answers, not for warnings. Red reads as shame. Use `--sky-soft` for guidance and `--yellow-soft` for correct.

### Typography

| Use | Font | Weight |
|---|---|---|
| Display headings, hooks, pull quotes | Playfair Display | 700, 800, italic 700 |
| All UI — body, buttons, labels, nav | Inter | 300, 400, 500, 600, 700 |

Load from Google Fonts:
```html
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,700;0,800;1,700&family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
```

---

## Module Architecture

### File structure
Each module is one `.html` file. All CSS lives in a `<style>` block in `<head>`. All JavaScript lives in a `<script>` block before `</body>`. No external dependencies except Google Fonts.

### Progress persistence
Each module uses `localStorage` with a unique key:
- Module 1: `bb_module1_progress`
- Module 2: `bb_module2_progress`
- Module N: `bb_moduleN_progress`

Progress persists between sessions on the same device. The platform tracks true completion via Firestore — localStorage is the local UX layer only.

### Section unlock model
Modules are divided into sections (typically 5–6). Each section is locked until the previous one is completed. Completion is triggered by a defined interaction — finishing a quiz, clicking through all tabs, making a decision, etc. The progress bar at the top reflects how many sections are complete.

### Standard component patterns

| Component | When to use |
|---|---|
| **Accordion** | 3-item expandable for foundational concepts at the start of a module |
| **4-tab panel** | Four distinct categories that benefit from side-by-side comparison |
| **Decision guide** | Scenario with branching — choose a path, get consequences and guidance |
| **Step flow** | Linear 3–4 step process (e.g., "what to do when X") |
| **Timeline cards** | Chronological sequence, regulatory deadlines, historical context |
| **True/False quiz** | Low-stakes comprehension check mid-module |
| **Capstone scenario** | 5-question multi-part scenario at the end — the "unlock" gate for the milestone |
| **Milestone screen** | Full-screen celebration on completion, triggers Firestore write |
| **Help panels** | Slide-in panels for definitions, legal citations, CPS contacts — non-blocking |

### Feedback tone
- Correct answers: `--yellow-soft` background, affirming language ("Exactly right.", "You got it.")
- Guided answers: `--sky-soft` background, explanatory language — never "Wrong." Always "Here's what to keep in mind..."
- Partial credit: acknowledge what was right before redirecting

---

## Voice & Tone for Bright Beginnings Modules

Bright Beginnings serves families and youth in Virginia, often in high-stress circumstances. Staff are practitioners, not academics. Write for someone on shift.

**Do:**
- Use plain language. If a sentence needs a second read, rewrite it.
- Ground every concept in a real scenario. "Here's what this looks like on a Tuesday."
- Acknowledge the difficulty of the work — don't sanitize it.
- Credit staff expertise. "You may already do this naturally — here's the language for it."
- Use Virginia-specific legal language where relevant (Virginia Code §, DSS, CPS hotline numbers).

**Don't:**
- Use corporate training voice ("Upon completion of this module, learners will be able to...")
- List information that should be taught through scenario
- Write trick questions
- Use any language that implies a wrong answer is a character failure

### CPS / Legal reference constants (Virginia)
- **Mandated reporting hotline:** 1-800-552-7096 (24/7)
- **Mandated reporting law:** Virginia Code § 63.2-1509
- **Standard for reporting:** Reasonable suspicion — not proof, not certainty
- **Reporting window:** Immediately, or as soon as practicable

---

## Current Module Inventory

| # | ID | Title | Duration | Status |
|---|---|---|---|---|
| 01 | `module-1` | Financial Literacy | 25 min | ✅ Live |
| 02 | `module-2` | Child Safety & Mandated Reporting | 30 min | ✅ Live |

---

## Planned Curriculum — Modules 3–10

| # | Title | Core Focus | Priority |
|---|---|---|---|
| 03 | Trauma-Informed Care | How trauma shows up in behavior; regulated staff response | High — foundational for all direct practice |
| 04 | Boundaries & Professional Ethics | Dual relationships, social media, gifts, reporting obligations | High |
| 05 | De-escalation & Crisis Response | Reading escalation, verbal tools, when to call for backup | High |
| 06 | Cultural Humility & Equity | Implicit bias, culturally responsive practice, not-knowing stance | Medium |
| 07 | Domestic Violence & Intimate Partner Violence | Coercive control, safety planning, lethality indicators | High |
| 08 | Confidentiality, HIPAA & Information Sharing | Releases, minimum necessary rule, safety vs. privacy | Medium |
| 09 | Documentation & Case Notes | Objective language, legal discoverability, timeliness | Medium |
| 10 | Secondary Trauma & Staff Wellness | Compassion fatigue, vicarious trauma, organizational responsibility | Medium |

---

## Building a New Module — Checklist

- [ ] Copy design tokens and font links from an existing module
- [ ] Set unique `STORAGE_KEY` — `bb_moduleN_progress`
- [ ] Set `MODULE_ID` — `module-N`
- [ ] Set `MODULE_TITLE` — matches the title in `Training.jsx` MODULES array
- [ ] `TRAINING_API` points to the Cloud Function URL (already set in template)
- [ ] `recordTrainingEvent('started')` fires in `startModule()` — first start only
- [ ] `recordTrainingEvent('completed')` fires in `triggerMilestone()`
- [ ] "← Back to My Training" button present, shown only when `uid` param exists
- [ ] No red used anywhere in feedback or UI
- [ ] Test: open with `?uid=test123` — back button should appear
- [ ] Push to `main` → Netlify deploys → add to `MODULES` array in `Training.jsx`
