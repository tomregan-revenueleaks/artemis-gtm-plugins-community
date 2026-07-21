# The Maturity Model

A ten-minute read. The engine map showed you the machine. The build order showed you the sequence. This file answers the question both of those raise: how do I know if a given number is actually a leak, or just average for a company my size?

That distinction is the whole point of a maturity model. A metric isn't good or bad in a vacuum. It's good or bad relative to what a company at your stage, in your industry, running your motion, should expect. This file gives you that yardstick, so you stop benchmarking yourself against companies you're nothing like.

**A note on the numbers below.** Every benchmark here is directional, drawn from patterns across a large body of B2B SaaS GTM audits, and re-tuned every quarter. Treat them as calibration, not as a scoreboard. Your own closed data always beats a table. When Artemis runs the free diagnostic, it uses the current benchmark for your exact industry and motion; the figures here are the general shape.

---

## Maturity is about which systems should exist yet

The most common mistake is building systems your stage doesn't need, or skipping ones it requires. A twelve-person startup does not need a RevOps automation layer; it needs a repeatable way to find its first hundred good-fit customers. A company past the twenty-million ARR mark that's still running qualification by gut feel is leaving a fortune on the floor.

Maturity, in engine terms, is two things: **which subsystems exist**, and **how well each one performs against its benchmark.** A young engine with three well-built systems is healthier than an older one with ten half-built ones. The model below tells you which systems your stage should have, and the benchmark tables tell you what "well-built" looks like.

---

## The four stages (by ARR)

ARR bands are a proxy for how much motion your engine has to carry. The transitions matter more than the exact numbers, so read the description, not just the band.

### Stage 1: Pre-engine (under 1M ARR)

You're finding a repeatable motion, not industrializing one. The goal is a first, honest ICP pulled from your earliest real wins, and one channel that reliably produces conversations. Do not over-build. A scoring model, a nurture engine, and an automation layer are premature when you don't yet know who buys and why. If this audit finds "leaks" here, the truth is usually a demand problem, not an engine problem. Build the aim (ICP) and prove one channel.

**Systems that should exist:** a working ICP hypothesis, one demand or capture channel, a place to respond to inbound fast.

### Stage 2: Early engine (1 to 5M ARR)

You have a motion that works; now you're making it repeatable. This is where the foundation gets formalized: ICP moves from a hunch to something anchored in closed-won data, your one channel gets a real capture layer (you stop letting known-fit traffic leave anonymous), and inbound gets a response process instead of a scramble. First scoring can appear here, kept simple.

**Systems that should exist:** formalized ICP, one strong demand or outbound channel, visitor capture, a real response process, the beginnings of scoring.

### Stage 3: Scaling engine (5 to 20M ARR)

The motion works and you're pouring volume through it. Now the joints matter. Lead scoring becomes non-negotiable because reps can no longer work every lead by hand. The qualification handoff needs to be a defined workflow, not an argument between marketing and sales. Content and outbound should both be running as systems, and nurture has to exist because your database is now large enough that ignoring dormant accounts is a visible leak.

**Systems that should exist:** scoring driving prioritization, a formal MQL-to-SQL handoff, content and outbound both systematized, nurture for the growing database.

### Stage 4: Industrializing (20M ARR and up)

The systems all exist; the job now is reliability and toil reduction. This is where the operations layer earns its place: AI RevOps to keep data clean and automate the manual work the systems generate, a rationalized tech stack instead of accumulated tool sprawl, and drift control so systems built two years ago still perform. At this stage your biggest risk isn't a missing system, it's a well-built system quietly decaying because nobody re-audits it.

**Systems that should exist:** all six systems running, an automation and hygiene layer, a rationalized stack, a quarterly re-audit habit.

---

## The motion overlay: PLG, SLG, and Hybrid

Stage tells you how many systems. Motion tells you which metrics those systems are graded on. Benchmarking a PLG company on sales-quota attainment, or an SLG company on trial-to-paid, is how a maturity read goes wrong.

- **PLG (product-led):** self-serve signups, free trials, freemium. The funnel is the product. Grade on trial-to-paid conversion, activation rate, time-to-value, and expansion. There is no SDR-quota benchmark to hit, because there are no SDRs to benchmark.
- **SLG (sales-led):** outbound, demos, enterprise deals. The funnel is the sales team. Grade on sales cycle, pipeline coverage, win rate, lead-to-opp, meetings per SDR, and demo-to-proposal.
- **Hybrid:** self-serve bottom, sales-assisted top. Grade on whatever you actually run across both worlds. Watch for drift: a "PLG" company where most revenue is now sales-assisted has quietly become Hybrid and should be measured like one.

---

## What good looks like (the benchmark canon)

These are the directional targets, oriented so that clearing the bar is healthy and falling well short of it is a leak candidate. The general rule the diagnostic uses: a metric running above roughly 1.3 times its benchmark is a strength worth naming, inside roughly 0.7 to 1.3 times is simply average (don't pathologize it), and below 0.7 times is a leak worth quantifying.

**Core metrics (all motions), directional SaaS shape:**

| Metric | Healthy neighborhood |
|---|---|
| Win rate (opportunity to close) | around 25 percent |
| Sales cycle | around 45 days |
| Lead-to-opportunity rate | around 15 percent |
| Pipeline coverage | around 3.5 times quota |

Industry shifts these. A Healthcare or Cybersecurity motion runs longer cycles and lower win rates by nature; an E-commerce or HR Tech motion runs faster and higher. The diagnostic swaps in your row; the shape above is the SaaS default.

**PLG motion, directional:**

| Metric | Healthy neighborhood |
|---|---|
| Trial-to-paid conversion | around 7 percent |
| Product activation rate | around 40 percent |
| Expansion revenue share | around 30 percent |

**SLG motion, directional:**

| Metric | Healthy neighborhood |
|---|---|
| Meetings booked per SDR per month | around 12 |
| Demo-to-proposal rate | around 50 percent |

**The qualification joint (Hybrid and SLG):** MQL-to-SQL conversion clusters around a 13 percent median across a large body of B2B SaaS GTM audits. Well below that, with leads marked qualified then sitting, is the signature of a broken handoff, not a demand problem.

Two honesty rules the diagnostic holds to, and you should read your own numbers the same way. First, if you borrowed a benchmark to fill a number you didn't have, you can't then call that number a leak; you'd be grading a benchmark against itself. Second, a number that looks five or ten times off its benchmark is almost always a units error (annual figures in a monthly field), not a miracle or a catastrophe. Re-check before you celebrate or panic.

---

## The signal-stack maturity ladder

Your ability to act fast is capped by your ability to see buying signals in the first place. You can't respond in five minutes to a signal you never captured. Signal maturity runs on its own ladder:

- **No-Tools:** flying blind on buying signals. A structural ceiling on how fast the engine can ever respond.
- **Bronze:** intent feeds with a thin activation layer. You see signals late.
- **Silver:** good data, some intent, weak real-time activation. Signals arrive, but not in time to act.
- **Gold:** real-time fit and intent you can act on the moment it fires. This is the ceiling worth reaching for.

The tier is set by the strongest tool in your stack, not the average. One real-time layer over solid data moves you up a tier. Maturing this ladder is often the highest-leverage move a Stage 2 or 3 company can make, because it lifts the ceiling on every action system below it.

---

## The health score as a maturity readout

When the diagnostic rolls your benchmarked metrics, your leak severity, and your signal tier into one number, it lands in one of four bands. Read them as maturity, not as a grade:

| Score | Band | What it means |
|---|---|---|
| 80 to 100 | Healthy | Strong motion, minor tuning left |
| 60 to 79 | Solid, leaking | A working engine with one or two real leaks |
| 40 to 59 | At risk | Multiple leaks, material revenue loss |
| 0 to 39 | Critical | Structural gaps; the motion itself is the problem |

A Stage 2 company at 62 is in good shape for its stage. A Stage 4 company at 62 has systems decaying. Same score, different verdict, because maturity is always read against where you should be, not against an absolute.

---

## How to use this

Don't treat the maturity model as an aspiration to sprint toward. Treat it as calibration. It tells you which systems your stage actually requires, which metrics your motion is graded on, and roughly where the bar sits. That's enough to stop you from over-building for a stage you haven't reached, and to stop you from mistaking an average number for an emergency.

To turn this general model into a read on your specific engine, run the free diagnostic. It applies the current benchmark for your exact industry and motion, scores each subsystem against your stage, and tells you which numbers are genuine leaks and which are simply average. That's the only next step this file points to, and it's free.

You now have the full lens: the machine, the order it's built in, and the yardstick for reading it. That's the map. The diagnostic is where you read your own.
