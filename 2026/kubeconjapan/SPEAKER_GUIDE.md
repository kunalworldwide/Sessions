# Beyond Single-Cluster Limits — Speaker Guide

**Talk:** Scaling GPU Workloads Across Kubernetes with Virtual Nodes
**Conference:** KubeCon + CloudNativeCon Japan 2026
**Speakers:** Kunal Das (Developer Advocate, APAC) & Esmira Bayramova (DevRel, CAST AI — Americas)
**Deck URL:** https://kunaldas.net/Sessions/2026/kubeconjapan/

---

## How to use this guide

This document explains every slide in plain language, assuming you know the basics of Kubernetes (pods, nodes, deployments, kubectl) but nothing about multi-cluster, GPU scheduling, or virtual nodes. For each slide you'll find:

- **What's on screen** — what the audience sees
- **What to say** — the key point in one or two sentences
- **Why it matters** — the concept behind it, explained simply
- **Pro tips** — delivery notes, timing, what to emphasize

Total talk time: ~30 minutes. Aim for ~1.5 minutes per slide on average. Slides 4, 10, 15 can go faster; slides 3, 7, 16 deserve a pause.

---

## Slide 1 — Title

**On screen:** KubeCon Japan 2026 title, talk name "Beyond Single-Cluster Limits", both speakers' names, the mosaic background.

**What to say:** "Good evening. Thank you for spending your last 30 minutes with GPUs and virtual nodes instead of the bar downstairs."

**Pro tips:** Keep it to 10 seconds. Don't read the title slide — everyone can read. Just welcome them and move on.

---

## Slide 2 — Who I Am

**On screen:** Both speakers' photos side by side, with a short bio list. Kunal's bio is on the left; both photos are on the right.

**What to say:**

**Kunal:** "Quick intro so you know why we're up here — DevOps/infra background, I run two cloud-native community chapters in India, and my work is making Kubernetes GPU infrastructure dense and cheap."

**Esmira:** "I'm Esmira — I run DevRel initiatives for CAST AI in the Americas. Before that I was a DevOps engineer and engineering manager for US federal space companies. So I've seen this problem from both the engineering floor and the procurement side."

**Kunal:** "That's the lens for today. Now — the wall."

**Why it matters:** Establishes both speakers' credibility in 45 seconds total. The audience needs to know you've both operated real infrastructure, not just read about it. Esmira's federal space background adds weight — if it works for federal compliance, it works for anyone.

**Pro tips:** 45 seconds max for both intros. Split it — Kunal does his, hands to Esmira, she does hers, then Kunal transitions to slide 3. Don't read the bio bullets — paraphrase. The phrase "That's the lens for today" signals you're getting to substance fast.

---

## Slide 3 — The Hook

**On screen:** A big bold sentence: "A fine-tuning job sat queued for 10 hours — while 8 idle H100s sat unused one region away." A pulsing "PENDING" indicator at top-left.

**What to say:** "Here's the moment this talk was born. A fine-tuning job stuck in Pending for 10 hours. Meanwhile, another cluster — same company, different region — had 8 idle H100s sitting at low utilization. Idle. We had the hardware. We couldn't route work to it. That's not a capacity problem, it's a reachability problem — and that's the whole talk."

**Why it matters:** This is the emotional anchor. Every person in the audience who has managed GPUs has felt this pain. The number "10 hours" is real — it came from our production environment. The "8 idle H100s" is representative of what we found when we looked across clusters.

**Pro tips:** Let the slide sit in silence for 2 seconds after reading it. Let the audience absorb the absurdity. Then deliver the punchline: "That's not a capacity problem, it's a reachability problem."

---

## Slide 4 — The Data

**On screen:** A chart showing queue depth (bar chart) vs. GPU utilization (line graph) over a week. Bars spike on Thursday (the worst day, 48 pending jobs), but utilization stays flat at ~60%. Two stat callouts: "10 hrs" (peak queue wait) and "60%" (avg fleet utilization).

**What to say:** "When we plotted it, the paradox was obvious. Queue depth spikes here, but utilization never gets near the top. On our busiest day, jobs waited hours while average fleet utilization was only 60%. Adding GPUs to one cluster just adds idle GPUs. The bottleneck was the cluster boundary itself."

**Why it matters:** This slide proves the hook wasn't a one-off. It was a pattern. The chart shows that the problem isn't lack of GPUs — it's that GPUs are trapped behind cluster boundaries.

**Key concepts for beginners:**
- **Queue depth** = how many jobs are waiting to run (stuck in Kueue, Kubernetes' batch scheduler)
- **Utilization** = what percentage of your GPUs are actually doing work
- The paradox: high queue + low utilization = your GPUs exist but work can't reach them

**Pro tips:** Point to the Thursday bar. Say "this day" and pause. The audience should see the mismatch between tall bars and the flat green line.

---

## Slide 5 — Three New Problems

**On screen:** Three cards, each with an icon: (1) kubeconfig sprawl, (2) blind queue, (3) fake failover. Each card animates in one by one. Below: "Multi-cluster dashboards are not multi-cluster scheduling."

**What to say:** "We did the obvious thing — added cloud GPU clusters for burst. Capacity solved, three new problems. Five kubeconfigs and wrong-cluster deploys. Each cluster ran its own Kueue, so no single queue knew where free GPUs were. And we called it multi-cluster, but it was five islands with a shared dashboard — nothing could fail over."

**Why it matters:** This is the "we tried the obvious thing and it didn't work" slide. Every audience member who has added a second cluster has hit at least one of these three problems. It builds empathy.

**Key concepts:**
- **kubeconfig sprawl** = developers have to switch between multiple cluster contexts, leading to mistakes (deploying to the wrong cluster)
- **Blind queue** = each cluster's Kueue (batch scheduler) only sees its own capacity — it doesn't know other clusters have idle GPUs
- **Fake failover** = you have a backup cluster, but nothing actually moves workloads there automatically

**Pro tips:** Deliver the final line with conviction: "A shared dashboard is not scheduling." Let each card appear before commenting on it.

---

## Slide 6 — The Requirements

**On screen:** Four requirement pills, each in a card with a checkbox-style icon: UNIFY, TRANSPARENT, GLOBAL QUEUE, REAL FAILOVER. Each animates in sequentially. Below: "Everything after this is how we hit these four."

**What to say:** "Before tools, here's the scorecard. Unify five clusters into one schedulable thing. Keep it transparent — zero manifest rewrites. One global queue. And real failover — defined, tested behaviour, not hope. Hold me to these four."

**Why it matters:** This is your promise to the audience. Everything after this slide is how you deliver on these four requirements. If someone zones out and comes back at slide 19, the four pills (lit up) tell them exactly what was achieved.

**Key concepts:**
- **UNIFY** = make N clusters look like one to the scheduler
- **TRANSPARENT** = developers shouldn't need to change their YAML or learn new APIs
- **GLOBAL QUEUE** = one queue that sees all GPU capacity across all clusters
- **REAL FAILOVER** = when something breaks, workloads move automatically with tested behaviour

**Pro tips:** Say "Hold me to these four" — it creates accountability. The audience will check whether you deliver.

---

## Slide 7 — The Mental Model

**On screen:** A diagram showing two clusters: "Home cluster" (left) with real nodes and a red dashed box labeled "liqo-node-cloud-a" (a virtual node), and "Remote cluster (cloud A)" (right) with real GPU nodes. A red dashed arrow connects the virtual node to the remote cluster with "pods offloaded here."

**What to say:** "What if multi-cluster looked like one? Liqo uses an extended Virtual Kubelet to make a peered remote cluster appear as a single virtual node in your home cluster. Pods scheduled onto that virtual node actually run in the remote cluster. The scheduler you already run doesn't change. The topology does."

**Why it matters:** This is the core concept of the entire talk. If the audience understands this one slide, they understand the talk.

**Key concepts for beginners:**
- **Virtual Kubelet** = a Kubernetes component that registers a "fake node" with your cluster. The scheduler thinks it's a real node and sends pods to it. But instead of running on a local machine, the pods are forwarded to a remote cluster.
- **Liqo** = an open-source project (started at Politecnico di Torino / ArubaKube) that uses Virtual Kubelet to peer clusters. It handles the networking, the pod offloading, and the health checking.
- **The key insight** = your existing scheduler (the one in your home cluster) doesn't need to change at all. It just sees more nodes. Some are real, some are virtual (backed by remote clusters).

**Pro tips:** This is the slide to slow down on. Point at the virtual node box. Say "this is the magic." Pause. Let them absorb that a whole cluster looks like one node. The red dashed style means "virtual" — consistent with the rest of the deck.

---

## Slide 8 — Offloading, Precisely

**On screen:** An animated list of steps explaining how pod offloading works, with numbered yellow badges. Steps reveal one by one.

**What to say:** Walk through each step:
1. A pod is created in the home cluster with a label selecting the virtual node
2. Liqo's offloading controller intercepts it
3. The pod is replicated to the remote cluster
4. It runs there with its real IP and networking
5. The home cluster sees it as running — but it's actually across the wire

**Why it matters:** Shows that offloading isn't magic — it's a precise, explainable mechanism. The audience needs to trust that this works predictably.

**Key concepts:**
- **Namespace offloading** = you opt-in at the namespace level; Liqo only offloads namespaces you explicitly enable
- **Node affinity** = you can pin pods to on-prem nodes or let them spill to virtual (remote) nodes using standard Kubernetes affinity — no new API
- **Pod replication** = the pod isn't "moved" — it's created in both clusters. The home cluster's pod is a placeholder; the remote cluster's pod is the real one

**Pro tips:** Don't rush. Each step builds on the last. If the audience misses step 2, step 3 won't make sense.

---

## Slide 9 — Production Topology

**On screen:** A hub-and-spoke diagram showing the HOME CLUSTER in the center, with 4 virtual nodes (red dashed boxes) connecting to 5 remote clusters: Remote A (on-prem, blue), Remote B (on-prem, blue), Remote C (spot, amber), Remote D (spot, amber), Remote E (on-demand, green). A legend explains the color coding.

**What to say:** "This is our production topology. One home cluster, four virtual nodes peering to five remote clusters. Blue is on-prem, amber is spot, green is on-demand. The virtual nodes — the red dashed ones — are Liqo abstractions. They look like nodes to the scheduler, but they're backed by entire clusters."

**Why it matters:** This is the "show, don't tell" slide. The audience sees a real topology, not a theoretical one. The color coding (on-prem blue / spot amber / on-demand green) is consistent across every diagram in the talk.

**Key concepts:**
- **Hub-and-spoke** = one central cluster (home) peers to multiple remote clusters. Not mesh — simpler to operate.
- **Capacity types** = on-prem (your own hardware), spot (cheap, can be reclaimed), on-demand (cloud, reliable but expensive). The scheduler can prefer or avoid each type using standard affinity.
- **Virtual node = entire cluster** = each virtual node represents a whole remote cluster with multiple GPU nodes behind it

**Pro tips:** Point out the color coding. Say "remember these colors — they're the same throughout the deck." The audience will subconsciously track capacity types.

---

## Slide 10 — No New Scheduler

**On screen:** A YAML code block showing a Pod spec with node affinity selecting virtual nodes, with key lines highlighted. A callout: "No new CRDs, no new scheduler."

**What to say:** "No new scheduler. Just labels you already know. You write a normal Pod spec, add node affinity for the virtual node label, and Kubernetes does the rest. The scheduler you already run places the pod on a virtual node — which is actually a remote cluster. Zero manifest rewrites for developers."

**Why it matters:** This delivers on the TRANSPARENT requirement from slide 6. Developers don't need to learn anything new. The same YAML they've always written works — just with a new label.

**Key concepts:**
- **nodeSelector / nodeAffinity** = standard Kubernetes feature to control which nodes a pod lands on. Here, you select the virtual node's label (e.g., `liqo.io/remote-cluster: cloud-a`)
- **No new CRDs** = no new Custom Resource Definitions. You don't need to install a new API or learn new object types. Just pods and nodes.
- **The highlighted lines** = the affinity block — that's all that changes

**Pro tips:** Point at the highlighted lines. Say "this is the only difference." The audience should see how small the change is.

---

## Slide 11 — Cross-Cluster Networking

**On screen:** A comparison table of two networking options: WireGuard (Liqo's built-in fabric) vs. Cilium Mesh, comparing setup, performance, and trade-offs.

**What to say:** "Cross-cluster networking has two honest options. Liqo's built-in WireGuard fabric is the default — it just works, zero config, but you're on WireGuard. If you already run Cilium, Cilium Mesh gives you the same connectivity with your existing CNI and better observability. Choose by your CNI and CIDR reality, not by hype."

**Why it matters:** Networking is the hardest part of multi-cluster. This slide acknowledges both options honestly without declaring a winner — the right choice depends on your existing infrastructure.

**Key concepts:**
- **WireGuard** = a fast, modern VPN protocol. Liqo sets it up automatically between peered clusters. It handles pod-to-pod traffic across clusters.
- **Cilium Mesh** = if you already use Cilium as your CNI (Container Network Interface), you can use its mesh feature for cross-cluster connectivity instead of WireGuard.
- **CIDR** = the IP address range each cluster uses. If two clusters have overlapping CIDRs, networking gets complicated. WireGuard handles this automatically; Cilium Mesh requires you to plan.

**Pro tips:** Don't pick a side. Say "both are valid — know your CNI and CIDRs." If someone asks "which should I use," the answer is always "it depends on what you already run."

---

## Slide 12 — One Queue That Sees Everything

**On screen:** A diagram showing the Kueue architecture: one ClusterQueue holding global quota, connected to LocalQueues in namespaces, with virtual nodes providing capacity. Arrows are yellow (virtual connections).

**What to say:** "One queue that sees everything. Kueue does not schedule pods — it's a gate. A job arrives created suspended; Kueue checks its ClusterQueue quota; only then it un-suspends and hands off to the real scheduler — the same one from slide 10 that lands it on a virtual node. Gate, then place. One ClusterQueue holds global quota across every virtual node. Developers submit to a LocalQueue and never think about where capacity lives."

**Why it matters:** This delivers on the GLOBAL QUEUE requirement. The key insight is that Kueue isn't a scheduler — it's a quota gate. Once it admits a job, the normal Kubernetes scheduler places it (on a virtual node if that's where capacity is).

**Key concepts:**
- **Kueue** = a CNCF project for batch job queueing in Kubernetes. It doesn't schedule — it gates. Jobs start suspended, Kueue checks if quota is available, then un-suspends them.
- **ClusterQueue** = holds quota across multiple ResourceFlavors (GPU types). One ClusterQueue can see all virtual nodes.
- **LocalQueue** = what developers submit to. It's namespaced. Developers just point their job at a LocalQueue and don't know or care which cluster runs it.
- **ResourceFlavor** = a named class of nodes by label (e.g., "H100", "A100", "spot"). Each GPU type is one ResourceFlavor.

**Pro tips:** Emphasize "gate, then place" — it's the mental model that makes Kueue click. The audience needs to understand Kueue doesn't replace their scheduler.

---

## Slide 13 — Why Not MultiKueue?

**On screen:** Two paths side by side. Path A (MultiKueue): a manager cluster dispatches jobs to separate worker clusters, first admit wins, others deleted. Path B (Liqo + Kueue): one scheduling plane, virtual nodes, transparent placement. Arrows in yellow (Path A) and green (Path B).

**What to say:** "Sharp folks are thinking: Kubernetes already has MultiKueue — why Liqo? MultiKueue dispatches jobs: a manager clones your job to independent worker clusters, first admit wins, the rest are deleted. Liqo + Kueue is one scheduling plane, remotes are nodes, and the ordinary scheduler places with the affinity you saw. MultiKueue shines when clusters must stay independent. We wanted one topology with transparent networking and plain affinity, so we chose the virtual-node plane. Neither is wrong — know which problem you have."

**Why it matters:** This is the slide for the Kubernetes experts in the audience who will ask "why not just use MultiKueue?" You preempt the question and give an honest comparison.

**Key concepts:**
- **MultiKueue** = a Kueue feature where a manager cluster dispatches jobs to worker clusters. Each worker is a separate scheduling domain. The job is cloned to all workers; the first to admit wins; the rest are deleted.
- **The difference** = MultiKueue keeps clusters separate (each is its own world). Liqo collapses them into one (remotes are nodes, not separate scheduling domains).
- **When to use which** = MultiKueue is better when clusters must stay fully independent (different teams, no shared network). Liqo + Kueue is better when you want one topology, transparent pod networking, and vanilla affinity placement.

**Pro tips:** Don't declare a winner. Say "neither is wrong." The audience respects honesty about trade-offs.

---

## Slide 14 — "Two Regions" Is Not DR

**On screen:** A big statement: "A backup cluster is not continuity. Tested behaviour on failure is." Two boxes below: "Backup region = capacity nobody has failure-tested" (with a server rack icon) and "What you need = a defined, tested answer" (with a green checkmark icon). A small decision tree diagram on the right.

**What to say:** "A backup cluster is not continuity. Tested behaviour on failure is. Having a second region with capacity is not disaster recovery. You need a defined, tested answer to: what happens to traffic and pods when a cluster or node dies? If you can't answer that in one sentence, you don't have DR."

**Why it matters:** This delivers the setup for REAL FAILOVER (slide 15). The audience needs to understand that "we have a backup region" is not enough — you need to know exactly what happens when things break.

**Key concepts:**
- **DR (Disaster Recovery)** = the ability to keep running when infrastructure fails. Real DR means tested, documented, automated failover — not "we have a spare cluster somewhere."
- **The key question** = "What happens to traffic and pods when a cluster or node dies?" If the answer is "I don't know" or "we'd figure it out," you don't have DR.

**Pro tips:** Deliver the statement with conviction. This is a truth-bomb slide. Many people in the audience will realize they don't have real DR.

---

## Slide 15 — Two Failure Modes

**On screen:** Two topology panels side by side. Case A: whole remote cluster unhealthy → virtual node goes NotReady after 40s → EndpointSlices not-ready → traffic reroutes automatically. Case B: single remote worker node fails → pods stuck Terminating forever unless `enableNodeFailureController=true`. Each panel animates in.

**What to say:** "Two failure modes, two behaviours — know both. Case A: a whole remote cluster dies. Liqo health-checks the API server and VPN tunnel; the virtual node goes NotReady after 40 seconds, then remote EndpointSlices go not-ready so Services stop sending there. That's real and automatic — but it's TRAFFIC continuity. Case B, write this down: if a single remote worker node fails while the cluster's up, by default the pods sit in Terminating forever and are NOT replaced. Fix is one Helm flag — `enableNodeFailureController` — so Liqo force-deletes the stuck pod and ShadowPod recreates it on a healthy node. Default off. Turn it on."

**Why it matters:** This delivers REAL FAILOVER. It's the most technically dense slide. The audience gets two concrete, tested failure scenarios with specific outcomes and fixes.

**Key concepts:**
- **Virtual node NotReady** = when the remote cluster's API server or VPN tunnel is unreachable, Liqo marks the virtual node as NotReady (just like a real node going down). After the default 40-second grace period (node-monitor-grace-period), Kubernetes stops sending traffic there.
- **EndpointSlices** = Kubernetes objects that track which pods are ready to receive traffic for a Service. When the virtual node goes NotReady, the remote pods' EndpointSlices go not-ready, so the Service stops routing to them.
- **ShadowEndpointSlice** = a Liqo controller that mirrors remote EndpointSlices to the home cluster, so failover is automatic.
- **enableNodeFailureController** = a Liqo Helm flag (default OFF). When ON, Liqo detects when a remote worker node fails and force-deletes stuck pods so they can be rescheduled. Without it, pods on a dead node stay in Terminating forever.
- **ShadowPod** = Liqo's controller that recreates pods on healthy nodes after force-deletion.

**Pro tips:** This is the "write this down" slide. Say it explicitly. The `enableNodeFailureController` flag is the single most actionable takeaway for anyone running Liqo in production. Pause after "Default off. Turn it on."

---

## Slide 16 — Results

**On screen:** A big stat: "42% → 78%" fleet GPU utilization. Below, three result cards with icons: (1) green checkmark — "< 30 min" job queue wait (was 10 hours), (2) amber document — "5 → 1" kubeconfigs, (3) yellow star — "+36%" more GPU capacity from same hardware. A bottom line: "0 manifest changes to burst · same hardware, same budget."

**What to say:** "The whole talk in one number. Forty-two to seventy-eight percent GPU utilization. Same GPUs. Same budget. Remember the job at the start that waited 10 hours while H100s sat idle? It waits less than 30 minutes now. Loop closed. Five kubeconfigs to one. Zero manifest changes to unlock burst. All on hardware we already owned. We didn't buy our way out. We made what we had reachable."

**Why it matters:** This is the payoff. The audience has sat through 15 slides of problem and solution. This is the "was it worth it?" answer. The numbers are representative of what multi-cluster pooling typically achieves.

**Key concepts:**
- **42% → 78%** = before multi-cluster pooling, average fleet GPU utilization was ~42% (GPUs trapped behind cluster boundaries). After Liqo unified the clusters, utilization jumped to ~78% because work could reach idle GPUs.
- **< 30 min** = the job that waited 10 hours now waits under 30 minutes because the global queue can see and route to idle capacity.
- **+36%** = same hardware, but 36% more of it is actually used because work reaches GPUs that were previously unreachable.

**Pro tips:** Let the "42% → 78%" sit in silence for 2 seconds. Don't rush to the next beat. The audience needs to feel the impact. Then deliver the "10 hours → 30 minutes" callback — it closes the loop from slide 3.

---

## Slide 17 — Battle Scars

**On screen:** Three scar cards, each with a number (01, 02, 03): (1) Spot reclamation mid-job, (2) Cross-cluster MTU on WireGuard, (3) The Terminating-forever trap. Below each, fix-tip boxes with emoji icons. The cards are larger and well-spaced.

**What to say:** "Where the bodies are buried. Three scars. Spot reclamation killed long fine-tunes until we added checkpointing and kept critical jobs off pure-spot with affinity. An MTU mismatch on the WireGuard fabric gave a silent throughput cliff — nothing dropped, just mysteriously slow — pinning MTU fixed it. And the one from a few slides ago: node failures leaving pods Terminating until we flipped that Helm flag. None fatal. Each cost a weekend. Now they cost you nothing."

**Why it matters:** This is the "we're not hiding the hard parts" slide. It builds trust. The audience knows you're not just selling a success story — you've hit real problems and have specific fixes.

**Key concepts:**
- **Spot reclamation** = cloud providers can reclaim (take back) spot instances with little notice. If a GPU is yanked mid-training, the job fails. Fix: checkpoint your training (save progress periodically) and use node affinity to keep critical jobs on on-prem or on-demand nodes.
- **MTU mismatch** = Maximum Transmission Unit is the largest packet size a network can handle. If the WireGuard tunnel has a different MTU than the underlying network, packets get fragmented or silently slowed down. Fix: explicitly set the MTU on the WireGuard interface.
- **Terminating-forever** = when a remote node fails, Kubernetes tries to gracefully terminate pods on it. But the node is gone, so the termination hangs forever. Fix: `enableNodeFailureController=true` in Liqo's Helm values.

**Pro tips:** Deliver each scar with a slight smile. "Each cost us a weekend" gets a laugh — it's relatable. The fix-tip boxes below give the audience the actionable fix without making them read the card text.

---

## Slide 18 — Where We Took It (The Only Vendor Slide)

**On screen:** A calmer slide with the Kimchi.dev logo, three points about what CAST AI built on top of open source, and a small topology diagram showing the automation layer. The list animates in.

**What to say:** "One slide on what my employer does with this, then we close — because I promised an open-source talk. At CAST AI we automated the painful parts around this pattern — provisioning VMs, wiring networking, onboarding a cloud region so an edge location just appears, with GPU nodes in a region sharing a hosted control plane. We connect it to an autoscaler so unscheduled GPU pods pull capacity across regions. And we open-sourced the orchestration harness on top — Kimchi.dev. That's the whole pitch. Back to open source."

**Why it matters:** This is the only vendor slide in the entire talk. It's deliberately calm (white background, no flashy graphics) to signal "this is a brief commercial interlude, not the main event." The three points are specific capabilities, not marketing fluff.

**Key concepts:**
- **CAST AI** = the speaker's employer. They build automation around open-source multi-cluster patterns.
- **k0smotron** = a CNCF project for running lightweight Kubernetes control planes. CAST AI uses it so multiple GPU nodes in a region can share one hosted control plane, reducing overhead.
- **Kimchi.dev** = an open-source AI orchestration harness built on top of this infrastructure. It's the layer that ties provisioning, scheduling, and autoscaling together.

**Pro tips:** Keep this to 60 seconds. The audience tolerates one vendor slide if you're honest about it. Say "back to open source" to signal the commercial is over.

---

## Slide 19 — Three Takeaways

**On screen:** Three numbered boxes with yellow badges, each animating in: (1) Virtual nodes turn N clusters into one schedulable topology, (2) Choose your cross-cluster network by CNI + CIDR reality, (3) Continuity is specific — traffic reroutes on cluster failure, pod re-creation on node failure is opt-in. Below: "That's your starting map for multi-cluster GPU scheduling."

**What to say:** "Three things. Virtual nodes collapse many clusters into one topology, no new scheduler. Pick your network by CNI and CIDRs, not hype. And be precise about continuity — traffic reroutes automatically when a cluster dies, but pod re-creation on node failure is a flag you turn on. If you're fighting GPU scarcity across clusters, that's your starting map."

**Why it matters:** This is the "if you remember nothing else" slide. Most audience members will remember 3 things from a 30-minute talk. These are the 3 you want them to remember.

**Pro tips:** Count them on your fingers as you deliver each one. It's a physical mnemonic for the audience. End with "that's your starting map" — it gives them a sense of direction.

---

## Slide 20 — Thank You

**On screen:** "Thank you / ありがとうございます" in large text. Both speakers' photos with names and handles. A large QR code (440px) linking to slides + reference configs. "Visit us at Booth S6" in yellow.

**What to say:** "Thank you — arigatou gozaimasu. Slides and a repo with the real Liqo peering, Kueue configs, and affinity examples are on the QR — the CFP promised reusable patterns and I want you to leave with them. Visit us at Booth S6 if you want to talk. Questions?"

**Why it matters:** The QR code is the most important practical element — it's how the audience takes your work home. The booth number drives foot traffic. The Japanese "ありがとうございます" shows respect for the host country.

**Pro tips:** Point at the QR code physically. Say "scan this" — people need a cue. Stay on this slide during Q&A so the QR is visible the whole time.

---

## Glossary for Beginners

| Term | Simple explanation |
|---|---|
| **Virtual Kubelet** | A piece of software that registers a "fake node" with your Kubernetes cluster. Pods sent to this node are actually forwarded to a remote cluster. |
| **Liqo** | An open-source project that peers Kubernetes clusters using Virtual Kubelet. It handles networking, pod offloading, and health checking. |
| **Kueue** | A CNCF project for batch job queueing. It doesn't schedule — it checks if quota is available, then un-suspends jobs so the normal scheduler can place them. |
| **ClusterQueue** | In Kueue, this holds the global quota. One ClusterQueue can see capacity across all virtual nodes. |
| **LocalQueue** | In Kueue, this is what developers submit jobs to. It's namespaced — developers don't need to know where capacity lives. |
| **ResourceFlavor** | In Kueue, a named class of nodes by label (e.g., "H100", "A100", "spot"). Each GPU type is one ResourceFlavor. |
| **Virtual node** | A node in your cluster that represents an entire remote cluster. It's backed by Liqo's Virtual Kubelet. To the scheduler, it looks like a real node. |
| **EndpointSlice** | A Kubernetes object that tracks which pods are ready to receive traffic for a Service. When a virtual node goes NotReady, its pods' EndpointSlices go not-ready, so traffic stops flowing there. |
| **ShadowEndpointSlice** | A Liqo controller that mirrors remote EndpointSlices to the home cluster, enabling automatic traffic failover. |
| **enableNodeFailureController** | A Liqo Helm flag (default OFF). When ON, Liqo detects remote node failures and force-deletes stuck pods so they can be rescheduled. **Turn it on.** |
| **ShadowPod** | A Liqo controller that recreates pods on healthy nodes after force-deletion. |
| **WireGuard** | A fast, modern VPN protocol. Liqo uses it by default for cross-cluster networking. Zero config, just works. |
| **Cilium Mesh** | An alternative cross-cluster networking option if you already run Cilium as your CNI. Better observability but requires CIDR planning. |
| **MTU** | Maximum Transmission Unit — the largest network packet size. Mismatches cause silent slowdowns. |
| **MultiKueue** | A Kueue feature where a manager cluster dispatches jobs to separate worker clusters. Different philosophy from Liqo's "one topology" approach. |
| **k0smotron** | A CNCF project for running lightweight Kubernetes control planes. Used by CAST AI to share control planes across GPU nodes in a region. |
| **Kimchi.dev** | An open-source AI orchestration harness built on top of this multi-cluster infrastructure. |
| **node affinity** | Standard Kubernetes feature to control which nodes a pod lands on, using labels. Here, you select virtual node labels to send pods to remote clusters. |
| **CIDR** | The IP address range a cluster uses. Overlapping CIDRs between clusters complicate networking. |

---

## Timing Guide

| Slides | Time | Section |
|---|---|---|
| 1–2 | 1 min | Intro |
| 3–4 | 3 min | The problem (hook + data) |
| 5–6 | 2 min | What we tried + requirements |
| 7–8 | 4 min | The mental model (core concept) |
| 9–10 | 3 min | Topology + YAML (show, don't tell) |
| 11–12 | 3 min | Networking + queue |
| 13 | 2 min | MultiKueue comparison |
| 14–15 | 4 min | Failure modes (densest section) |
| 16 | 2 min | Results (payoff) |
| 17 | 2 min | Battle scars |
| 18 | 1 min | Vendor (brief) |
| 19–20 | 2 min | Takeaways + thank you |
| **Q&A** | 2+ min | Keep QR visible |

---

## Q&A Preparation

**Likely questions and how to answer:**

**Q: Why not just use a bigger cluster?**
A: We tried. The problem isn't capacity — it's reachability. GPUs exist but are trapped behind cluster boundaries (different teams, regions, cloud accounts). Virtual nodes make them reachable without merging clusters.

**Q: Does this work with GPUs specifically, or any workload?**
A: Any workload, but GPUs are where the pain is sharpest because they're expensive and scarce. CPU workloads can tolerate queue waits; GPU jobs (training, fine-tuning) cannot.

**Q: What's the overhead of Liqo?**
A: Minimal — the Virtual Kubelet is lightweight. The main overhead is the WireGuard tunnel (negligible) and the pod replication (one extra API call per pod). We didn't measure any meaningful performance impact.

**Q: Is Liqo production-ready?**
A: We run it in production. It's not perfect (see the battle scars), but it works. The `enableNodeFailureController` flag is the one thing you must turn on that isn't default.

**Q: How does this compare to Karmada or Cluster API?**
A: Karmada federates clusters but doesn't make them look like one scheduling domain — you still submit workloads to specific clusters. Cluster API provisions clusters but doesn't solve the scheduling problem. Liqo is the only tool that makes remote clusters appear as local nodes.

**Q: What about security?**
A: Liqo uses WireGuard for mutual authentication between clusters. You control which clusters peer with which. Namespace offloading is opt-in — Liqo only offloads namespaces you explicitly enable.

---

*Good luck, Esmira! The deck is at https://kunaldas.net/Sessions/2026/kubeconjapan/ — open it and arrow through to practice with the animations.*
