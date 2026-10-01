# Brainstorm Intent: Runner App (2026-09-30)

## Project Purpose
- A simple, public app that shows off the BMAD method and agentic AI. The **agentic showcase is the BMAD build process** (an agent team builds the app). The app itself does not need to contain AI agents (in-app agents: WON'T).
- User role: **Vibe CEO** (human-in-the-loop). BMAD agents do the work wherever possible. The user supplies judgement, decisions and validation of the final product, and steps in and takes charge when needed.
- Full transparency across the lifecycle: every project decision is documented in deliverables.
- The codebase must be readable and easy for the user to understand, with plain comments, not cryptic code.
- Message: agentic AI can be trusted. Used properly, it delivers quality work with fewer resources, from ideation through delivery, and humans can take 100% control at any time.
- Paired with weekly LinkedIn "office story" posts (Vibe CEO + Mary the BA) with original visuals. These are planned in a separate session.

## Hard Constraints
- **$0** to build and host: free platforms only, no fees.
- Must be **demoable online**.
- No rush. The user's real race on **Nov 1, 2026** is not a deadline, and the app will not be ready for it.
- Agile: core features first for a working demo, then more features in later sprints.

## Core Intent (user's words)
> Help the runner train properly to the race plan and adapt it based on (1) how the runner feels and (2) race destination/route conditions.

## Root Need (Five Whys)
Runners lack a personal, honest early-warning voice calibrated to their own body and thresholds. Apps cheer, nobody warns. Only the runner can feel their pain, so they are the only sensor. The app must learn each runner's personal baseline and do both jobs: reassure novices who raise false alarms, and warn veterans whose grit blinds them to the injury line. Feasibility should be known early, because fitness takes time to build. Roughly 3 weeks before a marathon "the hay is in the barn".

Origin story: the user woke at night with glute pain in the middle of an intense training cycle, caused by ramping up too fast and overtraining.

## Positioning / Differentiation
- Strava/RunKeeper (the user uses both) record data and cheer. This app adds a **sense-making layer**: is this normal, what is wrong, am I on track.
- The app finds and closes **the gap between your training and your race**: terrain, climate, body, fuel. It turns a generic plan into a personal bridge.
- Coach line: "Most running apps cheer you on. This one gets you to the finish line, injury-free." (do not name competitors in public positioning)

## Target Users / Personas
- **Over-reaching qualifier chaser** (e.g. chasing a Boston Qualifier): at risk of injury and of missing the dream race.
- **Under-prepared novice**: undertrains for a marathon, risking stress fractures or a DNF (did not finish), and may mistake soreness for injury.
- **No-race runner**: wants to keep fit, stay accountable and stay motivated ("running longevity").
- Later: Couch-to-5K beginners, post-race returners.

## Known Facts & Data Sources
- The user's Apple Watch (Fitness app) records elevation gain, HR, pace, power, cadence, ground contact time, vertical oscillation, stride length and time in HR zones.
- The user uploads runs from Apple Fitness to Strava/RunKeeper.
- The user does not know which free tools exist. This needs technical research.
- *To verify (coach):* web apps cannot read Apple Health directly, and native iOS needs the paid Apple developer program (~US$99/yr), which conflicts with $0.
- *To verify (coach):* free paths exist: GPX/FIT/TCX export (Strava original-file download), Apple Health export, the Strava free API (with limits), and free hosting tiers (GitHub Pages, Netlify, Vercel, Cloudflare Pages).
- Direction: a web app with run-file upload and data kept in the browser.

## MVP Scope (MoSCoW, final)
**MUST (sprint 1)**
1. Run upload + run log
2. Training plan entry/upload + plan vs actual
3. Race-goal feasibility / projected finish
4. No-race fitness view (HR zone 80/20 + fitness trend)
5. Body map + mileage-ramp warning + see-a-professional escalation
6. Trust basics: "Why?" on recommendations, local-first, no sign-up, $0 hosting

**SHOULD (sprints 2-3)**
- Basic calorie bank (*moved from MUST to sprint 2*)
- Lightweight race profile: manual total elevation gain + expected race-day temperature (*promoted from COULD to sprint 2*, after the coach noted that route conditions are part of the core intent)
- A/B/C goals
- Phase-aware fatigue context
- Race week / Taper Jail
- Carb-loading alerts for upcoming withdrawals (predicted calorie burn)
- Good/bad pain translator
- Recovery Lap / reverse taper
- Distance-specific build/taper logic (5K, 10K, half, marathon)

**COULD (later)**: Course Twin, climate gap, ingredient substitutions, Fuel Fingerprint, Running Age, game layer + Smart Skip, Test Kitchen / Race-Day Recipe Card, "Should I Sign Up?", doctor-ready report, C25K mode, backtest against the Nov 1 race.

**WON'T (this time)**: native iOS / Apple Health sync, accounts / cloud sync, payments, in-app AI agents, shoe mileage, social features.

## Design Principles
- **Recommendations, not orders.** The runner makes the final call. The app is an aid, not an authority, because humans, including medical professionals, make mistakes too.
- **"Why?" explanations** on every recommendation (data trail), as rule-based features. No in-app AI needed.
- **See-a-professional escalation** when something seems off with the runner's health.
- **Personal baselines over population averages** for pain, fatigue, plans and fuel. *Coach:* design for calibration from day one.
- **Body map as the data hub.** *Coach:* one input powers injury warning, Smart Skip, good/bad pain, Recovery Lap and the doctor report, so its data model must be built well in sprint 1.
- **Local-first privacy.** Share the app, not the data: no sign-up, near-zero compute, no stored data to leak. Cloud sync only as a deliberate future decision.
- The same trust principle applies at both levels: the runner decides in the app, and the Vibe CEO decides in the build.

## Open Questions & Risks
- **Sharing/scaling (user):** How would other runners sign up and upload their data? How much compute would multiple users need? Should runners pay, or should components move to paid platforms? What are the privacy concerns with other runners' data? What if companies steal the idea? *Coach:* the local-first approach answers most of these, and the public, dated build record gives partial protection against idea theft.
- **$0 risk:** the final product may not be deliverable at zero cost.
- **Agent drift:** agents may produce something very different from the requirements.
- **Explainability:** there may be no accountable human able to explain the chosen processes and workflows.
- **LinkedIn visibility (biggest fear):** the series may go unnoticed, so it must be captivating.
