## TODO:
- [x] Convert AI chats to Markdown
- [x] Organize MD files for further reading and specs
- [ ] Streamlit chat for further research
    - [ ] draft idea
    - [ ] Rasmus PRP to create PRD, ADR, Engineering Specs

## Features:
- Parsing (AST, frontmatter, metadata)
- Indexing (vector store, graph store, keywords, metadata)
- Ontology/governance
- Retrieval
- MCP Server based on (https://github.com/luminosita/PRPs-agentic-eng) for chat-based system interaction
- Full context-aware workflow orchestration with human interaction (Archon-like system with phases: research, backlog, review, in progress, done ...)
- Github hook as a orchestration trigger
- New/existing deep research agent integration 
    - New products -> Main PRD document
    - Technology/framework/library -> ADR Record
    - Feature with user stories -> Feature PRD Spec

## PRPs:
- Main PRD + frontmatter
- Main ADR + frontmatter
- Feature PRD Spec (user stories) + frontmatter
- ADR Record Spec + frontmatter
- Engineering Spec + frontmatter

## Prompts:
- Improve frontmatter prompt - rarely produces correct markdown document

## Research:
- Streamlit chat platform for MCP Server communication

## Optional
- Explicit traceability matrices (e.g., mapping requirements → decisions → risks) ???

#### Feature PRD Spec
| Requirement                           | Decision / Approach              | Risk Mitigation                              | Linked ADR |
| ------------------------------------- | -------------------------------- | -------------------------------------------- | ---------- |
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