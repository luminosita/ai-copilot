# AI Copilot for Iterative Product & Engineering Workflow — Context Summary

## Overview

This document summarizes the strategic and technical rationale for adopting **RAG 2.0** as the core engine powering an **AI Copilot** designed to assist Product Owners, Managers, and Engineers through an iterative, document-driven software development lifecycle.

The envisioned workflow spans:
- **Phase 0**: Vision, scope, audience, initial features
- **Phase 1**: Main PRD + Architecture Decisions
- **Phase 1.x**: Feature-scoped PRDs (linked to main)
- **Phase 2**: User Stories + Engineering Specs (AI-assisted decomposition)
- **Phase 3**: Implementation (AI-assisted code/component generation with context engineering)

All artifacts are interlinked, versioned, and dynamically retrievable — forming a “living knowledge graph” of the product.

## Why RAG 2.0?

RAG 2.0 is uniquely suited because it enables:
- **Multi-hop, traceable retrieval** across evolving documents
- **Self-critique and reflection** (via Self-RAG) to ensure quality and consistency
- **Graph-based reasoning** (via Graph RAG) to map dependencies and impacts
- **Adaptive retrieval strategies** based on query type or phase
- **Citation and provenance** — every AI output references its source
- **Tool and agent integration** — to call APIs, generate schemas, validate specs

## Recommended Framework Stack

| Component              | Framework(s)             | Role                                                                 |
|------------------------|--------------------------|----------------------------------------------------------------------|
| Orchestration & Agents | LangChain 🦜️🔗           | Manages workflow phases, invokes tools, chains AI steps              |
| Knowledge Graph & Retrieval | LlamaIndex 🦙        | Indexes and links all product/engineering docs; enables traceability |
| Optimization & Reflection | DSPy                  | Tunes retrieval/generation; implements Self-RAG critique tokens      |
| Optional Enhancements  | Haystack, NVIDIA NeMo    | For enterprise pipelines or GPU-scale acceleration                   |

## Key Capabilities Leveraged

- Hybrid search (dense + sparse + keyword)
- Query rewriting & disambiguation
- Iterative/agent-driven loops
- Context-aware code & spec generation
- Automated traceability matrix generation

## Suggested First Steps

1. Index existing PRDs, ADRs, wikis using LlamaIndex.
2. Build a LangChain agent to answer traceability queries and draft Phase 0/1 docs.
3. Integrate DSPy to optimize retrieval precision per phase.
4. Implement Self-RAG for AI-generated artifacts to enforce citation and self-critique.
5. Connect to toolchain: Jira, GitHub, Confluence.

## Future Prompting & Research Directions

- How to model document versioning in the knowledge graph?
- What metadata schema best supports traceability (feature → story → spec → code)?
- How to evaluate RAG 2.0 performance per phase (e.g., faithfulness in PRDs vs code)?
- Can we auto-generate test cases from Engineering Specs using RAG + Code LLMs?
- How to handle conflicting decisions across document versions?

> This document serves as the seed for all future AI Copilot prompting, technical spikes, and architectural iterations.