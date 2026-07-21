# The Engine Map

A ten-minute read. This is the mental model everything else in the Academy hangs off: your go-to-market motion is not a pile of tactics, it's one machine. Once you can see the machine, you can see where it leaks.

Read this before the build-order and maturity-model files. Those two tell you what to build and what good looks like. This one tells you what the thing you're building actually is.

---

## GTM is a machine, not a to-do list

Most teams run go-to-market as a list of disconnected activities. Someone owns ads. Someone owns the website. Someone owns outbound. Someone owns the CRM. Each does their part, and revenue is whatever falls out the bottom.

An engineer looks at the same picture and sees one machine with connected parts. Raw input goes in one end (strangers who have never heard of you). Revenue comes out the other. In between, a stranger has to be found, captured, engaged, qualified, and closed, and every one of those steps is a subsystem that can be well-built, half-built, or missing.

When revenue is soft, the instinct is to push harder on whatever activity you can see. More ads. More SDRs. More content. That's like flooring the gas on a car with a clogged fuel line. The engine map exists so you stop guessing which part is clogged and start reading the machine.

Here's the core idea an engineer never forgets: **the output of the whole engine is capped by its weakest subsystem, not its strongest.** A brilliant outbound motion feeding a broken qualification handoff still loses the deals. You don't fix an engine by upgrading the part that already works. You find the constriction and you clear it.

---

## The engine in one picture

Read the machine top to bottom. A stranger enters, and each layer moves them one step closer to revenue.

```
 THE AIM (who the whole machine points at)
 ICP: your definition of a good-fit buyer
 |
 ------------------------------------------------------------
 THE FLOW (a stranger becomes a sales-ready opportunity)

 DEMAND → CAPTURE → ENGAGE → QUALIFY
 earn attention turn traffic reach out and decide who
 and pipeline into known, respond before gets sales
 high-intent the moment attention,
 contacts goes cold hand them off
 ------------------------------------------------------------
 THE SUBSTRATE (what everything runs on)
 Operations: clean data, connected tools,
 trustworthy reporting, low toil
```

Three layers. **The aim** points the machine. **The flow** carries a stranger through four stages. **The substrate** is the ground the whole thing is bolted to. Get the aim wrong and every downstream stage inherits the error. Let the substrate rot and you can't trust a single number the flow produces.

---

## The six systems you'll hear named

Inside this map sits the canonical model this whole methodology is built on: **the six systems.** They are Content, Outbound, Nurture, Conversion, Qualification, and AI RevOps. When Artemis talks about "your systems," those six are the backbone.

The engine map is wider than those six on purpose, because two things sit around them: your **ICP** is the foundation upstream of all six, and four **tactical reinforcements** (visitor identification, speed-to-lead response, lead scoring, and the tech-stack audit) wire into the systems to sharpen them. Think of the six systems as the working machinery, ICP as the aim it points at, and the tactical pieces as the parts that make specific joints in the machine hold up under load.

Here's how the whole cast maps onto the flow.

| Layer | Stage | The system that owns it | The tactical reinforcement |
|---|---|---|---|
| Aim | Targeting | ICP definition | (none: this is the foundation) |
| Flow | Demand | Content | (none) |
| Flow | Capture | Conversion | Visitor identification |
| Flow | Engage | Outbound, Nurture | Speed-to-lead response |
| Flow | Qualify | Qualification | Lead scoring |
| Substrate | Operations | AI RevOps | Tech-stack audit |

Notice that Outbound and Nurture both live in Engage: outbound reaches strangers who haven't raised a hand, nurture re-engages people who went quiet. Same stage, two directions. And notice that AI RevOps sits in the substrate, because it's the layer that keeps the data clean and the toil low so every other system can be trusted.

---

## The leak framework: eleven ways an engine bleeds

A leak is a subsystem that's missing or underperforming, measured against what good looks like for your industry and motion (that's the maturity model). Each leak lives at a specific point on the engine map. When you can name the stage, you can name the leak.

| Stage | Leak | What it looks like |
|---|---|---|
| Aim | Wrong-customer targeting | ICP is a guess or firmographic-only; win rate low and cycle long together |
| Demand | Content and organic gap | You pay for every lead; no editorial engine earning pipeline |
| Capture | Anonymous visitors | Most of your traffic leaves unidentified; no visitor ID running |
| Capture | Conversion-surface gap | Hidden pricing, weak demo pages, no case studies with numbers |
| Engage | Slow lead response | Time-to-first-touch measured in hours or days, not minutes |
| Engage | Outbound and sequencing gap | Static-list spray, poor deliverability, no signal triggers |
| Engage | Nurture and re-engagement gap | Newsletter-only or nothing; dormant accounts nobody works |
| Qualify | No lead scoring | Every lead treated the same; reps work the queue first-in-first-out |
| Qualify | MQL-to-SQL handoff gap | Leads marked qualified then sit; marketing and sales point fingers |
| Substrate | RevOps and data-quality gap | Duplicate records, competing fields, dashboards nobody trusts |
| Substrate | Tech-stack sprawl | Two tools in one category, spend high relative to what it returns |

Eleven leaks, one map. This is the whole diagnostic surface. When Artemis runs the free diagnostic on your real numbers, it's checking each of these points against the benchmark for your industry and motion, and only flagging the ones your data actually supports.

One rule that keeps the map honest: **a leak only counts when your metrics prove it.** A PLG company that runs no sales team doesn't have a "meetings-per-SDR" leak, because it runs no SDRs. The map tells you where leaks can live; your data tells you which ones are real.

---

## How leaks compound (why the biggest number isn't always the first fix)

Leaks don't sit in isolation. Some make others worse, and the map shows you which.

**Upstream leaks poison everything below them.** A wrong ICP doesn't just cost you the deals you lose to bad-fit buyers. It quietly degrades every system downstream: your content targets the wrong problems, your outbound signals fire on the wrong companies, your scoring model learns from noise. That's why the aim sits at the top of the map. Fix a downstream leak while the aim is still wrong and you've built a faster machine pointed at the wrong target.

**Force-multiplier leaks amplify the rest.** No lead scoring is a modest leak on its own. But it makes slow response worse (you can't fast-track your best leads if you can't tell which they are) and it makes qualification worse (the handoff queue is just date-ordered). Some parts of the engine are joints; when they're weak, the load they were supposed to carry lands on parts that weren't built for it.

**Substrate leaks tax every number you have.** When your CRM is full of duplicates and competing fields, every metric the flow produces is suspect. Your reported win rate, your pipeline coverage, your lead volume: all of it inherits the noise. A substrate leak rarely tops the dollar chart, but it discounts the reliability of the whole diagnosis.

One caution the diagnostic enforces, and you should too: **don't count the same lost dollars twice.** Slow response and no scoring both attack the same pool of inbound leads. If you add up "the full cost of slow response" and "the full cost of no scoring," you've double-counted the overlap and inflated the total. Real leaks overlap; honest math nets them out.

---

## Reading your own engine

The engine map is the lens. It doesn't tell you which of your subsystems is the constriction, because that depends entirely on your numbers, your industry, and your motion. That's what the diagnostic is for.

Two files complete the picture:

- **build-order.md** shows the dependency order: which subsystems have to exist before others can work, so you never automate a decision you haven't defined.
- **maturity-model.md** shows what good looks like at your stage, so you can tell a real leak from a metric that's simply average.

When you're ready to stop reading the general map and start reading your own engine, the free GTM diagnostic takes your real metrics, benchmarks each subsystem against your industry and motion, and hands back a ranked list of which leaks are actually costing you and which to close first. That's the only next step this file points to, and it costs nothing.

You now know what the machine is. Next, learn the order it has to be built in.
