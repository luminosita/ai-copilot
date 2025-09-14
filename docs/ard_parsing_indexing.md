# Architecture Decision Record — Parsing & Indexing Stack for RAG 2.0

## Status
Accepted

## Context
The **AI Copilot** requires reliable ingestion of Markdown and related documents into a knowledge base. Parsing and indexing must preserve metadata, structural fidelity, and contextual relationships, enabling **RAG 2.0** features such as multi-hop retrieval, provenance, and graph reasoning.

## Decision
We adopt a **Python-first stack** with the following components:

### Parsing
- **`python-frontmatter`** — Extract YAML/TOML frontmatter into structured metadata.
- **`markdown-it-py` + `mdit-py-plugins`** — CommonMark-compliant AST parsing with support for tables, footnotes, frontmatter.
- **Header-aware chunking** — Implemented with LlamaIndex’s `MarkdownNodeParser` or custom AST traversal to ensure semantic boundaries (headers, horizontal rules) are respected.
- **Optional Pandoc integration** — For heterogeneous inputs (Docx, LaTeX, HTML).

### Indexing
- **LlamaIndex** as the core framework for:
  - Vector indexing (embeddings).
  - Keyword/sparse indexing.
  - Graph indexing (parent-child, references).
- **Vector store backend** (e.g., FAISS for local prototyping, enterprise vector DB for scale).
- **Metadata-aware retrieval** — frontmatter metadata and header paths stored with nodes.
- **Graph edges** — based on header hierarchy and cross-document references.

### Orchestration & Optimization
- **LangChain** for workflow orchestration and retrieval augmentation.
- **DSPy** for phase-aware retrieval tuning and Self-RAG critique.

## Alternatives Considered
1. **JS-first (remark)**  
   - Mature AST tooling but introduces cross-runtime complexity for Python-based pipeline.
   - Rejected for simplicity and alignment with LlamaIndex (Python-first).

2. **Pandoc-only pipeline**  
   - Maximum fidelity for heterogeneous docs.
   - Rejected as primary approach due to overhead; kept as fallback for edge cases.

## Consequences
- ✅ Parsing pipeline is Python-native, integrates cleanly with LlamaIndex.  
- ✅ Metadata preserved and propagated for advanced retrieval/graph reasoning.  
- ✅ Scalable indexing with hybrid search + graph.  
- ⚠️ Added dependency on maintaining custom AST-based chunker.  
- ⚠️ Requires versioning strategy for evolving documents (to be detailed in future ADR).  

## References
- [markdown-it-py](https://markdown-it-py.readthedocs.io/)
- [mdit-py-plugins](https://github.com/executablebooks/mdit-py-plugins)
- [python-frontmatter](https://github.com/eyeseast/python-frontmatter)
- [LlamaIndex Docs](https://docs.llamaindex.ai/)

