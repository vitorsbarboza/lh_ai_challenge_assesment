# Loom Video Script — Hallucination Research Proposal

**Duration:** 5–8 minutes  
**Style:** Simple English. Short sentences. Concrete examples. You talk slowly — that's fine, it sounds more confident.  
**Format:** Screen-share GitHub markdown (diagrams render natively) + face cam  
**Rule:** Don't memorize. Read each block 2x before recording, then say it in your own words.

---

## INTRO — 0:00 to 0:40
**Show on screen:** Title + Executive Summary

**Say this:**

"Hi, I'm Vitor. Thank you for the opportunity.

Today I will show my approach to reduce hallucinations in long documents — 5 to 20 pages — for about 10,000 users.

My main idea is simple: **one single LLM call cannot write 20 correct pages.** The model will forget things, invent things, and contradict itself. So we need a system — not just one prompt — we need multiple agents that write, check, and fix.

Let me show you how."

---

## SECTION 1 — PROBLEM DECOMPOSITION — 0:40 to 2:30
**Show on screen:** Scroll to the Taxonomy Diagram (the big tree)

**Say this:**

"Most people say 'hallucination' like it is one problem. It is not. I found **7 different types**. Let me give you examples for each one.

*(Point to Grounding Errors on the diagram)*

**First group: Grounding Errors.** The model fails against the source document.

- **Fabrication.** The source says nothing about a CEO named 'John Silva.' But the model writes: 'According to CEO John Silva...' — he does not exist. The model invented him.

- **Faithfulness Distortion.** The source says: 'Revenue fell 12%.' The model writes: 'Revenue grew 12%.' Same number, same period — but the meaning is opposite. The model had the right data, but distorted it.

- **Citation Hallucination.** The model writes: 'As shown in the report by Smith et al., 2024.' But that report does not exist. The model learned to write citations as a pattern, not as a real reference.

*(Point to Coherence Errors)*

**Second group: Coherence Errors.** The model contradicts itself.

- **Intrinsic Contradiction.** On page 2, the model writes: 'The project cost was 5 million.' On page 12, it writes: 'The project cost was 3 million.' Same document, different numbers. This happens because the model loses attention over long texts — we call this **attention drift.**

- **Entity Conflation.** There are two companies in the source: Company A and Company B. The model mixes them — it gives Company A's CEO to Company B. Like confusing two people with similar names at a party.

*(Point to Reasoning Errors)*

**Third group: Reasoning Errors.** The model fails at logic.

- **Compositional Hallucination** — this one is the most dangerous. Let me explain. The model writes: 'Company X acquired Company Y in 2024.' Company X exists — true. Company Y exists — true. But the acquisition **never happened.** Every piece is correct. The combination is false. And this is the blind spot — if you check each fact individually, they all pass. You need to check the relationship between them.

- **Over-generalization.** The source says: '3 users complained.' The model writes: 'Most users are dissatisfied.' It turned a small fact into a big claim.

- **Temporal Displacement.** The source has data from 2023 and 2025. The model writes a 2023 event as if it happened in 2025. It mixes up the timeline.

*(Scroll to the Risk Priority table)*

For 10,000 users writing research reports, the worst ones are **Fabrication and Citation Hallucination** — because users trust the citations. They don't double-check. If a citation is fake, the user sends a report with a lie inside it."

---

## SECTION 2 — EVALUATION RUBRIC — 2:30 to 4:00
**Show on screen:** Scroll to the ACV Pipeline Diagram

**Say this:**

"Now, how do we know if our system actually works? This is the hardest part. The assessment says this is the most important section. I agree.

The problem is: **there is no 'correct answer' for a 20-page document.** There is no test with a green checkmark. So how do we measure success?

My approach is called **Atomic Claim Verification.** Let me explain with an example.

*(Point to the pipeline diagram — step by step)*

Imagine the model wrote this sentence: 'Amplify IT reported 20% revenue growth in Q3 2025 and expanded to 3 new markets.'

**Step 1 — Claim Extraction.** We break this into atoms:
- Atom 1: 'Amplify IT reported 20% revenue growth.'
- Atom 2: 'This was in Q3 2025.'
- Atom 3: 'Amplify expanded to 3 new markets.'

**Step 2 — Vector DB Lookup.** For each atom, we search the source documents. Did the source say this?

**Step 3 — LLM Judge.** A strong model — like GPT-4 or Claude — reads the atom and the source chunk, and classifies:
- ✅ **Supported** — the source confirms it.
- ❌ **Contradicted** — the source says the opposite.
- ⚠️ **Unverifiable** — the source says nothing about it. This is a hallucination.

So our main metric is the **Faithfulness Score**: how many atoms are Supported divided by the total. If we get 95% Supported — good. If we get 70% — the system is hallucinating too much.

*(Scroll to the trade-offs table)*

But there is a cost problem. Checking every sentence of 20 pages is expensive — about 50 cents to 1 dollar per document in LLM tokens. For 10,000 users, that adds up fast.

**My solution:** full verification on critical sections — like conclusions and key findings. For the rest, we sample. Check 1 in every 5 paragraphs. This reduces cost to about 10 cents per document.

And to make sure the LLM Judge is reliable, we take 1 to 2 percent of documents and give them to human reviewers. We compare the human score with the LLM score. If the correlation is above 0.85 — the Judge is trustworthy. If it drops below — we know something is wrong."

---

## SECTION 3 — ARCHITECTURE — 4:00 to 6:00
**Show on screen:** Scroll to the Multi-Agent Architecture Diagram

**Say this:**

"Now let me show the architecture. This is a **Multi-Agent System** with 4 phases.

*(Point to each box in the diagram as you explain)*

**Phase 1 — The Planner.** The user sends a query and uploads source documents. The Planner does NOT write the document. It creates an outline — like a table of contents — with specific topics for each section. Think of it as the architect who draws the blueprint before construction begins.

**Phase 2 — The Section Writer.** This agent writes ONE section at a time. Not the whole document. Just one piece. And here is the important detail: **it is stateless.** It sees only the outline and a short summary of the previous section. It does NOT see the full raw text of previous pages. Why? Because if the model wrote a wrong number on page 3 and reads it again on page 10, it will think that wrong number is correct. We break the chain.

**Phase 3 — The Critique Agent.** This is the most important agent. It does NOT write anything. It only checks. It takes the section that the Writer produced, extracts the atomic claims, searches the source documents, and scores each claim. If the Faithfulness Score is below our threshold — for example, below 90% — it sends the section back to the Writer with a message: 'Hey, claim 3 is not supported by any source. Fix it.' The Writer rewrites. The Critique checks again. This is an **iterative loop** — write, check, fix, check again.

**Phase 4 — The Editor.** After all sections pass the Critique, the Editor merges them into one document. It fixes transitions between sections and does a final consistency check.

*(Scroll to the Alternatives table)*

I also considered other approaches.

**Alternative 1: Just use a big context window.** Put all documents into Gemini 1.5 or Claude with one prompt. Why I rejected it: even with 1 million tokens of context, the model still loses information in the middle — the 'Lost in the Middle' problem. Also, if something goes wrong, you cannot debug it. You don't know which page caused the error.

**Alternative 2: Fine-tune the model.** Train the model on our specific documents. Why I rejected it: fine-tuning teaches the model style and format, but it does NOT guarantee facts. You cannot fine-tune reliability into a model.

**The core trade-off:** my architecture is **slow.** A 20-page document with the Critique loop takes 5 to 10 minutes. A single prompt takes 30 seconds. But for research reports, **one wrong fact in a sent report is more expensive than 10 minutes of waiting.** We handle this with streaming — the user sees the outline in 5 seconds, and then sections appear one by one: 'Drafting... Verifying... Done.'"

---

## SECTION 4 — VALIDATION PLAN — 6:00 to 7:15
**Show on screen:** Scroll to the Gantt chart → then to Kill Switches table

**Say this:**

"If I had two weeks to validate this, here is what I would do.

*(Point to the Gantt chart)*

**First thing I test: the Critique Agent — alone.** I create 50 synthetic documents. I take real source texts and I inject 5 false facts into each one — wrong dates, invented names, flipped numbers. Then I run the Critique Agent and ask: did it catch the lies?

If it catches 4 out of 5 — good. If it catches only 2 out of 5 — that means Recall is below 60%, and **the whole architecture fails.** The Critique Agent is the foundation. If the foundation is weak, nothing else works. That is why I test it first.

**Second test:** I compare the LLM Judge with human reviewers on the same 50 documents. If they agree — correlation above 0.85 — the automated Judge is reliable. If they disagree — I need to change the Judge model or add humans in the loop.

**Third test:** Shadow Mode. I run the system next to the current production system — silently, without showing users. I measure real latency and real cost with real documents.

*(Scroll to Kill Switches)*

I also defined clear stopping rules:
- If latency exceeds 15 minutes for one document → simplify the architecture.
- If cost exceeds 2 dollars per document → switch the Critique to a smaller, cheaper model.

*(Scroll to Open Assumptions)*

And I want to be honest about what I don't know.

**My biggest assumption:** I assume that checking a fact is easier than writing it. The Critique Agent only needs to say 'yes, this is in the source' or 'no, it is not.' That should be simpler than writing creative text. But if verification is just as hard as generation — if the Judge also hallucinates — then this architecture does not work. That is the first thing I validate.

**My second unknown:** What happens when the user asks for *new ideas* — not just a summary, but a synthesis? If I check everything strictly against the source, I might flag valid insights as hallucinations because they are new. I would need a 'novelty tolerance' setting — and that is still an open research question."

---

## CLOSE — 7:15 to 7:40
**Show on screen:** Scroll back to the top / Executive Summary

**Say this:**

"To summarize:

- I identified **7 types** of hallucination, not just one.
- I measure success with **Atomic Claim Verification** — break text into facts, verify each one.
- I use a **Multi-Agent System** where a Critique Agent checks every section before it goes to the user.
- And I test the Critique Agent first — because if the checker is broken, nothing else matters.

Thank you for the opportunity. I look forward to discussing this with the team."

---

## Before You Record — Checklist

- [ ] Open the GitHub repo in Chrome (diagrams render there)
- [ ] Zoom browser to 125% so text is readable on video
- [ ] Turn off notifications (phone + desktop)
- [ ] Do one practice run reading through this script (no recording)
- [ ] On the real recording: **don't read word by word** — just glance at the bold phrases and say it naturally
- [ ] Total target: **7 minutes** (leaves 1 min buffer under the 8-min cap)
- [ ] Upload to [loom.com](https://loom.com) and paste the link in the markdown
