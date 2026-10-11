---
title: 'Product Brief: runner-app'
status: final
created: '2026-10-08'
updated: '2026-10-10'
inputs:
  - _bmad-output/brainstorming/brainstorm-runner-app-2026-09-30/brainstorm-intent.md
  - _bmad-output/planning-artifacts/research/technical-free-data-import-and-hosting-for-runner-2026-10-08/research.md
  - _bmad-output/planning-artifacts/research/domain-sports-science-of-cross-training-and-loa-2026-10-08/research.md
---

# Product Brief: runner-app

## Executive summary

**Most running apps cheer you on. This one gets you to the finish line, injury-free.**

Today's apps record your runs and celebrate them. runner-app (a working name, to be renamed) turns your own training files into three plain answers: **a verdict** (are you on track for your goal?), **a warning** (is something about to break, and why?), and **permission** (you missed a run or swam instead, so here's what that really costs). When life gets in the way, a **Reality Check** shows how far you've drifted from your goal and lays out your options. You make the call.

It's a free web app with no sign-up. Your data never leaves your browser. Every recommendation comes with a "Why?" that cites the evidence and says how strong it is.

runner-app is also a public experiment. An agent team will build it with the BMAD method. Anita, as "Vibe CEO," will judge, decide, and step in when needed, and every decision will be documented. The goal is to show that agentic AI can deliver quality work with fewer resources, transparently, with a human able to take control at any time.

Technical constraints, the rule-to-scope mapping, and design notes are in the [addendum](addendum.md).

## The problem

Runners are the only sensor for their own bodies, and the tools they use don't help them read the signals. A runner with a goal (sub-4, meaning a marathon under four hours, or a personal best) knows what a hard training cycle feels like. That experience is exactly what makes them push through pain and illness that they should stop for, because it feels like "part of the process."

Anita's Apple Watch Ultra 2, Strava, and Runkeeper all logged her training, but none of them flagged that she'd increased her distance too fast. Then, in the middle of an intense cycle, she woke at night with glute pain. Strava predicts race times, but she finds them inaccurate, and it doesn't explain how it calculates them.

Today's apps record data and celebrate it. They don't answer the questions runners actually lie awake with:
- Am I still on track?
- Is this normal or an injury?
- I missed three key runs, so what now?
- Does my swim count?

The cost is injury, a missed goal race, or a DNF (did not finish) that honest feedback could have prevented. The cost is highest in the final weeks. Most modern plans taper 2–3 weeks before a marathon, and some runners don't taper at all, so there's little time left to close a fitness gap or recover from overreaching.

## The solution

runner-app is a sense-making layer on top of the files runners already have. You export activities from Strava or Runkeeper (FIT, GPX, or TCX), drop them into the app, and, optionally, add a training plan and a race goal. Files alone are enough for warnings, the intensity view, the fitness trend, and cross-training load. Adding a plan and goal unlocks the verdict and the Reality Check. The app gives three answers, plus the Reality Check:
- **Verdict:** projected finish against the goal, and whether training supports it.
- **Warnings:** a run more than 10% longer than your longest in the past 30 days, a weekly jump of more than 30%, easy runs drifting too hard, and bone-stress red flags that trigger "see a professional." Each warning has a "Why?" and an evidence label.
- **Permission:** a skipped run, or cross-training of any type, is logged honestly. "One swap costs nothing measurable. Here's what to protect this week."
- **Reality Check:** after missed training, a revised goal estimate based on where you are in your plan, plus options with their trade-offs: keep the goal, slow down, choose a shorter race, or postpone to a later one. **The runner decides.** The app never tells a runner to drop a race. Only health red flags get a plain "see a professional before racing."

All coaching logic is plain rules with a visible data trail. AI may help with data entry, such as reading a PDF plan into a table the runner checks, but it never gives advice, and it must be free.

## What makes this different

- **It catches the ramp.** It flags the training patterns linked to injury, such as a sudden long run or a jump in weekly distance, using the runner's own recent history rather than population averages.
- **It says how sure it is.** Every warning and prediction comes with a "Why?" and an evidence label ("early evidence," "one small trial"). It flags risk honestly but never claims to predict injury.
- **Its prediction is tested in public.** The race prediction is checked against Anita's real November 1 result, with a ±5% bar set in advance and the result published whether it passes or fails.
- **It counts the swim.** Cross-training of any type is logged honestly, not ignored and not turned into fake running miles.
- **It recommends; it doesn't order.** The runner decides. The same principle runs the build: the agents propose, and the Vibe CEO decides.
- **Honest moat:** there's no technical moat. The advantage is the approach plus a dated, public build record.

## Who this serves

**Primary: the goal-driven runner with racing experience.** They have a target (sub-4, a personal best or both) and know how training feels, so they're at risk of overreaching and missing the race that matters. Success for them: they trust the verdict, act on a warning before it's an injury, and adjust their goal without guilt when life intervenes. Anita is this runner, and the first real user.

**Secondary, served by the same minimum viable product (MVP):** the under-prepared novice, who needs reassurance or a warning about soreness and pain, and the no-race runner, who wants a fitness trend and accountability. Because the plan and goal are optional, these runners get value from their files alone. Later: Couch-to-5K beginners and runners returning after a race.

The MVP must work for **any stranger** who uploads their own files. Nothing may be built around Anita's data.

## Success criteria

**The app works:**
1. **Prediction accuracy:** the projected marathon finish is within **±5%** of Anita's actual November 1, 2026 result (about ±12 minutes on a 4-hour finish). The method is frozen and date-stamped before the race and benchmarked against Strava's prediction (protocol in the addendum).
2. **Warning backtest:** importing Anita's past cycle shows whether the spike or ramp warnings fired before her known glute-pain episode. This is honest evidence from one runner, not proof.
3. **Stranger-ready:** a first-time visitor can import their files and see their first useful insight (a warning, intensity view or fitness trend) without help, with no plan or goal required. Tested by 2–3 of Anita's running friends who haven't seen the app, given only the link. `[ASSUMPTION: target is about 5 minutes]`

**The showcase works:**
4. **Live:** a public URL anyone can visit, costing **$0** (with billing receipts as proof).
5. **Transparent:** the LinkedIn article publishes the November 1 result **whether it passes or fails**, with the reasons and the fix.
6. **Traceable quality:**
   - every decision is logged
   - the Vibe CEO's interventions and overrides of agents are counted and reported
   - the code is readable by a non-developer, with plain-English comments

**Longer term:**
- **Real-time trial:** Anita uses the app through her next full training cycle to race day (likely 2027), so the warnings and Reality Check are tested as they happen, not just in hindsight.
- **Method interest:** inbound inquiries from external parties (companies, friends, or anyone interested in AI-assisted workflows) about how the app was built, with the count reported in the LinkedIn series.

## Scope

**MVP (sprint 1, MUST):**
1. Import runs from FIT, GPX, and TCX files, with a run log.
2. Optional training plan and race goal, with plan vs. actual matched by week and session type.
3. Race-goal verdict and projected finish, plus the **Reality Check** with four options (keep goal, slower goal, shorter race, postpone).
4. Fitness view for runners without a race: a "mostly easy" intensity view plus a fitness trend.
5. Single-run spike and weekly ramp warnings, plus see-a-professional red flags as a short "Any of these?" checklist shown with a warning.
6. **Cross-training logging of any type**, with each sport kept separate.
7. Trust basics: a "Why?" on every recommendation, local-first storage with a backup file, no sign-up, $0 hosting.
8. **"Try with a sample runner"** (made-up data, never Anita's).

**Next (sprints 2–3, SHOULD):** 12 features, led by a body map check-in, PDF plan reading with free AI, and swim cross-training advice. Full list in the addendum.

**Needs research first:** plan rewriting after missed training, running while sick, and advice for other cross-training sports (see the addendum's research queue).

**Out (WON'T, for now):**
- native iOS and Apple Health sync
- the Strava API (it now needs a paid subscription)
- accounts and cloud sync
- payments
- paid AI services (any in-app AI must be free)
- shoe mileage
- social features

## Vision

**First, the method.** A year from now, success is external parties, from companies to friends to anyone interested in AI-assisted workflows, asking Anita how this app was built. The build record becomes a reusable template: proof that a small team of agents, guided by one accountable human, can take an idea from brainstorm to a trustworthy public product at almost no cost, with every step on the record.

**Then, the app.** runner-app stays live and free, in the hope that runners anywhere find it useful: an honest coach in their pocket that knows their baselines, learns to read their race course and climate, and grows its advice one evidence-backed rule at a time, never faster than the science allows.

## Open questions

- **Projection method:** choose, source, and freeze before November 1, 2026 (success criterion #1; addendum research queue item 3). `[ASSUMPTION: rule-based, using race-equivalence and training-volume factors]`
- **Free AI for PDF plan reading (sprint 2):** in the browser or a free online service (addendum research queue item 5). `[ASSUMPTION: in-browser AI preferred; needs a technical check]`
- **App name:** "runner-app" is a working name. A catchier name will be chosen later.
