# LLM RAG Enterprise Knowledge Base Resources

## Knowledge

- [Paper: "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks" - Lewis et al.](https://arxiv.org/abs/2005.11401)
  Foundational RAG paper. Use for: the core idea that a generation model can be grounded by retrieved external memory instead of relying only on model parameters.
- [OpenAI Docs: Vector embeddings](https://platform.openai.com/docs/guides/embeddings)
  Official explanation of embeddings and their search use cases. Use for: understanding how text becomes vectors and why similarity search works.
- [OpenAI API Reference: Create embeddings](https://platform.openai.com/docs/api-reference/embeddings/create)
  Official endpoint reference. Use for: exact embedding request/response shape, model choices, token limits, and output vector format.
- [OpenAI Docs: Text generation](https://developers.openai.com/api/docs/guides/text)
  Official guide for model text generation through the Responses API. Use for: basic LLM API calls and prompt structure.
- [Chroma Docs: Getting Started](https://docs.trychroma.com/docs/overview/getting-started)
  Official Chroma quickstart. Use for: local vector database basics, collections, adding documents, and querying similar documents.
- [Chroma Docs: Adding Data](https://docs.trychroma.com/docs/collections/add-data)
  Official Chroma collection ingestion guide. Use for: document IDs, metadata, and collection behavior.

## Wisdom (Communities)

- [OpenAI Developer Community](https://community.openai.com/)
  Use for: practical API questions, implementation tradeoffs, and error troubleshooting.
- [Chroma Discord](https://discord.gg/MMeYNTmh3x)
  Use for: Chroma setup, retrieval behavior, persistence, and production migration questions.

## Knowledge (continued - evaluation & re-ranking)

- [Stanford IR Book: Evaluation of ranked retrieval results](https://nlp.stanford.edu/IR-book/html/htmledition/evaluation-of-ranked-retrieval-results-1.html)
  Comprehensive explanation of Recall@K, MRR, NDCG, and precision-based metrics with worked examples. Use for: understanding retrieval evaluation fundamentals and metric calcuations.
- [Sentence-Transformers: Cross-Encoder](https://www.sbert.net/examples/applications/cross-encoder/README.html)
  Official documentation for Cross-Encoder re-ranking models. Use for: implementing two-stage retrieval with bi-encoder → cross-encoder pipeline.
- [MTEB Leaderboard](https://huggingface.co/spaces/mteb/leaderboard)
  Massive Text Embedding Benchmark — ranking of embedding models across 100+ datasets including retrieval, clustering, classification. Use for: selecting the right embedding model for a specific task (e.g., Chinese retrieval, legal, medical).
- [Cohere: What is re-ranking?](https://docs.cohere.com/docs/reranking)
  Official guide on re-ranking concepts, why it matters, and how it differs from embedding-based retrieval. Use for: understanding the theory behind two-stage retrieval and the role of re-rankers.
- [LangChain: How to do re-ranking](https://python.langchain.com/docs/how_to/contextual_compression/)
  Code-oriented guide to implementing re-ranking with LangChain's ContextualCompressionRetriever. Use for: alternative implementation approach (if learner chooses LangChain later).

## Knowledge (continued - hybrid retrieval & query transformation)

- [BM25: The Next Generation of Text Retrieval (Robertson & Zaragoza, 2009)](https://www.academia.edu/download/69711288/Foundations_of_TF-IDF_and_BM25_Explained.pdf)
  Foundational BM25 paper. Use for: understanding the TF-IDF evolution, BM25's three tunable parameters (k1, b, delta), and why BM25 still outperforms dense retrieval on exact-match and OOD queries.
- [Elasticsearch: BM25 as the default similarity](https://www.elastic.co/blog/practical-bm25-part-1-how-shards-affect-relevance-scoring-in-elasticsearch)
  Practical guide to BM25 in production search systems. Use for: understanding how BM25 is configured in enterprise search engines and how shard count affects scoring.
- [Rank-BM25: A two line implementation of BM25 in Python](https://github.com/dorianbrown/rank_bm25)
  Lightweight Python library implementing BM25Okapi, BM25L, and BM25Plus. Use for: quick prototyping of keyword retrieval without Elasticsearch dependency.
- [Paper: "Reciprocal Rank Fusion outperforms Condorcet and individual Rank Learning Methods" (Cormack et al., 2009)](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf)
  Original RRF paper. Use for: understanding why simple score averaging fails for heterogeneous rankers and how RRF's fixed constant (k=60) provides robust fusion across different scoring distributions.
- [Paper: "Precise Zero-Shot Dense Retrieval without Relevance Labels" - HyDE (Gao et al., 2022)](https://arxiv.org/abs/2212.10496)
  Hypothetical Document Embeddings paper. Use for: query expansion by generating a hypothetical document via LLM, then using its embedding for retrieval — particularly effective for short or ambiguous queries.
- [Paper: "Query Expansion by Prompting Large Language Models" (Jagerman et al., 2023)](https://arxiv.org/abs/2305.03653)
  Query expansion via LLM prompting. Use for: generating multiple query variants from a single user query to improve recall coverage — a low-effort, high-impact technique.
- [LangChain: How to do query transformation](https://python.langchain.com/docs/tutorials/qa_chat_history/#multi-query)
  Code examples of multi-query retrieval, query rewriting with history, and HyDE. Use for: alternative implementation reference when adopting LangChain.

## Knowledge (continued - answer quality evaluation & RAGAS)

- [RAGAS Official Documentation](https://docs.ragas.io/)
  Full framework documentation for RAG evaluation metrics. Use for: exploring the complete RAGAS metric suite beyond the four core metrics implemented in the lesson (including Aspect Critique, Score, and pairwise Comparison metrics).
- [Paper: "RAGAS — Automated Evaluation of Retrieval Augmented Generation" — Es et al., 2023](https://arxiv.org/abs/2309.15217)
  The original RAGAS paper defining Faithfulness, Answer Relevancy, Context Precision, and Context Recall. Use for: understanding the formal mathematical definitions, the claim extraction methodology, and the empirical validation across five different RAG configurations.
- [Paper: "Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena" — Zheng et al., 2023](https://arxiv.org/abs/2306.05685)
  Foundational paper on using LLMs as evaluators. Use for: understanding the four known biases (position, self-enhancement, length, sycophancy), strong/weak agreement patterns with human judges, and best practices for LLM-based evaluation.
- [OpenAI Cookbook: Using GPT-4 as an evaluator](https://cookbook.openai.com/examples/evaluation/how_to_eval_abstractive_summarization)
  Practical cookbook example of LLM-as-a-Judge for summarization quality. Use for: prompt engineering patterns for evaluation prompts (scoring rubrics, chain-of-thought before verdict, structured output schemas).
- [Anthropic: Building effective evaluations](https://docs.anthropic.com/en/docs/build-with-claude/evaluations)
  Anthropic's official guide on building evaluations for LLM applications. Use for: understanding eval design principles, sampling strategies, and when to use LLM-as-a-Judge vs human evaluation vs automated metrics.

## Gaps

- Need to add production architecture references for enterprise permissions, tenant isolation, monitoring, and cost controls after the demo stage.
