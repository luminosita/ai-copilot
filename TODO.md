## TODO:
- [x] Convert AI chats to Markdown
- [x] Organize MD files for further reading and specs

## Features:
- [ ] Parsing (AST, frontmatter, metadata)
- [ ] Indexing (vector store, graph store, keywords, metadata)
- [ ] Ontology/governance
- [ ] Retrieval
- [ ] MCP Server based on (https://github.com/luminosita/PRPs-agentic-eng) for chat-based system interaction
- [ ] Full context-aware workflow orchestration with human interaction (Archon-like system with phases: research, backlog, review, in progress, done ...)
- [ ] Github hook as a orchestration trigger
- [ ] New/existing deep research agent integration 
    - [ ] New products -> Main PRD document
    - [ ] Technology/framework/library -> ADR Record
    - [ ] Feature with user stories -> Feature PRD Spec

## Parsing/Indexing Features:
- [ ] Local/remote embeddings AI Model
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