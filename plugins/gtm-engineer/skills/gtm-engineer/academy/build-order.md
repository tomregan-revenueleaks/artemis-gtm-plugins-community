# The Build Order

A ten-minute read. Read the engine map first. This file answers the next question: once you know your engine leaks in three places, which one do you fix first?

The intuitive answer is "the one costing the most money." That's usually wrong. The right answer is a dependency order, the same way you'd sequence any engineering build: foundations before the things that stand on them. A faster machine pointed at the wrong target just loses money faster.

---

## The one principle: you can't automate a decision you haven't defined

Every rule in this file comes from a single idea. Each subsystem in your engine consumes the output of the subsystems above it. So you have to build in the order the machine actually depends on, not the order your biggest leak suggests.

Three plain examples of the principle:

- You can't **score leads** for fit until you've **defined the fit** (that's your ICP). Scoring without an ICP is scoring against a guess.
- You can't run a **qualification handoff** until you have a **score** that says which leads are ready. Otherwise the handoff queue is just date-ordered, which is no prioritization at all.
- You can't **respond in five minutes** to your best leads until you can **tell which leads are best**, and until you're **capturing** them in the first place.

Build a downstream system on a missing upstream one and you don't get a broken feature, you get a system that runs, produces confident-looking output, and is quietly wrong. Those are the most expensive builds, because you trust them.

---

## The dependency map

Here's the engine's build order as a layered diagram. Lower layers depend on higher ones. You build top-down.

```
LAYER 0 THE AIM + THE GROUND
 ICP definition ......... the target every system points at
 Operations hygiene ..... clean data + trustworthy reporting
 Tech-stack sanity ...... know what tools you have and why
 | (everything below reads from these)
 v
LAYER 1 GET FOUND, GET KNOWN
 Content ................ earn demand and organic pipeline
 Visitor identification . turn anonymous traffic into known accounts
 Conversion surfaces .... pricing, demo, case-study pages that convert
 | (these produce the raw contacts + intent signals)
 v
LAYER 2 DECIDE WHO MATTERS
 Lead scoring ........... rank contacts by fit + intent
 | (this reads ICP from layer 0 and signals from layer 1)
 v
LAYER 3 ACT, IN THE RIGHT ORDER
 Speed-to-lead .......... respond to the top band before it cools
 Outbound ............... reach good-fit strangers with signal triggers
 Qualification .......... run the scored handoff to sales
 Nurture ................ re-engage the ones not ready yet
 |
 v
LAYER 4 AUTOMATE THE WHOLE THING
 AI RevOps .............. take the manual toil out of everything above
```

Read it as a rule of standing: nothing in a lower layer works reliably until the layers above it exist. That's the DAG. Now here's why each edge is there.

---

## Why the layers stack this way

**Layer 0 is the aim and the ground, and it comes first because everything reads from it.**

Your ICP is the single most upstream decision in the whole engine. Content targets ICP problems. Outbound signals fire on ICP-fit companies. Scoring anchors its fit model to the ICP. Get the ICP from closed-won reality (not a marketing deck), and every downstream system inherits a good target. Skip it, and they all inherit a bad one. This is why a firmographic-only or year-old ICP is a hard-stop prerequisite, not a nice-to-have.

The ground is your operations and your stack. If your CRM is full of duplicates and competing fields, every number the engine produces is suspect, and you can't tell a real leak from a data artifact. If you're running two tools in the same category, you're paying twice and splitting your data. You don't have to make layer 0 perfect before you move, but you have to make it trustworthy, because the whole build sits on it.

**Layer 1 gets strangers found and known, because layer 2 needs contacts to rank.**

Content earns the top of the funnel. Visitor identification catches the demand that would otherwise leave anonymous. Conversion surfaces turn interest into a known, high-intent contact. These three produce the raw material, contacts and intent signals, that everything below them consumes. There's no point ranking leads you haven't captured.

**Layer 2 decides who matters, and it reads from both layers above it.**

Lead scoring is the joint that holds the machine together. It reads your ICP from layer 0 (what does good fit mean) and your signals from layer 1 (who's showing intent), and it produces a ranking. This is why scoring sits above the action layer: the actions in layer 3 all need to know which leads are worth the fast, expensive, human touch. Build the action layer first and every lead looks equally urgent, which means none of them really are.

**Layer 3 acts, in priority order, because now you finally know the priority.**

Speed-to-lead fast-tracks the top band before it cools. Outbound reaches good-fit strangers with the signals layer 1 surfaced. Qualification runs the scored handoff to sales. Nurture re-engages the ones who aren't ready yet. All four depend on the score from layer 2. Speed-to-lead in particular has two upstream parents: it needs visitor identification (layer 1) to catch the lead and scoring (layer 2) to know it's worth the five-minute response.

**Layer 4 automates last, because you can't automate a process you haven't built.**

AI RevOps takes the manual toil out of everything above it: the handoff prep, the deliverability monitoring, the CRM hygiene, the reporting. It comes last for the same reason you don't write automation for a workflow that doesn't exist yet. Automating a broken process just gives you a faster broken process. Rationalize the stack, build the systems, then automate the toil they generate.

---

## Four rules of sequencing

The DAG gives you the shape. These four rules resolve the judgment calls.

**1. Prerequisites first, even when the downstream leak is bigger.** If your biggest dollar leak is slow lead response but you have no scoring and no ICP, you do not start with response. You build the two layers it stands on first, because a five-minute response to an unscored lead from a bad-fit account is fast delivery of the wrong thing. Build the highest-leverage node whose prerequisites are already met.

**2. Fire the long-pole blockers early.** Some steps have long lead times that have nothing to do with effort: email domain warmup takes weeks before outbound can run at volume, IT has to issue access tokens before you can touch the CRM, customer interviews have to be scheduled before you can build a real ICP. Start those clocks on day one, in parallel, even for systems you'll build later. The build waits on the long pole, so get the long pole moving first.

**3. One active build at a time.** A subsystem half-built is worse than not started, because it produces output you might trust. Finish and verify one before you open the next. The only exception is when the first build is genuinely blocked and waiting (warmup running, IT ticket open), in which case you can start a second, but only deliberately, never by drift.

**4. Don't automate a broken manual process.** If a step is broken when a human does it, automating it scales the breakage. Get the process working by hand first, prove it, then hand it to automation. This is the whole reason AI RevOps sits at the bottom of the stack.

---

## The override: your diagnostic decides the entry point

The build order is a dependency graph, not a fixed checklist you march through from top to bottom every time. Most teams already have parts of layer 0 and layer 1 in place. The real question is: given what you've already built and where you're actually leaking, what's the highest-leverage node whose prerequisites are already met?

That's exactly what the free diagnostic computes. It reads your real engine, finds which subsystems are missing or leaking, checks which prerequisites you already satisfy, and hands back a build order that starts at your biggest fixable leak, not at the top of a generic list. If your ICP is already sharp and your scoring is solid, it won't send you back to rebuild them. It'll point you at the first real gap you can close today.

Run the diagnostic to get your own sequenced build order. It's free, and it's the only next step this file points to. You know the shape of the machine and the order it has to be built in. Next, learn how to tell a real leak from a number that's simply average: that's the maturity model.
