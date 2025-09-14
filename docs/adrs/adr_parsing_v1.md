# ADR 001: Markdown & Document Parsing for RAG 2.0

## Status
Accepted — September 2025

## Context
We need robust parsing of product and engineering documents into metadata-rich nodes for downstream indexing. Simplicity, correctness, and Python-native integration with LlamaIndex are priorities.

## Decision
- Use **Python-first approach**:
  - `python-frontmatter` for YAML/TOML metadata extraction.
  - `markdown-it-py` + `mdit-py-plugins` for CommonMark-compliant parsing.
  - Header-aware chunking via AST traversal or LlamaIndex’s `MarkdownNodeParser`.

## Rationale
- **Frontmatter extraction**: Reliable YAML/TOML support, widely used in static site generators.  
- **AST-level parsing**: Avoids regex pitfalls, preserves structural units (code, tables).  
- **Python ecosystem**: Direct LlamaIndex compatibility; avoids NodeJS dependency.  
- **Extensibility**: Plugins available for tables, footnotes, directives.

## Alternatives Considered
- **Remark (JS)**: Rich ecosystem, but introduces NodeJS dependency.  
- **Pandoc**: Excellent fidelity for multi-format, but heavier and external-process based.  
- **Regex-based parsing**: Fast but brittle, unsafe for complex structures.

## Consequences
- Parsing pipeline stays Python-native.  
- Metadata is guaranteed to be preserved and attached to chunks.  
- Easier integration with downstream indexing pipeline.

## References
- [`markdown-it-py`](https://github.com/executablebooks/markdown-it-py)  
- [`python-frontmatter`](https://github.com/eyeseast/python-frontmatter)  
- [LlamaIndex MarkdownNodeParser](https://docs.llamaindex.ai/en/stable/module_guides/loading/node_parsers/)  
