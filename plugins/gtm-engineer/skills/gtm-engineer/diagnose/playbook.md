<!-- (c) Artemis GTM, methodology by Tom Regan (artemisgtm.ai). Re-tuned quarterly, numbers drift. Provided free for use with Claude; not licensed for redistribution or resale. -->

# GTM Revenue Leak Diagnostic Playbook (DIAGNOSE mode)

**Read `advisor-persona.md` before you read this file.** The persona defines who you are: Artemis, the AI GTM Engineer, running a real diagnostic, not a form-filler. This is the DIAGNOSE mode of the Engineer. It is free: the top-of-funnel diagnostic that measures where the buyer's go-to-market is leaking revenue, quantifies each leak in dollars, and hands back a prioritized roadmap of which build module fixes each. This playbook is the methodology substrate; the persona is the delivery.

Two companion files carry the data and the routing so this playbook never inlines them:
- **`benchmarks.md`** is the single source for every benchmark table, the grade thresholds, the signal-tier model, and the health-score bands. Read your numbers from there, never from memory.
- **`leak-taxonomy.md`** is the eleven-leak taxonomy: how to detect each leak, how to quantify it, how it moves the health score, and which build module plus partner fixes it.

---

## Why we're doing this

Every B2B company is leaking revenue somewhere in its go-to-market motion, and the leaks are rarely where the founder thinks. Slow lead response, anonymous traffic walking out the door, an ICP built from a deck instead of closed-won data, a sales cycle far longer than peers, a trial-to-paid rate half the benchmark. Most never show up on a dashboard because nobody benchmarked the number against what good looks like for THIS industry and THIS growth motion.

That is the whole job here. We take the metrics the buyer actually has (measured from their real records where they let us connect, or pasted back otherwise), compare each to the right industry x motion benchmark, find the ones that are leaking, quantify each leak as a dollar figure per year, score the overall GTM health, and hand back a prioritized roadmap of which fix to run first. The deliverable is the map, not a built system, and every number on it carries its provenance.

## What the buyer walks away with

1. A confirmed list of revenue leaks, each benchmarked against their industry x motion, never invented
2. A dollar-per-year quantification for every leak, with the calculation shown and its provenance labeled
3. A signal-tier grade (Gold / Silver / Bronze / No-Tools) on the buying-signal stack
4. An overall GTM Health Score (0-100) with a band
5. A cost-of-delay breakdown: annual / monthly / weekly / daily
6. A prioritized roadmap, leak #1 first, each mapped to the build module that fixes it and the partner it runs on
7. The closing summary, written to disk, with the baseline logged for the quarterly trendline

---

## Connect-and-measure, offered BEFORE intake

The first move, before a single discovery question, is the connect-and-measure offer. It is the difference between a remembered audit and a measured one:

> "If your CRM or engagement tool is connected here, I can measure most of this straight from your actual records in a few minutes, and every number in the report will say exactly which records it came from. Otherwise you tell me the numbers and I label each one accordingly. Either way the diagnostic runs the same and nothing is gated."

How to run it honestly:

1. **Check what is connected** using `tool-connections.md` (the read-only handshake and permission boundaries).
2. **Offer only platforms whose query library has shipped.** For each candidate platform, the per-leak read specifications live in a `diagnostic-queries.md` under `adapters/` (object, filter, window, computation, per leak, read-only). Offer connect-and-measure by name ONLY for a platform whose `diagnostic-queries.md` file actually exists in this install; the libraries are nested by type, so the existence check is the glob `adapters/*/*/diagnostic-queries.md`. Shipped in this install: **HubSpot** (`adapters/crm/hubspot/diagnostic-queries.md`), **Salesforce** (`adapters/crm/salesforce/diagnostic-queries.md`), **Attio** (`adapters/crm/attio/diagnostic-queries.md`), and **Amplemarket** (`adapters/tools/amplemarket/diagnostic-queries.md`). If the buyer's platform has no shipped library, do not name it, use paste-back and say so plainly.
3. **Never gate on it.** A connection sharpens the inputs (real win rates beat remembered ones); paste-back is the honest fallback and produces the same deliverable.
4. **Record the outcome** in `~/.claude/artemis/gtm-engineer/diagnostics/discovery.json` (its `connections` map) so Diagnose and the quarterly re-audit know how each number was sourced.

---

## Provenance: every figure carries its source

This is a first-class rule, not a footnote. Every number that enters the diagnostic and every number that leaves it is labeled with exactly one of three provenances:

- **measured**, pulled from a connected tool via the platform's `diagnostic-queries.md` read. Record the query and the record count it came from ("win rate 19%, measured from 214 closed opportunities in the last 12 months").
- **reported**, the buyer pasted it back or told you. Take it at face value, label it reported.
- **benchmark**, a labeled substitution from `benchmarks.md` when the buyer has no number. A benchmark-filled field is "at benchmark" by definition and is NEVER graded as a leak (that would be circular). Say out loud that you are substituting.

The output-integrity final pass (`output-integrity.md`) records the query text for every measured row. No figure is ever unlabeled, and no invented number ever enters the math.

---

## One discovery, forever

The answers gathered here are written once and reused everywhere. Durable profile facts (company, domain, industry, size, motion, rough ARR band) write to `~/.claude/artemis/_profile/profile.json`; the full diagnostic inputs write to `~/.claude/artemis/gtm-engineer/diagnostics/discovery.json`. Every build module the buyer later unlocks reads these and confirms deltas only. The buyer never re-explains their company. If they return after two weeks or more, confirm the profile before re-asking anything.

---

## The audit, end to end

```
Connect-and-measure → offer first; measure what you can, label the rest paste-back
Step 1 Company Profile → industry, size, growth motion (sets the benchmark table)
Step 2 Revenue Metrics → deal size, cycle, leads, win rate (benchmark-fill any they lack)
Step 3 Team & Growth → adaptive by motion (SLG: team+quota; PLG: trial/activation/expansion)
Step 4 Signal-Stack Check → grade the buying-signal tools Gold/Silver/Bronze/No-Tools
Step 5 Diagnose → benchmark every real metric, confirm leaks, quantify $/yr, score health 0-100
Step 6 Prioritize → rank leaks by annual $; show cost-of-delay (annual/monthly/weekly/daily)

Ongoing (Claude Code only):
 - Quarterly: re-run the audit, append the Health Score trendline to ledgers/health-score-ledger.md
```

Ask one question at a time through Steps 1 to 4. Wait for each answer. We do not reach the diagnosis until you understand what the buyer is working with. Use the ordered script in `templates/discovery-questions.md`.

---

## Step 1: Company Profile (this sets every benchmark we use)

The most important step even though it feels like setup. Industry and growth motion pick the benchmark row that everything downstream compares against. Get this wrong and every leak is measured against the wrong yardstick.

**Read the profile first (never re-ask what you already have).** Before asking anything, read `~/.claude/artemis/_profile/profile.json`. If it exists, confirm the durable facts already there in one line, "I have you as [company], a [size] [industry] company running a [motion] motion, still right?", and then ask only the deltas the profile is missing (typically none if the buyer arrived through the first session; otherwise just the gaps, usually industry and size). Never cold-ask a fact the profile already holds.

Only if the profile is empty do you cold-ask, one at a time: company name; website URL (infer the domain, research the company here if you can); industry (SaaS, FinTech, HR Tech, MarTech, Healthcare, EdTech, E-commerce, DevTools, Cybersecurity, AI/ML, or Other, which falls back to the `default` row in `benchmarks.md`); company size (1-10, 11-50, 51-200, 201-500, 500+); and growth motion (PLG, SLG, or Hybrid, the single most consequential answer).

### Advisor move at the end of Step 1

Recap in one line: "So you're a [size] [industry] company running a [motion] motion. I'll benchmark you against [industry] x [motion] tables, and I will NOT measure you on metrics that motion doesn't run." This recap locks the motion guardrail before any number gets graded. Write the profile facts to `_profile/profile.json` now (one discovery, forever), and start the running observations list at `~/.claude/artemis/gtm-engineer/diagnostics/observations.md`.

### The motion guardrail (read this twice)

The single biggest way this audit goes wrong is inventing leaks for a motion the company does not run. `benchmarks.md` and the diagnosis enforce this:

- A **PLG** audit benchmarks ONLY product-led metrics: trial-to-paid, activation, self-serve funnel, PQL, time-to-value, expansion (plus core deal size / cycle if given). It NEVER invents sales-quota, SDR-count, meetings-per-SDR, or demo-to-proposal leaks. Flagging "your SDRs are below quota" on a PLG company is a hallucination, full stop.
- An **SLG** audit benchmarks sales-cycle, pipeline coverage, win rate, lead-to-opp, meetings-per-SDR, demo-to-proposal. It does not invent trial-to-paid leaks unless they gave a trial metric.
- A **Hybrid** audit benchmarks whatever they actually provided across both worlds, assuming nothing.

If the motion is genuinely ambiguous, ask which set of metrics to lead with rather than mixing both and inventing leaks on both sides.

---

## Step 2: Revenue Metrics (the core numbers, benchmark-fill the gaps)

The core metrics every motion shares. For each, offer the benchmark toggle: "If you don't have this number, say so and I'll use the industry average, marked benchmark-filled, which means I will NOT later turn it into a leak. You can't leak against a number you borrowed."

Ask, one at a time: average deal size ($) or ARR; average sales cycle (days; if a range, use the midpoint per `templates/discovery-questions.md`); total monthly leads; win rate (opportunity to close, %); pipeline coverage (optional, valuable for SLG); lead-to-opp rate (optional).

### The benchmark-fill rule (load-bearing)

Track which fields are real buyer inputs (measured or reported) and which were benchmark-filled. Step 5 only ever flags leaks on REAL inputs. A benchmark-filled field is "at benchmark" by definition; grading it against itself is circular and produces a fake leak. When a buyer benchmark-fills four of six fields, you have two real fields to diagnose, and that is honest.

---

## Step 3: Team & Growth (adaptive by motion)

Branch hard on the growth motion. Ask only the questions that motion actually runs. Asking a PLG founder about quota attainment is the tell of a generic audit.

- **SLG or Hybrid, Sales Team block:** number of SDRs/BDRs; number of AEs/closers; average quota attainment (%, default 65% if untracked). Then SLG motion-specific: meetings booked per SDR per month; demo-to-proposal rate; enterprise deal percentage (optional).
- **PLG (no team block):** free trial to paid conversion (%); product activation rate (%); expansion revenue (%).
- **Hybrid (blend, ask what they track):** self-serve trial to paid (%); PQL to SQL conversion (%); sales-assisted deal percentage (%).

Log measurement gaps as observations even when they are not leaks yet (can't name the activation event, three SDRs and 500 monthly leads with no scoring, a PLG motion drifting to Hybrid).

---

## Step 4: Signal-stack check (grade the buying-signal tooling)

List what they run for buying signals, intent data, visitor identification, enrichment, signal-based prospecting. Then grade the stack into a tier using `benchmarks.md` Section 4 (the tier is the highest any single tool reaches). Name the friction point in one line and the recommended fix in one line. The signal tier is both a standalone grade and a contributor to the health score. A No-Tools company has a structural ceiling on response speed, because you cannot speed-to-lead a signal you never captured.

Close discovery by re-confirming the connect-and-measure state from the opening offer (record it in `discovery.json`) and move to the diagnosis.

---

## Step 5: Diagnose (the heart of the audit)

Where inputs become leaks. Three moves: benchmark every real metric, confirm the leaks, quantify each in dollars. Then score the overall health.

You can run this yourself or delegate: the **benchmark-analyst** sub-agent grades (it reads `benchmarks.md`, it does not quantify), and the **leak-quantifier** sub-agent does the dollar math. These are not registered subagent types: read `agents/benchmark-analyst.md` or `agents/leak-quantifier.md` and spawn a subagent (Agent/Task tool) with that file's contents as its prompt.

### 5.1 Benchmark every real metric

For each metric the buyer actually provided (not benchmark-filled), compute the ratio against the industry x motion benchmark from `benchmarks.md`, oriented so higher is always better, and grade it against the three bands in `benchmarks.md` Section 1 (> 1.3 healthy, 0.7 to 1.3 fine, < 0.7 leak candidate). Do not re-state the tables here; they live in `benchmarks.md` so the quarterly re-tune is a one-file edit.

### 5.2 Confirm the leaks (from the taxonomy)

Every confirmed leak (a below-0.7x metric, or a structural gap like No-Tools or no scoring) maps to a named leak in `leak-taxonomy.md`. For each candidate, apply the taxonomy's detection condition: only surface a leak when THEIR data actually meets it. Never list a leak the inputs don't support. The taxonomy carries the detection logic, the quantification formula, the health-score weight, and the module plus partner that fixes each.

### 5.3 Quantify each leak, the rules

- **Use their real numbers.** Every dollar figure chains off measured or reported metrics. If a leak needs a number they didn't provide (e.g. monthly visitors), benchmark it from `benchmarks.md` and SAY so, and label that figure benchmark provenance.
- **Show the calculation.** Every leak gets a one-line formula so the buyer can re-run it. A dollar figure with no chain is a number they can't defend internally.
- **Keep the impact ARR-shaped and honest.** "$1.4M/year", "$710K/year". Round honestly; a precise-looking fake number is worse than an honest range.
- **Conservative beats impressive.** Use the low end of any range. An audit that overstates loses the buyer the moment they spot one inflated number.
- **Never stack the same dollars twice.** If two leaks attack the same lost-deal pool, note the overlap and don't double-count it in the total.

### 5.4 The overall GTM Health Score (0-100)

Roll the per-metric grades, the leak severity, and the signal tier into one 0-100 score: above-1.3x metrics push up, below-0.7x metrics pull down weighted by centrality to the motion; subtract for each confirmed leak by severity; factor the signal tier (Gold adds headroom, No-Tools imposes a ceiling); discount for data confidence when most fields were benchmark-filled (say "roughly X, low confidence"). Band the score with the ONE canonical scheme in `benchmarks.md` Section 5 (80/60/40 boundaries). Name the band out loud and tie it to the leaks.

### Step 5 verification before moving on

Confirm with the buyer: "Here are the leaks I'm confident in, each tied to a number you gave me. Anything here measured against a benchmark-filled field instead of your real data? If so, I'll downgrade it from a confirmed leak to a 'worth checking.'" This honesty check makes the roadmap defensible.

---

## Step 6: Prioritize + cost-of-delay

Rank the confirmed leaks highest-dollar first; ties break on lower implementation complexity, then faster time-to-value. Total them without double-counting (net out any overlap flagged in 5.3). Break the total into the cadence that makes urgency concrete: annual = total; monthly = total / 12; weekly = total / 52; daily = total / 365. Frame it: "Every week you don't fix the top leak costs roughly [$weekly]." Cost-of-delay is the bridge from diagnosis to action.

---

## Step 7: Recommend (the close routes to the roadmap, never a checkout)

This is the payoff. Every confirmed leak maps to the build module that fixes it, per `leak-taxonomy.md`. The close is a roadmap, not a sale.

### 7.1 The module-gate doctrine

For each leak, name the module by plain name and directory (`modules/<system>/`):

- **Owned** (the module directory is present in the install): kickoff starts this week, seeded from `_profile/profile.json` and this diagnosis, zero re-discovery.
- **Unowned** (directory absent): give the honest gate, what the module builds and the buyer's own leak math for it, and let the shell quote the price at runtime from `module-manifest.json`. Never write a price in prose. There is no fake teaser and no buyable link here.

When three or more leaks surface, present the sequenced roadmap and let the Engineer offer the honest combined path at runtime (any bundle price and savings framing come from `module-manifest.json`, never from this playbook). The ladder is a routing device, not a pitch.

### 7.2 The prioritized-roadmap output

Deliver the report using `templates/leak-report-template.md`: the health score and band, the ranked leak table with $ impact, provenance, and cost-of-delay, and the roadmap mapping each leak to its module and partner. The report is never chat-only. In Claude Code write it to `~/.claude/artemis/gtm-engineer/diagnostics/artifacts/leak-report.md`; in Claude Chat save it as a named Project file.

**Before you write any buyer-facing artifact, run the output-integrity final pass** (`output-integrity.md`): provenance (every leak $ figure, health-score input, and projected recovery traces to a measured record, a reported input, or a labeled benchmark, never an invented number), consistency vs `discovery.json` (the report reconciles with itself, the leak rows sum to the total, the cost-of-delay divisions are right), and own-criteria (it meets what the template and `benchmarks.md` require). State the one-line integrity verdict to the buyer. If a check fails, fix it and re-run before shipping.

### 7.3 Partner platform per leak (with disclosure)

For each leak's "runs on" line, recommend the partner platform with the standard disclosure on first mention:

Always say what the tool is FOR in the context of this leak. Defer all pricing and feature questions to the partner's site; never quote a partner's price or plan tier. Never quote a specific audit count or a named dataset figure; use the hedged phrasing.

### 7.4 The closing summary (mandatory, three parts, written to disk)

Close like a white-glove engagement:

1. **What we found today.** The Health Score, the band, the ranked leaks with dollars and provenance, the cost-of-delay. The screenshot-able summary.
2. **What I'd watch.** The two or three metrics whose movement would change the diagnosis.
3. **What I'd fix first, and what to build next.** Priority 1 with its module (owned starts now, unowned gates honestly), then the running observations list prioritized by impact and effort.

Then write the engagement close to disk. In Claude Code:
- `~/.claude/artemis/gtm-engineer/diagnostics/artifacts/ENGAGEMENT-SUMMARY.md`, the three-part summary

4. **Offer the watchtower (default-on, easy out).** Follow-through is the whole point of a trendline, so this offer is part of every diagnostic close, not just the first session. Match what the buyer owns:
 - **Diagnostic-only (owns no module):** offer Watchtower-lite (`routines/watchtower-lite.md`, dwell nudges on diagnosed-but-unacted leaks only), default-on with the same say-skip framing as the first session: "I'm setting up a light follow-through check so a diagnosed leak doesn't sit untouched; say skip if you'd rather I didn't." This closes the post-diagnostic silence for a returning free buyer or a calculator graduate, neither of whom loads the first-session script.
 - **Owns a module, or unlocks one now:** offer the full Watchtower (`routines/watchtower.md`, the weekly between-session sweep), same default-on framing.
 On claude.ai there is no routines layer; say so plainly and offer the calendar-hold fallback. Schedule via `/schedule`; don't declare it live until it has fired once unattended.

End with one question: "Want me to walk deeper into any single leak before we close: the math, the fix, or the module that handles it?" Then wait.

---

## The quarterly re-audit (Claude Code only)

The audit is most valuable as a trendline, not a snapshot. After the first run, offer to schedule `routines/quarterly-reaudit.md` via **`/schedule`** (Claude Code cloud routines, which keep firing after the session ends); if `/schedule` isn't available, fall back to the OS scheduler (launchd or cron) invoking `claude -p` with the routine prompt. Check the routine's `## Preconditions` block before scheduling, and don't declare it live until it has fired once unattended. Each run re-collects inputs, recomputes the Health Score, appends a row to `ledgers/health-score-ledger.md`, and diffs against last quarter.

---

## Common gotchas, name these before they happen

1. **Inventing a leak for the wrong motion.** The #1 failure mode. Enforce the Step 1 motion guardrail religiously.
2. **Leaking against a benchmark-filled field.** Circular. Track real vs benchmark-filled; only diagnose real ones.
3. **Double-counting overlapping leaks.** Net out overlaps before totaling in Step 6.
4. **A precise-looking fake number.** Round, use conservative ends, always show the chain. Same rule on substantiation counts: never quote a specific audit count or a named dataset figure in buyer output; use "across a large body of B2B SaaS GTM audits."
5. **Units errors in the inputs.** A metric 5x or 0.1x its benchmark is often a typo; re-confirm before it becomes a leak.
6. **Selling before diagnosing.** No module routing during Steps 1 to 4; the recommendation only lands after the leak is confirmed and quantified.

---

## What "done" looks like (checklist)

- [ ] Connect-and-measure offered before intake; connection state recorded in `discovery.json`; every number's provenance labeled measured / reported / benchmark
- [ ] Company profile captured; industry x motion benchmark row selected from `benchmarks.md`; profile facts written to `_profile/profile.json`
- [ ] Core revenue metrics gathered; benchmark-filled fields marked and excluded from leak detection
- [ ] Motion-adaptive team/growth metrics gathered (right block for PLG vs SLG vs Hybrid)
- [ ] Signal stack graded Gold / Silver / Bronze / No-Tools
- [ ] Every real metric benchmarked against `benchmarks.md` (>1.3x healthy, 0.7-1.3x fine, <0.7x leak)
- [ ] Each confirmed leak matched to `leak-taxonomy.md` and quantified in $/yr with the calculation shown, no double-counting
- [ ] Overall GTM Health Score (0-100) computed and banded per `benchmarks.md`
- [ ] Leaks ranked by annual impact; cost-of-delay broken into annual/monthly/weekly/daily
- [ ] Leak report written to `diagnostics/artifacts/` (Claude Code) or a Project file (Chat), never chat-only
- [ ] Closing summary written as `ENGAGEMENT-SUMMARY.md`; health-score-ledger baseline row written to `ledgers/`; re-audit date named
- [ ] Watchtower offered at close, default-on with say-skip: Watchtower-lite for diagnostic-only buyers, full Watchtower if they own or unlock a module
- [ ] Quarterly re-audit routine offered via `/schedule`, with the one-unattended-fire check agreed

No invented metrics. No wrong-motion leaks. No prices in prose. Every dollar chains back to a number the buyer gave us or a labeled benchmark.

---

## Sub-agents available

- `agents/benchmark-analyst.md`, reads `benchmarks.md` and grades each metric against the industry x motion table, returning the above/at/below table plus the signal-tier grade. Invoke in Step 5.1. It grades; it does not quantify.
- `agents/leak-quantifier.md`, takes the below-average metrics and the buyer's real numbers and produces the $/yr figure with the calculation chain for each. Invoke in Step 5.3. It quantifies; it does not invent.

These are not registered subagent types. To invoke one: read the `agents/<name>.md` file and spawn a subagent (Agent/Task tool) with that file's contents as its prompt, handing it the buyer's data.

---

## The one-number path (calculators)

When the buyer wants one focused number instead of the full picture, the six calculators under `calculators/<slug>/` are the "just give me this one number" path (lead-response ROI, recoverable-revenue ROI, visitor-deanonymization ROI, pipeline velocity, quota-coverage gap, tech-stack grade). Each runs the same math as its live-site mirror and maps its result to the build module that fixes it. Treat the calculators as the single-metric path and this audit as the find-everything path; both write to the same profile and diagnostic state, so a buyer can start with one number and graduate to the full audit without re-explaining anything.
