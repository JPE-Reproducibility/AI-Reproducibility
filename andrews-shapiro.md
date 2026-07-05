# Andrews & Shapiro (Jan 2026) — "Proposal to Streamline Replication Process for Econometric Society Journals"

**Internal reference only — not for talk slides**

---

## Core Insight

Separate two goals of replication into two distinct processes:
- **(i) Reproducibility**: did the code actually run and produce the claimed outputs?
- **(ii) Clarity**: would a trained third party understand how to do it?

Current process conflates these. They propose handling them with different mechanisms and different burden-bearers.

---

## Process for (i) — Reproducibility

- ES maintains a cloud VM (basic specs; authors can pay to upgrade with own research funds)
- Authors get 7-day temporary access with **full keylogging + security logging of all data transfers**
- Authors log into terminal and follow their own README to set up and execute
- If execution fails, repeat until it succeeds
- ES staff verify outputs match original submission

Key move: **burden of execution shifts entirely to authors**. They do the work on a standardized machine.

---

## Process for (ii) — Documentation Clarity

- LLM compares terminal log + data transfer log to README → flags undocumented steps
- ES staff review for policy violations (e.g. author copied output files onto VM to fake successful execution — visible in transfer log)
- LLM suggests README improvements → passed **unedited** to authors with the logs
- Authors revise README
- ES staff final check (read or re-execute)

Key move: **LLM as force-multiplier for staff on documentation**, not execution.

---

## Deliverables Published

1. README, code, data (as now)
2. Data transfer log + terminal log from successful VM session
3. (Optionally) Docker image of machine state at end of session

---

## Relevance to the Talk

**Supports the force-multiplier framing (advisor point 6):** concrete instantiation by prominent economists of LLM augmenting staff, not replacing them.

**Partially addresses the integrity problem (advisor point 2):** keylogging + transfer logging means you cannot silently copy output files onto the VM — it shows in the log. ES staff review the logs specifically for this. Not a complete solution (prompt-tuning and stochastic cherry-picking in LLM pipelines still undetectable) but a meaningful partial answer for conventional code packages.

**Addresses Case 1 of the three-case taxonomy:** standardized VM is the provisioning solution. Authors pay for upgrades — neat way to handle heterogeneous compute needs without journal bearing full cost.

**Limitation not addressed in proposal:** LLM pipelines. The keylogging approach works for conventional code. But if the author's code calls an external LLM API, the transfer log shows an API call, not the prompt or the output. The proposal was written for conventional replication packages, not LLM-as-data-generator use cases. That gap is yours to name.

---

## Oswald's Assessment

**Feasible in principle.** Roughly what CASD (Centre d'Accès Sécurisé aux Données, France) does — but CASD runs on a yearly budget of several million euros with a dedicated software engineer. Not a journal-level operation.

**Core implementation failure:** the VM cannot possibly contain all the software authors use. Commercial licenses (Stata, MATLAB, GAUSS, ArcGIS...), proprietary data systems, bespoke HPC environments. The proposal assumes a generic VM suffices. It doesn't. This is Case 3 of the three-case taxonomy: budget and infrastructure problem, not a process problem.

**SIVACOR** (Lars Vilhuber, Cornell + NCSA + UIUC; NSF-funded) is a step toward this but for deliberately limited scope — it doesn't try to provision arbitrary author environments. See separate notes below.

**Verdict:** decent idea, serious implementability problems. Interesting to cite as direction-of-travel, not as a solved proposal.

**Historical precedent — RunMyCode.org (2012, now dead):** Stodden, Hurlin & Pérignon launched exactly this concept: cloud-executed companion pages per published paper, backed by CNRS TGE Adonis compute. 100 companion sites in 6 months, then stalled and eventually died. Reasons: software scope limited at launch (5 languages), compute dependency on CNRS infrastructure that wasn't theirs to control, manual validation bottleneck, no sustainable funding, no journal mandate. The provisioning problem didn't go away — the platform did. See `runmycodeorg.md` for full summary.

**Budget reality:** Making this work at journal scale — provisioning arbitrary author environments, maintaining software compatibility, running dedicated cloud compute — requires a budget of several million euros per year plus a dedicated software engineer. The French CASD (Centre d'Accès Sécurisé aux Données) is the existence proof: it does this, and that is roughly what it costs. No economics journal operates at that budget level for reproducibility infrastructure.

**Cost calibration (personal data point):** Running JPE replication packages on Nuvolos — a controlled, non-public facility used by a single data editor — costs ~10K EUR/year. That is for one journal's packages, provisioned by one person who controls what gets submitted and how. A self-service public facility where anyone can run any package across arbitrary software environments would multiply that cost by orders of magnitude. Someone always pays for compute. That is not a technology problem; it is a funding model problem.

---

---

## SIVACOR — What It Actually Is

**Source:** https://docs.sivacor.org  
**Authors:** Lars Vilhuber (Cornell), Kacper Kowalik (NCSA), Craig Willis (UIUC)  
**Funding:** NSF collaborative grants (OAC-2209628/29/30) — TRACE project  
**Tagline:** "Trusting Computational Research Without Repeating It"

**What it does:** Automated execution + certification of replication packages. Author submits ZIP → selects a curated Docker image → system executes → issues a Trusted Research Object (TRO) with cryptographic signature + trusted timestamp. Editor can verify TRO without re-running.

**Supported software:** R (via renv.lock), Stata (stata-mp), MATLAB. That's it.

**Compute:** Runs on Jetstream2 (Indiana University). Max 28GB RAM, 8-core AMD EPYC-Milan. No runtime ceiling currently.

**Outputs:** replicated package ZIP, run log, error log, TRO declaration, cryptographic signature, trusted timestamp.

**The scope constraint is the design:** SIVACOR works because it doesn't try to provision arbitrary environments. Curated Docker images only. No commercial software outside what's in those images. No custom HPC. This is deliberate — it's a pilot for the AEA journal cluster.

**Key limitation for broader adoption:** same as Case 3 — if authors use software not in the curated image list (GAUSS, ArcGIS, proprietary data systems, bespoke HPC), SIVACOR cannot help. The curated image approach is the right trade-off for a pilot; it's not a general solution.

**Practical issue authors hit:** Linux containers are case-sensitive. `Main.do` ≠ `main.do`. Hard-coded absolute Windows paths break immediately. These are the Case 2 problems — gaps in packages that an agent could fix (and SIVACOR currently just fails on).

**Relationship to Andrews-Shapiro:** SIVACOR is the working implementation of a similar idea but smaller scope and already running. Andrews-Shapiro's VM proposal is more ambitious (author interactively sets up environment) but not yet built. SIVACOR trades flexibility for automation.

---

## Tensions / Questions Worth Raising

- Who bears VM maintenance cost for ES? Proposal says authors can pay to extend/enhance — but base cost is on ES.
- "Authors follow their own README" means README quality is still author-determined before the process starts. Circular for bad READMEs.
- Docker image of machine state (optional deliverable) is exactly Case 1 of your taxonomy — they arrive at the same conclusion independently.
- The LLM suggestion step is explicitly kept unedited before passing to authors — interesting norm: LLM as neutral messenger, not editorial voice.
