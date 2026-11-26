---
title: Declare capabilities and link functionalities to them
status: proposed
date: 2026-10-26
decision-makers:
  - Architecture Committee
consulted:
  - Architecture Working Group
  - GovStack Technical Committee
informed:
  - All Working Groups
  - GovMarket
---
<!-- markdownlint-disable-next-line MD025 -->
# Declare capabilities and link functionalities to them

## Context and Problem Statement

*Introductory Chapters* (`chapters/introductory_chapters.md`) asks a Working Group to name, in the *Purpose* of the overview, the **capabilities** the Building Block provides, as outcomes: "Credential Custody", not "Stores credentials". The *Description* then lists the **functionalities** the specification is organised by, each with its own file in `requirements/`. The chapter links the two with a rule, "Every capability must be realised by at least one functionality in the Description", and a check before publication, "every capability is realised by a functionality".

Nothing in the specification makes that rule checkable:

* Capabilities exist only as bold words in the prose of the *Purpose*. They have no identifier and no declaration.
* A functionality file does not say which capability it realises. The overview is asked to explain it in a free-text paragraph after the functionality table.
* The functionality file has a front-matter field called `capability`, which holds a description of the functionality itself, not a reference to a capability of the *Purpose*. The same word names two different things.

So a capability can be named in the *Purpose* that no functionality realises, a functionality can exist that serves no named capability, and neither a reviewer nor the compiler can tell. Capabilities are the part of the specification a decision-maker reads; if they are not tied to what is assessed, they can promise more than a conformant solution delivers.

The question is how to make capabilities useful: whether to tie them to the functionalities, and how, or to make them the unit that holds requirements in place of functionalities.

### Capabilities and functionalities are cut along different lines

The Wallet worked example (`resources/examples/wallet/introduction/overview.md`) shows the shape of the link:

| Capability | Realised by (core) | Realised by (extension) |
|---|---|---|
| Credential Custody | Credential Intake, Credential Management | Credential Issuance (Issuance) |
| Selective Disclosure | Key Management, Credential Presentation | Presentation Exchange (Presentation) |
| Holder Control | Holder Consent, Credential Management | |

The link is many-to-many. One capability needs several functionalities, and Credential Management serves two capabilities. A capability also crosses the line between the core and an extension, which ARCH-ADR-7 (proposed) draws per functionality.

The *Description* is built on functionalities: the functionality map, the actor table ("reaches the Building Block through") and the interface table ("realised by") all assume a unit that an actor reaches through an interface. A capability, stated as an outcome, is not such a unit.

## Decision Drivers

* The rule "every capability is realised by a functionality" must be checkable, by a reviewer and by the compiler
* The *Purpose* must stay readable by a decision-maker or buyer, in outcomes, without technology
* The unit of conformance must not change: core and extensions are assigned per functionality (ARCH-ADR-7)
* The model must allow a capability to be realised by several functionalities, on both sides of the core/extension line, and a functionality to realise several capabilities
* One word, one meaning: "capability" should not name both an outcome of the *Purpose* and a description of a functionality
* Published specifications must stay valid, and the change to templates and tooling must be small
* The one-way relationship with Domain Reference Architectures must be kept: a reference architecture may cite a specification, never the reverse

## Considered Options

* **Option 1:** Leave the link in prose, as today
* **Option 2:** Capabilities as containers for requirements, replacing functionalities
* **Option 3:** Capabilities as parents of functionalities: each functionality belongs to exactly one capability
* **Option 4:** Declared capabilities, linked many-to-many: capabilities are declared with identifiers, and each functionality lists the capabilities it realises

## Decision Outcome

*To be completed by the Architecture Committee.*

The author recommends **Option 4**. It is the only option that makes the link checkable without changing the unit of conformance, and without forcing capabilities into a shape decided by testing rather than by value. Option 2 was the starting point of this record; its costs are set out under *Pros and Cons of the Options*.

### Vocabulary

* **Capability**: an outcome the Building Block makes possible for its beneficiaries, named in the *Purpose*. Informative: it carries no requirements, is not claimed and is not assessed.
* **Functionality**: a service the Building Block provides to an actor outside it, realised by a set of requirements. The unit a specification is organised by, and the unit that is core or in an extension.
* **Realises**: the relationship from a functionality to the capabilities it contributes to.

### Fields

Capabilities are declared once, in `metadata.yml`, next to `extensions`:

```yaml
capabilities:
  - id: custody
    title: Credential Custody
    description: A person keeps the credentials issued to them and can use them whenever they need to.
```

Each functionality lists the capabilities it realises. Its existing `capability` field, which describes the functionality, is renamed `description`, and the *Functionalities* table of the overview repeats it:

```yaml
---
title: Credential Issuance
description: |
   The holder obtains credentials from an issuer through the issuance exchange.
realises: [custody]
conformance: EXTENSION
extension: issuance
requirements:
  - ...
---
```

### Rules

1. Every capability is realised by at least one functionality.
2. Every functionality realises at least one capability.
3. A capability that at least one core functionality realises is provided by every conformant solution. A capability that only extension functionalities realise is provided only by solutions that claim one of those extensions, and the *Purpose* says so.
4. When the core and an extension both realise a capability, the *Description* says what a solution that claims the extension provides beyond the core.
5. Capability ids are unique in the specification and never reused.

### Conformance and versioning

Capabilities do not take part in conformance. A solution is assessed against the functionalities in the core and in the extensions it claims, as before. GovMarket may show the capabilities a solution provides, derived from its claimed extensions by rule 3, but never as a score.

Capabilities are informative content under *Versioning and Change Documentation*. Adding, rewording or removing a capability, or changing the functionalities that realise it, is on its own a **patch** change.

### Compiler checks

* Every id in a functionality's `realises` is declared in `metadata.yml`, under `capabilities`
* Every declared capability is realised by at least one functionality
* Every functionality has a non-empty `realises`
* Capability ids are unique in the specification
* When `metadata.yml` declares no capabilities, the checks above are skipped with a warning, so that specifications published before this decision stay valid
* A functionality file that still has `capability` and no `description` is read as if `capability` were `description`, with a warning

### Consequences

* *Functionalities and Extensions* (`chapters/functionalities_and_extensions.md`) gains a *Capabilities* section with the vocabulary, the `metadata.yml` block, the rules and the contrast between capabilities and functionalities. The Wallet example shows `realises`.
* *Introductory Chapters* gains a **Realises** column in the *Functionalities* table, replaces the paragraph that explained in prose which capability each functionality realises with guidance on how functionalities divide a capability, and adds the new checks before publication.
* *Structure of Specifications* and `bb-template` replace `capability` with `description` and `realises` in the requirement file template, and `bb-template`'s `metadata.yml` gains `capabilities`.
* The preparatory work guide (`resources/preparatory_work.md`) gives each capability an id, and allows a functionality to realise several.
* ARCH-ADR-7 (proposed) shows a functionality file with the `capability` field. If both records are accepted, its example reads `description`.
* The Specification Framework's own requirement files (`requirements/`) use `capability`, and are migrated to `description` and `realises` when this record is accepted.
* A Domain Reference Architecture can cite a Building Block's capabilities by id in its Part 3, *Capability mapping*. The Building Block's capabilities never reference the reference architecture.
* The `publications-compiler` requirements parser implements the checks above.

### Confirmation

Confirmed by the Architecture Committee following Architecture Working Group discussion. Implemented in `chapters/functionalities_and_extensions.md`, `chapters/introductory_chapters.md`, `chapters/structure_of_specifications.md`, `resources/preparatory_work.md`, the Wallet worked example, `bb-template`, and the `publications-compiler` requirements parser.

## Pros and Cons of the Options

### Option 1: Leave the link in prose

* Good, because nothing changes in templates or tooling
* Bad, because the rule "every capability is realised by a functionality" cannot be checked, so the *Purpose* can promise what no requirement delivers
* Bad, because "capability" keeps two meanings

### Option 2: Capabilities as containers for requirements, replacing functionalities

* Good, because there is one concept fewer, and the *Purpose* names exactly the units that carry requirements
* Good, because the link between what is promised and what is assessed is structural by construction
* Bad, because a container partitions requirements, but outcomes overlap. A requirement that serves two capabilities, such as deletion serving both Credential Custody and Holder Control, is assigned to one arbitrarily or duplicated
* Bad, because conformance is assigned per container (ARCH-ADR-7). A capability that the core and an extension both realise, such as Credential Custody, must be split in two, so the capabilities of the *Purpose* are shaped by how the specification is tested rather than by the value it provides
* Bad, because the *Description* is built on units that an actor reaches through an interface. To fit the functionality map, the actor table and the interface table, capabilities must become services to actors, which is a renaming of functionalities that loses the outcome layer the *Purpose* needs
* Bad, because requirements that serve no outcome directly, such as data structures and internal checks, lose their natural home inside the functionality they support
* Bad, because rewording a capability for a policy audience renames files and requirement paths
* Bad, because it moves away from "Key Digital Functionalities", the vocabulary existing Building Block specifications and their authors use, and every specification must be restructured

### Option 3: Capabilities as parents of functionalities

* Good, because the link is structural and simple to check: one `capability` id per functionality
* Good, because the *Purpose* can be rendered as a tree of capabilities and their functionalities
* Bad, because it forbids a functionality from realising more than one capability, which the Wallet example already needs
* Bad, because a functionality that serves two outcomes must be split to fit the tree, and splitting a functionality changes the unit of conformance

### Option 4: Declared capabilities, linked many-to-many

* Good, because the link is structural and checkable in both directions, by the compiler and by a reviewer
* Good, because the unit of conformance stays the functionality, and ARCH-ADR-7 is unaffected
* Good, because capabilities stay outcomes, written for the reader of the *Purpose*, and can span the core and extensions
* Good, because which capabilities a core-only solution provides becomes derivable, not asserted
* Good, because "capability" gets one meaning
* Good, because existing specifications stay valid, with a warning
* Neutral, because `metadata.yml` gains one more block, next to `extensions`
* Bad, because every functionality carries one more field, and the overview table one more column, which must be kept in step with the files

## More information

* `chapters/introductory_chapters.md`, *Value* and *Functionalities*
* `chapters/functionalities_and_extensions.md`, *Capabilities*
* `chapters/versioning_and_change_documentation.md`, informative content
* `resources/preparatory_work.md`, section 4, *Capabilities and functionalities*
* `resources/examples/wallet/introduction/overview.md`
* `govstack-process/chapters/reference_architectures.md`, Part 3, *Capability mapping*
* ARCH-ADR-7 (proposed), *Organise a specification's functionalities into a core and extensions*
* ISO/IEC/IEEE 29148:2018, the "feature" organisation of requirements, which GovStack functionalities follow (`chapters/functionalities_and_extensions.md`, *Functionalities*)
