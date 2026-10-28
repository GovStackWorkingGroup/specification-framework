---
title: Organise a specification's functionalities into a core and extensions
status: proposed
date: 2026-10-26
decision-makers:
  - Architecture Committee
consulted:
  - Architecture Working Group
  - Wallet Working Group
  - Payments Working Group
  - GovStack Technical Committee
informed:
  - All Working Groups
  - GovMarket
---
<!-- markdownlint-disable-next-line MD025 -->
# Organise a specification's functionalities into a core and extensions

## Context and Problem Statement

A Building Block specification is organised by functionalities, one file each in `requirements/` (`chapters/structure_of_specifications.md`). Today every functionality applies to every conformant solution. GovMarket lists a solution only if it meets 100% of the `REQUIRED` requirements of the specification, so every `REQUIRED` requirement in every functionality binds every product.

Two Working Groups have found that this does not match their domains.

### Payments: a core and separately assessed parts

The Payments Working Group is re-scoping its specification "to better fit composability and compliance testing" (`bb-payments/ADR/ADR-bb-payments-001.md`). Its analysis proposes a Payments Hub as the core, which every payment direction needs, plus parts that not every deployment uses. Its preferred variant, C2, publishes one Payments specification made of "the Hub as the required core plus four optional, separately assessed extensions", and records that it "depends on GovStack defining how a BB extension is named, versioned and assessed".

### Wallet: obligations towards a domain role

GovStack Building Blocks are autonomous. A Domain Reference Architecture carries no requirements, references Building Block specifications in one direction only, and "Obligations that a Building Block has towards a domain role belong in that Building Block specification" (`govstack-process/chapters/reference_architectures.md`, Part 5).

The Wallet Building Block performs the Credential Holder role in the Issuer-Holder-Verifier model. Its 2.0 reorganisation (`bb-wallet/research/2.0-requirements-reorganization-proposal.md`) sorts its requirements into two kinds:

* **Autonomous:** testable on the wallet alone. For example, keys in a secure environment, holder authentication, consent, removal of a credential, transaction logs.
* **Bound to the reference architecture:** they need an Issuer or a Verifier that follows a given protocol. For example, requesting credentials over OpenID4VCI, presenting over OpenID4VP, checking a status list or a trust list.

Both kinds belong in the Wallet specification. But if every functionality binds every product, the specification must either make every wallet implement every exchange of the reference architecture, or let a protocol choice decide whether a product is a wallet at all. The Wallet Working Group met the second case directly. Read against its autonomous requirements only, a crypto hardware wallet that holds no issued credential passes almost all of them, and is excluded only by a requirement whose single published alternative is OpenID4VCI (`bb-wallet/ADR/WALLET-TDR-2026-10-3.md` and the proposal above).

### What the current model offers, and why it fails here

| Mechanism | Unit | Why it does not fit |
|---|---|---|
| `extends`, and profiles (`chapters/versioning_and_change_documentation.md`, *Extended specifications*) | A whole specification inherits another, and may tighten it | Too coarse. Each part would be a separate publication, owned by the same Working Group, with its own version and release, and a buyer would cite several documents for one product |
| `RECOMMENDED` | One requirement | Says the requirement may be skipped, and turns a whole capability into a quality score. A product that claims a capability must meet all of it, which `RECOMMENDED` cannot express |
| Variation points and conditional requirements (ARCH-ADR-6, proposed) | One requirement and the requirements that depend on it | Expresses "at least one of" inside a functionality. It has no unit for "these functionalities, as a whole, are claimed or not", and a variation point has `min` of at least 1, so it cannot be skipped |

What is missing is the middle: **a named set of functionalities inside one specification that a solution claims or does not claim, and must meet in full when it claims it.**

## Decision Drivers

* Building Blocks are autonomous. A solution must be able to conform without any counterpart following the protocol of a particular domain
* Domain Reference Architectures carry no requirements. A Building Block's obligations towards a domain role must have a place in the Building Block specification
* Not every conformant product offers every capability of a Building Block, and some capabilities make sense only in some deployments
* GovMarket's rule must keep its meaning: whatever a solution claims, it meets 100% of the `REQUIRED` requirements of
* A buyer must be able to cite precisely what they procure, in one identifier per Building Block
* One Working Group, one specification, one release cycle: the parts of a Building Block should not need separate publications
* The model should fit ARCH-ADR-5 (status and level) and ARCH-ADR-6 (variation points and conditional requirements), and work if ARCH-ADR-6 is not accepted
* The model should use established vocabulary rather than invent its own
* `extends`, profile and the new unit must not be confused with each other

## Considered Options

* **Option 1:** No new mechanism: mark non-core functionalities `RECOMMENDED`
* **Option 2:** Separate specifications: each non-core part is its own publication that `extends` the core specification
* **Option 3:** Core and extension functionalities: a classifier on each functionality, with extensions declared in the specification
* **Option 4:** Reuse ARCH-ADR-6: model each extension as an alternative of a specification-level variation point with `min: 0`

## Decision Outcome

*To be completed by the Architecture Committee.*

The Wallet Working Group recommends **Option 3**. It is the only option that keeps a Building Block in one publication, lets a buyer cite exactly what they procure, and keeps GovMarket's 100% rule meaningful for everything a solution claims. It also gives obligations towards a domain role a defined place, which the Domain Reference Architecture chapter already requires and the framework does not yet provide.

### Vocabulary

* **Core**: the functionalities that every conformant solution meets. They are what makes a solution an instance of the Building Block.
* **Extension**: a named set of one or more functionalities that a solution may claim in addition to the core. A solution that claims an extension meets it in full.
* **Core functionality** and **extension functionality**: a functionality that belongs to the core, or to one extension.
* **Conformance declaration**: the list of extensions (and, under ARCH-ADR-6, alternatives) a solution claims. It is supplied with the solution when it is assessed.

This is the structure of OGC 08-131r3, *The Specification Model — A Standard for Modular Specifications*, which organises a standard into a core and extensions, each a requirements class with a matching conformance class, and defines a profile separately.

The word "optional" is not used for extensions, for the reason given in ARCH-ADR-6: in GovStack usage it already means `RECOMMENDED`.

### Fields

Every functionality carries a `conformance` classifier. An extension functionality also names its extension:

```yaml
---
title: Credential Issuance
description: |
   The holder obtains credentials from an issuer through the issuance exchange.
conformance: EXTENSION # [CORE, EXTENSION]
extension: issuance # id of an extension declared in metadata.yml; only when conformance is EXTENSION
requirements:
  - ...
---
```

`conformance` defaults to `CORE` when missing, so existing specifications stay valid.

The extensions are declared once, in `metadata.yml`:

```yaml
extensions:
  - id: issuance
    title: Issuance
    description: Obtaining credentials from an Issuer through the issuance exchange.
    role: Credential Holder, towards the Credential Issuer
    reference_architecture: govstack:ra:credentials # optional
    requires: [] # ids of other extensions this one depends on
```

`role` and `reference_architecture` place the extension for the reader, as *Place in larger systems* does for the whole Building Block. They do not import the reference architecture: the requirements of an extension cite their standards directly, and are complete without it. The one-way relationship between specifications and reference architectures is kept.

### Rules

1. A specification has a core of at least one functionality. A specification without extensions is all core, which is every specification today.
2. A functionality is either core or in exactly one extension. Requirements are not split between the two within one file.
3. The core must stand alone: a solution that meets only the core is a useful, conformant instance of the Building Block.
4. An extension may add to the behaviour of a core functionality, for example by requiring more of it when the extension is claimed. It may not weaken or replace a core requirement.
5. An extension may require other extensions (`requires`). The core never depends on an extension.
6. Under ARCH-ADR-6, `applies_when` may name an extension id, so that a requirement in a core functionality applies only when the extension is claimed. Without ARCH-ADR-6, such a requirement is written in a functionality of the extension.
7. A variation point may sit in an extension. It then binds only solutions that claim the extension.

### Conformance

* Every conformant solution meets the core.
* A solution's conformance declaration lists the extensions it claims. A claimed extension fails if any of its `REQUIRED` requirements, or of the extensions it requires, is not met.
* The requirements of an extension that is not claimed are reported as **not applicable**, neither passed nor failed. A `RECOMMENDED` requirement in an unclaimed extension is left out of the share GovMarket reports.
* GovMarket shows the claimed extensions as facts about the solution ("Wallet 2.0: core, Issuance, Presentation"), not as a score. A procurer can filter on them.
* An extension takes part in compliance through its `PUBLISHED` requirements (ARCH-ADR-5). An extension whose requirements are all `DRAFT` can be declared and tested, but conformance to it means nothing yet.

### Versioning

Under `requirements/use_semantic_versioning.md`, an extension's requirements follow the same rules as the core's, judged against the solutions that claim the extension. In addition:

* Adding an extension does not change any existing claim: **minor**.
* Moving a functionality from the core into an extension removes a guarantee from every citation of the specification: **major**.
* Moving a functionality from an extension into the core adds obligations to every solution: **major**.
* Deprecating an extension, or adding a dependency between extensions: **major**.

### Compiler checks

* `conformance` is `CORE` or `EXTENSION`. `extension` is set if and only if `conformance` is `EXTENSION`, and names an id declared in `metadata.yml`.
* Every declared extension has at least one functionality.
* At least one functionality is `CORE`.
* Every id in `requires` is a declared extension, and there are no cycles.
* Every id in `applies_when` is an alternative or a declared extension (with ARCH-ADR-6).
* Extension ids are unique in the specification and are never reused, as requirement ids are not.
* Warn when an extension has no `REQUIRED` requirement: it may be better expressed as `RECOMMENDED` requirements in the core.

### Consequences

* The Specification Framework gains a chapter, *Functionalities and Extensions* (`chapters/functionalities_and_extensions.md`), with the vocabulary, rules and guidance above.
* The requirement file template in *Structure of Specifications* and `bb-template` gains `conformance` and `extension`. `metadata.yml` gains `extensions`.
* *Introductory Chapters* and the `bb-template` overview and scope explain the core and each extension: a conformance column in the functionality table, a table of extensions, extensions marked in the functionality map, and a scope that says what conforms to the core and what each extension adds.
* The glossary distinguishes `extends`, profile, extension and variation point, which sound alike.
* The compliance process and GovMarket take the extensions in a conformance declaration as input. The format of the declaration is defined with the compliance process, outside this ADR, as in ARCH-ADR-6.
* The namespaces chapter of the GovStack Process defines how an extension is cited, for example as an identifier of the specification plus the extension id. Naming is under revision, so this ADR does not fix the form.
* The Domain Reference Architecture chapter can map a capability to "Building Block in a role, through extension X" in its Part 3, Capability mapping.
* Specifications already published are all core, and need no change.

### Confirmation

Confirmed by the Architecture Committee following Architecture Working Group discussion. Implemented in `chapters/functionalities_and_extensions.md`, `chapters/structure_of_specifications.md`, `chapters/introductory_chapters.md`, `bb-template`, and the `publications-compiler` requirements parser. The Wallet Building Block 2.0 and the Payments Building Block (variant C2) are the first specifications to use it.

## Pros and Cons of the Options

### Option 1: Mark non-core functionalities `RECOMMENDED`

* Good, because no text, template or tooling changes
* Bad, because a solution that claims a capability is not held to all of it: it can meet half of an extension and still score
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

### Option 3: Core and extension functionalities

* Good, because the Building Block stays one publication, with one version and one identifier
* Good, because a claim is all or nothing, so GovMarket's 100% rule keeps its meaning for everything a solution claims
* Good, because a buyer can cite exactly what they procure: the specification, its version and the extensions
* Good, because obligations towards a domain role get a defined place, as the Domain Reference Architecture chapter requires
* Good, because the functionality is already the unit of organisation, so one classifier is enough
* Good, because it composes with ARCH-ADR-6 and works without it
* Good, because it follows established practice (OGC 08-131r3)
* Neutral, because "extension" sounds like `extends`; the glossary must keep them apart
* Bad, because every functionality carries one more field, and `metadata.yml` one more block

### Option 4: Extensions as alternatives of a variation point with `min: 0`

* Good, because it adds no new concept beyond ARCH-ADR-6
* Bad, because it needs ARCH-ADR-6 to allow `min: 0`, which turns a variation point into something a solution can skip, against its definition
* Bad, because it uses a requirement as a switch: the variation point and its alternatives state no behaviour, so they have no test
* Bad, because it has no place for the role an extension serves or for dependencies between extensions
* Bad, because it depends on ARCH-ADR-6 being accepted

## More information

* `chapters/structure_of_specifications.md`, requirement file template
* `chapters/introductory_chapters.md`, *Functionalities* and *Scope*
* `chapters/versioning_and_change_documentation.md`, *Extended specifications*
* `govstack-process/chapters/reference_architectures.md`, *Relationship to other publications* and Part 5
* `bb-payments/ADR/ADR-bb-payments-001.md` and `ADR-bb-payments-001-analysis.md`, variant C2
* `bb-wallet/research/2.0-requirements-reorganization-proposal.md`, *Autonomous and Reference Architecture bound requirements*
* ARCH-ADR-5, *Separate a requirement's obligation level from its lifecycle status*
* ARCH-ADR-6 (proposed), *Model alternative and conditional requirements*, `bb-wallet/research/adr-alternative-requirements.md`
* OGC 08-131r3, *The Specification Model — A Standard for Modular Specifications*, Open Geospatial Consortium, 2009
