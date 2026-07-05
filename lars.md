# Summary: Vilhuber — "Reproducibility in an AI World"

Source: https://larsvilhuber.github.io/reproducibility-for-llm/presentation/
Author: Lars Vilhuber (Cornell, AEA Data Editor)
Funding: Alfred P. Sloan Foundation (G-2023-22326)

---

## Core Thesis

> "None of this is AI-specific, but the difficulties may be magnified when using LLMs in research."

LLM reproducibility problems are not categorically new. They are standard computational reproducibility problems, scaled up and with some novel wrinkles. The framework is: same principles, harder execution.

---

## Key Definitions

- **Computational reproducibility**: re-run code on shared data, validate outputs
- **Replicability**: ultimate goal (independent team, same question) — this document focuses on the narrower computational reproducibility
- **LLM**: model trained for a specific purpose (offline or on-premise)
- **AI service**: online deployment of LLM (GPT, Claude, etc.) — the key distinction is offline vs. online, and who controls the weights

---

## Taxonomy of LLM Use in Research

Three-layer architecture, each requiring different preservation strategy:

1. **Pre-trained LLM** — version control issues, licensing concerns, may not be archivable
2. **Tuned/fine-tuned LLM** — privacy constraints (training data), preservation challenges
3. **Analysis data / LLM output** — shareable via institutional repositories (Dataverse, etc.)

Implication: what you can archive and what you must treat as "raw data to include" depends on which layer generated your research output.

---

## The Empirical Reality (2025, 384 papers assessed)

- 38% of papers had unrestricted data
- 62% had access restrictions
- 45% of restricted datasets were obtainable via institutional arrangements
- Only **31.33%** of replication packages in major econ journals contained a proper controller/master script

Case study: Korinek (2023) LLM replication package had:
- No requirements.txt
- Deprecated OpenAI API calls (`ChatCompletion` removed in openai v1.x)
- No environment variable handling for API keys
- No version pinning (`openai==0.28` vs current `openai==1.57.4`)

---

## Main Problems

### Inherent LLM variability
LLMs produce probabilistic output. Same prompt, same model, same parameters → similar but not bit-identical output. This is structural, not a bug.

### API instability
APIs change. Code written for `openai==0.28` breaks on `openai==1.57`. "Latest" is not a version. Version pinning is mandatory.

### Model non-persistence
- Hugging Face has no DOI system, no guaranteed long-term preservation
- Closed API models change silently even at a fixed "version" name
- Commercial software licenses expire or disappear
- Open-source repos (CRAN, PyPI) have failure points too

### Hardware variance
LLM output can vary by hardware (floating-point accumulation order, GPU model). Specifying hardware is part of reproducibility for open-weight models.

---

## Recommended Solutions

### Documentation
- Use **Template README** standard (social-science-data-editors.github.io/template_README)
- Document all data provenance, transformations, computational requirements
- Include prompts as metadata — treat prompts like code

### Code
- Master/controller script: runs everything top-to-bottom, no errors, hands-off
- Never hardcode API keys — use environment variables
- Pin all versions explicitly (`openai==0.28`, not `openai>=0.28`, never `latest`)
- Use virtual environments (venv, conda, renv)

### LLM-specific: handling variability
- Run queries **10+ times**, capture full output distribution
- Apply **multiple imputation** (Rubin/Reiter rules) to handle LLM-induced variation
- Report LLM-induced variance separately from sampling variance

### Logging
- Generate execution transcripts/log files documenting function calls, parameters, outputs
- Essential when data is proprietary or runs are expensive to reproduce

### Cost and metadata
- Document computational costs
- Record hyperparameters, temperature, random seeds
- Consider subsample testing for expensive operations
- Compare results across open-source alternatives as robustness check

### Preservation
- Raw data + LLM output → Harvard Dataverse or institutional repository (get a DOI)
- Open-weight models → Hugging Face (with caveats on permanence)
- Closed API → document model name, version, date accessed, all parameters; acknowledge re-run may not reproduce exactly

---

## Policy Positions

1. Journals should require controller scripts in all replication packages
2. Metadata documentation standards should be mandatory (Template README)
3. Computational cost transparency required
4. Prefer open-weight models for any pipeline step generating research data
5. Institutional support needed for model preservation (Hugging Face is insufficient)
6. Researchers should test packages on machines they do not control before submission

---

## Central Principle: Computational Empathy

"Remember your own difficulties getting this to work — then imagine someone with less context."

Reproducibility is not about what works on your machine. It is about what works on an unfamiliar machine, by an unfamiliar person, years later.

---

## What Vilhuber Does NOT Address

- The **integrity/verification gap**: cannot detect cherry-picking across stochastic runs or prompt-tuning toward desired results
- **Tolerance standards**: what counts as "close enough" when re-running an LLM pipeline?
- **Journal budget constraints**: assumes environment provisioning is solvable — doesn't engage with who pays
- **Author accountability chain**: treats LLM like any other tool without discussing whether "the LLM did it" shifts responsibility norms

These are gaps where a data editor's perspective (e.g., JPE) adds to Vilhuber's framework.
