## RIVA DSL Versioning Policy

Document version: 0.1.0  
Status: Draft

### 1. Version levels

Semantic versioning `MAJOR.MINOR.PATCH` is applied to two independent dimensions:

- **Language (DSL) version** — syntax and semantics of RIVA DSL.
- **Runtime/engine version** — implementation of the interpreter/executor.

Later we may add versions for individual workflow templates.

### 2. Semantic versioning

- **MAJOR**:
  - breaking changes in DSL syntax;
  - removal or radical change of core constructs;
  - dropping previously supported file formats.

- **MINOR**:
  - new constructs and fields without breaking existing workflows;
  - extending allowed values of existing attributes;
  - improvements that do not require migration.

- **PATCH**:
  - documentation clarifications;
  - specification fixes and disambiguation;
  - editorial changes that do not affect behaviour.

### 3. Version markers in the repository

- The main DSL version is stated in:
  - `README.md` / `README.en.md` (short);
  - `docs/OVERVIEW*.md` and `docs/LANGUAGE_REFERENCE*.md` (detailed headers).
- Runtime and tooling versions will be tracked in:
  - `RUNTIME_VERSION.md` (planned);
  - Git tags, for example `dsl-v1.0.0`, `runtime-v1.0.0`.

### 4. Managing language changes

For any DSL change:

1. **Proposal description**
   - short motivation;
   - impact on existing workflows;
   - before/after examples.

2. **Discussion**
   - issue tracker and/or RFC documents;
   - consensus on the version level (MAJOR/MINOR/PATCH).

3. **Implementation**
   - update specification in `docs/LANGUAGE_REFERENCE.en.md` and `.md`;
   - add or update examples in `examples/`;
   - if needed, provide migration notes.

4. **Release**
   - assign a new version number;
   - update all relevant documentation references;
   - describe changes in a changelog file.

### 5. Changelog

Change history will be tracked in a `CHANGELOG.md` file (planned) with the following structure:

- header with version number and date;
- sections:
  - `Added`;
  - `Changed`;
  - `Deprecated`;
  - `Removed`;
  - `Fixed`;
  - `Security`.

### 6. Deprecations and migrations

- Any planned removal of a construct in a future major release must:
  - be documented in advance;
  - have a deprecation phase where the construct is marked as `deprecated`;
  - include clear migration guidance.

- Migration tooling (CLI or scripts) will be designed separately, but the approach will be documented in runtime documentation.

### 7. Current status

- Current DSL concept version: `0.1.0` (design phase, runtime not implemented).
- Significant changes at this stage are possible but must be reflected in this document and in the language reference.

