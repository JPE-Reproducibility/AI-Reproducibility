# RunMyCode.org — Summary

**Paper:** Stodden, Hurlin, Pérignon (2012). "RunMyCode.org: a novel dissemination and collaboration platform for executing published computational results."  
**Source:** https://www.cs.columbia.edu/~julia/papers/stoddenetal10.pdf  
**Status: Dead.** Website no longer operational.

---

## What It Was

Launched January 2012. Non-profit, academic-run. Companion webpage per published paper: visitors could download code + data, or **execute the code directly in the cloud** and get results back in the browser (SaaS model). Authors uploaded their code; RunMyCode.org validated and hosted it.

Funded by Alfred Sloan Foundation and HEC Paris Foundation. Compute provided by CNRS TGE Adonis (France's National Center for Scientific Research grid infrastructure).

---

## How It Worked

1. Author creates companion webpage via step-by-step interface — uploads code, data, specifies inputs/outputs
2. RunMyCode.org Lab validates: (i) software compatibility, (ii) code robustness, (iii) security, (iv) CPU requirements and runtime
3. Code deployed on CNRS grid; companion page goes live
4. Visitors click "RunMyCode" → request sent asynchronously to backend → results (PDF + CSV) returned to browser

Supported languages at launch: **C++, Fortran, MATLAB, R, Rats.** "More will be added."

By August 2012: ~100 companion sites (90% economics/finance), 2,000+ executions, 15,000 visits.

---

## The Provisioning Model

Key dependency: **CNRS TGE Adonis** — a national grid computing infrastructure. RunMyCode.org did not own or operate the compute; it brokered it. The Distributed Task Manager (DTM) routed jobs to Adonis machines via Sun Grid Engine.

This is structurally identical to the Andrews-Shapiro ES VM proposal: a third party provisions compatible compute so that others don't have to. It worked as long as CNRS funded and maintained Adonis for this purpose.

---

## Why It Died (inferred — not stated in paper)

Paper doesn't discuss failure modes, written at launch. But the structural weaknesses are visible:

- **Software scope was already limited at launch** — only 5 languages, "more to come." Every new language requires new validation pipeline, new runtime support, new security review.
- **Compute dependency on CNRS Adonis** — external infrastructure, not owned by RunMyCode.org. When Adonis priorities shifted or funding ended, the execution capability died.
- **Validation bottleneck** — each companion page required manual IT review (20 min to several hours per submission). Doesn't scale.
- **100 companion pages in 6 months** — tiny relative to the volume of published computational economics. Network effect never materialized.
- **No sustainable funding model** — non-profit, grant-dependent, no journal mandate.

---

## Relevance to the Talk / Andrews-Shapiro

RunMyCode.org is the **historical proof of concept that the provisioning problem is real and hard.**

The Andrews-Shapiro ES VM proposal is RunMyCode.org rebuilt for a single journal cluster, with author-interactive setup instead of batch execution. It faces the same structural problem:

- Who pays for the compute?
- Who maintains compatibility with the software authors actually use?
- What happens when a language or library isn't supported?

CASD (France) solved this with a €multi-million annual budget and a dedicated engineer. RunMyCode.org tried to solve it with CNRS grid access and volunteer validation. It didn't last.

The lesson is not that the idea is wrong — it's that **the provisioning problem requires institutional commitment at a scale most journals cannot sustain.** SIVACOR's answer is to restrict scope aggressively (curated images only). That's the only version that appears to be surviving.

---

## Useful Quote (Stodden et al.)

> "Complicated computing environments pose an issue to reproducible research, but they are also a case where reproducibility is more important than the simpler case. A more complex computing environment has more possibilities for failure and fewer opportunities to check one's work."

True in 2012. Still true. RunMyCode.org tried to abstract away that complexity centrally. The abstraction collapsed.
