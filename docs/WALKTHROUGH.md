# EvidenceDesk walkthrough

## What to demonstrate

An actual document pipeline: PDF → page Documents → overlapping child chunks → embeddings → Chroma → owned retrieval → one evidence set → answer/citations.

### Walk through it

1. Upload the NIST PDF and ask about Govern, Map, Measure and Manage.
2. Inspect the original page number and exact evidence text.
3. Select **vector** for similarity; **mmr** for relevance/diversity; **hybrid** for BM25/vector reciprocal rank fusion.
4. Try **multi_query**, **parent**, **compression**, and **self_query**. Demo query expansion is explicitly deterministic; live expansion/self-query use structured LLM output.
5. Ask an unrelated question. Inspect the no-answer path instead of interpreting a missing answer as a system crash.
6. Change retrieval k in server configuration, restart, and compare evidence. This control is separate from generation top-k.
7. Enable Hugging Face embeddings, restart, and re-upload. Backend/model namespaces prevent accidental mixing of incompatible vectors.
8. Log in as Bob. Alice's indexed documents must not be returned.

### The seven strategies

| Strategy | Mechanism | Important limit |
|---|---|---|
| vector | Cosine-distance Chroma lookup | Similarity is not probability |
| hybrid | BM25 + dense/lexical vector weighted rank fusion | Rank scores are relative |
| mmr | Relevance/diversity over fetched candidates | Fetch count controls candidate work |
| multi_query | At most three extra query variants, stable chunk dedup | Expansion can miss intent |
| parent | Child-vector lookup, persisted parent page expansion | Parent context may be larger |
| compression | Extract relevant sentences from returned chunks | Extractive, not an LLM reranker |
| self_query | Typed source/page criteria AND trusted access constraints | Criteria never replace authorization |

### Read and modify

[retrieval.py](../src/portfolio/retrieval.py) owns loaders, filters, ranking, parent persistence and answer validation. [evaluation.py](../src/portfolio/evaluation.py) measures three public smoke cases. The metric is expected-term recall within retrieved context, not a general relevance/faithfulness benchmark.

Add one hand-labeled query with an expected page, one no-answer question and one adversarial instruction inside a document. Evaluate the actual evidence and answer separately. A syntactically valid citation is necessary but does not prove every generated statement follows from that citation.
