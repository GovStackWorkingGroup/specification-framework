---
title: Organise a specification's functionalities into conformance profiles
status: proposed
date: 2026-11-22
decision-makers:
  - Architecture Committee
consulted:
  - Architecture Working Group
  - Wallet Working Group
  - Payments Working Group
  - Document Management Working Group
  - GovStack Technical Committee
informed:
  - All Working Groups
  - GovMarket
---
<!-- markdownlint-disable-next-line MD025 -->
# Organise a specification's functionalities into conformance profiles

## Context and Problem Statement

A Building Block specification is organised by functionalities, one file each in `requirements/`. Today every functionality applies to every conformant software solution. GovMarket lists a software solution only if it meets 100% of the `REQUIRED` requirements of the specification, so every `REQUIRED` requirement in every functionality binds every product.

Three Working Groups have found that this does not match their domains.

### Payments: a core and separately assessed parts

The Payments Working Group is re-scoping its specification "to better fit composability and compliance testing" (`bb-payments/ADR/ADR-bb-payments-001.md`). Its analysis proposes a Payments Hub as the core, which every payment direction needs, plus parts that not every deployment uses. Its preferred variant, C2, publishes one Payments specification made of "the Hub as the required core plus four optional, separately assessed extensions", and records that it "depends on GovStack defining how a BB extension is named, versioned and assessed".

### Wallet: obligations towards a domain role

GovStack Building Blocks are autonomous. A Domain Reference Architecture carries no requirements, references Building Block specifications in one direction only, and "Obligations that a Building Block has towards a domain role belong in that Building Block specification" (`govstack-process/chapters/reference_architectures.md`, Part 5).

The Wallet Building Block performs the Credential Holder role in the Issuer-Holder-Verifier model. Its 2.0 reorganisation (`bb-wallet/research/2.0-requirements-reorganization-proposal.md`) sorts its requirements into two kinds:

* **Autonomous:** testable on the wallet alone. For example, keys in a secure environment, holder authentication, consent, removal of a credential, transaction logs.
* **Bound to the reference architecture:** they need an Issuer or a Verifier that follows a given protocol. For example, requesting credentials over OpenID4VCI, presenting over OpenID4VP, checking a status list or a trust list.

Both kinds belong in the Wallet specification. But if every functionality binds every product, the specification must either make every wallet implement every exchange of the reference architecture, or let a protocol choice decide whether a product is a wallet at all. The Wallet Working Group met the second case directly. Read against its autonomous requirements only, a crypto hardware wallet that holds no issued credential passes almost all of them, and is excluded only by a requirement whose single published alternative is OpenID4VCI (`bb-wallet/ADR/WALLET-TDR-2026-10-3.md` and the proposal above).

### Document Management: testing the model on a draft

The Document Management Working Group used its draft (`bb-file-management`) to test successive versions of this ADR:

| Draft | Model | What the test showed |
|---|---|---|
| v0.3 | A core and extensions, with `conformance` and `extension` fields on each functionality and `extensions` in `metadata.yml` | Works, but a reader cannot see which functionalities make up an extension without opening every file. The overview's functionality table repeats the fields and drifts from them |
| v0.4 | Three overlapping conformance profiles, each listing every functionality it requires; the two specialised profiles include the general one | A buyer reads the same functionalities in several profiles, and every change to the shared ones touches every profile |
| v0.5, v0.6 | Conformance modules: one core and two extensions that do not repeat it, declared in `metadata.yml` (with their functionalities) and described in a page per module in `conformance-modules/`; the functionality files stay in `requirements/` | The module page, written for a buyer and an assessor (why choose it, use cases, functionalities, actors, interfaces), is what readers open first. The overview's functionality table and its whole-Building-Block map become redundant. Membership is stated three times: in `metadata.yml`, on the module page and in `toc.yml`. `requirements/` holds functionality files, not requirements |

The test kept one core with extensions that add to it, and showed that each conformance profile needs a page of its own. It also showed that the profile, its page and its functionalities belong together on disk, so that membership is stated once.

### What the current model offers, and why it fails here

| Mechanism | Unit | Why it does not fit |
|---|---|---|
| `extends`, and profiles (`chapters/versioning_and_change_documentation.md`, *Extended specifications*) | A whole specification inherits another, and may tighten it | Too coarse. Each part would be a separate publication, owned by the same Working Group, with its own version and release, and a buyer would cite several documents for one product |
| `RECOMMENDED` | One requirement | Says the requirement may be skipped, and turns a whole capability into a quality score. A product that claims a capability must meet all of it, which `RECOMMENDED` cannot express |
| Variation points and conditional requirements (ARCH-ADR-6, proposed) | One requirement and the requirements that depend on it | Expresses "at least one of" inside a functionality. It has no unit for "these functionalities, as a whole, are claimed or not", and a variation point has `min` of at least 1, so it cannot be skipped |

What is missing is the middle: **a named set of functionalities inside one specification that a software solution claims or does not claim, and must meet in full when it claims it.**

## Decision Drivers

* Building Blocks are autonomous. A software solution must be able to conform without any counterpart following the protocol of a particular domain
* Domain Reference Architectures carry no requirements. A Building Block's obligations towards a domain role must have a place in the Building Block specification
* Not every conformant product offers every capability of a Building Block, and some capabilities make sense only in some deployments
* GovMarket's rule must keep its meaning: whatever a software solution claims, it meets 100% of the `REQUIRED` requirements of
* A buyer must be able to cite precisely what they procure, in one identifier per Building Block
* One Working Group, one specification, one release cycle: the parts of a Building Block should not need separate publications
* Each fact is stated once. Which functionalities belong to which part must not be repeated in fields, lists and tables that can drift apart
* A reader deciding what to require, and an assessor planning tests, should find everything about one part in one place
* Folder and file names should say what they hold
* The model should fit ARCH-ADR-5 (status and level) and ARCH-ADR-6 (variation points and conditional requirements), and work if ARCH-ADR-6 is not accepted
* The model should use established vocabulary rather than invent its own
* `extends`, profile and the new unit must not be confused with each other

## Considered Options

* **Option 1:** No new mechanism: mark non-core functionalities `RECOMMENDED`
* **Option 2:** Separate specifications: each non-core part is its own publication that `extends` the core specification
* **Option 3:** Core and extension classifiers: a `conformance` and `extension` field on each functionality, with extensions declared in `metadata.yml`
* **Option 4:** Reuse ARCH-ADR-6: model each extension as an alternative of a specification-level variation point with `min: 0`
* **Option 5:** Overlapping conformance profiles, each listing every functionality it requires
* **Option 6:** Conformance profiles declared in `metadata.yml`, with a page per profile and the functionality files in `requirements/`
* **Option 7:** Conformance profile folders: one folder per profile, holding its introduction and its functionality files, with a core profile and extension profiles

## Decision Outcome

*To be completed by the Architecture Committee.*

The author recommends **Option 7**. The previous revision of this record recommended Option 3, on behalf of the Wallet Working Group; Option 7 keeps its substance and changes where the profiles are declared. Like Options 3 and 6, it keeps a Building Block in one publication, lets a buyer cite exactly what they procure, keeps GovMarket's 100% rule meaningful for everything a software solution claims, and gives obligations towards a domain role a defined place. In addition, it states which functionalities belong to a profile once, by where their files are, and puts everything a reader needs about one profile in one folder. It was tested on the Document Management draft.

### Vocabulary

* **Conformance profile**: a named set of one or more functionalities that a software solution claims as a whole. Always written in full, so that it is not read as a profile in the sense of a separate specification.
* **Core**: the conformance profile of type `CORE`, which every conformant software solution meets. It is what makes a software solution an instance of the Building Block. A specification has exactly one.
* **Extension**: a conformance profile of type `EXTENSION`, which a software solution may claim in addition to the core. A software solution that claims an extension meets it in full. An extension holds only what it adds to the core.
* **Conformance declaration**: the list of extensions (and, under ARCH-ADR-6, alternatives) a software solution claims. It is supplied with the software solution when it is assessed.

This is the structure of OGC 08-131r3, *The Specification Model — A Standard for Modular Specifications*, which organises a standard into a core and extensions, each a requirements class with a matching conformance class, and defines a profile separately.

The word "optional" is not used for extensions, for the reason given in ARCH-ADR-6: in GovStack usage it already means `RECOMMENDED`.

### Layout

The `requirements/` folder is replaced by `conformance-profiles/`. It holds one folder per conformance profile:

```text
conformance-profiles/
  core-profile/              # the folder name is the id, in kebab-case
    introduction.md          # declares and describes the profile
    functionalities/
      functionality-a.md     # one file per functionality, as before
      functionality-b.md
  extension-x/
    introduction.md
    functionalities/
      functionality-x.md
```

* The folder name is the id of the profile. It names the profile, never its type, is unique in the specification and is never reused.
* A functionality belongs to the profile whose `functionalities/` folder holds its file. Membership is stated nowhere else.
* The folder that held functionality files is no longer called `requirements/`. The name now says what each level holds: profiles, their introductions, their functionalities.

### Fields

The profile is declared in the front matter of its `introduction.md`:

```yaml
---
title: Extension Y
type: EXTENSION # CORE, EXTENSION
description: <what a software solution that claims it can do that the core alone cannot>
requires: [extension-x] # ids of other extension profiles this one depends on
role: Role B, towards Role Q # optional
reference_architecture: govstack:ra:example # optional
---
```

`role` and `reference_architecture` place the profile for the reader, as *Place in larger systems* does for the whole Building Block. They do not import the reference architecture: the requirements of an extension cite their standards directly, and are complete without it. The one-way relationship between specifications and reference architectures is kept.

The functionality files lose nothing but gain no field either: there is no `conformance` or `extension` field, and `metadata.yml` has no `extensions` block.

### The introduction of a profile, and the Technical Overview

The body of `introduction.md` describes the profile for a buyer and an assessor: *Why choose this conformance profile?*, *Example use cases*, *Functionalities* (a table and a functionality map of the profile), and optionally *Actors relevant for testing* and *Interfaces*. An extension's introduction refers to the core's for the core's actors and interfaces.

The Technical Overview no longer lists the functionalities, draws the whole functionality map, or holds the actor and interface tables: those move into the introductions. It keeps the *Market assessment*, and explains the profiles as a whole in one table (profile, type, functionalities, requires, suited to), followed by what a conformance claim looks like for the kinds of system surveyed, and how the profiles divide the capabilities.

### Rules

1. A specification has exactly one profile of type `CORE`, with at least one functionality. A specification without extensions has only the core.
2. A functionality belongs to exactly one profile. Requirements are not split between profiles within one file.
3. The core must stand alone: a software solution that meets only the core is a useful, conformant instance of the Building Block.
4. An extension holds only what it adds, and does not repeat the core. It may add to the behaviour of a core functionality, for example by requiring more of it when the extension is claimed. It may not weaken or replace a core requirement.
5. An extension may require other extensions (`requires`). The core never depends on an extension.
6. Under ARCH-ADR-6, `applies_when` may name the id of an extension profile, so that a requirement in a core functionality applies only when the extension is claimed. Without ARCH-ADR-6, such a requirement is written in a functionality of the extension.
7. A variation point may sit in an extension. It then binds only software solutions that claim the extension.

### Conformance

* Every conformant software solution meets the core.
* A software solution's conformance declaration lists the extensions it claims. A claimed extension fails if any of its `REQUIRED` requirements, or of the extensions it requires, is not met.
* The requirements of an extension that is not claimed are reported as **not applicable**, neither passed nor failed. A `RECOMMENDED` requirement in an unclaimed extension is left out of the share GovMarket reports.
* GovMarket shows the claimed profiles as facts about the software solution ("Example 1.0: Core Profile, Extension X, Extension Y"), not as a score. A procurer can filter on them.
* A profile takes part in compliance through its `PUBLISHED` requirements (ARCH-ADR-5). An extension whose requirements are all `DRAFT` can be declared and tested, but conformance to it means nothing yet.

### Versioning

Under `requirements/use_semantic_versioning.md`, the requirements of a profile follow the same rules as any other, judged against the software solutions that claim the profile. In addition:

* Adding an extension does not change any existing claim: **minor**.
* Moving a functionality from the core into an extension removes a guarantee from every citation of the specification: **major**.
* Moving a functionality from an extension into the core adds obligations to every software solution: **major**.
* Moving a functionality between extensions, deprecating an extension, or adding a dependency between extensions: **major**.
* Moving a functionality between profiles is done by moving its file between folders, and is recorded in the changelog.
* A profile's folder is not renamed, because its name is the id cited in conformance declarations. Its `title` may change.

### Compiler checks

* `conformance-profiles/` exists and holds only folders. Each folder name is kebab-case and holds an `introduction.md` and a `functionalities/` folder with at least one file.
* Exactly one profile has `type: CORE`. Every other has `type: EXTENSION`.
* `requires` is empty for the core. Every id in it is the folder name of an extension profile, and there are no cycles.
* Every id in `applies_when` is an alternative or the id of an extension profile (with ARCH-ADR-6).
* Every functionality listed in the *Functionalities* table of an introduction is a file in that profile's `functionalities/` folder, and every file there is listed.
* `toc.yml` lists every profile once, as a subsection of `conformance-profiles` named after its folder, with `introduction.md` first.
* Warn when an extension has no `REQUIRED` requirement: it may be better expressed as `RECOMMENDED` requirements in the core.
* Warn when a functionality file still has a `conformance` or `extension` field, or `metadata.yml` an `extensions` block, and ignore them.

### Consequences

* *Structure of Specifications* (`chapters/structure_of_specifications.md`) replaces `requirements/` with `conformance-profiles/` in the repository outline, adds *Conformance profile files* with the front matter of `introduction.md`, renames *Requirement files* to *Functionality files* without the `conformance` and `extension` fields, and shows the `toc.yml` subsections.
* *Functionalities and Extensions* (`chapters/functionalities_and_extensions.md`) becomes *Functionalities and Conformance Profiles*, with the vocabulary, rules and guidance above, and a section on writing the introduction of a profile. The actor and interface guidance moves there from *Introductory Chapters*.
* *Introductory Chapters* reduces the Technical Overview to the *Market assessment* and *Conformance profiles*, with the profile table, the conformance claim table and the capability table, and updates the checks before publication.
* *Requirements* (`chapters/requirements.md`) points the *Interfaces* table to the introduction of each profile.
* The Wallet worked example is removed. The chapters illustrate the model with an abstract Building Block, `resources/abstract_example.md`, whose names are placeholders and do not go out of date as real specifications change.
* `bb-template` replaces `requirements/` with `conformance-profiles/`, and its `metadata.yml` drops `extensions`.
* The `publications-compiler` accepts `conformance-profiles` in `toc.yml` in place of `requirements`, with one level of subsections whose pages resolve relative to the profile's folder (today `toc_parser#req-7` allows subsections under `chapters` only), and implements the checks above.
* The compliance process and GovMarket take the extensions in a conformance declaration as input. The format of the declaration is defined with the compliance process, outside this ADR, as in ARCH-ADR-6.
* The namespaces chapter of the GovStack Process defines how a profile is cited, for example as an identifier of the specification plus the profile's folder name. Naming is under revision, so this ADR does not fix the form.
* The Domain Reference Architecture chapter can map a capability to "Building Block in a role, through extension X" in its Part 3, Capability mapping.
* Specifications already published have a single profile. Moving their `requirements/` files into `conformance-profiles/<core>/functionalities/` changes no requirement, and is a patch release.
* The Document Management draft moves its `conformance-modules/` pages and its `requirements/` files into `conformance-profiles/`, and drops `conformance_modules` from `metadata.yml`.

### Confirmation

Confirmed by the Architecture Committee following Architecture Working Group discussion. Implemented in `chapters/functionalities_and_extensions.md`, `chapters/structure_of_specifications.md`, `chapters/introductory_chapters.md`, `chapters/requirements.md`, `resources/abstract_example.md`, `bb-template`, and the `publications-compiler` requirements and toc parsers. The Wallet Building Block 2.0, the Payments Building Block (variant C2) and the Document Management Building Block are the first specifications to use it.

## Pros and Cons of the Options

### Option 1: Mark non-core functionalities `RECOMMENDED`

* Good, because no text, template or tooling changes
* Bad, because a software solution that claims a capability is not held to all of it: it can meet half of an extension and still score
* Bad, because it turns the presence of a capability into a quality score, which is not what the Working Groups mean
* Bad, because it cannot express that a part of the Building Block is `REQUIRED` once claimed
* Bad, because a buyer cannot cite "this Building Block, with this capability"

### Option 2: Separate specifications that extend the core

* Good, because it reuses `extends`, which exists
* Good, because each part has its own identifier, version and conformance
* Bad, because one Working Group maintains several publications for one Building Block, with separate releases to coordinate
* Bad, because a buyer cites several documents and versions for one product, and must check they are compatible
* Bad, because it stretches `extends` and profile, which mean inheriting and narrowing a whole specification, to mean adding a part
* Bad, because the parts' dependency on the core version makes every core release a cascade of releases

### Option 3: Core and extension classifiers

* Good, because the Building Block stays one publication, with one version and one identifier
* Good, because a claim is all or nothing, so GovMarket's 100% rule keeps its meaning for everything a software solution claims
* Good, because obligations towards a domain role get a defined place
* Good, because it follows established practice (OGC 08-131r3)
* Bad, because which functionalities make up an extension is visible only by opening every file, and the overview's table repeats the fields and drifts from them (Document Management v0.3)
* Bad, because an extension has no page of its own for a buyer or an assessor
* Bad, because every functionality carries two more fields, and `metadata.yml` one more block

### Option 4: Extensions as alternatives of a variation point with `min: 0`

* Good, because it adds no new concept beyond ARCH-ADR-6
* Bad, because it needs ARCH-ADR-6 to allow `min: 0`, which turns a variation point into something a software solution can skip, against its definition
* Bad, because it uses a requirement as a switch: the variation point and its alternatives state no behaviour, so they have no test
* Bad, because it has no place for the role an extension serves or for dependencies between extensions
* Bad, because it depends on ARCH-ADR-6 being accepted

### Option 5: Overlapping conformance profiles

* Good, because each profile lists everything a software solution that claims it must meet
* Good, because a profile can be read on its own
* Bad, because shared functionalities appear in several profiles, and every change to them touches every profile that lists them (Document Management v0.4)
* Bad, because there is no guaranteed common part: two profiles may share nothing, and "conformant to the Building Block" stops meaning one thing
* Bad, because a functionality may belong to several profiles, so membership cannot follow from where its file is

### Option 6: Profiles declared in `metadata.yml`, with a page per profile

* Good, because each profile has a page written for a buyer and an assessor (Document Management v0.5 and v0.6)
* Good, because functionality files carry no conformance fields
* Bad, because membership is stated three times: in `metadata.yml`, on the page and in `toc.yml`
* Bad, because a profile's page and its functionalities sit in different folders, and `requirements/` holds functionality files, not requirements

### Option 7: Conformance profile folders

* Good, because the Building Block stays one publication, with one version and one identifier
* Good, because a claim is all or nothing, so GovMarket's 100% rule keeps its meaning for everything a software solution claims
* Good, because membership is stated once, by where a functionality file is, so no table or field can disagree with it
* Good, because everything about one profile, its page and its functionalities, is in one folder, and an extension's folder holds only what it adds
* Good, because the folder names say what they hold, and the folder name is the id
* Good, because the Technical Overview becomes short: a reader chooses profiles there, and reads each profile's introduction for the rest
* Good, because it follows established practice (OGC 08-131r3), composes with ARCH-ADR-6 and works without it
* Good, because it was tested on a real draft (Document Management)
* Neutral, because "conformance profile" sounds like "profile"; the term is always written in full, and the glossary keeps them apart
* Bad, because moving a functionality between profiles moves its file, which changes its path and the links to it
* Bad, because the compiler must accept subsections in `toc.yml` outside `chapters`, and every existing specification moves its files once

## More information

* `chapters/structure_of_specifications.md`, *Conformance profile files* and *Functionality files*
* `chapters/functionalities_and_extensions.md`, *Conformance profiles*
* `chapters/introductory_chapters.md`, *Technical Overview* and *Scope*
* `chapters/versioning_and_change_documentation.md`, *Extended specifications*
* `govstack-process/chapters/reference_architectures.md`, *Relationship to other publications* and Part 5
* `bb-payments/ADR/ADR-bb-payments-001.md` and `ADR-bb-payments-001-analysis.md`, variant C2
* `bb-wallet/research/2.0-requirements-reorganization-proposal.md`, *Autonomous and Reference Architecture bound requirements* and *What a conformance claim looks like*
* `bb-file-management`, drafts v0.3 to v0.6, `CHANGELOG.md`
* ARCH-ADR-5, *Separate a requirement's obligation level from its lifecycle status*
* ARCH-ADR-6 (proposed), *Model alternative and conditional requirements*, `bb-wallet/research/adr-alternative-requirements.md`
* OGC 08-131r3, *The Specification Model — A Standard for Modular Specifications*, Open Geospatial Consortium, 2009
