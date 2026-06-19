# Lesson 8: RAG 技术栈全景图

The learner now has a comprehensive global view of the RAG technology landscape — the first step in Module 2 ("building a quality foundation") after completing the full RAG core pipeline in Module 1 (L1-L7).

**New capabilities:**
- Understand the **full RAG tech stack map**: 7 pipeline stages (Chunk → Embed → Store → Retrieve → Rerank → Generate → Evaluate) × optional technologies for each
- Compare **4 programming languages** for RAG: Python (ecosystem leader), JS/TS (full-stack), Go (high-performance services), Java (enterprise) — with Python being the clear winner for 90% of RAG use cases
- Compare **5 LLM framework categories**: LangChain (largest ecosystem but heavy), LlamaIndex (RAG-native), Native SDK (lightweight, fully controllable), Haystack (enterprise NLP), Dify/FastGPT (low-code) — understand the "native first, framework later" strategy
- Understand **Pydantic's role as the invisible foundation** of the Python RAG ecosystem — used by LangChain/LlamaIndex/FastAPI for data modeling, and via Instructor for LLM structured output extraction. Pydantic v2's Rust core (pydantic-core) makes validation 5-50x faster than v1.
- Compare **7 vector databases** across 5 dimensions (scale, latency, filtering, ops cost, Chinese support): Chroma, Milvus, Qdrant, Weaviate, Pinecone, PGVector, Elasticsearch
- Classify **embedding models** into 4 categories: closed API (OpenAI/Cohere), Chinese open-source (BGE-zh/m3e/stella/GTE), multilingual open-source (BGE-M3/E5/Jina), local deployment tools (Ollama/Infinity/Xinference)
- Classify **generation models** into 3 categories: global closed (GPT-4o/Claude/Gemini), Chinese closed (通义/DeepSeek/文心/Kimi), open-source local (Qwen/Llama/DeepSeek via Ollama/vLLM)
- Apply a **4-step decision framework**: vector database (hardest to change) → embedding model (quality ceiling) → generation model (quality upper bound, easiest to swap) → framework (last decision)
- Complete a **hands-on Chroma → Qdrant migration** using an adapter pattern (`vector_store.py` with factory function), experiencing the actual code change volume (~60 lines of new adapter code, zero caller changes)
- Evaluate **5 scenario-specific tech stack recommendations**: personal learning, SME customer-service Q&A, large enterprise knowledge base, high-compliance (finance/military), overseas multilingual

**Key insight:** The most important realization in this lesson is that technology choices are *trade-offs*, not *absolutes*. The lesson pushes back against three common fallacies: (1) "LangChain is mandatory for RAG" — most features you need are 50 lines of native SDK code; (2) "Milvus is the only production vector DB" — Qdrant runs in production just fine for most mid-scale scenarios; (3) "Local models are always cheaper" — the GPU cost (¥30-50k/month for an A100) often exceeds API costs for all but the largest deployments. The decision framework's 4-step priority order (vector DB → embedding → generator → framework) is the lasting mental model: choose infrastructure before models, models before orchestration.

**Why:** After completing L1-L7 with Chroma + OpenAI, the learner has a working system but faces a wall of choice questions: "Should I switch to LangChain?" "Is Chroma good enough for production?" "Which Chinese embedding model should I use?" Without a global map, each question is answered in isolation, leading to inconsistent architecture. This lesson provides the map — so every subsequent choice in Module 2 (L9 refusal, L10 conversation, L11 prompt, L12 embedding) can be made with full context of the alternatives and their trade-offs.

**How to apply:**
1. Draw your personal RAG tech stack map (current choices + alternatives) for your own project
2. Try the Chroma → Qdrant migration exercise to experience vector DB switching cost first-hand
3. When facing any RAG technology decision, apply the 4-step framework: "Have I locked in my vector DB first?"
4. Refer back to this lesson's cheat sheet as a quick reference whenever exploring a new RAG tool
5. For team discussions about tech stack, use the scenario-to-stack mapping table as a starting template

**Skill gaps observed:**
- No hands-on experience with Milvus, Weaviate, or Pinecone (only Chroma and Qdrant are code-exercised)
- No experience with non-English embedding models for production deployment
- No practical comparison of LangChain vs Native SDK in real production load
- No cost modeling for different scale levels (this lesson gives rough numbers but no per-user/per-query cost calculator)
- No experience with LangSmith or other observability platforms yet

**Next topic:** L9 — RAG 拒绝策略与兜底机制 (Refusal & Fallback). Now that the learner has a full tech stack map, the next step is to implement the most critical quality safeguard: knowing when NOT to answer.
