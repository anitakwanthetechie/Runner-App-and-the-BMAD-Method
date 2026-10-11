---
title: 'Addendum: runner-app product brief'
created: '2026-10-10'
---

# Addendum: runner-app product brief

This addendum holds detail for the PRD and architecture that doesn't belong in the brief itself. The personas and the two research sections give context, the design-note sections record decisions (including the prediction test protocol and the SHOULD backlog), and the research queue at the end lists open items.

## Personas

*From the brainstorm (2026-09-30).*

- **Over-reaching qualifier chaser:** became the brief's primary persona (2026-10-08).
- **Under-prepared novice:** undertrains, risks stress fractures or a DNF (did not finish), and may mistake soreness for injury.
- **No-race runner:** wants fitness, accountability, and motivation ("running longevity").

## Technical constraints

*From the technical research (2026-10-08).*

- **Data in:** file import only. FIT first, read with `fit-file-parser` (MIT), then GPX and TCX. Not used:
  - the Strava API, which needs a paid subscription to create an app, plus a server-side secret
  - Apple HealthKit, which has no web access, and a native app costs US$99 per year
- **Runner's export path:** Apple Watch → Strava (or Runkeeper) → Export Original or bulk export on the website. Anita confirmed on 2026-10-08 that export works from the Strava web portal.
- **Swims:** SwimOutlet automatically pushes Anita's swim workouts to Strava (they also appear in Apple Workout), so swims arrive in the same Strava export as runs. The app still asks for an effort rating per cross-training session, because wrist heart rate (HR) is unreliable in water.
- **Hosting:** GitHub Pages, with Cloudflare as the fallback. Avoid Netlify's free plan: when its credits run out, every site on the team (Netlify's account term) is paused.
- **Storage:** IndexedDB (Dexie or idb). The export/import backup file is a MUST. On iOS, prompt users to select **Add to Home Screen**, because Safari deletes stored data after 7 days without a visit.
- **Load:** session RPE (rating of perceived exertion) × minutes is the universal unit (see app rules R3 and R4). HR-based TRIMP (training impulse) is a secondary signal, with separate run and swim max HRs. Never sum run and swim TSS (training stress score).

Source: `planning-artifacts/research/technical-free-data-import-and-hosting-for-runner-2026-10-08/research.md` (technical report items R1–R7, not the app rules below).

## App rules

*From the sports-science research (2026-10-08).*

The domain report lists 12 rules, each with "Why?" text and an evidence label. The table maps them to scope:

| Rule | Topic | Scope |
|---|---|---|
| R3, R4 | Cross-training load = effort × minutes; never convert to running km | MUST (MVP) |
| R6 | Single-run spike warning (primary) and weekly ramp flag (secondary), labelled "early evidence" | MUST (MVP) |
| R7, R8 | "Mostly easy" by minutes in zone; easy-zone limit from the runner's own max HR (default cap about 80%) | MUST (MVP) |
| R9 | See-a-professional red flags (bone stress, under-fuelling); groin/hip pain is urgent | MUST (MVP, as a checklist shown with warnings) |
| R11 | Fatigue check-in: readiness, sleep, and soreness; resting HR trend; HRV as a trend only | SHOULD (sprint 2, body map check-in; moved from MVP 2026-10-10) |
| R1, R2, R5 | Swim swap guidance, extra swims vs. rest, return-to-run nudge | SHOULD (sprint 2) |
| R10 | Pain rule for muscle and tendon only (never applied to bone pain) | SHOULD (good/bad pain translator) |
| R12 | Taper 2–3 weeks, cut volume 40–60%, keep intensity | SHOULD (Taper Jail) |

Source: `planning-artifacts/research/domain-sports-science-of-cross-training-and-loa-2026-10-08/research.md`

## Reality Check: design notes

*Decided 2026-10-08; postpone option added 2026-10-10.*

- **Inputs:** missed or at-risk key sessions, the plan phase (weeks to race), and the current projection.
- **Outputs:**
  - a revised goal estimate as a range
  - options with trade-offs: keep the goal, set a slower goal, switch to a shorter race, or postpone to a later race
  - a "Why?" for each option
- **Postpone (MVP):** records the new race date and distance, carries the run history over, shows "N weeks until your new race," and lets the runner enter the new plan.
- **Health red flags come first.** The app never recommends dropping a race. Health red flags are the exception: they override this rule, and the app says "see a professional before racing." If a red flag triggered the Reality Check, "see a professional" comes before any new-plan talk.
- **Plan rewriting (feature B) is deferred** until research on how coaches re-plan after missed training is done (research queue item 1).

## Training plan entry

*Decided 2026-10-10.*

- **MVP:** manual weekly entry plus a downloadable spreadsheet template (week, session type, distance or time). Both feed the same plan table.
- **Matching:** planned and actual sessions are matched by week and session type, not by date. Anita often moves her long run from the weekend to midweek.
- **Sprint 2:** a free AI tool reads a PDF plan into a draft of the same table. The runner reviews and corrects it before saving. Manual entry and the template stay as the fallback (see research queue item 5).
- **AI boundary:** AI only assists data entry. It never produces verdicts, warnings, or Reality Check advice, which stay rule-based with a cited "Why?"

## Sample runner

*Decided 2026-10-10.*

- **Main audience:** business readers of the LinkedIn article, who mostly have no run files.
- It's a made-up training cycle, generated by the BMAD agents and never Anita's data, opened from a **Try with a sample runner** button.
- It must trigger a verdict, at least one warning with its "Why?" explanation, a swim swap, and a Reality Check, so a visitor sees every core answer.
- It doubles as a test fixture for the developers.

## Prediction test protocol

*Decided 2026-10-10. Supports success criterion #1.*

- **Threshold:** the projected marathon finish must be within ±5% of Anita's actual November 1, 2026, result. The threshold is set before the race.
- **Freeze:** because the app will be finished after November 1, the projection method, its input data (only training up to race day), and its settings are written down and date-stamped in the memlog before the race. The frozen method is then run once, after the race.
- **Version 2:** any later improvement to the method is reported separately and never replaces the frozen result.
- **Benchmark:** Anita screenshots Strava's predicted marathon time before the race. The article compares Strava's prediction, the runner-app projection, and the actual finish.
- **Known handicap:** the MVP doesn't model weather, course elevation, or aid stations.

## Scope: SHOULD backlog

*Sprints 2–3.*

- body map check-in (soreness, sleep, readiness)
- PDF training plan import with free AI, into a draft table the runner reviews
- swim cross-training advice
- race profile (elevation and temperature)
- A/B/C goals
- basic calorie bank
- phase-aware fatigue
- race week / Taper Jail
- carb-loading alerts
- good/bad pain translator
- recovery lap
- distance-specific build and taper logic

## Research queue

Open items, not yet decided:

1. Re-planning after missed training, the minimum training to finish a marathon safely, and moving from a stopped cycle to a postponed race (recovery, fitness carry-over, where to start the new plan). Feeds feature B and Reality Check wording.
2. Running and racing while sick.
3. Race-time projection methods suitable for rule-based use. **Time-critical:** the method must be chosen, sourced, and frozen with a date stamp before November 1, 2026 (success criterion #1).
4. Evidence-backed advice for other cross-training sports (cycling has better run-substitute evidence than swimming; HIIT is not yet covered by our research).
5. Free AI for reading PDF training plans: in-browser models versus free online services (cost, privacy, device support). AI that runs in the browser keeps the plan on the device; a free online service would send it to an outside company and could stop being free. Needed before sprint 2.
