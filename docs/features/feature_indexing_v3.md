# Feature Specification: Knowledge Base Indexing for RAG 2.0

## Context
The AI Copilot requires advanced indexing of parsed documents (PRDs, ADRs, Specs, Wikis) to support retrieval, traceability, and graph-based reasoning. Indexing must support hybrid search, metadata filtering, and graph linkages.

## Goals
- Build index over parsed documents with attached metadata.
- Support hybrid retrieval (dense + sparse + keyword).
- Enable graph-based reasoning (header paths, doc links).
- Ensure provenance: each node traces back to source.

## Non-Goals
- Document parsing (covered in separate spec).
- Self-RAG optimization (covered elsewhere).

## Functional Requirements
1. **Ingestion**
   - Accept parsed `Document`/`Node` objects.
   - Store text + metadata.

2. **Vector Indexing**
   - Embed chunks into dense vectors.
   - Support hybrid retrieval (vector + BM25/keyword).

3. **Graph Indexing**
   - Build graph edges based on:
     - Header hierarchy
     - Cross-references between docs
     - Frontmatter relationships (tags, links)

4. **Metadata-aware Retrieval**
   - Support filtering by file, author, tags, phase.
   - Enable scoped queries (e.g., “Phase 1 ADRs”).

5. **Provenance & Traceability**
   - Store chunk-source mapping.
   - Ensure retriever can cite original file/section.

## Technical Approach
- **Framework**: LlamaIndex for vector + graph indexing.
- **Index Types**:
  - `VectorStoreIndex` for embeddings.
  - `KnowledgeGraphIndex` for graph edges.
  - Hybrid retriever combining dense + sparse.
- **Embeddings**: OpenAI / local model via `llama-index` embeddings API.
- **Metadata**: Attached to each node during ingestion.

## Interfaces
- Input: List of `Document` or `Node` objects (with metadata).
- Output: Index objects stored in persistent backend.

```py
index = build_kb_index(docs)
results = index.query("What ADRs affect login architecture?")
```

## Risks & Mitigations

- Risk: Explosion of graph edges → performance bottleneck.
Mitigation: Prune low-signal edges; batch builds.

- Risk: Hybrid retrieval mismatch.
Mitigation: Adaptive retrieval policy (DSPy tuning).

## Success Criteria

- Metadata-filtered queries return correct subset.
- Multi-hop queries trace across docs.
- Provenance consistently included in outputs.