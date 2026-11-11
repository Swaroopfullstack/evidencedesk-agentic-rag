# EvidenceDesk architecture

PDF → Pages → Chunks → Embeddings → Chroma → Retrieval → Cited answer

The dedicated [Streamlit entry point](../app.py) calls the authenticated [FastAPI boundary](../src/portfolio/api.py). The [runtime](../src/portfolio/runtime.py) owns the SQLite store, checkpointer, Chroma lifecycle, provider client and concurrency controls. The main project implementation is [retrieval.py](../src/portfolio/retrieval.py).

The common runtime is vendored here. No package is fetched from the original portfolio repository. `uv.lock` resolves all external dependencies. Runtime data is owned by this checkout; private uploads and keys are ignored by Git.

HTTP caller identity comes from a server-trusted bearer mapping. Tenant/user constraints are added by application code, not accepted from model output. Structured model output still needs business validation. Pending human review, durable sessions, tool effects and graph state are separate concepts.

Read the [walkthrough](WALKTHROUGH.md), [configuration](CONFIGURATION.md), [API examples](API_EXAMPLES.md) and [deployment contract](DEPLOYMENT.md) for the applicable workflow and storage boundaries.
