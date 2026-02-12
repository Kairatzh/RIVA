## RIVA DSL Overview

Document version: 0.1.0  
Status: Draft specification

### Project goals

- **Simple language for complex LLM workflows**: RIVA DSL is designed to describe multi‑step pipelines, multi‑agent scenarios and integrations with external systems in a human‑readable way.
- **Accessible for non‑programmers**: syntax and core concepts are aligned with natural language and domain terms rather than low‑level programming constructs.
- **Embedded “mini‑AI” in the language**: the special `auto` semantics delegates prompt engineering, schema design, step decomposition and similar technical details to an internal assistant.
- **Focus on reproducibility and versioning**: each DSL and workflow version should be reproducible with explicit change management.

### Design principles

- **Declarative style**: users describe *what* should be done, not *how* to implement it.
- **Clear domain entities**:
  - `workflow` — a complete scenario;
  - `step` — a single model action or operation;
  - `agent` — a role/persona built on top of a model;
  - `data` — inputs and outputs (text, json, table, file, etc.).
- **Minimal keywords, maximum natural language**: `intent` and `goal` fields are written in plain language and interpreted by an LLM.
- **Safe evolution**: changes to the DSL must be compatible by default; breaking changes require a major release and migration guidance.

### High‑level model

1. **Workflow**  
   Describes the overall process as a set of steps, models and external sources.

2. **Steps**  
   Each step performs a single clear task:
   - content generation (text, code, tables, JSON);
   - data transformation (cleaning, formatting, conversion);
   - analysis and extraction (classification, summarization, NER);
   - infrastructure operations (logging, cost estimation, caching).

3. **Mini‑AI (`auto`)**  
   Special keyword used to delegate configuration details to the built‑in assistant:
   - prompt construction for models;
   - JSON/table schema design;
   - multi‑step chain planning;
   - retry and validation strategies.

4. **Flow control**  
   High‑level constructs:
   - sequential steps;
   - conditional branches;
   - parallel blocks;
   - declarative loops.

### Target audience

- creators of LLM products and workflows without deep programming skills;
- product managers and analysts who describe processes on a “what should happen” level;
- engineers who need a central, readable and versioned description of LLM pipelines.

### Repository contents

- `README.md` / `README.en.md` — project overview and status;
- `docs/OVERVIEW.md` / `docs/OVERVIEW.en.md` — conceptual overview of the language;
- `docs/LANGUAGE_REFERENCE.md` / `.en.md` — language reference (syntax and constructs);
- `docs/VERSIONING.md` / `.en.md` — versioning policy and change management rules;
- `docs/LEARNING.md` / `.en.md` — learning materials for new users;
- `examples/` — ready‑to‑use workflow examples in RIVA DSL.

