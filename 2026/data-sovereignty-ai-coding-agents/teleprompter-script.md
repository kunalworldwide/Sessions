# Teleprompter Script
## Who Owns Your Code? Data Sovereignty in the Age of AI Coding Agents
### Cloud-Native & AI Unlocked: Bengaluru Edition — Aug 22, 2026
### Speaker: Kunal Das, Developer Advocate at Kimchi

**Estimated run time:** 22–25 minutes (30-min slot with Q&A)
**Cues in [brackets] — do not read them aloud**

---

## SLIDE 1 — Title

Good afternoon, everyone. Thank you for being here.

I'm Kunal Das, Developer Advocate at Kimchi, and today I want to talk about something that I think every single person in this room is already dealing with — whether you realize it or not.

The title of this talk is: "Who Owns Your Code? Data Sovereignty in the Age of AI Coding Agents."

And the reason I'm giving this talk is simple. We all use AI coding tools now. Copilot, Claude Code, Cursor, Kimchi — pick your weapon. But every single time one of those tools reads your repository, your code is traveling through infrastructure you don't control. And very few people are asking where it goes, who holds it, and what happens to it.

That's what we're going to unpack today.

[Next slide]

---

## SLIDE 2 — Your code is already out there

Let me paint a picture for you.

Your codebase lives on your machine, or in your private GitHub repo. It's proprietary IP. Your business logic, your schemas, your configs, your internal APIs. Maybe even secrets in comments that shouldn't be there but are.

Now, you fire up an AI coding agent. That agent reads your code — it has to, that's how it helps you. But where does that code go? It travels to the AI vendor's inference endpoint. An unknown region, with an unknown retention policy. And then, somewhere down the line, it might end up as training data. You hope not. But you don't actually know.

Look — the question here is NOT "should we use AI coding agents?" That ship has sailed. We're all using them. The question is: "who owns the data that flows through them?"

That's the question I want you to leave with today.

[Next slide]

---

## SLIDE 3 — Three surfaces where your code leaks

Let me break this down. There are three surfaces where your code is exposed when you use an AI coding tool.

**Surface one: Source code in transit.** Every file the agent reads — schemas, configs, secrets in comments, internal APIs — travels to the model provider's inference endpoint. And that endpoint is often in a different country. If you're in India using a US-based AI tool, your code just crossed a border. That has legal implications.

**Surface two: Context as training fuel.** Your codebase, your architecture patterns, your business logic — all of that becomes context for the model. Whether that context is retained, logged, or used for training depends entirely on the vendor's terms of service. And here's the thing — those terms change. They get updated retroactively. What was fine yesterday might not be fine tomorrow.

**Surface three: Generated code provenance.** When the agent writes code for you, who owns the output? Is it derived from your proprietary patterns? Is it license-clean? If an auditor shows up, can you prove where the code came from? This is already becoming a legal battleground.

And the regulatory reality is: GDPR, India's DPDP Act 2023, CCPA, and the EU AI Act all impose data residency and processing obligations. "The vendor said it was fine" is not a compliance strategy.

[Next slide]

---

## SLIDE 4 — What you don't know can hurt you

So let's get specific. Here's what most AI coding tools don't tell you.

On the left side of this table, you see the typical AI coding tool. **Data residency?** Unknown region. The vendor chooses where inference happens. **Retention policy?** Usually a 30-day default, with an opt-out buried somewhere in settings that nobody reads. **Training on your data?** "May use" clauses — and I've seen terms updated retroactively to broaden what they can do with your data. **Audit trail?** "Trust us." No per-request provenance.

On the right side — what you actually need. **Data residency:** your VPC, your region. Data never leaves your perimeter. **Retention:** zero. No prompts stored, no outputs cached, no logs. **Training:** contractually prohibited, AND enforced by architecture — not just words in a privacy policy. **Audit trail:** phase-tagged. Every LLM call logged with its purpose.

If your AI coding tool looks like the left column, you have a sovereignty gap. And you probably don't even know how big it is.

[Next slide]

---

## SLIDE 5 — This is not theoretical

And I know what some of you might be thinking: "This is a hypothetical problem." It's not.

Ninety-two percent of enterprises using AI coding tools have no formal data governance policy for what code the agent can access. Ninety-two percent. That's not a gap — that's a canyon.

There is currently $1.2 billion in IP litigation pending over AI-generated code provenance. Who owns the output when the model was trained on your proprietary patterns? The courts are going to be figuring this out for years.

And in the real world, Samsung, Amazon, Apple, and major banks have already banned or restricted public AI coding tools after code leaks. The pattern is always the same: an engineer pastes proprietary code into a chat, the vendor logs it, and it surfaces in training data months later. Every time, the company says: "We didn't know."

[Next slide]

---

## SLIDE 6 — Enter Kimchi: Sovereign by Design

So, what's the alternative? This is where I introduce Kimchi — but I want to be clear about something. The principles I'm about to talk about apply to evaluating ANY AI coding tool. This isn't just a product pitch. It's a framework.

Kimchi was built by Cast AI. It's an open-source coding agent plus inference cloud plus spend console, all in one. And it was built with data sovereignty as a design constraint from day one — not a compliance retrofit bolted on after a breach.

That means: your code never leaves your control. No third-party cloud inference. No training on your data. No cross-border transfers.

On the right side of this slide, you can see the terminal. When you run `kimchi --self-host`, inference happens on localhost — inside your VPC. Data residency is your region. Retention is zero. Training on your data? Never.

The key difference is: sovereignty is not a feature you add later. It's an architecture you build from the start.

[Next slide]

---

## SLIDE 7 — Four Pillars of Sovereignty

I mentioned a framework. Here it is. Four pillars of code sovereignty. You can use this to evaluate any AI coding tool — not just Kimchi.

**Pillar one: Data residency.** Your code and prompts are processed in a region you control. No cross-border inference. This makes you DPDP Act and GDPR compliant by architecture, not by policy.

**Pillar two: Zero retention.** No prompt logging, no output caching, no context storage. Data is processed in-memory and discarded. Nothing persists after the session ends. The data is gone. It has to be — because you can't leak what you don't store.

**Pillar three: No training on your data.** This has to be both contractual AND architectural. A privacy policy that says "we don't train on your data" is words on a page. A self-hosted model that physically cannot phone home — that's architecture. Your codebase never becomes someone else's training set.

**Pillar four: Full audit trail.** Every LLM request is phase-tagged: explore, plan, build, review, research. You can see exactly what was sent, to which model, and why. Per request. Per user. Per team.

If your AI coding tool can't answer all four of these — with architectural proof, not just a privacy policy — you have a sovereignty gap.

[Next slide]

---

## SLIDE 8 — Self-host in your VPC

Now let's get into how this actually works. And this is the architecture slide.

When you self-host Kimchi, everything runs inside your VPC. The developer connects to the Kimchi agent — the CLI harness — which talks to the orchestrator, which routes tasks locally to the model inference layer running on Nvidia B300 GPUs. Your codebase, the spend console, the audit log — all inside the perimeter.

Nothing crosses the boundary. No data exits to a vendor cloud. No training data collection. No cross-border transfers.

And if you look at the bottom of the diagram — those red check marks are not features. They're constraints. The system is designed so that data cannot leave, even if someone wanted it to.

[Next slide]

---

## SLIDE 9 — Multi-model routing without data leakage

Now, one of the things that makes Kimchi powerful is multi-model routing. The orchestrator takes each task and routes it to the best-fit model. The planner uses Kimi K2.7. The builder uses MiniMax M3. The judge uses Kimi K2.7. The explorer uses Nemotron-3. Seven specialized roles, each matched to the right model.

Here's the key difference. With proprietary tools, every model call sends your code to a different vendor endpoint. Each routing step is a data egress event. With self-hosted Kimchi, all inference happens inside your VPC — even multi-model routing. The data stays inside the perimeter the entire time.

And this is not just about sovereignty — it's also cheaper. Forty to sixty percent cheaper than a single proprietary endpoint. Because you're routing to the cheapest model for each task. You get sovereignty AND cost savings at the same time.

Now, if you prefer managed inference instead of self-hosting, Kimchi supports BYOK — bring your own keys. Kimchi routes to YOUR provider accounts, never its own. So even in managed mode, the billing relationship is between you and the model provider. Kimchi is just the router.

[Next slide]

---

## SLIDE 10 — Spend governance is data governance

Now I want to talk about something that might not be obvious. Spend governance IS data governance.

Think about it. If you can't see what the agent is sending, you can't govern what's leaving your perimeter. The spend console gives you both — cost visibility AND data provenance in one view.

You can set hard ceilings at four levels: per org — a hard ceiling on both data egress and spend. Per team — controlled access to proprietary repositories. Per user or API key — individual provenance for every single request.

And every LLM request is phase-tagged: explore, plan, build, review, research. So you know exactly what data was sent, for what purpose. Was it an exploration request? A build request? A review? You can audit it. You can prove it.

This is the spend console on the right. This is what Kimchi gives you out of the box. One view for cost, one view for data. Because they're the same problem.

[Next slide]

---

## SLIDE 11 — Compliance

Now, compliance. This is where most AI coding tools fall apart. Because compliance isn't something you can bolt on. It has to be there from the beginning.

Kimchi is ISO 27001 certified. SOC 2 Type II certified. GDPR compliant. And here's the one that matters for everyone in this room — DPDP Act compliant. India's Digital Personal Data Protection Act.

And for full sovereignty, you can deploy in your own VPC. Self-hosted, on dedicated Nvidia B300 GPUs, with contractual zero-retention guarantees.

Your code never leaves your control. Period. Whether you run it standalone in your VPC or on Cast AI's inference infrastructure — the guarantees are the same.

[Next slide]

---

## SLIDE 12 — Proof

Now, I don't want you to just take my word for it. Let me give you some real numbers.

Over 2,100 enterprises run Kimchi with full data sovereignty — including regulated industries like finance and healthcare that literally cannot send code to third-party clouds. This isn't a beta feature. This is production.

And here's the number I'm most proud of: zero. Zero data retention incidents. Zero prompts stored. Zero outputs cached. Zero training data collected. By architecture, not just by policy.

And this quote from Dekel Shavit at Akamai: "We needed an AI coding tool that our compliance team would actually approve. Kimchi's self-hosted deployment meant our code never left our VPC. The audit trail gave us per-request provenance that no other tool could match. It passed security review in two weeks, not six months."

Two weeks, not six months. That's what architectural sovereignty gets you. Not just compliance — speed.

[Next slide]

---

## SLIDE 13 — Your AI coding sovereignty checklist

So, I want to leave you with something actionable. A checklist. Five questions you should ask before letting any AI coding agent touch your codebase. Not just Kimchi — any tool.

One: Where does inference happen? Can the vendor prove your code stays in your region, or does it route to an unknown cloud?

Two: What is the retention policy? Are prompts and outputs stored? For how long? Can you enforce zero-retention at the architecture level — not just in the settings?

Three: Can your data be used for training? Is it contractually prohibited AND architecturally impossible? "We don't train on your data" in a privacy policy is not enough. Can they prove it?

Four: Can you self-host? If your compliance team says full sovereignty is required, can you run the entire stack — agent, models, inference — inside your VPC?

Five: Is there a per-request audit trail? Can you see exactly what was sent, to which model, for what purpose, by whom?

If any of those answers are "I don't know" or "trust us" — you have work to do.

[Next slide]

---

## SLIDE 14 — Sovereignty at every tier

Now, I know what you might be thinking: "This sounds great for enterprise, but what about me?" Sovereignty isn't just for big companies.

Community tier — free. Multi-model routing, ferment, everything you need to try it out.

Coder tier — $20 a month. Priority speed, inference API, teleport workspace.

Teams — $35 per seat per month. This is the most popular tier. Studio board, RBAC, everything a team needs.

And Enterprise — that's where you get VPC self-hosting. Full sovereignty. Unlimited everything. 24/7 dedicated CSM.

No credit card to start. No waiting list. No lock-in. Start free, and when you need sovereignty, it's there.

[Next slide]

---

## SLIDE 15 — Thank You

So let me wrap up with this.

Your code is your most valuable asset. Not your infrastructure, not your cloud bill — your code. The patterns, the business logic, the IP that took years to build.

Sovereignty isn't about distrust. It's about knowing exactly where your most valuable asset lives.

If you want to try Kimchi, the repo is open source at github.com/getkimchi/kimchi. Star it, fork it, try it out. And if you want to chat about data sovereignty, multi-model routing, or anything else — scan the QR code, connect with me, I'm here all day.

Thank you, Bengaluru.

[End of talk — open Q&A]

---

## QUICK REFERENCE — Slide Timing

| Slide | Topic | Est. Time |
|-------|-------|-----------|
| 1 | Title & intro | 1:00 |
| 2 | Your code is already out there | 1:30 |
| 3 | Three surfaces where code leaks | 2:30 |
| 4 | Vendor transparency gap | 2:00 |
| 5 | Real consequences | 2:00 |
| 6 | Enter Kimchi | 2:00 |
| 7 | Four pillars of sovereignty | 2:30 |
| 8 | VPC architecture diagram | 1:30 |
| 9 | Multi-model routing | 2:00 |
| 10 | Spend governance = data governance | 1:30 |
| 11 | Compliance | 1:00 |
| 12 | Proof & testimonials | 1:30 |
| 13 | Sovereignty checklist | 2:00 |
| 14 | Pricing | 0:45 |
| 15 | Thank you & CTA | 1:00 |
| **Total** | | **~23 min** |

## SPEAKER NOTES

- **Energy pattern:** Start strong and direct (slides 1-2). Build urgency (3-5). Shift to confident/problem-solving (6-9). Ground in reality (10-12). Actionable + empowering close (13-15).
- **Audience engagement moments:**
  - Slide 2: "Show of hands — who here uses an AI coding tool?" (optional icebreaker)
  - Slide 5: "Has anyone here seen a code leak happen at their company?" (optional)
  - Slide 13: Read the checklist slowly. Let each question land. This is the takeaway.
- **Pacing cues:** Slides 3, 7, and 13 are the meat — slow down. Slides 4, 8, and 10 are visual — point at the screen. Slides 1, 14, 15 are transition slides — keep moving.
- **If running short on time:** Slide 9 (multi-model routing) can be compressed to 1 min. Slide 11 (compliance) can be 30 sec if you just say "ISO 27001, SOC 2, GDPR, DPDP Act — all certified" and move on.
- **If running long:** Skip the BYOK detail on slide 9 and the Akamai quote detail on slide 12.
