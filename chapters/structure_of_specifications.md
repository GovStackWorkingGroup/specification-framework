---
title: Structure of Specifications
authors:
  - name: Ali González-García
  - name: Kristo Vaher
references:
  - ISO/IEC/IEEE 29148:2018(E) Systems and software engineering — Life cycle processes — Requirements engineering, International Organization for Standardization, International Electrotechnical Commission, 2018.
---

## Repository outline

The structure of GovStack Specifications is based off the Software Requirements Specification instrument outlined at ISO/IEC/IEEE 29148:2018 Systems and software engineering — Life cycle processes — Requirements engineering. It takes the sections as a starting point and discards everything related to specific implementations. The minimum structure is as follows:

```mermaid
treeView
  .github/
  ADR/
  assets/
  introduction/
      purpose.md
      scope.md
      technical_overview.md
      terminology.md
      references.md
  requirements/
      functionality-1.md
      functionality-2.md
  resources/
      api.yml
      skills.md
      tests/
  metadata.yml
  authors.yml
  toc.yml
```

## Compilation files

metadata.yml, authors.yml and toc.yml are files necessary for the compilation of a specification. Further explanation can be read at `govstack:process` on its Compiling Specifications chapter.

A specification declares its capabilities in metadata.yml, under `capabilities`, and its extensions, if it has any, under `extensions`. See `chapters/functionalities_and_extensions.md`.

## Introduction files

The `introduction/` directory opens the specification. It tells a reader why the Building Block exists, what this document covers, and what a conformant solution is. Each kind of content has its own file, so that a reader looking for the scope of a specification always finds it in the same place.

| File | Holds |
|---|---|
| `purpose.md` | The value the Building Block provides, the capabilities it offers, and its place in the Digital Service Systems and reference architectures it operates in |
| `scope.md` | What this document specifies, where the boundary of the Building Block sits and what conforms to it, what it extends, what it covers, and what it does not cover, with the owner of each excluded item |
| `technical_overview.md` | The market assessment that led to the functionalities, the functionalities and how they divide into a core and extensions, and, optionally, the actors around the Building Block and the interfaces that cross its boundary |
| `terminology.md` | Terms used in a non-obvious sense, and terms shared with adjacent specifications |
| `references.md` | Every external document the specification depends on |

Readers meet the files in the order Purpose, Scope, Technical Overview. `purpose.md` and `scope.md` are written for a wide, non-technical audience such as decision-makers and buyers; `technical_overview.md` is written for teams that build, implement or assess a solution, and comes last so that other readers can stop before it. Authors usually write the Technical Overview before the Scope, because the scope is derived from it. The functionalities named in the Technical Overview are the files in `requirements/`, with the same names.

See `chapters/introductory_chapters.md` for guidance on writing each section, and `resources/examples/wallet/introduction/` for a worked example.

## Requirement files

The `requirements/` directory holds one file per functionality. A specification has requirements rather than chapters, so this directory takes the place that `chapters/` has in other GovStack publications. Each file carries its requirements in its YAML front matter and describes the functionality, its workflows and its data structures in the body. Here's an example template

```yaml
---
title: <functionality name>
description: |
   <description from the business point of view>
realises: [] # ids of capabilities declared in metadata.yml
conformance: CORE # CORE, EXTENSION
extension: # id of an extension declared in metadata.yml; only when conformance is EXTENSION
requirements:
  - id: '1'
    status: DRAFT # DRAFT, WITHDRAWN, CANDIDATE, ACCEPTED, PUBLISHED, DEPRECATED
    level: RECOMMENDED # RECOMMENDED, REQUIRED
    observability: # OBSERVABLE, AUDITABLE
    mutability: # IMMUTABLE, EXTENSIBLE, REPLACEABLE, INAPPLICABLE
    interaction: # SERVICE_API, CONSUMED_API, PROTOCOL, EVENT, USER_INTERFACE, DEVICE, FILE, INTERNAL
    contract: # where the contract is defined, when it is defined elsewhere
    statement: <one sentence>
    documentation: # conditions, constraints and, for AUDITABLE requirements, the evidence a vendor produces
    api_operation_id:
    test:
---
```

`realises` lists the capabilities the functionality realises. Every functionality realises at least one, and every capability declared in metadata.yml is realised by at least one functionality.

`conformance` says whether the functionality is part of the core, which every conformant solution meets, or of an extension, which a solution may claim in addition to the core. It defaults to `CORE`. See `chapters/functionalities_and_extensions.md`.
