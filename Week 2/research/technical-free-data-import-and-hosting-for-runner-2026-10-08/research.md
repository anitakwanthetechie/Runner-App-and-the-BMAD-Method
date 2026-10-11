---
title: 'technical research: Free data import, cross-sport load and hosting for runner-app'
type: 'technical'
topic: 'Free data import, cross-sport load and hosting for runner-app'
decision: 'Can runner-app get run and swim data in, measure training load across both sports, and go live for $0 with no native app, no accounts and data kept in the browser? Which import path, load measure and host should the first build use?'
source: 'native run'
status: complete
claims_verified: 20
citation_audit: '2026-10-08 (1 wrong source replaced, 9 partial citations reworded or downgraded)'
editorial_review: '2026-10-08 (structure, prose)'
claims_unverified: 3
preset: 'standard'
validation: 'normal'
created: '2026-10-08'
updated: '2026-10-08'
---

# Technical research: Free data import, cross-sport load and hosting for runner-app

**Decision this research serves:** Can runner-app get run and swim data in, measure training load across both sports, and go live for $0 with no native app, no accounts and data kept in the browser? Which import path, load measure and host should the first build use?

## Executive summary

**Yes, runner-app can launch for $0. The runner imports files they export themselves.** The plan in brief:
- **Import (R1):** FIT files first, read in the browser with the MIT-licensed `fit-file-parser` [22]. GPX and TCX as a fallback.
- **Load (R3):** session RPE (rating of perceived exertion: effort rating × minutes) as the main load measure for runs and swims alike [27][29].
- **Hosting (R5):** a static site on GitHub Pages or Cloudflare [45][47].
- **Storage (R6):** data in IndexedDB, with an export/import backup file.
- **Your decision (R7):** where swim cross-training sits in MoSCoW, once the sports-science research run is back.

The findings behind the answer:

1. **There's no free live data connection, so files are the only route in.**
   - **Apple:** Apple Health has no web API [62]. A native app needs the US$99/year program to be demoed publicly [3][4].
   - **Strava:** the API now needs a paid Strava subscription just to create an app [13]. Its login needs a server-side secret [15], and its terms cap caching at 7 days [14].
   - **What's left:** the runner already uploads to Strava, which can export each activity as its original file. Strava's website and Runkeeper both offer file export [18][21]. *(Whether export works on free accounts is unconfirmed. Medium confidence.)*
2. **Your swim requirement decides two things.**
   - **The file format:** pool swims lose their data in GPX and most of it in TCX [18][20]. FIT is likely the best format for them. *(Low confidence: forum-level evidence only.)*
   - **The load measure:** session RPE is the only measure in this research that works the same way for runs and swims, with or without heart rate, and needs no fitness test [27][29]. *(This is an inference from each measure's required inputs.)* HR-based TRIMP (training impulse, a heart-rate load score) can add a second signal, but only with a separate swim max heart rate, which is about 7 bpm lower than in running [33]. No source supports simply adding run and swim training-stress scores together.
3. **Safari can silently delete local-first data.** iPhone Safari deletes stored data after 7 days without a visit unless the app is on the Home Screen [57]. The browser's request to keep data permanently (`persist()`) can't be relied on there [58]. So a backup file and an "Add to Home Screen" step are requirements, not nice-to-haves.

**Biggest caveat:** several details need a 30-minute hands-on test on your own phone and accounts rather than more web research:
- Is Strava's per-activity export still free on non-subscriber accounts?
- Which swim fields does an Apple Watch FIT export actually contain?
- Can a free Apple Shortcut export workouts?

## 1. Apple data access

**No web route into Apple Health exists, and no native route is $0 and demoable at the same time.**

- **Web access.** Apple's HealthKit documentation lists only native platforms (iOS, iPadOS, macOS, watchOS, visionOS) and no web or JavaScript one [62]. Apple's own developer forum has an unanswered 2020 question on exactly this [1]. Pages that describe an "Apple Health Web API" come from content-farm domains with no Apple backing, and this research rejects them [2]. *(Verified. High confidence.)*
- **Native app.** The Apple Developer Program costs US$99/year [3]. A free Apple Account can still build to your own device with Xcode. Limits: provisioning profiles expire after 7 days, at most 3 devices, at most 3 apps per device, and no TestFlight or App Store [4]. Apple's capability table allows HealthKit on free accounts [5]. *(Verified.)* Even so, a free native build is a personal tool and **cannot be a public demo**.
- **Apple's own export.** "Export All Health Data" produces `export.xml` plus a `workout-routes/` folder of GPX files [6]. *(The source could not be re-checked. Medium confidence.)* The files are large: a blogger calls it "a huge file" [7], and one user reports 1 GB of XML [8], so an in-browser importer would have to read them in pieces (stream). *(Low-medium confidence.)* This research did not confirm whether swim lengths and running dynamics (power, ground contact time and so on) are in `export.xml`. That needs a hands-on test.
- **Single-workout export apps.** Some exist, and the useful ones cost money. HealthFit is US$6.99 [9]. RunGap's export needs a subscription (US$4.99/quarter or US$14.99/year) [10]. "GPX Export" is listed as free but exports routes only [11].
- **Apple Shortcuts.** A "Find Health Samples" action exists [12]. No source confirms that it can read whole workouts and save them to a file. A hands-on test can settle this.

## 2. Import formats, Strava and swim data

**File import is the only $0 path that works on a static site. FIT is the format to support first, because only FIT is likely to keep pool-swim detail.**

- **Strava API is closed to a $0 static app.** Two blockers settle it:
  - Strava's developer docs state that "A Strava subscription is a prerequisite for creating an app" [13]. The API Policy (effective June 1, 2026) adds subscription requirements for small apps [14].
  - The OAuth token exchange needs the app's client secret [15]. A site with no backend would expose that secret.
  - **Further limits if the API is revisited:**
    - New apps start in single-player mode (developer only), then get a 10-athlete cap until Strava reviews them [13].
    - The API Agreement allows data to be shown only to the athlete who owns it [16]. The Policy caps caching at 7 days and bans use in any AI application [14].
    - Rate limits: the default is 200 requests per 15 minutes and 2,000 per day overall, of which reads are 100 per 15 minutes and 1,000 per day. The higher limits (400 per 15 minutes, 4,000 per day) apply only after an app is upgraded to 10 athletes [17][13]. *(Verified. The two pages describe different tiers.)*
- **Strava export (the free alternative).** Export works on the Strava website only, not in the mobile app. "Export Original" returns the file as it was uploaded, often FIT. GPX export needs GPS data, so **pool swims export as unusable GPX** [18]. TCX is available through a URL suffix [18]. A bulk "Download your data" archive contains one file per activity plus `activities.csv` [18][19]. This research didn't confirm whether free accounts can use per-activity export.
- **Runkeeper.** The web export gives GPX files plus CSV summaries. Apple Watch-only and manual activities appear only in the CSV [21]. Runkeeper's help page [21] doesn't say whether export is free.
- **Swim data by format.** A Garmin forum thread reports that TCX files don't export stroke and swim-efficiency data [20]. GPX and TCX files of activities without GPS carry no GPS points [63], and FIT pool swims carry lap records [64]. *(Low confidence: user forums only, with no official vendor statement.)* The likely ranking for pool swims is **FIT first**, then TCX, then GPX, which keeps nothing. This research did not confirm the exact FIT fields for pool lengths and strokes.
- **Browser parsers.**
  - `fit-file-parser`: MIT license, last release September 22, 2026 [22]. This is the safe default.
  - Garmin's official `@garmin/fitsdk` runs in the browser [23] and is royalty-free [24]. Its license, though, forbids "distribut[ing] … the Licensed Technology … to any third party" [24]. Bundling it into a public web app may conflict with that clause. *(Ambiguous; needs a human read.)*
  - `gpxparser` hasn't had a release since 2021 [25].
  - `@tmcw/togeojson` (BSD-2 license, last release May 2025) converts GPX and TCX to GeoJSON [26].

## 3. Cross-sport training load measures

**Session RPE (effort rating × minutes) is the simplest measure that works for every workout, including a pool swim with no heart rate. HR-based TRIMP is a good second signal, provided swims use their own max heart rate.**

| Measure | Inputs | Run (GPS + HR) | Pool swim, wrist HR | Pool swim, no HR |
|---|---|---|---|---|
| Session RPE | minutes + 0–10 rating | ✅ | ✅ | ✅ |
| Banister TRIMP | HR, resting HR, max HR (per sport), sex | ✅ | ✅ with swim max HR | ❌ |
| rTSS / sTSS | threshold pace / critical swim speed test | ✅ (test needed) | ✅ (test needed) | ✅ (test needed) |

**Load measures:**
- **Session RPE (Foster).** Load = rating on Foster's 0–10 effort scale × session minutes [27][28]. In 12 well-trained swimmers over 20 sessions it correlated with HR-based load at r = 0.55–0.94 [29]. *(Verified.)* It also supports simple "monotony" and "strain" warnings [27]. The weakness: the runner has to enter a rating, and the rating is subjective.
- **Banister TRIMP.** The formula is duration × HRr × 0.64 × e^(1.92 × HRr), where HRr = (avg HR − resting HR)/(max HR − resting HR). The exponents are 1.92 for men and 1.67 for women [67][31]. *(Verified.)* Using the full heart-rate trace rates interval sessions better than using average HR [32].
  - **Formula note:** the peer-reviewed source leaves out the 0.64 (men) / 0.86 (women) multipliers. Only non-peer-reviewed sources give them [30][68]. This matters only if the app's numbers must match other tools.
  - **Swim caveat:** max heart rate in swimming is about 7 bpm lower than in running (6.7 ± 5.3 bpm, elite swimmers) [33]. An older study put the gap at about 12 bpm [34]. *(Verified.)* Without a separate swim max HR, swim TRIMP comes out too low.
- **TSS family.** rTSS, hrTSS and swim sTSS each need a threshold test (threshold pace or critical swim speed). TrainingPeaks cubes intensity for swims and calls its own method "simplistic" [35]. Other calculators square it instead [36]. HR-based scores differ between platforms [37]. **No source validates adding run TSS and swim TSS into one number.**

**Models built on a load series:**
- **Fitness/fatigue curves.** CTL (fitness) and ATL (fatigue) track the last 42 and 7 days of load. TSB ("form") = CTL − ATL [38][39][69]. intervals.icu implements them as exponentially weighted averages [71]. *(Verified.)* These can run on any daily load series, including session RPE.
- **ACWR (acute:chronic workload ratio).** Impellizzeri and colleagues conclude that ACWR has no predictive advantage and that no study shows a causal effect on injury [40][70]. Lolli and colleagues show that the usual calculation creates a spurious correlation, because recent load is counted in both halves of the ratio [41]. *(Verified.)* Other researchers find that this coupling issue has only trivial effects in practice [42][72]. The app should not present ACWR as an injury predictor.

**What commercial tools do:**
- Strava Relative Effort (subscribers only) uses heart rate or perceived exertion, and weights activity types "so that your efforts can be compared across sports" [43].
- Garmin's acute load is a weighted sum of EPOC (excess post-exercise oxygen consumption) over recent days [44].
- Neither publishes its weights.

## 4. $0 hosting and browser storage

**GitHub Pages or Cloudflare can host the app for $0. Browser storage is large enough, but Safari can delete it, so an export/import backup file and Add to Home Screen are required.**

### Hosting

| Host | Free limit | Commercial use | Over the limit | Card required |
|---|---|---|---|---|
| GitHub Pages | 1 GB site, soft 100 GB/month bandwidth, free HTTPS on custom domains [45][46] | Not for commercial ventures; a hobby demo fits [45] | Soft limits [45] | Unverified |
| Cloudflare | Workers: "Requests to static assets are free and unlimited" [47]. Pages: 500 builds/month [48] | Not restricted in fetched pages | — | No [66] |
| Vercel Hobby | 100 GB/month transfer [51] | Non-commercial personal use only [50] | Can't buy extra usage [50] | Unverified |
| Netlify (accounts since September 4, 2025) | 300 credits/month, hard cap [52] | — | **Every site on the team is paused** until the next cycle [53] | No [65] |

Cloudflare offers two hosting products, Workers and Pages. This research found no deprecation notice for Pages [49]. Netlify is the riskiest free host for a public demo.

### Browser storage

- **Storage space is not a constraint.** The browser's built-in storage (IndexedDB and OPFS) gets about 60% of disk per site in Chrome and Safari 17+. Firefox gets the smaller of 10% of disk or 10 GiB. localStorage is capped at 5 MiB [54][55]. OPFS works in about 97% of browsers [56].
- **Eviction is the real risk.** Safari deletes "all … script-writeable storage" after 7 days without a visit to the site. **Home Screen web apps are exempt** [57][54].
  - Chrome and Safari grant or refuse `navigator.storage.persist()` silently; Firefox asks the user [54].
  - A WebKit bug reporting that `persist()` "always returns false" is still open [58], so Safari can't rely on it.
- **Backup.** The File System Access API (save directly to a chosen file) works only in Chromium browsers [59]. The portable backup is therefore a plain download/upload file.
- **Libraries.** Dexie.js (Apache-2.0) and idb (ISC) are both actively maintained as of September/October 2026 [60][61].

## Cross-dimension insights

1. **Strava is the free bridge out of Apple's walled garden.** Apple data can't reach a web app directly (§1), and the Strava API is paywalled (§2). But your Apple Watch workouts already land in Strava, and Strava's website returns the original file [18]. The route Apple Watch → Strava → Export Original → runner-app should cost nothing at any step, if free accounts keep export. *Inference:* exported files that the user uploads are the user's own data, not "Strava API Data", so the API Policy's caching and AI rules should not apply. No Strava source confirms this.
2. **The swim requirement and the core intent point to the same input.** Session RPE is a "how did that feel" rating (§3). Your brainstorm's core intent already relies on the runner as the only sensor of how they feel. One post-workout check-in (effort rating plus the body map) can drive both load tracking and injury warnings.
3. **Local-first privacy and Safari eviction are two sides of one design.** Keeping data in the browser removes the account, server and privacy-breach risks (§4). The same choice makes the backup file the only copy outside the browser. That file can also feed the later "doctor-ready report" idea from your COULD list.
4. **Host risk follows from the LinkedIn plan.** A post that gets attention brings a traffic spike. On Netlify's credit system, a spike pauses every site on the team [53]. GitHub Pages' soft limits [45] and Cloudflare's unlimited static requests [47] have no such cliff.

## Contrary evidence

This run skipped the dedicated search for counter-evidence (the red-team pass). Points against the conclusions that turned up during research:
- **Session RPE depends on the user.** It needs a rating after every session and is subjective [27]. If runners skip the rating, load for swims without heart rate goes missing. Strava's Relative Effort can use either heart rate or perceived exertion [43].
- **The ACWR criticism has pushback.** Other researchers find the coupling problem has only trivial effects in practice [42][72]. Even so, no study shows ACWR causally predicts injury [70].

## Recommendations

| # | Recommendation | Confidence basis | Feeds |
|---|---|---|---|
| R1 | **Import by file only in the MVP.** FIT first, using `fit-file-parser` (MIT) [22]. GPX and TCX through the browser's XML parser or `@tmcw/togeojson` [26]. Avoid Garmin's SDK until a human reads its license [24]. Drop the Strava API and HealthKit from MVP scope. | High (all API and license claims verified) | Brief: feasibility and constraints · Architecture: ingest |
| R2 | **Document the user's export path** as onboarding: Apple Watch → Strava (or Runkeeper) → Export Original or bulk export on the website [18][21]. Treat Apple's full Health export as a later option, one the app must read in pieces (stream) [6][7]. | Medium (free-account export not confirmed) | Brief: user journey · UX |
| R3 | **Use session RPE as the main load unit**, collected in the same post-workout check-in as the body map [27][29]. Add HR-based TRIMP as a secondary signal, with **separate run and swim max HR** settings [33]. Do not sum TSS across sports. Do not show ACWR as an injury predictor [40][70]. | High for formulas and validity; the cross-sport fitness interpretation is waiting on the queued sports-science research run | PRD: new cross-training requirement · Architecture: load model |
| R4 | **Fitness/fatigue curves (42/7-day exponentially weighted averages) on daily session-RPE load** [38][71] power the "am I maintaining fitness?" view, as rule-based math with a "Why?" trail. | Medium-high | PRD: no-race fitness view, cross-training |
| R5 | **Host on GitHub Pages** (free HTTPS, non-commercial use fits) [45][46]. Cloudflare is the fallback [47]. Avoid Netlify's free plan for a public demo [53]. | High (card requirement for GitHub unverified, low risk) | Architecture: deploy |
| R6 | **Store data in IndexedDB** (Dexie or idb) [60][61]. **Make export/import backup a MUST.** Prompt iOS users to Add to Home Screen [57]. Call `persist()` as a best effort and show the result [54][58]. | High | Brief: trust basics · PRD: MUST #6 |
| R7 | **Add "cross-training (swim) load" to the brief as a requirement candidate.** The technical side is feasible at $0. Its MoSCoW slot is your call after the sports-science run. | n/a (scope decision) | Brief: scope |

## Open questions

| Question | How to answer it | Blocks |
|---|---|---|
| Can a free (non-subscriber) Strava account still use Export Original, GPX and TCX, including from a phone browser? | Anita tries it on one run and one swim (5 minutes). | R2 |
| Which fields does an Apple Watch pool swim contain in its Strava FIT file (lengths, strokes, HR)? | Export one swim, then parse it with `fit-file-parser` in a short developer test. | R1 |
| Can a free Apple Shortcut ("Find Health Samples") export workouts to a file? | Hands-on test on Anita's iPhone. | R2 (optional path) |
| Does Apple's `export.xml` include running dynamics and swim laps? | Inspect Anita's own export, or a parser project's schema. | R2 (later option) |
| Does Safari (iOS 26/27) grant `persist()` to Home Screen apps? | Device test in the first build. | R6 |
| Does Garmin's FIT SDK license allow bundling in a public web app? | Human or legal read of LICENSE §2(c). Moot if `fit-file-parser` works. | R1 |
| Do GitHub Pages or Vercel Hobby need a credit card? | Sign up when deploying (GitHub Pages is the recommended host). | R5 |
| Is wrist HR accurate in the pool, and does swimming maintain running fitness? | **Queued sports-science research run.** | R3, R7 |

## Source appendix

| # | Supports | Publisher | Published | Accessed | Confidence |
|---|---|---|---|---|---|
| [1] | Unanswered forum question on HealthKit for web apps | [Apple Developer Forums](https://developer.apple.com/forums/thread/668521) | 2020-12 | 2026-10-08 | medium |
| [2] | 'Apple Health Web API' pages (rejected as content farm) | [Unknown content farm](https://1.us.push.bas-ip.com/active-series/apple-health-web-api-guide-2025-1770668326) | n/d | 2026-10-08 | low |
| [3] | Apple Developer Program US$99/yr | [Apple](https://developer.apple.com/programs/) | n/d (live) | 2026-10-08 | high |
| [4] | Free account: on-device only, 7-day profiles, 3 devices | [Apple](https://developer.apple.com/support/compare-memberships/) | n/d (live) | 2026-10-08 | high |
| [5] | HealthKit available to free accounts | [Apple](https://developer.apple.com/help/account/reference/supported-capabilities-ios) | n/d (live) | 2026-10-08 | high (checked in the page's raw HTML; an AI summary of the page got it wrong) |
| [6] | Health export = export.xml + workout-routes GPX | [Peerlist](https://peerlist.io/siddhartha/project/healthsync) | n/d | 2026-10-08 | medium (search snippet only; page returned 403 on re-check) |
| [7] | Health export is huge/slow; free GPX apps | [RasterWeb](https://rasterweb.net/raster/2025/10/20/gpx-files-from-an-iphone-apple-watch/) | 2025-10 | 2026-10-08 | medium |
| [8] | Health export sizes ~1 GB XML | [Quantified Self forum](https://forum.quantifiedself.com/t/apple-health-xml-data-with-xslt/15924) | n/d | 2026-10-08 | low |
| [9] | HealthFit price US$6.99 | [Apple App Store](https://apps.apple.com/us/app/healthfit/id1202650514) | 2026-10 | 2026-10-08 | medium-high |
| [10] | RunGap export needs subscription | [Apple App Store](https://apps.apple.com/us/app/rungap/id534460198) | 2026-09 | 2026-10-08 | medium-high |
| [11] | GPX Export free route exporter | [Apple App Store](https://apps.apple.com/app/gpx-export/id1667613575) | n/d | 2026-10-08 | medium |
| [12] | Shortcuts 'Find Health Samples' action | [Apple Support](https://support.apple.com/en-us/101583) | n/d (live) | 2026-10-08 | medium |
| [13] | Strava subscription prerequisite; single-player; 10-athlete tier | [Strava Developers](https://developers.strava.com/docs/getting-started/) | n/d (live) | 2026-10-08 | high |
| [14] | Strava API Policy: subscription tiers, 7-day cache, no AI | [Strava](https://www.strava.com/legal/api_policy) | 2026-06 | 2026-10-08 | high |
| [15] | OAuth token exchange needs client secret | [Strava Developers](https://developers.strava.com/docs/authentication/) | n/d (live) | 2026-10-08 | high |
| [16] | API Agreement: display only to owning athlete | [Strava](https://www.strava.com/legal/api) | 2026-06 | 2026-10-08 | high |
| [17] | Default rate limits 200/2,000; read 100/1,000 | [Strava Developers](https://developers.strava.com/docs/rate-limits/) | n/d (live) | 2026-10-08 | high |
| [18] | Strava export: website only, Export Original, GPX needs GPS, TCX, bulk | [Strava Support](https://support.strava.com/hc/en-us/articles/216918437-Exporting-your-Data-and-Bulk-Export) | 2026-09 | 2026-10-08 | high |
| [19] | Bulk archive contents | [takeoutday.org](https://takeoutday.org/guides/how-to-export-strava-data) | n/d | 2026-10-08 | medium |
| [20] | TCX pool swims lose stroke detail | [Garmin Forums](https://forums.garmin.com/sports-fitness/sports-fitness/f/garmin-swim/68161/no-stroke-efficiency-in-tcx-file) | n/d | 2026-10-08 | medium |
| [21] | Runkeeper export GPX + CSV | [ASICS Runkeeper Help](https://help.runkeeper.com/en/hc/how-to-export-your-runkeeper-data) | n/d | 2026-10-08 | high |
| [22] | fit-file-parser MIT, v6.1.2 | [npm registry](https://registry.npmjs.org/fit-file-parser) | 2026-09 | 2026-10-08 | high |
| [23] | Garmin FIT JS SDK runs in browser | [Garmin (GitHub)](https://github.com/garmin/fit-javascript-sdk) | n/d | 2026-10-08 | high |
| [24] | Garmin FIT licence: royalty-free, no third-party distribution | [Garmin](https://raw.githubusercontent.com/garmin/fit-javascript-sdk/main/LICENSE.txt) | n/d | 2026-10-08 | medium (interpretation) |
| [25] | gpxparser last release 2021 | [npm registry](https://registry.npmjs.org/gpxparser) | 2021-06 | 2026-10-08 | high |
| [26] | @tmcw/togeojson BSD-2, v7.1.2 | [npm registry](https://registry.npmjs.org/@tmcw/togeojson) | 2025-05 | 2026-10-08 | high |
| [27] | sRPE formula; monotony/strain; validity review | [Frontiers in Neuroscience (Haddad et al.)](https://www.frontiersin.org/articles/10.3389/fnins.2017.00612/full) | 2017-11 | 2026-10-08 | high |
| [28] | Foster 2001 original sRPE paper | [Scinapse (index)](https://scinapse.io/papers/1966843711) | 2001 | 2026-10-08 | medium |
| [29] | sRPE vs HR load in swimmers r=0.55-0.94 | [UTS OPUS (Wallace et al.)](https://opus.lib.uts.edu.au/handle/10453/9495) | 2009 | 2026-10-08 | high |
| [30] | Banister TRIMP with 0.64 prefactor (reverse-engineered) | [intervals.icu forum](https://forum.intervals.icu/t/trimp-formula-required/68634) | 2024-06 | 2026-10-08 | medium |
| [31] | TRIMP sex coefficients 1.92/1.67 | [Garmin Connect IQ forum](https://forums.garmin.com/developer/connect-iq/f/app-ideas/6776/data-field-trimp---training-impulse) | n/d | 2026-10-08 | medium |
| [32] | Sample-based TRIMP rates intervals better | [Runalyze](https://runalyze.com/glossary/trimp) | n/d | 2026-10-08 | medium |
| [33] | Swim HRmax 6.7±5.3 bpm lower than running | [Sports (MDPI), Olstad et al.](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6915385/) | 2019 | 2026-10-08 | high |
| [34] | Treadmill HR ~12 bpm above swim HR (DiCarlo 1991) | [SDSU Coaching Science Abstracts](https://coachsci.sdsu.edu/swim/physiol/dicarlo.htm) | 1991 | 2026-10-08 | medium |
| [35] | sTSS = IF³ × hours × 100; 'simplistic' | [TrainingPeaks](https://www.trainingpeaks.com/learn/articles/calculating-swimming-tss-score/) | 2026-04 (updated) | 2026-10-08 | high |
| [36] | Some calculators use IF² for swim TSS | [prommer.net](https://prommer.net/en/training/calculators/swimming/swim-tss/) | n/d | 2026-10-08 | low |
| [37] | No standard HR-based TSS across platforms | [intervals.icu forum](https://forum.intervals.icu/t/hrtss-vs-load-on-intervals-icu/76155) | ~2024 | 2026-10-08 | medium |
| [38] | CTL 42-day / ATL 7-day | [TrainingPeaks Help](https://help.trainingpeaks.com/hc/en-us/articles/204071884-Fitness-CTL) | n/d | 2026-10-08 | medium-high |
| [39] | Performance Manager Chart setup | [FasCat Coaching](https://fascatcoaching.com/tips/setting-performance-manager-chart/) | n/d | 2026-10-08 | medium |
| [40] | ACWR: no predictive advantage (Impellizzeri et al.) | [Univ. Verona IRIS](https://iris.univr.it/handle/11562/1139126) | 2020-21 | 2026-10-08 | medium-high |
| [41] | ACWR coupling spurious correlation (Lolli et al., BJSM 53(15):921) | [Teesside University repository](https://research.tees.ac.uk/en/publications/mathematical-coupling-causes-spurious-correlation-within-the-conv-3/) | 2019 | 2026-10-08 | high |
| [42] | Counterpoint: coupling effects trivial | [Edith Cowan University](https://ro.ecu.edu.au/ecuworkspost2013/7321/) | ~2019 | 2026-10-08 | medium |
| [43] | Strava Relative Effort: HR or PE, cross-sport weighting, subscriber | [Strava Support](https://support.strava.com/hc/en-us/articles/360000197364-Relative-Effort) | 2026-09 | 2026-10-08 | high |
| [44] | Garmin acute load = weighted EPOC sum | [Garmin](https://www8.garmin.com/manuals/webhelp/GUID-61792F90-CAB8-4B7C-954A-1E1A080A6FB6/EN-US/GUID-AEDB0872-C5A1-4378-86D5-2239734B59E8.html) | n/d | 2026-10-08 | high |
| [45] | GitHub Pages limits and prohibited uses | [GitHub Docs](https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits) | n/d (live) | 2026-10-08 | high |
| [46] | GitHub Pages HTTPS on custom domains | [GitHub Docs](https://docs.github.com/en/pages/getting-started-with-github-pages/securing-your-github-pages-site-with-https) | n/d (live) | 2026-10-08 | high |
| [47] | Workers static asset requests free and unlimited | [Cloudflare Docs](https://developers.cloudflare.com/workers/static-assets/billing-and-limitations/) | n/d (live) | 2026-10-08 | high |
| [48] | Cloudflare Pages free: 500 builds/month | [Cloudflare Docs](https://developers.cloudflare.com/pages/platform/limits/) | n/d (live) | 2026-10-08 | high |
| [49] | Pages→Workers migration; no deprecation found | [Cloudflare Docs](https://developers.cloudflare.com/workers/static-assets/migration-guides/migrate-from-pages/) | n/d (live) | 2026-10-08 | medium |
| [50] | Vercel Hobby non-commercial only | [Vercel](https://vercel.com/docs/limits/fair-use-guidelines) | 2026-09 | 2026-10-08 | high |
| [51] | Vercel Hobby limits; no extra usage | [Vercel](https://vercel.com/pricing) | 2026-09 | 2026-10-08 | high |
| [52] | Netlify credit-based plans since 4 Sep 2025; 300 credits | [Netlify Docs](https://docs.netlify.com/manage/accounts-and-billing/billing/billing-for-credit-based-plans/credit-based-pricing-plans/) | 2025-09 | 2026-10-08 | high |
| [53] | Netlify pauses all team projects when credits run out | [Netlify Docs](https://docs.netlify.com/manage/accounts-and-billing/billing/resume-paused-projects/) | n/d (live) | 2026-10-08 | high |
| [54] | Storage quotas, eviction, persist() behaviour | [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Storage_API/Storage_quotas_and_eviction_criteria) | 2026-01 | 2026-10-08 | high |
| [55] | Safari 17 storage policy | [WebKit](https://webkit.org/blog/14403/updates-to-storage-policy/) | 2023-08 | 2026-10-08 | medium |
| [56] | OPFS support ~97% | [caniuse](https://caniuse.com/mdn-api_storagemanager_getdirectory) | current | 2026-10-08 | high |
| [57] | ITP 7-day deletion; Home Screen apps exempt | [WebKit](https://webkit.org/tracking-prevention/) | n/d (live) | 2026-10-08 | high |
| [58] | Bug: persist() always returns false in Safari | [WebKit Bugzilla](https://bugs.webkit.org/show_bug.cgi?id=271401) | 2024-03 | 2026-10-08 | medium |
| [59] | File System Access API Chromium-only | [caniuse](https://caniuse.com/native-filesystem-api) | current | 2026-10-08 | high |
| [60] | Dexie.js Apache-2.0, active | [GitHub](https://github.com/dexie/Dexie.js) | 2026-09 | 2026-10-08 | high |
| [61] | idb ISC, active | [GitHub](https://github.com/jakearchibald/idb) | 2026-10 | 2026-10-08 | high |
| [62] | HealthKit platforms: native only | [Apple](https://developer.apple.com/documentation/healthkit) | n/d (live) | 2026-10-08 | high |
| [63] | Pool/indoor swim GPX/TCX contain no GPS | [Garmin Forums](https://forums.garmin.com/apps-software/mobile-apps-web/f/garmin-connect-web/401797/download-gpx-kml-or-tcx-files-contain-no-gps-data/1888615) | n/d | 2026-10-08 | low |
| [64] | FIT pool swims carry laps/lengths | [Strava Community Hub](https://communityhub.strava.com/developers-api-7/fit-file-import-pool-swim-missing-laps-9444) | n/d | 2026-10-08 | low |
| [65] | Netlify free: no credit card required | [Netlify](https://www.netlify.com/blog/introducing-netlify-free-plan/) | n/d | 2026-10-08 | medium |
| [66] | Cloudflare Workers: no credit card required | [Cloudflare](https://workers.cloudflare.com/) | n/d (live) | 2026-10-08 | high |
| [67] | Banister TRIMP exponents 1.92/1.67 (peer-reviewed) | [Frontiers in Physiology (Falk Neto et al.)](https://pmc.ncbi.nlm.nih.gov/articles/PMC7435063/) | 2020 | 2026-10-08 | high |
| [68] | TRIMP 0.64/0.86 prefactors | [TrainAsONE](https://trainasone.com/faqs/2023/training-impulse-trimp) | 2023 | 2026-10-08 | medium |
| [69] | CTL/ATL/TSB coach's guide | [TrainingPeaks](https://www.trainingpeaks.com/blog/a-coachs-guide-to-atl-ctl-tsb/) | 2026-06 (updated) | 2026-10-08 | high |
| [70] | ACWR conceptual issues (Impellizzeri et al., IJSPP) | [UTS OPUS](https://opus.lib.uts.edu.au/citation/handle/10453/141898) | 2020 | 2026-10-08 | high |
| [71] | CTL/ATL as exponentially weighted averages (42/7-day defaults) | [intervals.icu forum (developer posts)](https://forum.intervals.icu/t/fitness-and-form-calculation/31549) | n/d | 2026-10-08 | medium-high |
| [72] | Editorial: coupled and uncoupled ACWR give similar injury likelihoods (Windt & Gabbett) | [BJSM](https://bjsm.bmj.com/content/53/16/988) | 2019 | 2026-10-08 | medium |

## Staleness map

Living documents are dated by access (2026-10-08). Results from settled research papers (sRPE, ACWR, swim HRmax) don't go stale on a fixed window and are not listed. The windows follow the technical pack: versions ≤1 month, pricing and terms 3 months, capability 6 months.

| Re-check by | Claim |
|---|---|
| 2026-11-01 | Strava rate limits · `fit-file-parser` version/licence · Safari `persist()` bug status |
| 2027-01-01 | Strava API subscription and Policy terms · free-tier limits and terms of the four hosts · Apple Developer Program price and free-account limits |
| 2027-04-01 | Safari 7-day eviction and Home Screen exemption · Strava export options and pool-swim GPX · no web HealthKit access |

**Earliest re-check: 2026-11-01.** Refresh this research before the build sprint starts.

