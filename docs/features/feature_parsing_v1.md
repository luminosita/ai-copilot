# Feature Spec — Advanced Markdown & Document Parsing for RAG 2.0

## Overview
This feature defines the document parsing strategy for the **AI Copilot** knowledge base ingestion pipeline. It ensures all product and engineering documents are parsed into structured units while preserving metadata, formatting, and semantic boundaries.

## Goals
- Extract **frontmatter metadata** (YAML/TOML) and propagate it as structured fields.
- Parse Markdown and related document formats into **AST-preserved blocks** (paragraphs, tables, code blocks, lists).
- Avoid breaking context (e.g., splitting inside code or tables).
- Enable **header-aware hierarchical chunking** for traceable graph relationships.
- Provide Python-native tooling that integrates with **LlamaIndex** ingestion.

## Inputs
- Markdown documents (PRDs, ADRs, specs, wikis).
- Files containing frontmatter metadata (author, date, tags, dependencies).
- Other structured text formats converted into Markdown (via Pandoc if needed).

## Outputs
- **Parsed nodes** containing:
  - Clean block-level text (no broken code/tables).
  - Associated metadata (frontmatter + file path + header path).
  - Node boundaries aligned with headers/sections.

## Key Requirements
1. **Frontmatter extraction**
   - Parse YAML/TOML frontmatter into Python `dict`.
   - Attach metadata to every node.

2. **AST-based parsing**
   - Use a CommonMark-compliant parser (`markdown-it-py` + `mdit-py-plugins`).
   - Preserve fenced code blocks, tables, blockquotes, inline HTML.

3. **Chunking logic**
   - Split at semantic boundaries (headers, horizontal rules).
   - Maintain context by grouping block elements.
   - Never split mid-code-block or mid-table.

4. **Graph metadata**
   - Capture **header paths** (H1 → H2 → …) for each node.
   - Add relationships to support knowledge graph indexing.

## Non-Goals
- Non-text binary parsing (PDFs, images) — handled separately.
- Execution of code blocks (security risk).

## Dependencies
- `python-frontmatter`
- `markdown-it-py`
- `mdit-py-plugins`
- Optional: `pandoc` for heterogeneous inputs.

## Acceptance Criteria
- ✅ Frontmatter correctly extracted into metadata.
- ✅ No code/table broken into partial chunks.
- ✅ Chunked nodes maintain full section context.
- ✅ Works seamlessly with LlamaIndex ingestion.

