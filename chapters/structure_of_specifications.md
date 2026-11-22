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
      terminology.yml
      references.md
  conformance-profiles/
      core-profile-name/
          introduction.md
          functionalities/
              functionality-1.md
              functionality-2.md
      extension-profile-name/
          introduction.md
          functionalities/
              functionality-3.md
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

A specification declares its capabilities in metadata.yml, under `capabilities`. Its conformance profiles are not declared in metadata.yml: each is declared in the front matter of its own `introduction.md` (see *Conformance profile files* below, and `chapters/functionalities_and_extensions.md`).

## Introduction files

The `introduction/` directory opens the specification. It tells a reader why the Building Block exists, what this document covers, and what a conformant software solution is. Each kind of content has its own file, so that a reader looking for the scope of a specification always finds it in the same place.

| File | Holds |
|---|---|
| `purpose.md` | The value the Building Block provides, the capabilities it offers, and its place in the Digital Service Systems and reference architectures it operates in |
| `scope.md` | What this document specifies, where the boundary of the Building Block sits and what conforms to it, what it extends, what it covers, and what it does not cover, with the owner of each excluded item |
| `technical_overview.md` | The market assessment that led to the conformance profiles, one table of the conformance profiles, what a conformance claim looks like, and how the profiles divide the capabilities |
| `terminology.yml` | Terms used in a non-obvious sense, and terms shared with adjacent specifications |
| `references.md` | Every external document the specification depends on |

Readers meet the files in the order Purpose, Scope, Technical Overview. `purpose.md` and `scope.md` are written for a wide, non-technical audience such as decision-makers and buyers; `technical_overview.md` is written for teams that build, implement or assess a solution, and comes last so that other readers can stop before it. Authors usually write the Technical Overview before the Scope, because the scope is derived from it. The conformance profiles named in the Technical Overview are the folders in `conformance-profiles/`, with the same titles.

See `chapters/introductory_chapters.md` for guidance on writing each section, and `resources/abstract_example.md` for the abstract Building Block the examples use.

## Conformance profile files

The `conformance-profiles/` directory holds the normative part of the specification. It has one folder per conformance profile, and nothing else. A specification has requirements rather than chapters, so this directory takes the place that `chapters/` has in other GovStack publications.

```mermaid
treeView
  conformance-profiles/
      file-management/
          introduction.md
          functionalities/
              file-storage.md
              access-control.md
      records-management/
          introduction.md
          functionalities/
              records-capture.md
```

- **The folder name is the id of the conformance profile.** It is written in kebab-case, is unique in the specification and is never reused. It names the profile, as `file-management` or `records-management`, never its type, as `core` or `extension-1`
- **`introduction.md`** declares the profile in its front matter and describes it in its body: why a buyer would choose it, example use cases, its functionalities, and the actors and interfaces relevant for testing it. See `chapters/functionalities_and_extensions.md`, *The introduction of a conformance profile*
- **`functionalities/`** holds one file per functionality of the profile. A functionality belongs to the profile whose folder it is in, so membership is stated once, by where the file is, and never in a field or a list

A specification with a single conformance profile still has one folder, of type `CORE`.

The front matter of `introduction.md`:

```yaml
---
title: <conformance profile name>
type: CORE # CORE, EXTENSION
description: <one sentence: what a software solution that meets the profile can do>
requires: [] # ids (folder names) of other extension profiles this one depends on
role: # optional: the role the Building Block performs through the profile, and towards whom
reference_architecture: # optional: the Domain Reference Architecture that describes the role
---
```

## Functionality files

Each file in a `functionalities/` folder is one functionality. It carries its requirements in its YAML front matter and describes the functionality, its workflows and its data structures in the body. Here's an example template

```yaml
---
title: <functionality name>
description: |
   <description from the business point of view>
realises: [] # ids of capabilities declared in metadata.yml
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

A functionality file has no field for its conformance profile. The folder it is in says which profile it belongs to. Moving a functionality to another profile means moving its file. See `chapters/functionalities_and_extensions.md`.

## Table of contents

`toc.yml` lists the conformance profiles under `conformance-profiles`, one subsection per profile, named after its folder. The pages of a subsection are relative to the profile's folder, and the introduction comes first:

```yaml
toc:
  conformance-profiles:
    - file-management:
        - introduction.md
        - functionalities/file-storage.md
        - functionalities/access-control.md
    - records-management:
        - introduction.md
        - functionalities/records-capture.md
```
