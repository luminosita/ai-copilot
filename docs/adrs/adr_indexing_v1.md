# ADR 002: Knowledge Base Indexing with LlamaIndex

## Status
Accepted — September 2025

## Context
For RAG 2.0, knowledge base indexing must support both vector similarity search and graph-based reasoning. Provenance, metadata, and multi-hop retrieval are essential for AI Copilot traceability.

## Decision
- Use **LlamaIndex** as primary indexing framework.
- Construct hybrid indexes:
  - `VectorStoreIndex` for dense embeddings.
  - `KnowledgeGraphIndex` for graph edges.
  - Hybrid retriever combining sparse + dense.
- Ingest parsed Markdown/ADR nodes with metadata attached.

## Rationale
- **Hybrid retrieval** improves recall/precision balance.  
- **Graph index** supports dependency/traceability queries.  
- **LlamaIndex** integrates cleanly with LangChain/DSPy orchestration.  
- **Metadata-first design** ensures filtering and scoping queries.

## Alternatives Considered
- **Haystack**: Mature, but heavier and less graph-oriented.  
- **Weaviate / Neo4j direct integration**: Strong graph features, but adds infrastructure complexity.  
- **ElasticSearch-only**: Good keyword search, lacks dense/graph reasoning.  

## Consequences
- Dependency on LlamaIndex as core component.  
- Future portability possible (Weaviate/Neo4j integration optional).  
- Strong Python-native workflow alignment.

## References
- [LlamaIndex Indexes](https://docs.llamaindex.ai/en/stable/module_guides/indexing/)  
- [LlamaIndex Knowledge Graph](https://docs.llamaindex.ai/en/stable/module_guides/indexing/knowledge_graph/)  
- [DSPy](https://dspy-docs.vercel.app/) for adaptive retrieval  