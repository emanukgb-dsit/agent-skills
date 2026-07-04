---
name: equity-and-inclusion-first
description: |
  Guides agents through structured equity and inclusion analysis before, during, and after development. 
  Use when building systems that serve public sectors, vulnerable populations, underrepresented communities, or any user-facing product.
  Prevents digital and physical exclusion by design.
lifecyclePhase: define,plan,build,review
category: Impact & Ethics
---

# Equity and Inclusion First

**Principle:** Exclusion is a bug. Design systems assume a normative user—able-bodied, digital-native, native English speaker, no assistive tech need, standard banking access, no cognitive disabilities, no neurodivergence. This skill embeds exclusion audits into every phase of development.

---

## When to Use

- **Public sector systems** — government platforms, benefits calculators, health guidance, education tools
- **Citizen-facing services** — especially those targeting vulnerable households, disenfranchised groups, underrepresented communities
- **Health/social care tech** — mental health, disability services, elderly care, child protection
- **Financial inclusion tools** — benefits advisors, debt guidance, financial planning
- **Education & early years** — learning platforms, accessibility for neurodivergent learners
- **Climate/environment tools** — serving communities most impacted by climate risk
- **Any system claiming to serve "everyone"** — if you can't articulate who's excluded, you haven't thought about it

---

## Process

### Phase 1: Discovery & Mapping (DEFINE)

**Step 1: Name the served population explicitly**

Don't say "users." Say:
- "People with long-term health conditions, including neurodivergence, seeking benefits advice"
- "Non-English speakers accessing urgent health information"
- "Single parents managing childcare and work logistics"
- "People with visual impairments using assistive tech"
- "Elderly households with limited digital access"

**Rationale:** Abstractions hide exclusion. Naming populations forces specificity.

**Evidence requirement:**
- User personas named with specific needs (not generic "user")
- Stakeholder list including disabled people, community reps from served populations (co-design rule: "Nothing About Us Without Us")
- Accessibility/inclusion audit question: "Who is this system built for?" and "Who is it accidentally built against?"

---

**Step 2: Conduct a blindspot analysis for each served population**

For each population group, enumerate:

| Population | Blindspot Example | Exclusion Barrier | Design Mitigation |
|------------|------------------|------------------|------------------|
| Blind/low vision | "Our PDF is gorgeous" | PDFs are image-first; screen readers can't read them | Structured HTML + ARIA; plain text transcript |
| Deaf/HoH | "We have a video walkthrough" | No captions; can't hear audio narration | Captions + transcript; visual equivalents for sound cues |
| Neurodivergent (ADHD, autism) | "We have lots of options" | Cognitive overload; decision paralysis; unclear navigation rules | Progressive disclosure; explicit rules ("must fill all fields"); defaults that are safe |
| Non-English speaker | "English is international" | Documentation + UX all in English; no support for home language | Multilingual support (not machine-translated); community-validated terminology |
| No smartphone/data-poor | "Download our app" | Requires smartphone, data plan, storage; locks out 3G/older devices | Web-based, lightweight; works on older browsers; low-bandwidth mode |
| No standard ID | "Verify with email + SMS" | Doesn't have smartphone; email account uses shared device; can't receive SMS | Fallback verification (security Q, postal mail, in-person); no SMS-only auth |
| Elderly/low digital literacy | "It's intuitive" | Assumption of familiarity with conventions (icons, gestures); no tutorial | Plain language; step-by-step; video walkthrough + transcript; phone support |
| Low income | "It's free" | Hidden costs (printing, travel to office, phone calls, data overage); paywall for accessibility features | Free at point of use; no hidden costs; accessibility built-in, never paywalled |
| Neurodivergent (PTSD/anxiety) | "We collect full details upfront" | Scary form requesting sensitive info (medical, financial) upfront causes abandonment | Progressive disclosure; explain why info needed; offer phone/human alternative |
| Care leavers/unstable housing | "Create an account" | No stable email, no address, frequently changing phone numbers | Offer immediate access without account; fallback to phone-verified one-time use |

**Rationale:** Blindspots are the gap between "we think we're accessible" and "we actually are." They're discovered by talking to people with lived experience, not by assumption.

**Evidence requirement:**
- Blindspot table completed for at least 3 served population groups
- At minimum one blindspot per group is addressed in design
- Signed-off by someone from that community (co-design verification)

---

### Phase 2: Build with Exclusion Audits (PLAN & BUILD)

**Step 3: Add accessibility/inclusion as a Definition of Done**

Don't treat it as "nice to have." Add to acceptance criteria:

- [ ] **Readability:** Plain language checked (Flesch-Kincaid Grade ≤6 for citizen-facing tools)
- [ ] **Contrast:** WCAG 2.2 AA color contrast verified (4.5:1 normal, 3:1 large text)
- [ ] **Keyboard nav:** Every function accessible via keyboard; Tab order logical; focus visible
- [ ] **Screen reader:** Content semantic; heading hierarchy; alt text complete; ARIA labels where needed
- [ ] **Neurodivergence:** Flashy animations controllable; form errors specific; no time-dependent completion
- [ ] **Multilingual:** Key terms defined; UI translatable (no hardcoded strings); RTL-safe if needed
- [ ] **Fallback:** Always a human route; no tech-only flows
- [ ] **Tested with real assistive tech** — not just automated checks
- [ ] **Representative testing** — at least one person from each served population group has tested

**Rationale:** If it's not in Definition of Done, it ships broken.

**Evidence requirement:**
- Automated accessibility scan passing (axe, WAVE, or similar)
- Manual testing with screen reader (NVDA/JAWS on Windows, VoiceOver on Mac)
- Manual keyboard-only navigation test
- Readability report generated (Hemingway Editor, Grammarly, or automated check)
- Testing sign-off from at least one person with lived experience in each served population

---

**Step 4: Data minimization & privacy-first design**

Exclusion happens when systems demand data people can't or won't provide.

- **Medical:** Don't ask for diagnosis; ask for barriers ("Can you lift 20kg?")
- **Financial:** Don't demand proof of income; ask about benefits status
- **ID:** Don't demand government photo ID; ask "Can you verify via email/phone/postal mail?"
- **Family structure:** Don't assume "spouse/children"; ask for "dependents"
- **Name:** Allow non-Western name order; support initials/mononyms
- **Gender:** Always include "Prefer to self-describe"; never required
- **Work history:** Don't demand continuous employment; ask about interruptions (caring, health)

**Rationale:** Data collection is a barrier. Minimize it, especially for vulnerable populations.

**Evidence requirement:**
- Data schema reviewed against blindspot analysis
- Every required field justified (delete if "nice to have")
- Alt collection methods documented (phone, postal, in-person fallback)

---

### Phase 3: Review & Audit (REVIEW)

**Step 5: Conduct pre-launch equity audit**

Before shipping to production, run through:

| Audit Question | Pass/Fail | Evidence | Owner |
|---|---|---|---|
| Is this system usable for someone without a smartphone? | | | |
| Is this system usable for someone who doesn't read English fluently? | | | |
| Is this system usable by someone with no prior digital experience? | | | |
| Is this system usable by someone who's blind? | | | |
| Is this system usable by someone who's Deaf? | | | |
| Is this system usable by someone with ADHD/autism? | | | |
| Is this system usable by someone with severe anxiety/PTSD? | | | |
| Is this system usable by someone who has unstable housing/no fixed email? | | | |
| Is this system usable by someone with limited income (no printer, no car)? | | | |
| Is this system usable by someone who doesn't have a bank account? | | | |
| Is this system usable by someone with caring responsibilities? | | | |
| Is this system usable by someone with no legal ID? | | | |
| Can a human always access support? | | | |
| Have we talked to at least 3 people from each served population? | | | |

**Rationale:** You can't see your own blindspots. External audit forces specificity.

**Evidence requirement:**
- Audit table completed with Pass/Fail + evidence
- At minimum: 2 failed questions remediated before launch, or documented deferral with timeline
- Independent equity review (internal DEIA lead, community partner, or external auditor)

---

**Step 6: Monitoring & continuous feedback**

Launch is not the end. Exclusion often emerges in production.

- **Accessibility defect SLA:** P1 (can't access service at all), P2 (major workaround needed), P3 (minor friction)
- **Feedback route:** Always include "This is hard to use because..." in support; escalate patterns to product
- **Regular community review:** At least quarterly, demo with served population groups; capture feedback loop
- **Anti-pattern catch:** If support logs show one population group using the human fallback 10× more than others, that's exclusion in the system; fix it

**Evidence requirement:**
- Monitoring dashboard tracking accessibility defects by population (anonymized)
- Regular review cadence set in calendar
- Feedback loop documented (how community input changes the system)

---

## Anti-Rationalizations (Common Excuses & Rebuttals)

| Rationalization | Why This Fails | Rebuttal |
|---|---|---|
| "We'll add accessibility later" | Accessibility bolted on is half-baked. Retrofitting costs 2-3x more. Worse: you'll miss structural issues that need redesign. | Build it from day 1. Cost is the same; quality is better. |
| "We don't have blind users" | You don't know. Blind people don't use broken systems; they just don't show up in your analytics. This is selection bias, not absence. | Assume you have blind users. If you ship broken, they'll avoid you. |
| "Our users don't speak multiple languages" | Your users do. Immigrants, refugees, international students, elderly people whose home language isn't English. They just don't tell you because they've learned to stay silent. | Support at least one additional language if your system is public-sector. Validate with speakers, not machine translation. |
| "Accessibility is expensive" | Not if it's designed in. Retrofitting is expensive. | Cost of building it right: 10-15% extra upfront. Cost of fixing it in production: 200%+. |
| "We tested with a screen reader" | Did you test with a *real person using a screen reader* who uses it daily? Or did a sighted person turn on VoiceOver for 10 minutes? | Test with people who depend on assistive tech. They'll find things you miss. |
| "We have a11y checklist" | Checklists are minimums. They catch 30% of exclusion. Lived experience catches 70%. | Use checklists as gates. Use community testing as your north star. |
| "Our population is mostly young/tech-savvy" | Assume 15% cognitive disability, 8% visual/hearing impairment, 20% neurodivergence in any population. Age doesn't change this. | Design for the full spectrum. Fast users are fast; slow users get there. |
| "Plain language is condescending" | Plain language isn't about intelligence. It's about cognitive load. Even PhDs prefer clarity. | Offer both: default to plain; link to full jargon. Everyone's happy. |

---

## Red Flags (Early Signs of Exclusion)

- ✋ "We'll target mainstream users first, then add accessibility"
- ✋ "Our user research was internal interviews only" (no external community)
- ✋ "Accessibility person is external consultant, not core team"
- ✋ "WCAG AA is the goal" (should be baseline, not goal—aim for AAA where possible)
- ✋ "We don't have neurodivergent people on our team" (hire them, or recruit community partners)
- ✋ "We tested with [assistive tech], but we didn't test with [people]"
- ✋ "The feature is too complex to make accessible" (then it's too complex for anyone)
- ✋ "Our data shows no [population] using the system" (absence = exclusion, not absence of need)
- ✋ "Fallback to phone is available" (but only if you make it obvious and free)

---

## Verification & Evidence

**Before code review:**
- [ ] Blindspot analysis completed for 3+ population groups; co-design sign-off received
- [ ] Definition of Done includes accessibility/inclusion checklist

**Before merge:**
- [ ] Accessibility audit passed (automated + manual)
- [ ] Representative testing completed (at least 1 person per served population)
- [ ] Pre-launch equity audit completed; all P1 gaps remediated or deferred with timeline
- [ ] Community feedback loop documented and set in calendar

**Before production:**
- [ ] Monitoring dashboard live; team knows how to interpret exclusion signals
- [ ] Support routing for accessibility defects established
- [ ] Quarterly review meeting scheduled with community partners

---

## Why This Matters

**The stakes are real:**

- A broken auth system locks out people without phones. Single parents. Elderly people. Refugees.
- A time-limited form excludes people with ADHD (executive dysfunction), anxiety (paralysis), or caring responsibilities (interrupted focus).
- A screen-reader-hostile PDF excludes blind people from accessing benefits or legal guidance.
- No multilingual support excludes 1 in 6 UK residents who speak another language at home.
- SMS-only 2FA excludes people in low-signal areas, pay-as-you-go users, people with hearing disabilities.

**Public sector systems are not optional.** If your system is the only way to access a benefit, book a school place, or report a safety issue, and you exclude someone, you've caused material harm. That's not a bug. That's a failure of duty.

This skill makes exclusion visible. It won't make it zero—we all have blindspots. But it makes it deliberate, measured, and remediated.

---

## References & Links

- **GDS Service Standard, Point 5:** [Make sure your service is accessible](https://www.gov.uk/service-manual/service-standard/point-5-make-sure-your-service-is-accessible)
- **WCAG 2.2 AA:** https://www.w3.org/WAI/WCAG22/quickref/
- **WebAIM Screen Reader Testing:** https://webaim.org/articles/screenreader_testing/
- **UK Parliament DEIA Framework:** Equality Act 2010 + Public Sector Equality Duty
- **Co-Design Principle (Disability Rights UK):** "Nothing About Us Without Us"
- **Readability Tools:** Flesch-Kincaid, Hemingway Editor, Grammarly, Microsoft Accessibility Checker
- **Assistive Tech:** NVDA (free, Windows), JAWS (commercial), VoiceOver (Mac), Narrator (Windows)

---

## Composition

**Invoke directly when:**
- Starting a new public-sector or citizen-facing project
- Conducting a pre-launch review for a system serving vulnerable populations
- Responding to an accessibility defect in production

**Invoke via `/spec` + `/review`:**
- `/spec` pulls this to frame discovery questions
- `/review` pulls this to audit before merge

**Invoke via agent personas:**
- The `security-auditor` persona calls this to check for exclusion + security overlaps
