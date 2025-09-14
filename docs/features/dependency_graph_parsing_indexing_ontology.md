# Dependency Graph — Knowledge Base Ingestion & Reasoning

This section describes how the **Parsing**, **Indexing**, and **Ontology** features integrate to form the layered knowledge base backbone for the AI Copilot.

## Layered View

### 1. Parsing Layer
- **Responsibility**: 
  - Extract frontmatter metadata.
  - Parse documents into structured nodes.
  - Preserve block integrity (paragraphs, code, tables).
- **Outputs**: 
  - Parsed nodes + metadata + header paths.

### 2. Indexing Layer
- **Responsibility**: 
  - Ingest parsed nodes into a multi-modal index.
  - Support vector + keyword + graph retrieval.
  - Maintain document hierarchy and relationships.
- **Dependencies**: 
  - Consumes nodes from **Parsing**.
- **Outputs**: 
  - Indexed nodes with embeddings and relationship edges.
  - Retrieval-ready structure with provenance.

### 3. Ontology Layer
- **Responsibility**: 
  - Define semantic schema (document types, relationships, lifecycle states).
  - Enforce metadata consistency and governance rules.
  - Provide context-aware and governance-aware retrieval.
- **Dependencies**: 
  - Consumes indexed nodes from **Indexing**.
  - Normalizes metadata defined in **Parsing**.
- **Outputs**: 
  - Ontology-enriched knowledge graph.
  - Governance-aware knowledge base (states, lineage, dependencies).

---

## Dependency Graph (Textual)

```
[Parsing] → [Indexing] → [Ontology]
```

- **Parsing → Indexing**  
  Parsing provides clean, metadata-rich nodes to Indexing.  
- **Indexing → Ontology**  
  Indexing structures the knowledge for retrieval; Ontology enriches it with semantic context and governance.  
- **Ontology → Retrieval & Reasoning**  
  Ontology enables context-aware queries, lifecycle enforcement, and dependency tracing.

---

## Ontology Constraints (Governance Rules)

The ontology enforces governance through declarative constraints:

1. **Document Lifecycle**
   - Every document must have a `status` ∈ {Draft, Approved, Deprecated}.
   - Transitions must follow:  
     `Draft → Approved → Deprecated` (no skipping).

2. **Traceability**
   - Every **ADR** must reference at least one **PRD**.
   - Every **User Story** must link to at least one **Feature PRD** or **Spec**.
   - Every **Spec** must link to either a **PRD** or a **User Story**.

3. **Metadata Completeness**
   - Each document must include: `author`, `created_at`, `phase`.
   - `tags` are optional but recommended.

4. **Versioning**
   - A document marked `supersedes` must point to exactly one older version.
   - Deprecated documents must specify their replacement if one exists.

5. **Governance Exceptions**
   - Exceptions (e.g., orphan ADRs) must be explicitly approved and logged.

---

## Integration Notes
- Each layer remains **loosely coupled**:
  - Parsing can evolve (e.g., new formats) without breaking Indexing.
  - Indexing can adopt new retrieval strategies without altering Ontology.
  - Ontology can extend schemas and rules without reworking parsing logic.
- Together, they form the **semantic backbone** for RAG 2.0 reasoning and traceability.

## SHACL/OWL-style representation

Here’s a tool-agnostic SHACL/OWL-style representation of the ontology constraints. This format can be used to automatically enforce governance rules if the knowledge graph is stored in an RDF/graph database.

```turtle
@prefix ex: <http://example.org/ontology#> .
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

# =====================================================
# Document Lifecycle Constraint
# =====================================================
ex:DocumentShape
    a sh:NodeShape ;
    sh:targetClass ex:Document ;
    sh:property [
        sh:path ex:status ;
        sh:in ("Draft" "Approved" "Deprecated") ;
        sh:message "Document status must be Draft, Approved, or Deprecated." ;
    ] ;
    sh:property [
        sh:path ex:previousStatus ;
        sh:or (
            [ sh:hasValue "Draft" ]
            [ sh:hasValue "Approved" ]
            [ sh:hasValue "Deprecated" ]
            [ sh:minCount 0 ] # optional for first version
        ) ;
        sh:message "Status transitions must follow Draft → Approved → Deprecated." ;
    ] .

# =====================================================
# Traceability Constraints
# =====================================================
ex:ADRShape
    a sh:NodeShape ;
    sh:targetClass ex:ADR ;
    sh:property [
        sh:path ex:referencesPRD ;
        sh:minCount 1 ;
        sh:message "Every ADR must reference at least one PRD." ;
    ] .

ex:UserStoryShape
    a sh:NodeShape ;
    sh:targetClass ex:UserStory ;
    sh:property [
        sh:path ex:linkedFeature ;
        sh:minCount 1 ;
        sh:message "Every User Story must link to at least one Feature PRD or Spec." ;
    ] .

ex:SpecShape
    a sh:NodeShape ;
    sh:targetClass ex:Spec ;
    sh:property [
        sh:path ex:linkedPRDOrUserStory ;
        sh:minCount 1 ;
        sh:message "Every Spec must link to either a PRD or a User Story." ;
    ] .

# =====================================================
# Metadata Completeness
# =====================================================
ex:MetadataShape
    a sh:NodeShape ;
    sh:targetClass ex:Document ;
    sh:property [
        sh:path ex:author ;
        sh:minCount 1 ;
        sh:message "Author metadata is required." ;
    ] ;
    sh:property [
        sh:path ex:createdAt ;
        sh:datatype xsd:dateTime ;
        sh:minCount 1 ;
        sh:message "Creation timestamp is required." ;
    ] ;
    sh:property [
        sh:path ex:phase ;
        sh:minCount 1 ;
        sh:message "Phase metadata is required." ;
    ] .

# =====================================================
# Versioning and Supersedes
# =====================================================
ex:VersionShape
    a sh:NodeShape ;
    sh:targetClass ex:Document ;
    sh:property [
        sh:path ex:supersedes ;
        sh:maxCount 1 ;
        sh:message "A document may supersede at most one previous version." ;
    ] ;
    sh:property [
        sh:path ex:deprecatedReplacement ;
        sh:message "Deprecated documents should reference a replacement document if applicable." ;
    ] .

# =====================================================
# Governance Exceptions (manual override)
# =====================================================
ex:GovernanceExceptionShape
    a sh:NodeShape ;
    sh:targetClass ex:Document ;
    sh:property [
        sh:path ex:governanceExceptionApproved ;
        sh:datatype xsd:boolean ;
        sh:maxCount 1 ;
        sh:message "Any governance exceptions must be explicitly approved and logged." ;
    ] .
```

**Notes**:

- Each NodeShape corresponds to a document type or constraint category.
- The sh:property rules enforce required metadata, traceability links, and lifecycle governance.
- Optional fields or exceptions (e.g., first draft, orphan ADRs) can be marked with sh:minCount 0 or handled via governanceExceptionApproved.
- This SHACL schema is directly usable in RDF or graph databases that support SHACL validation (e.g., Blazegraph, Stardog, TopBraid, Neo4j with SHACL plugin).