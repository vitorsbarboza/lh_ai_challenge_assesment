# Loom Video Script — Hallucination Research Proposal

**Duration:** 5–8 minutes  
**Format:** Screen-share the GitHub markdown (diagrams render natively) + face cam  
**Tone:** Conversational, not rehearsed. Use this as bullet points, NOT a teleprompter.

---

## INTRO — 0:00 → 0:30
**Show:** Title + Executive Summary

> "Hi, I'm Vitor. I'm going to walk you through my approach to detecting and reducing hallucinations in long-form AI writing — documents of 5 to 20 pages, serving 10,000 users.
>
> My core thesis: **this is not a prompt engineering problem, it's a systems engineering problem.** A single LLM call cannot reliably produce 20 factually correct pages. We need a Compound AI System with verification built into the loop."

---

## SECTION 1 — KEY INSIGHT — 0:30 → 2:00
**Show:** Taxonomy Diagram → scroll to Risk Priority table

**Key phrases to hit:**
- "Most people treat hallucination as one thing. I found **7 distinct failure modes** across 3 categories."
- Point to **Grounding Errors**: fabrication, faithfulness distortion, citation hallucination
- Point to **Coherence Errors**: intrinsic contradiction, entity conflation
- Point to **Reasoning Errors** — pause here:

> "**Compositional Hallucination** is the most interesting one — and the blind spot. Every individual fact is true, but the combination is false. 'Company X acquired Company Y in 2024' — both companies exist, but the acquisition never happened. Atomic verification misses this because each atom passes independently."

- Scroll to risk table:

> "For research reports, **P0 is fabrication and fake citations** — users won't double-check citations. They trust the system."

---

## SECTION 2 — RUBRIC DEFENSIBILITY — 2:00 → 3:30
**Show:** ACV Pipeline Diagram → Trade-offs table

**Key phrases to hit:**
- "The assessment says this is the most important component. I agree — **defining 'solved' is harder than proposing a solution.**"
- Point to the pipeline:

> "**Atomic Claim Verification**: decompose generated text into individual falsifiable statements, verify each against the source. The LLM Judge classifies each as Supported, Contradicted, or Unverifiable."

- "Why is this defensible?"

> "Because it gives us a **concrete number** — a Faithfulness Score — not a vibe. And we calibrate against human annotators. If Pearson correlation drops below 0.85, we know our metrics are broken."

- Scroll to trade-offs:

> "Full ACV on 20 pages costs $0.50–$1.00 per doc. So we **sample** body text and do full verification only on critical sections."

---

## SECTION 3 — CORE TRADE-OFF — 3:30 → 5:30
**Show:** Multi-Agent Architecture Diagram → Alternatives table

**Walk through each phase (point to diagram):**

1. **Planner** → "Creates the outline. Does NOT write prose. Secures macro-coherence first."
2. **Section Writer** → "Writes one section at a time. **Stateless** — sees Plan + Summary, not raw previous pages. Prevents error compounding."
3. **Critique Agent** → "The core. Does NOT write — only verifies. Extracts claims, runs ACV. If score is below threshold → sends back to Writer."
4. **Editor** → "Merges, resolves style, final consistency."

**Then the trade-off:**

> "I considered **massive context window zero-shot** — dump everything into Gemini 1.5 or Claude. Rejected: Lost-in-the-Middle still applies, can't debug where hallucination started."
>
> "The trade-off that most influenced my choice: **I'm trading latency for accuracy.** This takes 5–10 minutes instead of 30 seconds. But for research reports, **one wrong fact costs more than 10 minutes of wait time.**"
>
> "We mitigate UX with streaming — outline appears instantly, sections show Drafting → Verifying → Done."

---

## SECTION 4 — WHAT I'D TEST FIRST — 5:30 → 7:00
**Show:** Gantt Roadmap → Kill Switches table → Open Assumptions

> "**Day one, I test the Critique Agent in isolation.** Feed it known hallucinated summaries. Can it catch them? If recall is below 60%, the entire architecture fails. **That's the load-bearing wall.**"

- "Second: LLM Judge vs. human correlation. Below 0.7 → pivot to hybrid with humans in the loop."

**Scroll to Kill Switches:**

> "Clear kill switches: latency > 15 min → simplify. Cost > $2/doc → switch to fine-tuned small model."

**Scroll to Open Assumptions:**

> "**My biggest assumption: verification is easier than generation** — the Judge is more accurate at catching errors than the Writer is at making them. If that's false, the architecture collapses."
>
> "The second unknown: **synthesis vs. summary**. If users want novel insights, strict faithfulness checking flags valid reasoning as hallucination. We'd need a 'novelty tolerance' parameter — that's a research question, not an engineering task."

---

## CLOSE — 7:00 → 7:30
**Show:** Scroll back to top / Executive Summary

> "To summarize: 7 hallucination types, atomic claim verification as the evaluation backbone, a multi-agent system with a Critique loop, and a validation plan that tests the Judge first — because everything depends on it.
>
> Thanks for the opportunity. Happy to discuss further."

---

## Recording Tips

- [ ] Open GitHub repo page (diagrams render natively)
- [ ] Zoom browser to ~125% so text is readable
- [ ] Scroll slowly — let each diagram be visible ~10–15 sec
- [ ] Don't read this script — use it as bullet points
- [ ] Record at [loom.com](https://loom.com) (free tier works)
- [ ] Aim for 6–7 minutes (buffer under the 8-min cap)
- [ ] If you hit 7:00 → wrap up immediately
