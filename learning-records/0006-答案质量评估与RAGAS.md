# Lesson 6: 答案质量评估与 RAGAS

The learner can now evaluate RAG answer quality using four RAGAS-inspired metrics (Faithfulness, Answer Relevancy, Context Precision, Context Recall) via LLM-as-a-Judge, and run end-to-end evaluation pipelines that surface answer-side issues invisible to retrieval metrics alone.

**New capabilities:**
- Understand the distinction between retrieval-side metrics (Recall@K, MRR from Lesson 4) and answer-side metrics (Faithfulness, Answer Relevancy)
- Implement `answer_evaluator.py` with four independent judge prompts, each producing structured JSON output (score + claims + reasoning)
- Implement Faithfulness evaluation: claim extraction → verdict per claim → proportion score, detecting hallucinations where the LLM contradicts or invents facts
- Implement Answer Relevancy evaluation: LLM judges whether the answer directly addresses the user query (0-5 scale)
- Implement Context Precision evaluation: per-document relevance judgment → weighted precision (rank-sensitive), measuring retrieval noise
- Implement Context Recall evaluation: claim extraction from answer → match against context → proportion score, measuring missing information
- Build a batch evaluation pipeline (`batch_evaluate()`) that runs retrieve → generate → judge across a golden dataset and produces a summary report with mean/std/min/max per metric
- Understand the four known LLM-as-a-Judge biases (position, self-enhancement, length, sycophancy) and mitigation strategies
- Know the difference between offline evaluation (golden dataset) and online evaluation (user feedback sampling)

**Key insight:** Good retrieval is necessary but not sufficient for good answers. Faithfulness is the "red line metric" — hallucinations can destroy user trust even when retrieval Recall@K is 1.0. The four metrics divide into two diagnostic groups: retrieval-side (Context Precision + Context Recall) and generation-side (Faithfulness + Answer Relevancy). A drop in one group but not the other pinpoints where the problem lies. LLM-as-a-Judge is cost-effective but has known biases — always use a different model family for judging than for generating, and periodically calibrate with human evaluation.

**Why:** After Lessons 4 (retrieval evaluation) and 5 (retrieval optimization), the learner has a well-tuned retrieval system. But a RAG system's end-user experience depends on the generated answer, not the intermediate retrieval. Without answer quality metrics, the learner could be optimizing retrieval while the LLM hallucinates — a fatal blind spot. This lesson closes that gap, completing the full RAG evaluation loop.

**How to apply:** The learner should now:
1. Run `answer_evaluator.py` against their existing golden dataset to establish a baseline for all four metrics
2. Make the answer_evaluator part of their development workflow — run before and after every system change
3. Check Faithfulness first when users report answer quality issues; if Faithfulness < 0.7, fix the generation prompt or model before touching retrieval
4. Use the "different model family" rule for judging vs generating to reduce self-enhancement bias
5. Periodically run manual spot-checks on low-Faithfulness samples to verify the judge's accuracy

**Skill gaps observed:**
- No experience with RAGAS library itself (this lesson implements from scratch using LLM-as-a-Judge) — may want to compare against RAGAS framework later
- No structured "golden answer" annotation for Answer Relevancy — the current approach relies entirely on LLM judgment
- No calibration experiments comparing LLM judge vs human judge scores
- No production monitoring setup — the batch evaluation is manual, not integrated into a CI/CD pipeline
- Context Precision/Recall scores may be noisy because they depend on the LLM judge's understanding of the domain

**Next suggested topic:** RAG refusal strategies (when to say "I don't know") and fallback mechanisms — the learner now has the evaluation framework to detect when the knowledge base lacks answers, and needs the system design to handle those cases gracefully.
