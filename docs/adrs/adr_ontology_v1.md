# ADR: Ontology Layer for Knowledge Base Governance and Context Awareness

## Status
Accepted

## Context
The AI Copilot requires a **knowledge base backbone** capable of not only storing and retrieving documents, but also reasoning across their relationships, lifecycle, and governance rules.  

While **Parsing** ensures block-preserving extraction and **Indexing** enables efficient retrieval, neither guarantees:
- Consistent metadata across documents,
- Enforceable governance rules (e.g., lifecycle states, traceability),
- Contextual awareness of dependencies between artifacts (e.g., PRDs → ADRs → Specs → Stories).

Without a semantic schema, the system risks inconsistent metadata, broken traceability, and poor reasoning quality in **RAG 2.0 multi-hop retrieval**.

## Decision
We will introduce an **Ontology Layer** on top of the Indexing system. This Ontology will:

1. **Define Semantic Schema**
   - Explicit document types (PRD, ADR, User Story, Spec, Wiki).
   - Relationships (references, supersedes, derivesFrom).
   - Metadata requirements (author, createdAt, phase, status).

2. **Enforce Governance Rules**
   - Lifecycle constraints (Draft → Approved → Deprecated).
   - Traceability rules (ADRs must reference PRDs; Specs must link to PRDs or Stories).
   - Metadata completeness (author, phase, timestamp required).
   - Versioning (supersedes one predecessor; replacements required for deprecations).
   - Exceptions must be explicitly approved.

3. **Enable Context Awareness**
   - Normalize metadata from Parsing into a consistent schema.
   - Enhance Indexing results with semantic context (e.g., “find all approved specs derived from PRD-123”).
   - Provide governance-aware retrieval (queries return only valid/approved artifacts by default).

The ontology will be expressed in a **machine-readable schema** (e.g., SHACL/OWL) to allow automated validation, graph reasoning, and enforcement of governance rules.

## Consequences
- **Positive**
  - Enforces consistency across all documents and metadata.
  - Enables lifecycle-aware and dependency-aware reasoning.
  - Supports advanced RAG 2.0 features such as multi-hop retrieval with governance filtering.
  - Provides a single semantic backbone for integrating future phases (toolchain integration, Jira, GitHub, Confluence).

- **Negative**
  - Additional complexity: requires maintaining ontology definitions and SHACL/OWL rules.
  - Validation overhead: ingestion pipeline must include ontology checks, which may slow down indexing.
  - Governance rigidity: strict enforcement may block ingestion of incomplete or exploratory artifacts unless exception mechanisms are used.

## Alternatives Considered
- **Flat Indexing without Ontology**
  - Simple, but risks unstructured and inconsistent metadata.
  - Harder to implement governance, lifecycle enforcement, or semantic queries.
- **Ad-hoc Metadata Enforcement**
  - Metadata rules applied manually during Parsing.
  - Less flexible, brittle when schema evolves, no reusable graph semantics.
- **External Governance Tools**
  - Would separate governance from retrieval, breaking the “living knowledge graph” principle.

## Related Decisions
- **Parsing Feature Spec**: Supplies metadata and document structure to Ontology.
- **Indexing Feature Spec**: Provides embeddings and relationships consumed by Ontology.
- **Ontology Constraints (SHACL/OWL)**: Define enforceable governance rules.

## Decision Outcome
Adopting the Ontology Layer ensures that the knowledge base becomes not just a document store, but a **semantically governed knowledge graph**, enabling traceable, context-aware reasoning and retrieval in support of iterative product and engineering workflows.
