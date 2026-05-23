# BB Learn — Module 2 Build Prompt
## "Child Safety & Mandated Reporting"
### Paste this entire prompt to start the build.

---

You are building **Module 2** of the **BB Learning Platform** for Bright Beginnings
Preschool — a multi-site childcare organization in Charlottesville, Virginia.

This is a **single self-contained HTML file** with embedded CSS and JavaScript.
No frameworks. No build tools. No dependencies except Google Fonts.
It must run by opening the file directly in a browser.

The finished file is: `bb-module2.html`

---

## Reference — Module 1

Module 1 (`bb-module1.html`) established the design system, architecture, and
interaction patterns. Module 2 must match it exactly in visual language, tone,
and technical approach. Do not deviate from the design system below.

---

## The North Star

> "A digital colleague — always available, never makes you feel stupid, knows
> the answer, and walks you to it at your pace."

This topic carries emotional weight. Staff may feel afraid of being wrong,
worried about a family, unsure if something "counts," or anxious about
consequences. The module must normalize that anxiety — and replace it with
clarity, confidence, and a concrete process.

**The outcome is not compliance. It is a staff member who knows exactly what
to look for, knows they are legally protected, and knows precisely what to do.**

---

## Design System

### Fonts
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,700;0,800;1,700&family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
```

### CSS Variables
```css
:root {
  --coral:        #F08782;
  --coral-soft:   #FAE8E7;
  --coral-dk:     #C45E59;
  --yellow:       #FFD437;
  --yellow-soft:  #FFF8D6;
  --yellow-dk:    #6d4c00;
  --sky:          #AEDFE5;
  --sky-soft:     #E8F7F9;
  --sky-dk:       #2a7a82;
  --mid-gray:     #A7A9AC;
  --lt-gray:      #D1D2D4;
  --dk-gray:      #6D6E71;
  --charcoal:     #545454;
  --cream:        #FAFAF5;
  --white:        #FFFFFF;
}
```

### Color Semantics — DO NOT deviate
| Color  | Use for                                       | Never use for  |
|--------|-----------------------------------------------|----------------|
| Coral  | CTAs, active progress, forward-motion buttons | Errors, danger |
| Yellow | Achievements, milestones, "great thinking"    | Warnings       |
| Sky    | Help panels, gentle correction, support       | Danger         |
| Red    | **Does not exist in this system**             | Anything       |

> **Topic note:** This module covers serious subject matter. The color system
> still applies. Gravity comes from the writing — not from red alerts or
> alarming visuals. Keep the palette warm and clear.

### Typography
- **Playfair Display** — situation hooks and milestone titles ONLY
- **Inter** — all UI, body copy, labels, buttons, navigation

### Spacing & Shapes
- Cards: border-radius 16–20px
- Pills/badges: border-radius 50px
- Left-border accent (situation hooks, callouts): 4px, border-radius 0
- Max content width: 560px, centered
- Touch targets: min-height 44px
- Body copy: 15px, line-height 1.8, color var(--dk-gray)

---

## Architecture

### Single-file structure
```
<html>
  <head> — fonts, all CSS in one <style> block </head>
  <body>
    #app
      #cover-screen
      #progress-bar    — fixed top strip
      #module-content
        .section       — 6 sections, stacked
      #milestone-card  — completion screen
  </body>
  <script> — all JS at bottom </script>
</html>
```

### Navigation model
- No page routing. All sections exist in the DOM, stacked vertically.
- Sections revealed progressively — next section fades in after current
  section's interaction is complete.
- Locked sections show: "Complete the section above to continue."
- "Continue" buttons smooth-scroll to the next section.
- Progress bar tracks completed sections.

### Progress tracking
- Stored in `localStorage` under key `bb_module2_progress`
- Object: `{ completedSections: [], quizAnswers: {}, startedAt, completedAt }`
- On reload: restore scroll position and unlock completed sections.
- Progress bar label: **"You've completed Step X of 6"** — never a percentage.

---

## Legal & Factual Foundation

Build all content on these verified facts. Do not alter them.

**Virginia Law:** Code of Virginia § 63.2-1509
All childcare workers, teachers, and assistants in Virginia are **mandatory
reporters** by law. This is not a company policy — it is a legal obligation.

**The standard:** Reasonable suspicion. Not proof. Not certainty.
If you reasonably suspect abuse or neglect — you report. The investigation
is not your job.

**Who to call:** Virginia Child Protective Services (CPS)
**Hotline:** 1-800-552-7096 (24 hours, 7 days)

**Timing:** Report immediately — oral report as soon as possible,
written report within 72 hours.

**Protection:** Good-faith reporters are legally protected from retaliation.
You cannot be fired for making a report in good faith.

**Anonymity:** You may report anonymously, but identifying yourself as a
mandated reporter is preferred and recommended.

**BB internal step:** After calling CPS, notify your site director immediately.

---

## Module Content — All 6 Sections

Build each section exactly as specified. Do not summarize or shorten the content.

---

### SECTION 0 — Cover / Hero Screen

**Layout:** Full-viewport cover. Charcoal background (#545454).
Decorative blobs: coral top-right (opacity 0.10), yellow bottom-left (opacity 0.08).

**Content:**
- Eyebrow pill: "Module 2 · Child Safety" — coral pill, Inter 11px 700 uppercase
- Title (Playfair Display 42px 800 white):
  *"A child in your classroom is showing signs that worry you. Do you know what to do next?"*
- Subtitle (Inter 16px rgba(255,255,255,0.60)):
  "By the end of this module, you'll know exactly what to look for, when to act,
  and how — with no second-guessing."
- Who this is for (Inter 13px rgba(255,255,255,0.40)):
  "Required for all BB staff — teachers, assistants, floaters, and site admins."
- CTA button: coral pill, Inter 700 14px white, "Start Module 2 →"
  On click: hide cover, reveal Section 1, show progress bar.

---

### SECTION 1 — You Are a Mandated Reporter

**Eyebrow label:** "Your Legal Role"

**Situation Hook Card:**
- Yellow-soft background, 4px left border yellow, border-radius 0 16px 16px 0
- Playfair Display 22px 700 italic charcoal:
  *"A parent drops off a child with unexplained bruising on their arms. The child
  seems withdrawn. You're not sure if it's anything. You don't want to get anyone
  in trouble. You don't say anything. Under Virginia law, that silence has consequences."*

**Body content** (Inter 15px dk-gray 1.8lh):

> Working with children is one of the most trusted roles in any community.
> Part of that trust — and part of your legal responsibility — is knowing
> when a child needs protection.
>
> In Virginia, every childcare worker, teacher, and assistant is a mandated
> reporter. That's not a BB policy. It's state law: **Code of Virginia § 63.2-1509.**
>
> Being a mandated reporter means one thing: if you reasonably suspect that a
> child has been abused or neglected, you are required by law to report it.
> Not investigate it. Not prove it. Report it.

**Key distinction callout:**
- Sky-soft background, 4px left border sky, border-radius 0 12px 12px 0
- Label (Inter 11px 700 uppercase sky-dk): "The standard that matters"
- Body: "You do not need proof. You do not need to be certain. You need
  **reasonable suspicion** — a genuine concern based on what you've seen or heard.
  The investigation is CPS's job, not yours."

**Common fear — addressed directly:**
Build a 3-item accordion (each item expands on click):
- "What if I'm wrong?" →
  "Good-faith reporters are legally protected. If you report honestly based
  on what you observed, you cannot be fired, sued, or penalized — even if
  the report is not confirmed. The law protects people who act in good faith."
- "What if it affects the family?" →
  "Your job is to report what you observed. CPS determines what happens next.
  Families who need support get connected to services. That process can only
  start if someone speaks up."
- "What if my director disagrees?" →
  "Your reporting obligation is personal — it belongs to you, not to BB as
  a company. Even if a director asks you not to report, your legal obligation
  remains. You report, then notify your director."

**Unlock:** "I understand my role — show me what to look for →" coral button → reveals Section 2.

---

### SECTION 2 — Recognizing Signs of Concern

**Eyebrow label:** "What to Look For"

**Intro:**
> You don't need to be a social worker to recognize the signs. You just need
> to know what's normal — and what's worth noticing.
>
> The signs below don't always mean abuse or neglect. But they are worth
> taking seriously, documenting, and — if they persist or combine — reporting.

**Four-tab component** (Inter 13px 700 tab labels):
Tabs: Physical Abuse | Neglect | Emotional Abuse | Sexual Abuse

Each tab shows a card with:
- Tab badge (coral pill with tab name)
- Section: "Observable signs" — bulleted list
- Section: "What you might hear a child say" — italicized examples
- Section: "What's normal vs. what's concerning" — brief note

**Tab content:**

**Physical Abuse**
Observable signs:
- Unexplained bruises, burns, cuts, or welts
- Bruising in unusual locations: torso, back, ears, neck, face
  (bruising on shins and knees is typical in toddlers — other locations are not)
- Patterned marks that suggest an object (cord, belt buckle, hand)
- Injuries inconsistent with the explanation given
- Child flinches, recoils, or shows fear at sudden movements from adults
- Reluctance to go home or to a specific person

What you might hear:
- *"My dad hurt me."*
- *"I fell"* — paired with an injury that doesn't fit
- *"I'm not supposed to talk about it."*

Normal vs. concerning:
Scrapes and bruises on knees, shins, and forehead are typical for active
toddlers. Multiple bruises in soft tissue areas, patterned marks, or injuries
the child can't explain are not.

---

**Neglect**
Observable signs:
- Consistently hungry — asking for food, hoarding snacks
- Poor hygiene — unwashed clothing, body odor, matted hair (ongoing, not occasional)
- Clothing inappropriate for the weather
- Untreated medical or dental conditions
- Extreme fatigue — falling asleep repeatedly, reports of not sleeping at home
- Child left unsupervised at home or describes being alone regularly

What you might hear:
- *"There's no food at my house."*
- *"Nobody was home when I got there."*
- *"I don't have a jacket."*

Normal vs. concerning:
Children have off days — occasional messy hair or a forgotten jacket is normal.
A pattern of the same concerns across multiple days or weeks is not.

---

**Emotional Abuse**
Observable signs:
- Extreme behaviors: severe aggression, or extreme withdrawal and passivity
- Persistent low self-esteem: *"I'm stupid," "I can't do anything right"*
- Delayed emotional development
- Excessive need for approval or fear of making mistakes
- Child describes being called names, threatened, or humiliated at home

What you might hear:
- *"My mom says I'm bad."*
- *"I'm going to get in trouble if I do that wrong."*
- *"Nobody likes me at home."*

Normal vs. concerning:
All children have moments of self-doubt or emotional outbursts. A persistent
pattern — especially language a child wouldn't naturally have without hearing it
repeatedly — is worth documenting.

---

**Sexual Abuse**
Observable signs:
- Age-inappropriate sexual knowledge, language, or behavior
- Reluctance to be around a specific adult (that was not previously present)
- Physical indicators (bruising or discomfort in private areas)
- Regressive behavior: bedwetting, thumb-sucking after developmental milestone
- Nightmares or sleep disruption reported by parents or observed at rest time
- Drawing or play with sexual themes beyond what's developmentally expected

What you might hear:
- A child describing sexual acts using language they shouldn't know
- *"[Person] touches me in a bad way."*
- *"I have a secret I'm not allowed to tell."*

Normal vs. concerning:
Young children naturally explore their bodies and have some curiosity about
differences. Explicit sexual knowledge, grooming-type language ("this is our
secret"), or physical indicators are not developmentally normal.

---

**Documentation callout** (sky-soft, sky border):
"If you notice any of these signs — write it down the same day.
Date, time, what you observed, exactly what the child said (in their words).
Do not interpret or editorialize. Stick to what you saw and heard.
This documentation matters — both for your protection and for the child's."

**Unlock:** "I know what to look for — now show me how to report →" coral button → reveals Section 3.

---

### SECTION 3 — When to Report

**Eyebrow label:** "The Threshold"

**Situation Hook Card:**
- *"You've noticed things over the past two weeks. A bruise here. A comment there.
  Nothing you can 'prove.' You keep waiting to be sure. Here's what Virginia law says
  about waiting until you're sure."*

**The answer:**
> Virginia law does not require certainty. It requires **reasonable suspicion.**
>
> If you have observed signs that a reasonable person in your position would
> find concerning — that is enough. The moment you cross that threshold,
> the obligation to report is active.

**Decision guide — interactive component:**

Three scenario cards, each with two buttons: "Report" or "Document & Watch"
After each answer, reveal the recommended action and reasoning.

1. *"A 4-year-old has a large bruise on their upper arm. When you ask about it,
   they say 'I don't know.' The parent at drop-off said nothing."*
   → **Report.** Unexplained bruising in a soft tissue area in a child who
   can't explain it meets the threshold for reasonable suspicion.

2. *"A child comes in without a proper coat for the third time this week in
   40-degree weather. They say 'we don't have one.'"*
   → **Report.** A pattern of a child being exposed to the elements without
   adequate clothing is a form of neglect — not a one-time oversight.

3. *"A child had a meltdown yesterday and today seems tired and sad.
   Drop-off seemed rushed."*
   → **Document & Watch.** This is not unusual behavior. Document what you
   observed with date and time. Look for patterns before escalating.

**After all 3:** reveal a callout:
- Sky-soft, sky border
- "When in doubt — document it. If concern grows, report it.
  You will never be penalized for documenting too carefully.
  But failing to report when the threshold was crossed is a legal risk."

**Unlock:** "I understand when to report — show me how →" coral button → reveals Section 4.

---

### SECTION 4 — How to Report

**Eyebrow label:** "The Process"

**Situation Hook Card:**
- *"You know you need to report. Now what? Who do you call? What do you say?
  What happens after? Here is the exact process — step by step."*

**3-step visual flow** (vertical, connecting lines):

**Step 1** (coral circle):
"Call Virginia CPS — right away."
Sub: "Do not wait for the end of the day. Do not ask a coworker to do it.
The obligation is yours. Call as soon as it is safe to do so."

**Virginia CPS contact card** (white card, coral-soft header):
- Name: Virginia Child Protective Services
- Hotline: **1-800-552-7096**
- Hours: 24 hours a day, 7 days a week
- "Copy number" button (coral outline)
- What to have ready (expandable):
  - Child's name, age, and classroom
  - Your name and role (you are a mandated reporter)
  - What you observed — dates, specific behaviors, exact words the child used
  - Any prior documentation you've made

**What to say — tap to copy template:**
*"My name is [your name]. I am a childcare worker at Bright Beginnings Preschool
in Charlottesville, Virginia. I am a mandated reporter and I am calling to report
a concern about a child in my care. The child's name is [name], age [age].
I have observed [describe what you saw, in your own words]."*

**Step 2** (charcoal circle):
"Notify your site director — immediately after."
Sub: "Once the CPS call is made, tell your director what you observed and
that you have reported. This is not asking permission — it is informing them."

**Step 3** (sky circle):
"Complete a written report within 72 hours."
Sub: "CPS will tell you what form is required. Your director can also help with
this. The written report supplements the oral report — both are required."

**What NOT to do** (sky-soft callout):
- Do not confront the parent or caregiver about your suspicion
- Do not promise a child that you will "keep it between us"
- Do not wait to see if it happens again before reporting
- Do not investigate on your own — your job is to report, not to determine what happened
- Do not discuss the report with coworkers who are not directly involved

**Unlock:** "Got it — what happens after I report? →" coral button → reveals Section 5.

---

### SECTION 5 — After You Report

**Eyebrow label:** "What Happens Next"

**Intro:**
> Making a report can feel heavy. You may feel uncertain, worried about the
> child, or anxious about the family. Those feelings are normal. Here is
> what you can expect after the call.

**Timeline flow** — horizontal scroll on mobile, visible cards on desktop:

**Card 1 — Immediately**
CPS receives your report and assigns it a priority level based on the information
you provided. A high-priority report will receive a response within 24 hours.

**Card 2 — The investigation (not your job)**
CPS investigators are trained for this. They will contact the family,
interview the child (in a child-appropriate setting), and consult with
other professionals. You may or may not be contacted for follow-up.

**Card 3 — Confidentiality**
The contents of your report are confidential by law. You will not be told
the outcome of the investigation unless you are directly involved in proceedings.
This is not a failing of the system — it is protection for the child.

**Card 4 — Your role going forward**
Continue to document your observations. Be consistent in how you treat
the child — warmth, routine, and stability are protective factors.
If you are contacted by CPS for a follow-up interview, cooperate fully.

**Your protection — callout** (yellow-soft, yellow border):
"Virginia law protects good-faith reporters from civil and criminal liability.
If you reported based on honest observation — you are protected. Always.
Your job is to report what you saw. What happens next is not your burden to carry."

**If you need support:**
- Sky-soft card
- "Reporting is emotionally hard. If you are struggling after making a report,
  talk to your director or contact BB's EAP (Employee Assistance Program).
  You don't have to process this alone."

**Quick quiz** (unlocks Section 6):

> *True or false — you should wait until you are certain abuse has occurred
> before making a report.*

Two tap buttons: True | False
- False: correct — yellow-soft: "Exactly right. Reasonable suspicion is the threshold — not certainty.
  The investigation is CPS's job."
- True: guided — sky-soft: "Actually, Virginia law requires reporting at the level of reasonable
  suspicion — which means a genuine concern based on what you've observed.
  Certainty is not the standard. CPS determines what happened — your job is
  to report the concern."

**Unlock:** "Last section — let's practice the full process →" coral button → reveals Section 6.

---

### SECTION 6 — Practice & BB Protocol

**Eyebrow label:** "Putting It Together"

**Situation Hook Card:**
- *"Everything you've learned is only useful if you can apply it in the moment —
  when you're tired, when you're uncertain, and when it involves a child you know."*

**BB Protocol summary card** (white card, coral top accent 4px):
Title: "BB's Mandated Reporting Protocol"
Steps (numbered, coral circle badges):
1. Trust your instinct — document what you observed with date and time
2. When reasonable suspicion exists — call CPS: **1-800-552-7096**
3. Notify your site director immediately after
4. Complete written report within 72 hours
5. Continue to support the child with warmth and routine

"Save this protocol" — coral outline button (copies to clipboard)

**Final capstone scenario:**

> *You are a teacher at BB's Greenbrier site. Over the past three days,
> you've noticed that 3-year-old Jaylen has been unusually quiet and clingy.
> Today at arrival, you notice redness and what looks like a linear mark on
> the back of his left hand. When you gently ask if he's okay, he says
> "I got in trouble." His dad drops him off quickly without making eye contact.*

Five questions, revealed one at a time:

**Q1:** Do you have enough to report?
- Two tap buttons: Yes — report now | No — wait and watch
- Answer: Yes
- Correct: "That's right. You have a physical mark, a behavioral change over
  several days, and a statement from the child. Together, these meet the threshold
  for reasonable suspicion."
- Guided: "Let's think about what you have: a pattern of behavioral change,
  a physical mark in an unusual location, and a child saying 'I got in trouble.'
  Combined, these meet the threshold. You don't need more."

**Q2:** What is your first call?
- Three tap options: Your site director | Virginia CPS — 1-800-552-7096 | Jaylen's parent
- Answer: Virginia CPS
- Correct: "Exactly. CPS first. Then your director. Never the parent — not before the report."
- Guided: "Your director is step two, not step one. And you never contact the parent
  before making the report — that could put the child at greater risk. CPS first."

**Q3:** What do you tell CPS?
- Three tap options:
  - "I think a child might be being abused"
  - Jaylen's name and age, what you observed over three days, and the child's exact words
  - "A parent seems aggressive and I'm concerned"
- Answer: Jaylen's name and age, what you observed over three days, and the child's exact words
- Correct: "Exactly right. Specific, factual, and based on direct observation.
  Dates, what you saw, and the child's words verbatim."
- Guided: "CPS needs specifics — the child's name and age, what you observed and when,
  and exactly what Jaylen said. Vague concerns are harder to act on. Be specific."

**Q4:** You make the CPS call. Jaylen's dad walks back in to drop off a forgotten bag and asks if everything is okay. What do you say?
- Three tap options:
  - "I just called CPS about a concern I had."
  - "Everything is fine — have a great day."
  - "I've noticed some things I wanted to check on. Nothing to worry about."
- Answer: "Everything is fine — have a great day."
- Correct: "Right. You do not disclose the report to the parent. CPS will contact
  the family through the appropriate channel. Your job is done — let the process work."
- Guided: "Disclosing the report to the parent could put Jaylen at risk before
  CPS has a chance to respond. Say nothing about the report. Warmly and normally.
  Let CPS handle the family contact."

**Q5:** How do you feel — and what's important to remember?
- Three tap options:
  - "Guilty — what if I was wrong?"
  - "I reported what I honestly observed. I'm protected, and I did the right thing."
  - "Relieved it's over and I don't need to think about it again."
- Answer: "I reported what I honestly observed. I'm protected, and I did the right thing."
- Correct: "That's exactly right. Good-faith reporters are legally protected.
  You may never know the outcome — but you gave Jaylen the best possible chance.
  That is the job."
- Guided: "Being wrong doesn't make you liable — reporting in good faith does.
  And it's not entirely over: continue documenting your observations and supporting
  Jaylen with consistency and warmth."

After all 5: trigger milestone screen.

---

### MILESTONE SCREEN — Module Complete

Full-screen overlay with yellow background.

**Layout:**
- Large icon: coral star/checkmark circle (52px), white bg circle
- Title (Playfair Display 32px 700 charcoal): "Module 2 complete."
- Sub (Inter 15px yellow-dk): "You now know how to recognize signs of concern,
  understand your legal role as a mandated reporter, and know exactly what
  to do — step by step — if a child needs protection."
- Spacer
- "What you've learned" — 4 items with coral circle checkmarks (Inter 14px):
  1. The signs of physical abuse, neglect, emotional abuse, and sexual abuse
  2. Your legal obligation as a mandated reporter under Virginia law
  3. The reporting process — who to call, what to say, and what happens next
  4. BB's internal protocol and how to support a child after a report
- CTA button (charcoal pill, white text): "Save BB Protocol Card"
  → Triggers print/save of the BB Protocol card from Section 6
- Secondary link (Inter 13px coral): "Review any section →" → smooth scrolls to top

**localStorage:** Mark module as complete, record timestamp.

---

## Interaction & Animation Rules

```
Section reveal:    opacity 0 → 1, translateY(16px) → 0, duration 400ms ease-out
Accordion open:    max-height 0 → auto, opacity 0 → 1, 300ms ease
Tab switch:        opacity fade 150ms, no slide (content is dense)
Feedback flash:    background-color transition 200ms
Button hover:      translateY(-2px), box-shadow increase, 200ms ease
Quiz correct:      yellow-soft bg fade in
Quiz guided:       sky-soft bg fade in — no red, no X, no "wrong"
Progress bar fill: coral, width transition 600ms ease
```

---

## Accessibility & Mobile Rules

- All interactive elements: min 44px height
- Focus rings: 2px coral outline, 2px offset
- Font sizes: never below 13px
- Color is never the ONLY indicator (always paired with icon or label)
- Touch targets: no two interactive elements closer than 8px
- Tap-to-select: clear visual selected state before confirming

---

## Help System

A quiet "I need help with this" link lives at the bottom of every section.
- Inter 12px, mid-gray, underline on hover
- Never prominent. Never alarming.
- On click: expands a sky-soft help panel with relevant contact or next step
- Section-specific help text:

| Section | Help panel content |
|---|---|
| 1 | "Questions about your role as a mandated reporter? Talk to your director or call the CPS hotline for guidance: 1-800-552-7096." |
| 2 | "Not sure if what you're seeing is a concern? Document it, date it, and bring it to your director. When in doubt — write it down." |
| 3 | "Unsure whether to report? The CPS hotline accepts consultation calls — you can describe what you've observed and they will help you decide. 1-800-552-7096." |
| 4 | "Need help with the reporting process? Your director can walk through it with you. But remember: the obligation to report is yours — not your director's." |
| 5 | "Feeling unsettled after reporting? Talk to your director or access BB's Employee Assistance Program. You don't have to carry this alone." |
| 6 | "Questions about BB's protocol? Ask your site director. They have the same training you do." |

---

## Content & Voice Rules

- **Second person always.** "You notice a bruise" — not "The teacher notices."
- **Application first.** Scenario before rule. Always.
- **Short sentences for key instructions.** One idea per sentence.
- **No jargon without plain-language definition.**
- **Warm, not alarming.** Gravity comes from clarity — not from dramatic language.
- **No red. No "Wrong." No "Incorrect." No "Try again."** Ever. In any state.
- **Progress = forward movement, not scoring.**

---

## Final Deliverable Checklist

- [ ] Cover/hero screen with start button
- [ ] Fixed progress bar (hidden on cover, appears after start)
- [ ] All 6 sections with progressive unlock
- [ ] 3-item accordion (Section 1 — common fears)
- [ ] Four-tab signs component (Section 2)
- [ ] 3-scenario decision guide: Report or Document & Watch (Section 3)
- [ ] 3-step visual flow with CPS contact card + copyable message (Section 4)
- [ ] Timeline flow cards — what happens after reporting (Section 5)
- [ ] True/False quiz (Section 5)
- [ ] BB Protocol card with copy/save (Section 6)
- [ ] 5-question capstone scenario (Section 6)
- [ ] Milestone completion screen
- [ ] Help panel on every section with section-specific content
- [ ] localStorage progress persistence (`bb_module2_progress`)
- [ ] Smooth-scroll navigation
- [ ] Mobile-responsive (375px base, max-width 560px content)
- [ ] BB brand colors and fonts throughout
- [ ] Zero red states. Zero "wrong" language. Zero percentages.
- [ ] CPS number (1-800-552-7096) copyable in Section 4
- [ ] BB Protocol card printable in Section 6

---

## Output

Deliver as a single file: `bb-module2.html`

Clean, well-structured, fully functional. No placeholder content.
No "TODO" comments. No lorem ipsum. Every word specified above — use it.

---

*BB Learn · Module 2 Build Prompt v1.0*
*Bright Beginnings Preschool · Charlottesville, VA*
*Rob Hichens · May 2026*
