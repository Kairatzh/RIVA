## RIVA

Run Intelligent Virtual Automations — open source DSL for LLM workflows

DSL version (concept): 0.1.0  
Project status: early language and documentation design (runtime not implemented yet)

### About the project

RIVA is a declarative domain‑specific language (DSL) for describing:

- content generation (text, code, structured data);
- data transformation and analysis;
- multi‑agent LLM scenarios;
- interaction with external systems (APIs, files, databases);
- infrastructure aspects (logging, cost estimation, caching).

The main goal is to enable users, including non‑developers, to describe complex workflows and multi‑agent systems in a readable text format.

### Repository structure

- `README.md` — main Russian‑language overview;
- `README.en.md` — this English overview;
- `docs/OVERVIEW.md` / `docs/OVERVIEW.en.md` — conceptual description of the language and goals;
- `docs/LANGUAGE_REFERENCE.md` / `.en.md` — language reference (syntax and constructs);
- `docs/VERSIONING.md` / `.en.md` — versioning policy and change management;
- `docs/LEARNING.md` / `.en.md` — learning guides with step‑by‑step examples;
- `examples/` — example workflows written in RIVA DSL.

### Examples

Basic examples located in `examples/`:

- `article_basic.riva` — generate an article by topic and a short summary;
- `article_multi_agent.riva` — “writer + critic” collaboration scenario;
- `news_summary.riva` — fetch news via API and build a summary.

Each example is explained in more detail in `docs/LEARNING.en.md` and `docs/LEARNING.md`.

### Versioning

The versioning approach for the DSL and the future runtime is described in `docs/VERSIONING.en.md` and `docs/VERSIONING.md`.  
At this stage, changes are expected and are tracked via documentation updates.

