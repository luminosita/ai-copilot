# Feature Specification: Markdown & Document Parsing for RAG 2.0

## Context
The AI Copilot requires robust ingestion of Markdown and related product/engineering artifacts (PRDs, ADRs, Specs, Wikis). Parsing must ensure:
- Accurate metadata extraction (frontmatter, file paths, header hierarchy).
- Structural preservation of paragraphs, code blocks, tables.
- Safe and context-aware chunking for embedding/indexing.

## Goals
- Parse Markdown and associated docs into structured, metadata-rich nodes.
- Extract frontmatter (YAML/TOML) into key-value metadata.
- Preserve semantic units (paragraphs, code blocks, tables, lists).
- Maintain header hierarchy for graph-aware linking.
- Produce nodes ready for LlamaIndex ingestion.

## Non-Goals
- Indexing or retrieval (covered in separate spec).
- Multi-format canonicalization (Pandoc integration considered later).

## Functional Requirements
1. **Frontmatter Extraction**
   - Parse YAML/TOML frontmatter.
   - Expose metadata as dictionary attached to all nodes.

2. **Markdown Parsing**
   - Use CommonMark-compliant parser.
   - Generate an AST to avoid regex-based heuristics.
   - Preserve fenced code, tables, inline HTML.

3. **Chunking Strategy**
   - Chunk at semantic boundaries: headers, horizontal rules.
   - Avoid splitting code blocks or tables mid-structure.
   - Header-path based chunk labeling.

4. **Metadata Propagation**
   - Attach file-level metadata (path, name).
   - Attach hierarchical header-path.
   - Support provenance and traceability.

## Technical Approach
- **Library**: `markdown-it-py` + `mdit-py-plugins` (Python-native, CommonMark compliant).
- **Frontmatter**: `python-frontmatter` for YAML/TOML.
- **Chunking**: AST traversal; fallback to `LlamaIndex.MarkdownNodeParser`.
- **Output**: Python objects (`Document`, `Node`) with metadata.

## Interfaces
- Input: Markdown file (string or path).
- Output: List of `Document` or `Node` objects, each with:
  - `text`: content chunk
  - `metadata`: dict (frontmatter, file info, header-path)

## Example
```py
docs = parse_markdown_to_nodes("example.md")
# docs[0].metadata = {"author": "Alice", "tags": ["PRD"], "header_path": ["Phase 1", "Architecture"]}
```

## Risks & Mitigations

- Risk: Large tables or code blocks exceed token window.
Mitigation: Keep as atomic chunks; truncate or externalize selectively.

- Risk: Non-UTF8 input files.
Mitigation: Enforce UTF-8 decoding with fallback to errors="replace".

## Success Criteria

- 100% preservation of frontmatter fields as metadata.
- Zero broken code/table structures during parsing.
- Verified round-trip integrity: chunk recombination ≈ original doc.