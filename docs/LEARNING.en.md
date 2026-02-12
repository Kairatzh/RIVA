## RIVA DSL Learning Guide

Document version: 0.1.0  
Goal: help users (including non‑programmers) start using RIVA DSL to describe LLM workflows.

---

### 1. What is RIVA DSL

RIVA DSL is a text format where you describe:

- which tasks a model (or several models) should perform;
- which steps must be executed in sequence;
- which data is used as input and what outputs are produced.

You do not write traditional program code. Instead, you define a scenario in plain language and structured fields. The RIVA runtime (interpreter) will execute this scenario by calling LLM models and external sources.

---

### 2. Minimal terminology

- **Workflow** — a full scenario or pipeline.
- **Step** — a single step in the scenario (for example, “write article”, “create summary”).
- **Model** — a specific LLM model (for example, gpt‑4.1).
- **Agent** — a role built on top of a model (for example, “writer”, “critic”).
- **Input / Output** — workflow inputs and outputs.
- **auto** — special keyword meaning “let the built‑in assistant construct details”.

---

### 3. First example: generate an article and short summary

Scenario: the user provides a topic, the system generates an article and a short summary.

File `examples/article_basic.riva`:

```yaml
workflow article_basic:
  description: "Simple pipeline: article + short summary"

  models:
    writer: gpt-4.1
    analyzer: gpt-4.1-mini

  inputs:
    topic: text

  outputs:
    article_text: text
    summary_text: text

step generate_article:
  model: writer
  kind: text.generate
  intent: auto "Write a clear article on {topic} for a wide audience"
  output: article_text

step summarize_article:
  model: analyzer
  kind: text.summarize
  input:
    - article_text
  output: summary_text
```

Explanation:

- the `workflow` block defines the name, models and inputs/outputs;
- `generate_article` creates the main article;
- `summarize_article` produces a short summary from the generated article.

---

### 4. Step‑by‑step: how to design a workflow

1. **Describe the goal in plain language**  
   Example: “I need automatic articles on a topic and summaries for management reports.”

2. **Identify entities**  
   - input: article topic (`topic`);
   - intermediate result: full article text (`article_text`);
   - final output: short summary (`summary_text`).

3. **Split into steps**  
   - step 1: generate article;
   - step 2: summarize article.

4. **Write the steps in DSL**  
   - choose models;
   - formulate `intent` using natural language (with `auto` if desired).

---

### 5. Second example: multi‑agent “writer + critic”

Goal: create a draft article, then review and improve it.

File `examples/article_multi_agent.riva`:

```yaml
workflow article_multi_agent:
  description: "Writer and critic collaboratively create an improved article"

  agents:
    writer:
      model: gpt-4.1
      role: "Writes articles"
    critic:
      model: gpt-4.1-mini
      role: "Reviews and proposes improvements"

  inputs:
    topic: text

  outputs:
    draft: text
    improved_article: text

step write_draft:
  agent: writer
  kind: text.generate
  intent: auto "Write a draft article on {topic}"
  output: draft

step review_draft:
  agent: critic
  kind: text.generate
  input:
    - draft
  intent: auto "Provide concrete recommendations to improve this text"
  output: review_notes

step improve_article:
  agent: writer
  kind: text.generate
  input:
    - draft
    - review_notes
  intent: auto "Improve the draft using all recommendations"
  output: improved_article
```

This example demonstrates:

- how to declare agents with roles;
- how to organise “writer → critic → writer” collaboration.

---

### 6. Third example: work with external data (headlines from API)

Scenario: fetch a list of news items from an API and generate a summary.

File `examples/news_summary.riva`:

```yaml
workflow news_summary:
  description: "News headlines overview and summary"

  models:
    summarizer: gpt-4.1-mini

  outputs:
    headlines_text: text
    summary_text: text

step fetch_news:
  from:
    url: "https://example.com/api/news"
    method: GET
  output: headlines_json

step format_headlines:
  model: summarizer
  kind: text.generate
  input:
    - headlines_json
  intent: auto "Convert the news JSON list into a human‑readable list of headlines"
  output: headlines_text

step summarize_headlines:
  model: summarizer
  kind: text.summarize
  input:
    - headlines_text
  output: summary_text
```

---

### 7. Recommendations for writing your own workflows

- **Start with text, not DSL**: write down what should happen and in which order in plain language.
- **Use meaningful names**: `generate_article`, `summarize_article`, `fetch_news`, etc.
- **Leverage `auto`**: describe the goal of each step clearly and leave prompt details to the assistant.
- **Prefer small steps**: several simple steps are easier to understand and maintain than one overloaded step.
- **Add descriptions**: fill in `description` in `workflow` blocks to help others understand your intent.

---

### 8. Next learning steps

After you are comfortable with the basic examples, you can:

1. Read `docs/LANGUAGE_REFERENCE.en.md` to understand all available constructs.
2. Design a workflow for your own use case (for example, report generation, email preparation, feedback analysis).
3. Gradually add:
   - more steps;
   - multi‑agent interactions;
   - external data sources;
   - conditional behaviour (for example, notifications when certain keywords appear).

Runtime implementation and execution logic will be documented separately and is not part of this learning guide.

