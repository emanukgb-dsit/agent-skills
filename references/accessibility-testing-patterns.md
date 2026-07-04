---
name: accessibility-testing-patterns
description: Test patterns for inclusive design, assistive tech compatibility, and representative user testing. Use with frontend-ui-engineering and test-driven-development skills.
---

# Accessibility Testing Patterns

Testing accessibility is not "run axe, call it done." It's three pillars: **automated checks** (catch 30%), **manual testing** (catch another 40%), **representative testing** (catch the remaining 30%). Most teams do 1-2. Do all three.

---

## Pillar 1: Automated Checks (Foundation)

### Tool Stack

| Tool | Coverage | Platform | Free? | Integrates? |
|------|----------|----------|-------|------------|
| **axe** | WCAG 2.2 AA/AAA | Browser, Node | Yes | Yes (CI, browser plugin) |
| **WAVE** | WCAG 2.2 AA | Browser | Yes (freemium) | Via API |
| **Lighthouse** | Web Vitals + a11y | Chrome DevTools | Yes | Built-in |
| **Pa11y** | WCAG 2.1 | CLI, Node | Yes | Yes (CI) |
| **Deque axe-core** | WCAG 2.2 AAA | npm package | Yes | Yes (embed in your tests) |

### Setup Example (React + Playwright)

```typescript
// accessibility.test.ts
import { test, expect } from '@playwright/test';
import { injectAxe, checkA11y } from 'axe-playwright';

test('Homepage meets WCAG 2.2 AA standards', async ({ page }) => {
  await page.goto('http://localhost:3000');
  
  // Inject axe and run scan
  await injectAxe(page);
  await checkA11y(page, null, {
    detailedReport: true,
    detailedReportOptions: {
      html: true
    }
  });
});

test('Payment form passes accessibility scan', async ({ page }) => {
  await page.goto('http://localhost:3000/payment');
  await injectAxe(page);
  
  // Exclude vendor iframes if needed
  await checkA11y(page, '#app', {
    rules: {
      'iframe-title': { enabled: false }
    }
  });
});
```

### CI Integration (GitHub Actions)

```yaml
name: Accessibility Tests

on: [push, pull_request]

jobs:
  a11y:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run build
      - run: npx playwright install
      - run: npx playwright test accessibility.test.ts
      - uses: daun/playwright-report-comment@v3
        if: always()
        with:
          report-path: 'playwright-report'
```

### What Automated Checks Catch

✅ **Catches reliably:**
- Missing alt text
- Color contrast violations
- Missing form labels
- Heading hierarchy breaks
- ARIA syntax errors
- Missing landmarks (nav, main, etc)
- Keyboard trap detection

❌ **Misses (needs manual testing):**
- Logical reading order
- Whether alt text is *meaningful* (not just "image123.jpg")
- Keyboard nav *usability* (technically works but is awkward)
- Screen reader verbosity (technically correct but confusing)
- Cognitive load / overwhelm
- Form error messaging clarity
- Timing issues (form submits in 3 seconds; can't read instructions)

---

## Pillar 2: Manual Testing (Spot Checks)

### Keyboard Navigation Audit

**Tool:** Keyboard only (disable mouse entirely)

```
1. Can every interactive element be reached via Tab?
2. Is the Tab order logical? (left-to-right, top-to-bottom)
3. Is focus always visible? (Can you see where Tab took you?)
4. Can every action be completed via keyboard (Enter/Space)?
   - Form submission? ✓
   - Opening menus? ✓
   - Dismissing modals? ✓
5. Are there any keyboard traps? (can't Tab back out)
6. Does Escape close modals/popovers?
```

**Test checklist:**

```markdown
- [ ] Start at page top; Tab through full page
- [ ] Focus indicator has 3:1 contrast against background
- [ ] Tab order: navigation → main content → footer (not jumping around)
- [ ] All buttons/links reachable via Tab
- [ ] Form submission works via Enter key
- [ ] Dropdown menus open via Enter/Space, navigate via arrow keys, close via Escape
- [ ] Modal: Tab cycles within modal only; Escape closes it; focus returns to trigger
- [ ] Skip link works: Can user jump to main content?
```

### Screen Reader Testing (NVDA on Windows, VoiceOver on Mac)

**Windows: NVDA (free)**

```bash
# Download
https://www.nvaccess.org/

# Basics
- Start reading: Ctrl + Home (top of page)
- Read next: Down arrow
- Jump to next heading: H
- Jump to next form field: F
- List links on page: K
- List form fields: F7
- Announce current item: Ctrl + numpad 5
```

**Mac: VoiceOver (built-in)**

```bash
# Enable
System Preferences → Accessibility → VoiceOver → Enable

# Basics
- Start reading: VO (Control + Option) + A
- Read next: VO + Right arrow
- Jump to next heading: VO + Command + H
- Jump to next link: VO + Command + L
- Open rotor: VO + U
- Exit: VO + Q
```

**Screen reader test script:**

```markdown
## Heading Structure
- [ ] Page has exactly one <h1>
- [ ] Headings are sequential (h1 → h2 → h3, no h1 → h3 jumps)
- [ ] Heading text describes the section

## Lists
- [ ] Lists are announced as "list with X items"
- [ ] List items are announced as "1 of X"

## Forms
- [ ] Label is read with input field
- [ ] Error messages are read when field is focused
- [ ] Form structure is clear (fieldset → legend announced)

## Images & Icons
- [ ] Image alt text is read
- [ ] Icon-only buttons have aria-label
- [ ] Decorative images are marked aria-hidden="true"

## Links
- [ ] Link text is descriptive ("Read more" vs "Click here to read more about accessibility")
- [ ] Links announce destination

## Buttons
- [ ] Button text is read
- [ ] Button state is announced (pressed, disabled, loading)

## Dynamic Content
- [ ] Page updates announced via aria-live
- [ ] Notifications don't startle user (aria-live="polite" not "assertive")
```

### Readability Test

**Tools:**
- Flesch-Kincaid Grade Level (automated)
- Hemingway Editor (visual guidance)
- Microsoft Accessibility Checker (built-in Word/Office)

**Target:**
- Public sector citizen-facing: Flesch-Kincaid Grade ≤6
- Internal/expert tools: Grade ≤8 acceptable
- Health/benefits guidance: Grade ≤5 (lowest cognitive load)

**Automated check (Node):**

```bash
npm install flesch-kincaid

# Test file
node -e "
const fk = require('flesch-kincaid');
const text = require('fs').readFileSync('./guidance.md', 'utf-8');
const score = fk.gradeLevel(text);
console.log(\`Grade level: \${score}\`);
"
```

**Common plain language patterns:**

| Before | After | Why |
|--------|-------|-----|
| "Utilize the portal to submit your application" | "Use the website to submit your form" | Shorter words, active voice |
| "In the event that you are unable to attend" | "If you can't come" | Contractions + common phrasing |
| "Subsequent to the submission" | "After you submit" | Familiar word order |
| "The aforementioned documentation" | "Those documents" | Concrete reference |
| "Pursuant to the requirements" | "You must" | Active instruction |

---

## Pillar 3: Representative Testing (The Real Deal)

### Why This Matters

Automated tools + manual testing still miss 30-40% of real-world exclusion. Why?

- Assistive tech users develop *workarounds* that code misses. They struggle but get through.
- Disabled people develop coping strategies for bad design. You think your system works because they've adapted, not because it's good.
- You and your team aren't representative. Your blindspots compound.

**Solution:** Test with people who depend on these tools daily.

### Setup: Recruiting Representative Testers

#### Where to recruit:

- **Accessibility orgs:** RNIB (blind/low vision), British Deaf Association, Neurodiversity organizations
- **User research agencies:** UserVoice, Userlytics, Loop11 (some specialize in a11y)
- **Community partners:** Charities serving populations you're building for (social care, benefits orgs, etc)
- **LinkedIn/Twitter:** Post a call; offer compensation (£50-100 per session is standard)
- **Your own organization:** Do you have disabled staff? Ask them (respectfully, with compensation)

#### What you're testing:

- **Essential only:** Can they complete core tasks? (not "nice to have" features)
- **With their own tools:** Don't hand them NVDA/VoiceOver. Let them use what they use.
- **In their own environment:** Remote testing. Don't force them to a lab or office.

#### Participants to recruit:

For a citizen-facing public service, recruit at least:
- 1-2 blind/low vision users (screen reader + magnification)
- 1-2 Deaf/hard of hearing users (captions + visual alerts)
- 1-2 neurodivergent users (ADHD, autism—test for overwhelm, clarity)
- 1-2 older adults or low digital literacy users
- 1-2 users with motor disabilities (limited dexterity, speech control)
- 1-2 users with cognitive disabilities (learning disabilities, brain injury)

**Budget:** ~7-10 sessions × £75 = £525-750 upfront cost. Compare that to shipping broken software and having to retrofit.

### Test Script Example

```markdown
# Representative Test Script (30 min session)

## Intro (2 min)
"Thanks for your time. We're testing whether our [system] works for people using assistive tech. 
There's no wrong way to do this—use your normal setup and tools. We're testing the system, not you.
You can stop any time. Questions?"

## Task 1: Can you navigate to [task]? (5 min)
"I'd like you to [specific task: 'check your benefits status', 'book a GP appointment']"
- Don't help unless they ask
- Observe: Where do they struggle? What's unclear?
- Note: Time taken, number of corrections, emotional tone

## Task 2: [Repeat with different task] (5 min)

## Interview (5 min)
- What worked well?
- What was confusing?
- What would help?
- Would you recommend this to a friend?

## Exit (1 min)
"Thanks. You'll receive £[amount] in your account within 2 days."
```

### What You're Looking For

| Red Flag | Indicates | Fix Priority |
|----------|-----------|--------------|
| User keeps saying "sorry" or "I'm doing this wrong" | System blames user for being unclear; confusing UX | P1 (design issue, not user issue) |
| User takes 3x longer to complete task | Cognitive overload or accessibility barrier | P1 if essential task; P2 if nice-to-have |
| User abandons part of task (e.g., skips form validation) | Barrier too high; user giving up | P1 |
| User uses unexpected workaround to complete task | System works by accident; fragile | P2 (make it intentional) |
| User couldn't complete task at all | Exclusion | P0 (block shipping) |
| Assistive tech acts unexpectedly (misreads, misannounces) | Semantic markup issue | P1 |
| User says "I'd never use this, I'd phone instead" | System less accessible than fallback; user voting with feet | P1 |

### Documentation Template

```markdown
# Representative Testing Report: [System Name]

## Participants
- 3 blind/low vision users (1 NVDA, 1 JAWS, 1 iPhone VoiceOver)
- 2 Deaf users (1 relies on captions, 1 uses sign language interpreter)
- 2 ADHD users
- 2 users age 70+ with limited digital experience

## Key Findings

### Task 1: [E.g., "Submit benefits application"]
- ✅ All sighted users completed in 3-4 min
- ⚠️ Screen reader users: struggled with form structure (no fieldset), took 8-10 min
- ❌ 1 Deaf user couldn't understand video walkthrough (no captions)
- **Fix:** Restructure form with fieldsets; add captions

### Task 2: [E.g., "Check application status"]
- ✅ All participants completed
- ⚠️ Cognitive load for ADHD users (too many status options); needed to re-read
- **Fix:** Progressive disclosure; show only current status by default

## Recommendations by Priority

### P0 (Fix before shipping)
- [ ] Add form labels (currently invisible to screen readers)
- [ ] Add captions to video

### P1 (Fix before or immediately after launch)
- [ ] Simplify form structure (too many nested fields)
- [ ] Add a "what does this mean?" for each status type

### P2 (Fix in next sprint)
- [ ] Add a "need help?" sidebar with phone number
- [ ] Test with speech-control users (not yet done)

## Quotes
"I gave up after 5 min. The form doesn't tell me what's wrong when I fill it in."
"I'd just phone instead. It's faster."
```

---

## Integration Into Your Dev Workflow

### Pre-Merge Checklist

```markdown
## Accessibility
- [ ] `npm run test:a11y` passes (automated)
- [ ] Manual keyboard nav tested (Tab works end-to-end)
- [ ] Screen reader tested (NVDA or VO; headings/forms/labels semantic)
- [ ] Readability: Flesch-Kincaid Grade ≤[target] (automated check)
- [ ] [If citizen-facing] representative testing scheduled or completed
```

### Sprint Planning

- **Story template:** Add acceptance criteria
  ```
  - User can complete task via keyboard alone
  - WCAG 2.2 AA passes automated scan
  - Screen reader announces content semantically
  ```

- **Velocity:** +10-15% for accessibility work (it's real work)

- **Representative testing:** Allocate 1 sprint per major feature release (3-5 external testers, £400-600/sprint)

### Definition of Done

**Add to your team's DoD:**

```
- [ ] Accessibility: automated scan passes
- [ ] Accessibility: keyboard nav tested
- [ ] Accessibility: reviewed by accessibility lead
- [ ] [For public-sector] citizen-facing systems: representative testing scheduled
```

---

## Neurodiversity-Specific Testing

Because you're neurodiverse yourself, don't assume your neurotypeical team sees what you see.

### ADHD Testing Focus

| Barrier | Test | Fix |
|---------|------|-----|
| Form overwhelm | 5+ form fields visible at once; user loses track | Progressive disclosure; step-by-step |
| Decision paralysis | 10+ option buttons; unclear which to pick | Reduce choices; highlight recommended; explain each |
| Interruption recovery | Task takes 10+ min; user gets distracted; loses context on return | Autosave; "you were here"; no hard time limits |
| Time blindness | "Form closes in 5 min"; no countdown visible | Countdown timer visible + warning at 1 min before close |
| Working memory | "Remember step 1 while you do step 3" | Show all steps simultaneously + progress indicator |

### Autism/Sensory Processing Testing Focus

| Barrier | Test | Fix |
|---------|------|-----|
| Sensory overload | Animations, parallax, auto-playing video | Animations off by default; prefers-reduced-motion respected |
| Consistency | Button styles inconsistent; navigation unpredictable | Predictable patterns; consistent interaction model |
| Overwhelm from ambiguity | Instructions unclear; multiple interpretations | Explicit, literal instructions; no implied context |
| Social communication | Error messages blame user ("You did this wrong") | Error messages are factual, helpful ("The zipcode must be 5 digits") |

### Test Script (Neurodiversity Focus)

```markdown
## ADHD/Neurodivergence Testing

1. "Please complete [task] while I narrate what I see."
   - Observe: Where do they lose focus?
   - Do they skip steps to move faster?
   - Do they restart halfway through?

2. "If you got distracted halfway, could you find your place?"
   - Test autosave, progress persistence

3. "How many times did you need to re-read instructions?"
   - Count; if >2, instructions are unclear

4. "Did any animations or sounds distract you?"
   - Note which ones; they should respect prefers-reduced-motion

5. "Would you recommend this to someone like you (ADHD/autistic)?"
   - Final verdict
```

---

## Reporting & Follow-Up

### Accessibility Defect Template

```
Title: [User Impact] - [Component] - [WCAG criterion]
Example: "Can't navigate form via keyboard - payment form - WCAG 2.1.1"

Body:
- **Severity:** P0 (blocks all users) / P1 (major workaround) / P2 (minor friction) / P3 (nice-to-have)
- **Affected users:** Screen reader users, keyboard-only users, etc.
- **Steps to reproduce:** [Specific steps]
- **Current behavior:** [What happens now]
- **Expected behavior:** [What should happen]
- **Evidence:** Screenshot, video, WCAG ref link

Example:
P0 - Form cannot be submitted via keyboard
Screen reader users, keyboard-only users
- Go to /checkout
- Tab to all form fields (works)
- Tab to "Pay" button
- Press Enter
- Nothing happens (button doesn't submit)
Current: Button is `<div onclick=...>`, not `<button>`
Expected: Button is semantic `<button>` or has proper ARIA role
WCAG: https://www.w3.org/WAI/WCAG22/Understanding/keyboard
```

### Sample Metrics Dashboard

```
Accessibility Metrics (Live)

| Metric | Target | Current | Trend |
|--------|--------|---------|-------|
| WCAG AA automated scan pass rate | 100% | 98% | ↑ |
| Keyboard navigation: critical paths | 100% | 95% | ↓ (new feature) |
| Screen reader: heading hierarchy | 100% | 92% | → |
| Flesch-Kincaid Grade (citizen-facing) | ≤6 | 6.2 | ↓ (improving) |
| A11y defects P0 (blocking) | 0 | 1 | ⚠️ |
| A11y defects P1 (major) | 0 | 4 | ↑ (new features) |
| Representative testing: % users able to complete core task | ≥95% | 87% | ↓ (needs work) |
| Time to fix P0 a11y bug | <24h | 12h avg | ✅ |
```

---

## Tools Quick Reference

| Task | Tool | Link |
|------|------|------|
| Automated scan (CI) | axe-core + Playwright | https://github.com/dequelabs/axe-core |
| Screen reader (Windows) | NVDA | https://www.nvaccess.org/ |
| Screen reader (Mac) | VoiceOver | Built-in |
| Readability (CLI) | flesch-kincaid | https://npmjs.org/flesch-kincaid |
| Contrast check | WebAIM | https://webaim.org/resources/contrastchecker/ |
| Color blindness simulator | Sim Daltonism | https://michelf.ca/projects/sim-daltonism/ |
| Keyboard nav test | Your keyboard | Disable mouse: Settings → Ease of Access |
| Mobile screen reader | TalkBack (Android) | Built-in |
| Mobile screen reader | VoiceOver (iOS) | Built-in |

---

## Composition

**Referenced by:**
- `frontend-ui-engineering` skill
- `test-driven-development` skill
- Code review pre-merge checklists
- CI/CD pipelines

**Used in:**
- Sprint planning (capacity estimation)
- Accessibility audits
- Representative testing projects
