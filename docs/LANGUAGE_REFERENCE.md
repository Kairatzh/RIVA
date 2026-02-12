## RIVA DSL: справочник по языку

Версия документа: 0.1.0  
Статус: черновик (Language Draft)

### 1. Общий формат

RIVA DSL задуман как человеко‑читаемый декларативный язык. В первой версии синтаксис основан на структурированном текстовом формате (YAML‑подобный стиль), ориентированном на:

- читаемость в Git;
- удобство ручного редактирования;
- простоту диффов и ревью.

Рекомендуемое расширение файлов: `.riva` или `.riva.yaml`.

### 2. Базовые сущности

#### 2.1. Workflow

`workflow` описывает полный сценарий работы с LLM.

Пример:

```yaml
workflow article_pipeline:
  description: "Пайплайн генерации и анализа статьи"

  models:
    writer: gpt-4.1
    analyzer: gpt-4.1-mini

  inputs:
    topic: text

  outputs:
    article_text: text
    summary_text: text
```

Ключевые поля:

- `description` — человеко‑читаемое описание.
- `models` — набор именованных моделей, используемых в шагов.
- `inputs` / `outputs` — именованные данные, доступные workflow с типами.

#### 2.2. Step

`step` описывает один логический шаг внутри workflow.

Общий вид:

```yaml
step <имя_шага>:
  model: <имя_модели_из_models>
  kind: <тип_операции>
  intent: <описание_задачи_на_естественном_языке>
  input:
    - <имя_входной_переменной>
  output: <имя_выходной_переменной>
```

Типичные значения `kind`:

- `text.generate` — генерация текста;
- `text.summarize` — суммаризация;
- `text.classify` — классификация;
- `code.generate` — генерация кода;
- `table.from_text` — извлечение табличной структуры;
- `json.from_text` — извлечение структуры JSON.

#### 2.3. Agents

`agent` — именованная роль, основанная на модели. Агент может использоваться в шагах вместо прямой ссылки на модель.

Пример:

```yaml
agents:
  writer:
    model: gpt-4.1
    role: "Пишет структурированные тексты"
  critic:
    model: gpt-4.1-mini
    role: "Проверяет и улучшает тексты"
```

В шагах:

```yaml
step draft_article:
  agent: writer
  kind: text.generate
  intent: "Написать черновик статьи по теме {topic}"
  output: draft_text
```

#### 2.4. Data

Данные в DSL описываются через именованные переменные с типами:

- базовые типы: `text`, `json`, `table`, `file`, `number`, `bool`;
- составные/коллекции: `list<text>`, `list<json>` и т.д.

Пример объявления:

```yaml
data:
  article_text: text
  summary_text: text
  entities: json
```

### 3. Мини‑ИИ и ключевое слово `auto`

Ключевое слово `auto` говорит рантайму, что часть настроек должна быть автоматически сконструирована встроенным ИИ‑помощником.

Типичные места применения:

- `intent: auto "<описание_цели>"` — автоматическое построение промпта;
- `schema: auto "<словесное_описание_структуры>"` — выведение схемы JSON/таблицы;
- `chain: auto "<описание_workflow_на_словах>"` — автогенерация цепочки шагов.

Пример:

```yaml
step generate_article:
  model: writer
  kind: text.generate
  intent: auto "Написать развёрнутую статью про {topic} для начинающих"
  output: article_text
```

### 4. Контроль потока (flow control)

Первая версия DSL поддерживает:

- последовательное выполнение шагов (дефолт);
- простые условные блоки `if`;
- декларативные циклы `for each`;
- параллельные группы `parallel`.

#### 4.1. Последовательное выполнение

Последовательность шагов в файле определяет порядок их выполнения, если не указано иное.

#### 4.2. Условия

Общий вид:

```yaml
if <условие>:
  step <имя_шага>:
    ...
```

Пример:

```yaml
if summary_text contains "urgent":
  step send_alert:
    model: notifier
    kind: text.generate
    intent: auto "Предупреди команду, что обнаружен срочный контент"
    output: alert_message
```

#### 4.3. Циклы

Общий вид:

```yaml
for each <элемент> in <коллекция>:
  step <имя_шага>:
    input:
      - <элемент>
    output: <результат_в_коллекцию>
```

Пример:

```yaml
for each article in articles:
  step summarize_article:
    model: analyzer
    kind: text.summarize
    input:
      - article
    output: summaries[]
```

#### 4.4. Параллельные блоки

Общий вид:

```yaml
parallel:
  step <имя_шага_1>:
    ...
  step <имя_шага_2>:
    ...
```

Пример:

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

### 5. Взаимодействие с внешними источниками

#### 5.1. HTTP / API

```yaml
step fetch_data:
  from:
    url: "https://example.com/data.json"
    method: GET
  output: raw_data
```

#### 5.2. Файловая система

```yaml
step load_documents:
  from:
    folder: "./docs"
    pattern: "*.pdf"
  output: docs_files
```

### 6. Инфраструктурные параметры шагов

Каждый `step` может опционально задавать:

- `retry` — стратегию повторных попыток;
- `timeout` — таймаут;
- `cache` — настройки кеширования.

Пример:

```yaml
step generate_article:
  model: writer
  kind: text.generate
  intent: auto "Написать статью о безопасности ИИ"
  output: article_text

  retry:
    max_attempts: 3

  timeout: 60s

  cache:
    ttl: 24h
    key: topic
```

### 7. Ограничения и статус разработки

- Текущая спецификация отражает раннюю версию языка и может меняться.
- Обратная совместимость будет учитываться в соответствии с политикой в `docs/VERSIONING.md`.
- Реализация рантайма и исполнение DSL в коде на данном этапе находятся в фазе проектирования.

