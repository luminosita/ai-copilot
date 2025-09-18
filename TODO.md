## Watch
- [ ] Cole Medin - Introducing RAG 2.0: Agentic RAG + Knowledge Graphs
- [ ] Cole Medin - The EASIEST Possible Strategy for Accurate RAG
- [ ] Cole Medin - I Built the Ultimate RAG MCP Server for AI Coding
- [ ] Cole Medin - 10x Your AI Agents with this ONE Agent Achitecture

## TODO:
- [ ] RAG 2.0 (rag_2_0.md)
    - [ ] HyDE
    - [ ] Self-RAG
    - [ ] Adaptive RAG / Corrective RAG
    - [ ] Graph RAG
    - [ ] Tool-augmented RAG
    - [ ] Agentic RAG
- [x] Convert AI chats to Markdown
- [x] Organize MD files for further reading and specs

## Features:
- [ ] Long-term document storage (markdown_document_strategy.md)
- [ ] Parsing (AST, frontmatter, metadata)
- [ ] Indexing (vector store, graph store, keywords, metadata)
- [ ] Ontology
- [ ] Governance (SME review, graph_db_benefits.md)
- [ ] Retrieval
- [ ] MCP Server based on (https://github.com/luminosita/PRPs-agentic-eng) for chat-based system interaction
- [ ] Full context-aware workflow orchestration with human interaction (Archon-like system with phases: research, backlog, review, in progress, done ...)
- [ ] Github hook as an orchestration trigger
- [ ] New/existing deep research agent integration 
    - [ ] New products -> Main PRD document
    - [ ] Technology/framework/library -> ADR Record
    - [ ] Feature with user stories -> Feature PRD Spec

## Parsing/Indexing Features:
- [ ] graph_db_benefits.md
- [ ] graph_db_for_company_docs.md
- [ ] Local/remote embeddings AI Model (Cole Medin - Create Your Own Private Local AI Cloud Stack Under 20)
- [ ] Data chunks
- [ ] Document sources
    - [ ] Markdown files
    - [ ] Slack conversations (incident reports)
    - [ ] Runbooks
    - [ ] Ticket system (incident reports)
    - [ ] K8s manifests
    - [ ] Terraform configs
    - [ ] Security/Network policies (Kyverno, Cilium)
    - [ ] Architecture decisions
    - [ ] Troubleshoot guides
    - [ ] Source code comments in general
    - [ ] Doc strings (e.g. Python)
    - [ ] Example snippets in documentation
    - [ ] Historical implementations
- [ ] Document chunking strategies
- [ ] Embedding optimizations
- [ ] Production-ready vector DB
- [ ] Same model for indexing and searching (CRITICAL)

# Kubernetes CRD Documentation
- [ ] Index CRD documentation
- [ ] Create CRD registration hook (admission hook)
- [ ] Initial indexing on deployment
- [ ] MCP tool for retrieval 

## Context Engineering
- [ ] rag_2_0_iterative_software_development_process.md
- [ ] context_enginnering_traditional_vs_context.md
- [ ] context_engineering.md

## PRPs:
- [ ] Main PRD + frontmatter
- [ ] Main ADR + frontmatter
- [ ] Feature PRD Spec (user stories) + frontmatter
- [ ] ADR Record Spec + frontmatter
- [ ] Engineering Spec + frontmatter

## Prompts:
- [ ] Victor and Claude Tutorials
- [x] Each AI API call share the same context?
- [ ] Shared prompt repository: `dot-ai/shared-prompts/prd-done`
- [x] What is context in Claude?
- [ ] Spec Kit GitHub
- [ ] Prompt Improver tool?
- [ ] Serena MCP
- [ ] Archon OS
- [ ] 12-Factor Agent methodology
- [ ] OWASP for LLMs (Large Language Models)

## Research:
- [ ] Streamlit chat platform for MCP Server communication

## Optional
- [ ] Explicit traceability matrices (e.g., mapping requirements → decisions → risks) ???

#### Feature PRD Spec
| Requirement                           | Decision / Approach              | Risk Mitigation                              | Linked ADR |
| ------------------------------------- [ ] | -------------------------------- [ ] | -------------------------------------------- [ ] | ---------- [ ] |
| Extract YAML/TOML frontmatter         | Use `python-frontmatter`         | Standardize parsing before AST traversal     | ADR 001    |
| Preserve semantic Markdown structures | Use `markdown-it-py` AST parsing | Avoid regex parsing                          | ADR 001    |
| Avoid mid-block splits                | Header-aware chunking strategy   | Chunk by headers, preserve code/table blocks | ADR 001    |
| Metadata propagation                  | Attach metadata dict per node    | Ensure file + header-path stored             | ADR 001    |

#### ADR Record
| Requirement / Need                   | Decision                          | Alternative Rejected   | Consequence                          |
|--------------------------------------|-----------------------------------|------------------------|--------------------------------------|
| Python-native parsing                | `markdown-it-py`                  | Remark (JS)            | No NodeJS dependency introduced      |
| Metadata preservation (frontmatter)  | `python-frontmatter`              | Pandoc-only pipeline   | Lightweight YAML/TOML support        |
| Structural integrity (tables/code)   | AST traversal via `markdown-it-py` | Regex heuristics       | Safer parsing with fewer breakages   |
| Integration with LlamaIndex          | Use MarkdownNodeParser if needed  | Custom splitter only   | Reuse proven component               |