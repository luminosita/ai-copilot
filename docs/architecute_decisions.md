# 🏗️ Architecture Decisions Document: AI Copilot System

**Version**: 1.0  
**Authors**: Engineering Architects + AI Team  
**Date**: 2025-04-05  
**Status**: Proposed → Pending Review

---

## AD-01: Use RAG 2.0 as Core Reasoning Engine

**Decision**: ✅ Adopt RAG 2.0 architecture over fine-tuning or basic RAG.

**Rationale**:
- Requires grounding in evolving, interlinked documents — not static datasets.
- Must support traceability, multi-hop queries, self-critique, and tool use.
- RAG 2.0 frameworks (LangChain, LlamaIndex, DSPy) provide modular, extensible primitives.

**Alternatives Considered**:
- Fine-tuned LLM: Lacks traceability, hard to update with new docs.
- Basic RAG: No self-reflection, poor at complex queries or iterative workflows.

**Impact**: All components must support document retrieval, linking, and citation.

---

## AD-02: Framework Stack — LangChain + LlamaIndex + DSPy

**Decision**: ✅ Use hybrid stack:

- **LangChain** for orchestration, agent workflows, tool integrations
- **LlamaIndex** for document indexing, graph relationships, retrieval
- **DSPy** for optimizing retrieval/generation and implementing Self-RAG

**Rationale**:
- **LangChain**: Best for chaining complex, stateful agent behaviors.
- **LlamaIndex**: Superior for document graph construction and metadata-rich retrieval.
- **DSPy**: Enables programmatic optimization and reflection — critical for quality control.

**Alternatives**:
- Haystack: Good for pipelines but less flexible for agentic workflows.
- Pure LLM prompts: No optimization, no retrieval tuning, brittle.

**Impact**: Engineering team must learn and integrate all three frameworks. Shared context object must be compatible across them.

---

## AD-03: Document as Nodes in a Knowledge Graph

**Decision**: ✅ Model all artifacts (PRDs, Specs, Stories, ADRs) as nodes in a LlamaIndex-powered knowledge graph.

**Rationale**:
- Enables traceability queries: “What code was generated from this spec?”
- Supports impact analysis: “If we change ADR #3, what features are affected?”
- Allows Graph RAG techniques for thematic or cross-cutting queries.

**Schema**:
```json
{
  "doc_id": "prd-001",
  "type": "main_prd | feature_prd | eng_spec | user_story",
  "title": "AI Copilot PRD",
  "content": "...",
  "links": ["arch-001", "feat-005"],
  "version": "1.0",
  "phase": "1",
  "created_by": "PO_Alpha",
  "embedding": [...]
}
```

**Impact**: All document creation/updates must go through ingestion pipeline that updates graph.

---

## AD-04: Enforce Self-RAG for All AI-Generated Artifacts

**Decision**: ✅ All LLM generation must use Self-RAG pattern — insert retrieval and critique tokens.

**Rationale**:
- Ensures outputs are grounded and self-validated.
- Reduces hallucination risk in critical documents (PRDs, Specs).
- Provides audit trail: “Why did the AI generate this sentence? Because it retrieved doc X.”

**Implementation**:
- Use DSPy to compile Self-RAG modules.
- Critique dimensions: Consistency, Completeness, Conflict, Citation.

**Impact**: Generation will be slower but more reliable. UI must display citations.

---

## AD-05: Tool Integration via LangChain Tools

**Decision**: ✅ Expose Confluence, Jira, GitHub as LangChain Tools.

**Rationale**:
- AI Agent must “do things”, not just “say things” — create tickets, save drafts, link PRs.
- LangChain’s Tool abstraction is mature and secure (supports auth, rate limiting).

**Tools to Implement**:
- **ConfluenceWriterTool**: Save draft to page, with metadata
- **JiraTaskCreator**: Log user story or task with links
- **GitHubFileReader**: Retrieve code examples or past specs

**Impact**: Requires OAuth/config per tool. Must implement human approval gates.

---

## AD-06: Context Engineering for Code Generation

**Decision**: ✅ In Phase 3, retrieve Engineering Spec + Style Guide + Past Examples → feed as context to Code LLM.

**Rationale**:
- Code must reflect spec accurately — context engineering ensures alignment.
- Prevents “generic code” — enforces team patterns, error handling, logging, etc.

**Implementation**:
- Use **LlamaIndex** to retrieve relevant spec sections + code examples.
- Prompt: “Generate Python service adhering to spec {spec_id}. Use pattern from {example_id}.”

**Impact**: Requires structured spec templates. Code output must include # Source: spec-123 comments.

---

## AD-07: Evaluation & Observability Built-In

**Decision**: ✅ Instrument all phases with RAG triad metrics (faithfulness, context relevance, answer relevance).

**Rationale**:
- Must measure AI quality per phase — PRD drafting vs code generation have different standards.
- Enables continuous improvement via feedback loops.

**Tools**:
- **LlamaIndex RAG Evaluator**
- **LangSmith** for tracing
- Custom dashboard for hallucination rate, citation coverage

**Impact**: Adds latency but required for production readiness.

---

**Open Architecture Questions**

- How to version and diff document nodes in the graph?
- Should we support vector + graph + keyword hybrid queries? (Yes — via LlamaIndex)
- What’s the caching strategy for frequent queries (e.g., “show me main PRD”)?
- How to handle document access control (RBAC) in retrieval?

> This document will evolve. All future feature-specific architecture decisions must link back to this root ADR. 