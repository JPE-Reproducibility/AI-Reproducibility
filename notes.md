# Master Content: Reproducibility and LLMs — Talk Notes

**Occasion:** Large academic economics conference, annual meeting  
**Duration:** 20 minutes (short version) / 45-60 minutes (extended version)  
**Speaker role:** Data Editor, Journal of Political Economy

---

## Version Tags

- `[20MIN]` — in the short talk. Estimated minutes noted.
- `[EXTENDED]` — in the longer version only; cut from 20-min
- `[BACKUP]` — demote to backup/Q&A slides; too detailed for either main version

**Language rule:** Avoid "AI" as a term throughout — too vague. Use: LLM, coding agent, deep neural network, computer vision, statistical learning. Fix section headers when making slides.

---

## 20-Minute Spine (total ~19 min)

| Block | Content | Time |
|-------|---------|------|
| Opening | 3 questions + framing | ~2 min |
| Defs | LLM taxonomy for economists | ~2 min |
| Q1 | Three-case taxonomy + wrong-question reframe | ~4 min |
| Q2 | DeadReckoning pitch | ~3 min |
| Q3 | Andrews-Shapiro + SIVACOR + integrity gap | ~5 min |
| Close | Honest bottom line | ~2 min |

---

## Opening `[20MIN ~2min]`

Three questions that frame the talk:

1. Will LLMs solve all our troubles regarding reproducibility? Why or why not?
2. Will LLMs help authors prepare replication packages?
3. Will LLMs help data editors check replication packages?

Framing: I am not here to debate whether my human replicators are better than a coding agent — for now they are, because most code is still human-written, hence errors are human. The question is how to augment replicators with LLM-generated help.

---

## Definitions + How Economists Use LLMs `[20MIN ~2min]`

**What is an LLM:** a model trained on large text corpora to predict and generate language. Weights are fixed after training. Not magic; not AGI. A very capable pattern-completion engine.

**What is a coding agent:** an LLM with the ability to read/write files and execute code in a loop. Observes errors, revises, retries. The "get a coffee" version of automation.

**Three ways economists use LLMs — only one matters for us:**

| Use | What it is | Reproducibility problem? |
|-----|-----------|--------------------------|
| Statistical learning | Deep nets, nonparametric estimation, model solving | No new problem — code is code |
| LLM as code assistant | Prompts LLM to write Stata/Python/R | No — output is conventional code, same rules as RA-written code |
| **LLM in data pipeline** | **LLM generates/classifies/scores research data** | **Yes — LLM output enters the data chain** |

We only care about row 3. If the LLM wrote your loop, irrelevant. If the LLM assigned sentiment scores that appear in your regression, that is a reproducibility problem.

---

## Q1: Will LLMs Solve Reproducibility? `[20MIN ~4min]`

The dream: Elliott's tool. Supply URL of package, get a coffee, agent cranks through it, reports back.

Reality: depends entirely on the package. Three cases:

**Case 1: Complete, documented, containerized package (Docker/Singularity)**
Agent can run it. But so can a shell script. The binding constraint was never intelligence — it was a compatible environment. Agent adds speed, not capability. Aside: Singularity/Apptainer was built for HPC — Docker-on-HPC is largely solved. Nuvolos is another solution.

**Case 2: Package has gaps — missing deps, broken paths, incomplete README**
This is where an agent earns its keep. Infers missing library versions from error messages, fixes relative paths, searches for undocumented dependencies, iterates on environment setup. A human replicator does this too — but it costs hours; an agent does it in minutes. Realistic near-term value: not full automation, but force-multiplication on diagnostic work.

**Case 3: Package requires software or OS you don't have licensed/provisioned**
Agent is irrelevant. Problem is budget and infrastructure, not intelligence. No LLM fixes a missing Stata license.

**The reframe:** AI helps most in Case 2. Case 1 is the standard to push toward (containerization). Case 3 is an institutional problem no technology solves. The question "will LLMs solve reproducibility?" is wrong. The right question: **where in the workflow do LLMs provide leverage?**

---

## Q2: Will LLMs Help Authors Prepare Packages? `[20MIN ~3min]`

Yes — the most tractable near-term application. Problem is well-defined: author has working code on their machine, needs it to run on someone else's.

**DeadReckoning** (github.com/floswald/DeadReckoning, *proposed / in development*) is a concrete design for this. Two-phase agent pipeline:

**Phase 1 — Understand:** detect languages, scan live installed environment, reconstruct dependency graph rooted in LaTeX source. Every figure and table must trace back to a script.

**Phase 2 — Repair:** iterative build-test-fix loop on author's native machine until all exhibits regenerate. Handles economics' multi-language zoo (R, Stata, Julia, Python, MATLAB). Docker containerization is the *last* step — fix it natively first, then freeze the environment.

Key design choices:
- **Confidentiality protocol:** restricted mode — only code/metadata transmitted to remote LLM, never data values
- **No lockfile ≠ information is gone:** reconstructs software versions from file timestamps, code parsing, installed environment
- **AGENT_REPORT.md:** every decision documented; author must be able to explain it to a reviewer

This is Case 2 automation — agent as force-multiplier for the author, doing what a good RA does, at scale, without the author having to remember what they installed two years ago.

---

## Q3: Will LLMs Help Data Editors Check Packages? `[20MIN ~5min]`

**Andrews & Shapiro proposal (Econometric Society, Jan 2026):**
- ES maintains a cloud VM; authors get 7-day access with full keylogging + transfer logging
- Authors run their own code following their own README
- **LLM compares terminal log + data transfer log to README** → flags undocumented steps
- **LLM suggests README improvements** → passed unedited to authors
- ES staff review logs for policy violations (e.g. copying output files to fake success — visible in transfer log)

Right framing: LLM augments editor on tedious log-vs-README diff; human editor retains integrity judgment. Burden of execution shifts to authors. *Implementability concern: a journal-maintained VM cannot contain all commercial software authors use. CASD (France) does something similar on a budget of several million euros/year with a dedicated engineer.*

**SIVACOR** (Vilhuber/Cornell/NCSA/UIUC, NSF-funded, already running for AEA):
Author submits ZIP + selects Docker image → automated execution → Trusted Research Object (TRO) with cryptographic signature + trusted timestamp. Editor verifies TRO without re-running. Works for R, Stata, MATLAB on curated images. Limited scope by design — that's what makes it work.

**The integrity gap — what neither of these solves:**

Keylogging catches file manipulation. It does not catch:
- Prompt-tuning toward a desired result ("give me scores where positive > 0.6")
- Cherry-picking across stochastic runs (run 20 times, keep the best)
- Outright output fabrication (skip the LLM, write the CSV yourself)

Both proposals were designed for conventional code packages. The LLM-as-data-generator case is an **open problem for editors.** We can verify *that* a pipeline was applied, not *how honestly* it was applied.

---

## Close `[20MIN ~2min]`

**The honest position:**
- LLMs don't solve reproducibility — they shift *where* the leverage is
- For authors: Case 2 automation is real and near-term (DeadReckoning direction)
- For editors: force-multiplication on documentation checking is real and already partially deployed (SIVACOR, Andrews-Shapiro direction)
- For LLM-as-data: same principle (author responsible), harder enforcement, new integrity surface we don't yet have tools to close
- Treat LLM output as raw data. Archive it with a DOI. Describe the model as you would any instrument.

---

---

# Extended Content `[EXTENDED]`

*Everything below is for the longer version or backup slides. Do not include in 20-min talk.*

---

## The Coordination Temptation — and Why It Fails `[EXTENDED]`

A natural response: if economists all coordinated on open-source tools (Python, R, Julia), every package could be a Docker container and Case 3 largely disappears.

Half right, half wrong.

**The right half:** provisionability is easier with a standard open-source stack. SIVACOR and Andrews-Shapiro both exploit this — they work because they restrict the environment, not because they mandate a language. Correct version of the insight: **mandate the outcome (must run in a clean standard container), not the tool.** Language-agnostic, captures most of the benefit.

**The wrong half:** toolset standardization does not fix reproducibility-in-principle. Failures that dominate in practice survive the mandate:
- Only 31% of packages have a controller script — not a language problem
- Dependency rot (transitive deps, BLAS/GDAL drift) hits Python and R as hard as anything
- Unset seeds, thread-order FP nondeterminism, GPU variance — all survive a language mandate

**The proprietary-software framing is wrong.** Stata is deterministic, version-pinnable, archivable — Yes/Yes/Yes on reproducibility-in-principle. More reproducible than a closed LLM API. The problem isn't Stata's proprietary-ness; it's licensing cost on shared infrastructure. Different problem, different solution.

**Technical note:** you cannot legally redistribute Stata/MATLAB in a public Docker image. Containerization moves the licensing problem, doesn't dissolve it. The frozen image is the reproducible artifact; the Dockerfile bit-rots.

**Why this motivates agents:** toolset heterogeneity is exactly what makes Case 2 agents valuable. The messier the world, the higher the payoff. AI force-multiplication is the pragmatic substitute for coordination we'll never achieve.

---

## Same Principle, New Problem — Full Detail `[EXTENDED]`

**The principle hasn't changed.** Author solely responsible for anything producing relevant output. "The LLM wrote it" is not a defense.

**The mechanism is novel:**

| Tool | Deterministic? | Archivable? | Can editor re-run? |
|------|---------------|-------------|-------------------|
| GAUSS/Stata/R | Yes | Yes (license or version-pin) | Yes |
| Open-weight LLM (Llama, Mistral) | Yes (given seed+temp) | Yes (weights on disk) | Yes, with effort |
| Closed API (GPT-4, Claude) | No | No (silent updates, routing) | No guarantee |

"Just another tool" holds for rows 1-2. Breaks on row 3. Model-name-plus-version insufficient — `gpt-4o` today ≠ `gpt-4o` six months ago.

**On archivability:** Hugging Face has no DOI system. Will GPT-3.5 be accessible in 10 years? Unknown. Prefer open weights for any pipeline step generating research data — say so in journal policy.

**On hardware variance:** LLM output can vary by GPU model (floating-point accumulation order). Documented, not theoretical. Full reproducibility for open-weight models requires hardware specification. No norms yet. Open problem. `[BACKUP]`

**On API instability:** APIs change and break code. Version pinning mandatory — same as any other software dependency. LLM APIs move faster and break more often than most. "Latest" is not a version.

**Three-layer archive taxonomy:**

| Layer | What it is | Archive strategy |
|-------|-----------|-----------------|
| Pre-trained LLM | Base model weights | Hugging Face if open; model name+date if closed |
| Tuned/fine-tuned LLM | Your fine-tuned weights | Privacy constraints may apply |
| Analysis output | What the LLM returned | Always — include in package with DOI |

Most economists use layer 3 (prompt → output from closed API). Hardest to make reproducible, easiest to archive.

---

## The Integrity Problem — Full Detail `[EXTENDED]`

"Treat LLM output as raw data and include it" sounds clean. But it opens a verification gap that doesn't exist with conventional code.

With a human RA: write code, read it, run it, diff outputs. Accountability chain intact.

With an LLM pipeline: author holds prompt, API key, output. Editor sees only output. We cannot detect:
- Prompt tuning toward desired result ("give me scores where positive > 0.6")
- Cherry-picking across stochastic runs (run 20 times, keep the best)
- Outright fabrication (skip the LLM, write the CSV yourself)

Prompt + model doesn't uniquely determine output. Requiring the prompt is necessary but not sufficient.

**What we can actually ask for (disclosure checklist):** `[BACKUP — flash on slide, don't speak all 7]`
1. Full prompt (mandatory)
2. Raw output exactly as received (mandatory)
3. Model name, version, temperature/sampling parameters (mandatory)
4. If open-weight: archived weights or link (strongly preferred)
5. If closed API: state re-running may not reproduce exactly — note limitation in paper
6. Use Template README standard (social-science-data-editors.github.io) — extend to LLM pipelines
7. Execution logs capturing function calls, parameters, outputs

**On stochastic variability:** Vilhuber recommends running queries 10+ times, applying multiple imputation (Rubin/Reiter rules), reporting LLM-induced variance separately from sampling variance. Right standard for results hinging on LLM scoring/classification. `[BACKUP]`

**Empirical anchor:** Only 31.33% of replication packages in major economics journals have a proper controller script (Vilhuber 2025, n=8,280). We have not solved basic reproducibility. LLMs add complexity on top of an already shaky foundation.

---

## Is LLM Software or Data? `[EXTENDED]`

| Layer | Software or data? | Archive it? |
|-------|------------------|-------------|
| Pre-trained weights | Software (trained on data) | Yes if open-weight |
| Fine-tuned weights | Software + embedded data | Yes, with privacy caveats |
| LLM output (your results) | **Data** | **Always — this is your raw data** |

For most economists: the LLM output is data. Archive with DOI. The model is the instrument — describe it as you would any instrument.
