## RIVA DSL: Language Reference

Document version: 0.1.0  
Status: Draft

### 1. General format

RIVA DSL is a human‑readable declarative language. The initial version uses a YAML‑like structured text format, optimised for:

- readability in Git;
- manual editing;
- simple diff and review.

Recommended file extensions: `.riva` or `.riva.yaml`.

### 2. Core entities

#### 2.1. Workflow

`workflow` describes a complete scenario.

Example:

```yaml
workflow article_pipeline:
  description: "Pipeline for article generation and analysis"

  models:
    writer: gpt-4.1
    analyzer: gpt-4.1-mini

  inputs:
    topic: text

  outputs:
    article_text: text
    summary_text: text
```

Key fields:

- `description` — human‑readable description;
- `models` — named models used by steps;
- `inputs` / `outputs` — named data available to the workflow with types.

#### 2.2. Step

`step` defines a single logical operation within a workflow.

General form:

```yaml
step <step_name>:
  model: <model_name_from_models>
  kind: <operation_type>
  intent: <natural_language_description>
  input:
    - <input_variable_name>
  output: <output_variable_name>
```

Typical `kind` values:

- `text.generate` — text generation;
- `text.summarize` — summarization;
- `text.classify` — classification;
- `code.generate` — code generation;
- `table.from_text` — table extraction;
- `json.from_text` — JSON extraction.

#### 2.3. Agents

`agent` is a named role built on top of a model. An agent can be used in steps instead of a direct model reference.

Example:

```yaml
agents:
  writer:
    model: gpt-4.1
    role: "Writes structured texts"
  critic:
    model: gpt-4.1-mini
    role: "Finds issues and suggests improvements"
```

In steps:

```yaml
step draft_article:
  agent: writer
  kind: text.generate
  intent: "Write a draft article on {topic}"
  output: draft_text
```

#### 2.4. Data

Data in DSL is represented by named variables with types:

- primitive types: `text`, `json`, `table`, `file`, `number`, `bool`;
- collections: `list<text>`, `list<json>`, etc.

Example:

```yaml
data:
  article_text: text
  summary_text: text
  entities: json
```

### 3. Mini‑AI and the `auto` keyword

The `auto` keyword signals that some configuration should be constructed automatically by the built‑in assistant.

Typical usage:

- `intent: auto "<goal_description>"` — automatic prompt construction;
- `schema: auto "<schema_description>"` — schema inference for JSON/table;
- `chain: auto "<workflow_description>"` — automatic chain design.

Example:

```yaml
step generate_article:
  model: writer
  kind: text.generate
  intent: auto "Write an introductory article on {topic} for beginners"
  output: article_text
```

### 4. Flow control

The first version of DSL supports:

- sequential execution (default);
- conditional blocks `if`;
- declarative loops `for each`;
- parallel groups `parallel`.

#### 4.1. Sequential execution

The order of steps in the file defines the default execution order unless otherwise specified.

#### 4.2. Conditions

General form:

```yaml
if <condition>:
  step <step_name>:
    ...
```

Example:

```yaml
if summary_text contains "urgent":
  step send_alert:
    model: notifier
    kind: text.generate
    intent: auto "Notify the team that urgent content was detected"
    output: alert_message
```

#### 4.3. Loops

General form:

```yaml
for each <item> in <collection>:
  step <step_name>:
    input:
      - <item>
    output: <result_collection>
```

Example:

```yaml
for each article in articles:
  step summarize_article:
    model: analyzer
    kind: text.summarize
    input:
      - article
    output: summaries[]
```

#### 4.4. Parallel blocks

General form:

```yaml
parallel:
  step <step_name_1>:
    ...
  step <step_name_2>:
    ...
```

Example:

```yaml
parallel:
  step make_short_summary:
    model: analyzer
    kind: text.summarize
    input:
      - article_text
    output: summary_short

  step make_detailed_summary:
    model: analyzer
    kind: text.summarize
    input:
      - article_text
    output: summary_long
```

### 5. External sources

#### 5.1. HTTP / API

```yaml
step fetch_data:
  from:
    url: "https://example.com/data.json"
    method: GET
  output: raw_data
```

#### 5.2. File system

```yaml
step load_documents:
  from:
    folder: "./docs"
    pattern: "*.pdf"
  output: docs_files
```

### 6. Infrastructure parameters

Each `step` may optionally specify:

- `retry` — retry policy;
- `timeout` — time limit;
- `cache` — caching configuration.

Example:

```yaml
step generate_article:
  model: writer
  kind: text.generate
  intent: auto "Write an article about AI safety"
  output: article_text

  retry:
    max_attempts: 3

  timeout: 60s

  cache:
    ttl: 24h
    key: topic
```

### 7. Limitations and status

- This specification describes an early version of the language and may change.
- Backwards compatibility will follow the policy described in `docs/VERSIONING.en.md`.
- Runtime implementation and execution semantics are under design and are not part of this document.

