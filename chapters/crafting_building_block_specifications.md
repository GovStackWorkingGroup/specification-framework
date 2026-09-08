---
title: Crafting Building Block Specifications
authors:
  - name: Ali González-García
  - name: Kristo Vaher
references:
  - ISO/IEC/IEEE 29148:2018(E) Systems and software engineering — Life cycle processes — Requirements engineering, International Organization for Standardization, International Electrotechnical Commission, 2018.
---

# 1. Introduction

    A document that specifies, in a complete, precise, verifiable manner, the requirements, design, behavior, or other characteristics of a system or component, and, often, the procedures for determining whether these provisions have been satisfied. See also: formal specification; product specification; requirements specification.

According 

a document that specifies, in a complete, precise,
verifiable manner, the requirements, design, behavior, or other characteristics of a system, component, product,
result, or service and, often, the procedures for determining whether these provisions have been satisfied. Examples
are: requirement specification, design specification, product specification, and test specification [A Guide to the
Project Management Body of Knowledge (PMBOK® Guide) — Fifth Edition]

Every GovStack Building Block specification is, at its core, a set of Functional Requirements (FR) that a solution must satisfy to be considered GovStack compliant. This publication establishes the outline of GovStack Specifications, and guidance on how to write their different chapters. Its main focus is on the proper crafting of requirements, by addressing the metadata necessary to work with the [GovStack Architecture](https://specs.govstack.global/architecture/) and by drawing guidance from canonical literature of Requirement Engineering. 

## 1.1 Overview

A GovStack Building Block (BB) specification is, in substance, a Software Requirements Specification (SRS): it identifies the requirements a conformant implementation must satisfy, together with the terminology, data structures, interfaces and verification information needed to build and test such an implementation. ISO/IEC/IEEE 29148:2018 already defines what a well-formed SRS looks like and what content it should carry.

This document proposes a GovStack BB specification template that is explicitly derived from that SRS outline, adapted to two things GovStack already does but does not yet enforce structurally: grouping specification content by **functionality**, and requiring that every functionality be accompanied by the **workflow(s)** that realize it.

This is not a proposal written from a blank page. GovStack's current canonical BB
specification layout — `1-version-history`, `2-description`, `3-terminology`,
`4-key-digital-functionalities`, `5-cross-cutting-requirements`,
`6-functional-requirements`, `7-data-structures`, `8-service-apis`, `9-workflows`,
`10-other-resources` — is itself already a recognizable, if implicit, SRS: Version
History and Description correspond to SRS front matter and product overview,
Terminology to SRS definitions, Functional/Cross-Functional Requirements to the SRS
"Requirements" clause, Data Structures to SRS logical database requirements, and
Service APIs to SRS external interface requirements. `cfr-architecture`'s
`5-specification-framework/5.3-specification-template.md` documents this layout and its
intent in detail, and dozens of published BB repositories (`bb-cloud-infrastructure-hosting`,
`bb-consent`, `bb-digital-registries`, and others under `bb-specification-template/`)
implement it.

## 0.2 Problem Statement

Two things this document can support directly from what is already in this repository
and its siblings:

1. **Key Functionalities and Functional Requirements are only loosely linked.** Per the
   GovStack skill's requirement-writing rules, a Functional Requirement (FR) "may link
   to one or more Key Functionalities (KF) via a `KF: <exact KF name>` label in the
   body. No KF link = global." This is a textual convention, not a structural one: a
   reader must open `4-key-digital-functionalities.md` to see what capability is being
   claimed, then separately search `6-functional-requirements.md` for the FRs carrying
   a matching `KF:` label, and the link only exists if an author remembered to add it.
2. **Workflows are decoupled from both.** The template guidance shipped in the older
   `bb-template-old` GitBook layout states only that "each internal workflow must be
   linked to one of the Functional Requirements defined in section 6" — again an
   informal cross-reference, and one that names section 6 (Functional Requirements),
   not section 4 (Key Functionalities). In a real, published specification
   (`bb-cloud-infrastructure-hosting/spec/9-workflows.md`), the four documented
   workflows are numbered independently of the seven Key Functionalities in
   `4-key-digital-functionalities.md`, and the correspondence between the two lists
   (e.g. workflow 9.4 "Integrating with IAM Systems" ↔ Key Functionality 4.4 "IAM") is
   left for the reader to infer from the prose, not asserted by the document's
   structure. Nothing in the current template requires a Key Functionality to have any
   documented workflow at all.
3. **This is already being worked on for the Functional Requirements half of the
   problem, independently, in two places** — see 0.3 below — but neither of those
   efforts yet extends functionality-grouping to workflows, which is what this proposal
   adds.

This document does not claim these are the only problems with the current template, and
it does not speculate about causes beyond what the source material shows.

## 0.3 Relationship to Ongoing Template Work

Two in-progress, independent redesign efforts already move Functional Requirements
toward functionality-grouping, and this proposal is written to extend — not
contradict — both:

- `bb-specification-template/bb-govspecs-2-template/spec/functional-requirements/*.md`
  already splits functional requirements into one file per functional group (`1.md`,
  `2.md`, …), each carrying a `slug`, a `name` (e.g. `management-of-hardware` /
  "Management of physical hardware", which is also the name of Key Functionality 4.1 in
  the equivalent published spec) and a `requirements` list with per-requirement
  classifiers.
- `bb-specification-template/new_bb_template/machine-readable-tests/src/information-mediator/`
  documents (in its `new-format/README.md`, "New machine readable document structure")
  a `functionalities/` folder with one file per functionality (`01. Service Access.md`,
  `02. Directory Services.md`, …), each carrying its Functional Requirements as YAML
  front matter and free-form descriptive content below. Its stated implication is
  explicit: "Currently the requirements and free-style content are mixed. This is no
  longer possible."

In both efforts, **workflows remain a single flat, ungrouped file** —
`internal_workflows.md` in the Information Mediator rewrite, `9-workflows.md` in the
canonical layout — numbered independently of the functionality files. This document's
proposal is to close that specific, remaining gap: to make the Workflows subsection a
mandatory, physically-grouped part of each functionality, using the same file format
and free-form content area the `new_bb_template` README already reserves for
non-requirement content. Section 4 (Open Design Questions) flags where this proposal
has to make a call that the in-progress work has not yet made, rather than deciding it
silently.

# 1. Scope

This document proposes a concrete chapter/section outline for GovStack BB
specifications, grounded in the SRS outline of ISO/IEC/IEEE 29148:2018, and explains how
that outline groups content by functionality and makes workflows a mandatory part of
each functionality group. It also states explicitly what the proposal keeps from the
current canonical layout, what it changes, and why, and shows how the proposal remains
compatible with GovStack's existing Functional Requirement (FR) / Cross-Functional
Requirement (CFR) / Key Functionality (KF) / classifier model as defined in
`cfr-architecture/5-specification-framework/`.

This document does **not** cover:

- The governance process by which a new template would be adopted, phased in, or made
  mandatory for Working Groups — that belongs in `publication_tracks.md`.
- How to write the text of an individual requirement (wording, testability, precondition
  statements) — that is the subject of `crafting_requirements.md`.
- How to write the Terminology chapter — that is the subject of `crafting_terminology.md`.
- A migration plan or tooling for converting the 14 published Building Blocks to this
  outline.

# 2. Normative References

- ISO/IEC/IEEE 29148:2018(E), *Systems and software engineering — Life cycle processes
  — Requirements engineering*, cited by clause:
  - **Clause 8.4** (System requirements specification) and **8.4.2** / Figure 7 (example
    SyRS outline) — used here for the system-context content that GovStack's
    Description chapter already carries.
  - **Clause 8.5** (Software requirements specification) and **8.5.2** / Figure 8
    (example SRS outline), including its NOTE 2 on organizational approaches to
    requirements (mode, user class, object, **feature**, stimulus, response, functional
    hierarchy) — the basis of this proposal's functionality-grouping and mandatory
    workflow rule.
  - **Clause 9.6** (Software requirements specification (SRS) content), 9.6.1–9.6.20 —
    used here to justify the content of each proposed chapter, in particular 9.6.5
    (Product functions), 9.6.9 (Apportioning of requirements), 9.6.10 (Specified
    requirements) and 9.6.11 (External interfaces).
- `cfr-architecture/5-specification-framework/5.1-specification-scope.md`,
  `5.2-building-block-specification.md`, `5.3-specification-template.md` and
  `5.4-specification-use.md` — the current GovStack requirements/specification model
  (FR vs CFR, Key Functionalities, requirement identifiers, classifiers, extensibility
  rules) that this proposal must remain compatible with.
- The `govstack` skill's canonical spec structure and requirement-writing conventions,
  and the real BB repositories inspected under `bb-specification-template/`
  (`bb-cloud-infrastructure-hosting`, `bb-consent`, `bb-digital-registries`,
  `bb-govspecs-2-template`) and under `new_bb_template/machine-readable-tests/src/information-mediator/`.

# 3. Proposed Template: an SRS-Grounded, Functionality-Grouped Outline

## 3.1 Design Principles

The proposal rests on four principles, each traceable to a specific piece of source
material:

1. **A GovStack BB specification is an SRS.** Its "Requirements" content (ISO/IEC/IEEE
   29148:2018, 8.5.2, Figure 8, clause 3) SHOULD be organized the way clause 8.5.2 says
   any SRS's specific requirements clause should be organized: by whichever scheme "a
   consensus of the system stakeholders agrees... aids understanding of the
   requirements." GovStack has already made that choice implicitly (Key Functionalities
   exist, and FRs already carry `KF:` labels); this proposal makes it explicit and
   structural.
2. **"Feature" is the organizational scheme GovStack has already chosen, and 29148
   already ties it to workflows.** Figure 8's NOTE 2 defines the "Feature" scheme as: "a
   feature is an externally desired service by the system that may require a sequence
   of inputs to effect the desired result... Each feature is generally described in a
   sequence of stimulus-response pairs." A GovStack Key Functionality already matches
   this definition almost word for word (cfr-architecture 5.3.4: "a descriptive list of
   capabilities provided by a Building Block... achieved through technical functions
   provided by Functional and Cross-Functional requirements"). A "sequence of
   stimulus-response pairs" realizing a feature **is a workflow**. The standard itself
   is the justification for requiring a workflow per functionality, not an invention of
   this document.
3. **Cross-cutting content stays global.** Cross-Functional Requirements are, by
   GovStack's own definition (cfr-architecture 5.1.1, 5.3.6), rules that "govern every
   specification" or "every compliant BB" — they are explicitly not scoped to one
   functionality, so they MUST NOT be forced into the functionality-grouped structure.
4. **Parallel structure, where GovStack already recommends it, becomes a rule.**
   cfr-architecture 5.3.5.1 already recommends that Service APIs be grouped "by
   functional domain," and 29148's Figure 8 explicitly instructs that SRS clause 4
   (Verification) be organized "parallel to subsections in Section 3" (Requirements).
   This proposal makes both of those informal recommendations structural: Service APIs
   and Testing chapters MUST mirror the functionality breakdown of the Functionalities
   chapter, subsection for subsection.

## 3.2 Chapter Outline at a Glance

| # | Chapter | Status vs. current template |
|---|---|---|
| 1 | Version History | Kept, unchanged |
| 2 | Description | Kept, unchanged |
| 3 | Terminology | Kept, unchanged |
| 4 | **Functionalities** | **Changed** — absorbs today's Key Digital Functionalities, Functional Requirements and Workflows chapters into one functionality-grouped chapter |
| 5 | Cross-Functional Requirements | Kept, renumbered |
| 6 | Data Structures | Kept, renumbered |
| 7 | Service APIs | Kept, renumbered; now REQUIRED to mirror Chapter 4's functionality subsections |
| 8 | Testing | Kept (already present in the cfr-architecture 5.3 template, not in the older GitBook chapter set); RECOMMENDED to mirror Chapter 4's functionality subsections |
| 9 | Other Resources | Kept, renumbered |

Section 3.9 below gives the row-by-row justification for each of these decisions.

## 3.3 Chapters 1–3: Front Matter

These three chapters are unchanged from the current canonical layout and correspond
directly to SRS clauses that are inherently document-level, not functionality-level,
and so are out of scope for functionality-grouping:

- **1. Version History** — unchanged. No equivalent SRS clause; this is a GovStack/BIBO
  convention already covered by the `1-version-history` chapter and the `index.yml` /
  publication-metadata model documented in the `new_bb_template` README.
- **2. Description** — unchanged. Maps to SRS 8.5.2 clause 1 (Introduction: Purpose,
  Scope, Product overview — 1.3.1 Product perspective, 1.3.2 Product functions
  (summary), 1.3.3 User characteristics, 1.3.4 Limitations) and to `srs_outline.md`'s
  existing (partial) extraction of the same clauses. cfr-architecture 5.3.1 already
  describes this content correctly; nothing here needs to change to accommodate
  functionality-grouping, because Description is explicitly "high-level" and
  "explanatory, not prescriptive" (5.3.1) — it introduces the functionalities detailed
  in Chapter 4 without duplicating them. One addition to this chapter is proposed
  in 3.3.1 below: an explicit actor table and out-of-scope statement, which SRS 8.5.2
  clause 1.3.1 (Product perspective) already calls for and which the current layout
  leaves to each Working Group's discretion.
- **3. Terminology** — unchanged. Maps to SRS 8.5.2 clause 1.4 (Definitions). See
  `crafting_terminology.md` for how this chapter should be written; it is out of scope
  here.

### 3.3.1 Actors and the System Boundary

A specification almost always sits inside a larger process than the one it specifies. The Wallet Building Block is only meaningful in an ecosystem containing credential Issuers and Verifiers, but the specification covers the wallet alone. Left implicit, this gap is read in both of the wrong directions: a reader concludes that the specification is incomplete because it says so little about issuance, or an implementer concludes that conformance requires building an Issuer. Both readings are avoidable, and 29148 requires them to be avoided.

ISO/IEC/IEEE 29148:2018 clause 9.6.4, *Product perspective*, states the obligation directly: "If the product is an element of a larger system, relate the requirements of that larger system to the functionality of the product covered by the SRS", and "If the product is an element of a larger system, identify the interfaces between the product covered by the SRS and the larger system of which the product is an element." Clause 9.6.6, *User characteristics*, adds that the intended groups of users are described in order to "state the reasons why certain specific requirements are later specified", not to state requirements themselves.

The instrument that discharges both obligations at once is an **actor table** in Chapter 2, Description. Listing the actors of the larger process, and marking each one in or out of scope, states the system boundary in a form a reader can check against every later requirement.

#### The actor table

Chapter 2 SHOULD contain a table of every actor that participates in the process the Building Block takes part in, whether or not the specification covers it:

| Actor | Role in the larger process | In scope | Interface to it | Specified where |
|---|---|---|---|---|
| Wallet Holder | Natural or legal person controlling the wallet and deciding what to present | Yes | User interaction with the wallet | This specification |
| Credential Issuer | Issues credentials into the wallet | No | Credential issuance API | Interface specified here; behaviour out of scope |
| Credential Verifier | Requests and validates a presentation from the wallet | No | Presentation exchange API | Interface specified here; behaviour out of scope |

An actor marked **in scope** is part of what the specification specifies, and a solution claiming conformance must implement it. An actor marked **out of scope** is part of the environment the Building Block operates in: the specification says what the Building Block must be able to exchange with it, and says nothing about how the actor behaves internally.

Three rules follow, and they are the reason the table earns its place:

1. An out-of-scope actor with which the Building Block exchanges anything **MUST** still have its interface specified. This is the second half of 9.6.4: the interfaces between the product and the larger system are in scope even when the larger system is not. A specification that lists an actor as out of scope and then says nothing about how to reach it has not delimited its boundary, it has abandoned it.
2. No Functional Requirement **SHALL** place an obligation on an out-of-scope actor. A requirement stating what the Issuer must do is unverifiable against a wallet implementation and unenforceable in procurement. Where the behaviour of an out-of-scope actor genuinely matters, it is stated as a precondition or an environment assumption of the requirement, not as the requirement.
3. Every actor name in the table is a term, and **SHALL** be treated as one. Actor names are among the highest-traffic designations in a specification and among the most likely to be shared with adjacent Building Blocks, which makes them prime candidates for Common Terminology. See `crafting_terminology.md` §5.5 and §6.

#### Relationship to the System Under Test

The actor table is what makes a specification testable as a unit, which is its second function.

At conformance time, the implementation of everything inside the system boundary is what gets exercised — the System Under Test, defined in ISO/IEC/IEEE 24765:2017 as the parts of the system to be tested, consisting of hardware, system software, data communication features or application software, or a combination of them. Everything outside the boundary has to be supplied by the test environment instead, which in GovStack terms means a Building Block Emulator: "a lightweight implementation used in a sandbox or demonstration environment to simulate the behaviour of a building block" (GovStack Architecture, clause 2).

The mapping is one to one, and it is the practical payoff of drawing the boundary explicitly:

- **In-scope actor** → implemented by the solution under assessment → part of the System Under Test.
- **Out-of-scope actor** → stubbed, emulated or otherwise supplied by the test harness → an environment assumption, which clause 5.4.9 of the GovStack Architecture already requires the Testing chapter to declare alongside fixtures and sample data.

This is why rule 1 above is not pedantry. An out-of-scope actor whose interface is unspecified cannot be emulated, and a Building Block whose counterparties cannot be emulated cannot be conformance-tested in isolation. The Wallet cannot be tested without something that issues and something that verifies; the specification's job is to define those two interfaces precisely enough that a harness can stand them up, and then to stop.

#### The out-of-scope section

The actor table establishes that a scoping statement needs somewhere to record what was deliberately left out. Chapter 2 SHOULD therefore carry an explicit out-of-scope subsection, and the GovStack Architecture already anticipates one: clause 5.4.4 lists "optional out-of-scope capabilities (what could be in scope but is not)" as content for Key Functionalities.

An out-of-scope statement SHOULD record, for each excluded item, what it is and which publication or party owns it. Naming the owner is what distinguishes a boundary from a gap. The categories worth recording are:

- **Actors** outside the boundary, as in the table above.
- **Capabilities** that a reader would plausibly expect in this Building Block and that were considered and assigned elsewhere.
- **Layers** outside the technical one. The GovStack Architecture clause 5.1.2 places legal and organizational matter outside a Building Block specification by default; where a domain has a strong legal dimension, saying so explicitly is more useful than leaving the reader to infer it from silence.
- **Adjacent Building Blocks** whose responsibilities border on this one, referenced rather than restated.

An out-of-scope statement SHOULD NOT be used to defer work that belongs in the specification, and SHOULD NOT list things no reader would have expected in the first place. The test is whether a competent reader could plausibly have looked for the item here.

The delimitation recorded in `templates/preparatory_work.md` §2 is where this content is derived during preparatory work; this subsection is how the result is published.

## 3.4 Chapter 4: Functionalities (the Core Change)

### 3.4.1 Functionality Group Structure

Chapter 4 replaces the current `4-key-digital-functionalities.md`,
`6-functional-requirements.md` and `9-workflows.md` chapters with a single chapter
containing one subsection per functionality. A specification MUST have at least one
functionality group. Each functionality group `4.N <Functionality Name>` MUST contain:

- **`4.N.1 Description`** — the capability's purpose and outcome, in the same
  non-normative, implementation-agnostic style cfr-architecture 5.3.4 already
  prescribes for Key Functionalities ("what value it brings... primary actor(s) and
  system boundary... avoid requirements language"). This subsection is what today's
  `4-key-digital-functionalities.md` entries already are — it is simply no longer a
  standalone chapter separated from the requirements it motivates.
- **`4.N.2 Functional Requirements`** — the FRs that realize this functionality, each
  with a unique identifier and classifiers exactly as defined today in
  cfr-architecture 5.2.3–5.2.10 (Requirement level, Observability, Mutability). Because
  the requirement is now physically inside its functionality's section, the `KF:` label
  convention described in the `govstack` skill becomes redundant for single-functionality
  requirements (the file/section location *is* the link) — see Open Question 1 in
  Section 4 for the case of a requirement that legitimately serves more than one
  functionality.
- **`4.N.3 Workflows`** — **MUST** contain at least one workflow: a numbered,
  actor-and-step description (or sequence diagram) of how the functionality's inputs
  produce its outputs, in the same style as today's `9-workflows.md` entries
  (cfr-architecture 5.3.8: "actors, preconditions, numbered steps, key decisions...,
  postconditions"). A functionality group with no workflow that can be documented
  SHOULD be reconsidered — it may be more accurately expressed as a single Functional
  Requirement rather than a Key Functionality (cfr-architecture 5.3.4 already implies
  this: "Each capability should be testable later through Functional Requirements and
  Testing").
- **`4.N.4 Service Interfaces`** (optional at the group level) — a short pointer to
  which endpoints/operations in the global Service APIs chapter (Chapter 7) realize
  this functionality. Full API definitions stay centralized in Chapter 7 and in the
  `api/` folder's OpenAPI files (see 3.6) so that a single machine-readable contract is
  not duplicated across functionality files; this subsection exists only to make the
  cross-reference explicit at the point of use, the same way cfr-architecture 5.3.5.1
  already asks: "Every API endpoint listed here should trace back to at least one
  functional requirement."

### 3.4.2 Worked Example

The Information Mediator rewrite draft
(`new_bb_template/machine-readable-tests/src/information-mediator/govstack_im_bb_spec_rewrite_2_1_draft.md`)
already contains, scattered across three separate chapters, everything a single
functionality group under this proposal would need:

- Chapter 4, `KF-1 Service Access`: "Secure mediation of synchronous API requests
  between authorized consumers and providers."
- Chapter 5, `5.1 Service Access`: six Functional Requirements (`fr#req-1`–`req-6`),
  e.g. "The Information Mediator SHALL mediate synchronous service requests between an
  authorized consumer and an authorized provider..."
- Chapter 8, `8.2 Synchronous service invocation workflow`: a seven-step numbered
  workflow from "a consumer application sends a request to the mediation gateway" to
  "the IM records transaction and operational metadata."

Under this proposal, these three fragments become one functionality group:

```
## 4.1 Service Access

### 4.1.1 Description
Secure mediation of synchronous API requests between authorized
consumers and providers.

### 4.1.2 Functional Requirements
[fr#req-1 .. fr#req-6, unchanged from the draft]

### 4.1.3 Workflows
#### 4.1.3.1 Synchronous service invocation
1. A consumer application sends a request to the mediation gateway.
2. The IM authenticates the sender and evaluates policy.
...
7. The IM records transaction and operational metadata.

### 4.1.4 Service Interfaces
See Chapter 7, §7.1 Service Access.
```

No content is invented in this transformation; the three source chapters are relocated
and nested, not rewritten.

### 3.4.3 Machine-Readable Realization

This structure is directly expressible using the file format the `new_bb_template`
README already defines for the `functionalities/` folder — no new file format is
required:

```
functionalities/
    01. Service Access.md
    02. Directory Services.md
    03. Pub-Sub Service.md
    ...
```

with each file's YAML front matter unchanged (`title`, `requirements: [...]` with
`id`/`level`/`observability`/`mutability`/`requirement`) and its free-form content
extended to include a `## Workflows` heading, e.g.:

```
---
title: Service Access
requirements:
  - id: govstack-bb-information-mediator-fr#1
    level: REQUIRED
    observability: OBSERVABLE
    mutability: IMMUTABLE
    requirement: The Information Mediator SHALL mediate synchronous
      service requests between an authorized consumer and an
      authorized provider without requiring direct consumer
      integration with the provider implementation.
  # ...
---
# Service Access

Secure mediation of synchronous API requests between authorized
consumers and providers.

## Workflows

### Synchronous service invocation
1. A consumer application sends a request to the mediation gateway.
2. The IM authenticates the sender and evaluates policy.
...
```

This is consistent with the `new_bb_template` README's own statement of what is and is
not allowed in a functionality file's free-form content area: "Free-style content,
charts, pictures, bullets that do not embed requirements in them" — a Workflows section
is exactly that. The standalone `internal_workflows.md` file that exists today alongside
the `functionalities/` folder in the Information Mediator working directory would, under
this proposal, either be removed (its content redistributed into the relevant
functionality files) or repurposed to hold only the residual, genuinely
cross-functionality workflows — see Open Question 2.

## 3.5 Chapters 5–6: Global Cross-Functional Requirements and Data Structures

- **5. Cross-Functional Requirements** — kept, renumbered only. By definition (design
  principle 3, §3.1) CFRs are not scoped to a single functionality and MUST remain in a
  single global chapter, extending `govstack-cfr` exactly as cfr-architecture 5.3.6
  already describes. Nothing about functionality-grouping applies here.
- **6. Data Structures** — kept, renumbered. This chapter remains the canonical,
  de-duplicated resource/entity model (cfr-architecture 5.3.7; SRS 9.6.15 Logical
  database requirements) that functionality groups reference rather than redefine.
  Whether some entities that are used by exactly one functionality (e.g. the
  Information Mediator's `Room`, `Event Type` and `Subscription`, which belong only to
  its Pub/Sub functionality) should instead be defined inside that functionality's
  section is **not resolved by this proposal** — see Open Question 5.

## 3.6 Chapters 7–8: Service APIs and Testing, Organized in Parallel to Chapter 4

Per design principle 4 (§3.1), both chapters remain global — a single OpenAPI contract
and a single conformance-testing narrative are easier to maintain and machine-process
than one per functionality file — but their internal subsection numbering and naming
MUST mirror Chapter 4's functionality groups, exactly as SRS Figure 8 already instructs
for its own clause 4 (Verification): "(parallel to subsections in Section 3)."

- **7. Service APIs** — `7.N <Functionality Name>` mirrors `4.N <Functionality Name>`.
  This turns cfr-architecture 5.3.5.1's existing recommendation ("group endpoints by
  functional domain... Every API endpoint listed here should trace back to at least one
  functional requirement") from a suggestion into a structural rule, and gives Chapter
  4's `4.N.4 Service Interfaces` pointer a fixed, predictable target.
- **8. Testing** — `8.N <Functionality Name>` mirrors `4.N <Functionality Name>`. This
  chapter did not exist in the older `bb-template-old` GitBook chapter set at all; it
  was introduced in the cfr-architecture 5.3 template ("Every REQUIRED requirement in
  this specification SHALL be mapped to at least one verification item"). Retaining it
  as a global chapter (rather than nesting it inside Chapter 4) keeps conformance
  verification auditable as a single artifact, while the parallel numbering keeps it
  traceable to the functionality it verifies — again matching 29148 Figure 8's own
  advice for organizing an SRS's Verification clause.

## 3.7 Chapter 9: Other Resources

Kept, renumbered only. cfr-architecture 5.3.10 already scopes this correctly
("supporting material that is useful but not part of the requirements... Do not
introduce new rules here"); functionality-grouping does not apply to it.

## 3.8 Compatibility with the GovStack Requirements Model

This proposal changes *where* FRs, CFRs, Data Structures and Service APIs live inside
the document, not *what they are* or *how they are identified and classified*:

- **Functional Requirements** keep their unique identifiers
  (`govstack-bb-<name>-fr#req-N`) and their three classifier dimensions (Requirement
  level, Observability, Mutability) exactly as defined in cfr-architecture 5.2.3–5.2.10.
  What changes is that a requirement's location in the document (inside `4.N`) now
  carries the meaning the `KF:` label used to carry for single-functionality
  requirements.
- **Cross-Functional Requirements** are unaffected: they keep their own identifier
  namespace (`govstack-bb-<name>-cfr#req-N`), their `extends`/`replaces` relationship to
  `govstack-cfr`, and their global, non-functionality-grouped chapter.
- **Key Functionalities**, as a labeled concept, are subsumed by the `4.N.1
  Description` subsection of each functionality group rather than eliminated — the
  descriptive, non-normative content cfr-architecture 5.3.4 requires of a KF is
  unchanged; it simply now sits directly above the requirements and workflow it
  motivates instead of in a separate chapter.
- **Data Structures and Service APIs** remain global, shared artifacts (§3.5, §3.6),
  referenced from functionality groups rather than duplicated inside them.

No part of this proposal requires a change to the identifier scheme, the classifier
model, or the versioning rules in cfr-architecture 5.2.

## 3.9 What This Keeps, Changes, and Why

| Current chapter | Proposed | Change | Why (source) |
|---|---|---|---|
| `1-version-history` | 1. Version History | None | No SRS equivalent needed; GovStack-specific, already adequate |
| `2-description` | 2. Description | None | Maps to SRS 8.5.2 clause 1 (Purpose/Scope/Product overview); already correctly scoped per cfr-architecture 5.3.1 |
| `3-terminology` | 3. Terminology | None | Maps to SRS 8.5.2 clause 1.4 (Definitions); see `crafting_terminology.md` |
| `4-key-digital-functionalities` | 4.N.1 (inside Functionalities) | Absorbed | Today only connected to its FRs by an optional `KF:` text label (govstack skill); no structural link |
| `6-functional-requirements` | 4.N.2 (inside Functionalities) | Absorbed / grouped | Same reason; already prototyped in `bb-govspecs-2-template` and `new_bb_template` |
| `9-workflows` | 4.N.3 (inside Functionalities) | Absorbed / grouped / **made mandatory** | Today only informally linked to a FR, not to a KF, per `bb-template-old`'s workflow guidance; a published spec (`bb-cloud-infrastructure-hosting`) already shows workflow numbering drifting from KF numbering; SRS 8.5.2 Figure 8 NOTE 2 ties "Feature" organization directly to "a sequence of stimulus-response pairs" |
| `5-cross-cutting-requirements` | 5. Cross-Functional Requirements | Renumbered only | CFRs are explicitly cross-functionality by definition (cfr-architecture 5.1.1) |
| `7-data-structures` | 6. Data Structures | Renumbered only | Kept global by design; see Open Question 5 |
| `8-service-apis` | 7. Service APIs | Renumbered; **subsections now mirror Chapter 4** | Formalizes cfr-architecture 5.3.5.1's existing "group by functional domain" recommendation |
| (none in `bb-template-old`; present in cfr-architecture 5.3) | 8. Testing | Renumbered; RECOMMENDED to mirror Chapter 4 | Matches SRS Figure 8's own instruction for its Verification clause: "parallel to subsections in Section 3" |
| `10-other-resources` | 9. Other Resources | Renumbered only | Scope unchanged per cfr-architecture 5.3.10 |

# 4. Open Design Questions

The standards and the existing/in-progress templates do not resolve the following.
This document does not decide them; they are listed here for the Working Groups and the
authors of the `new_bb_template` / `bb-govspecs-2-template` efforts to resolve.

1. **Requirements spanning more than one functionality.** The Information Mediator
   rewrite draft already has this case (`fr#req-28`, "External APIs... SHALL be
   published using OpenAPI," carries `KF: KF-1, KF-2, KF-3`). Once grouping is
   physical rather than a text label, where does such a requirement live? Candidates,
   none adopted here: (a) place it in the functionality where it is primarily exercised
   and cross-reference it from the others; (b) keep a small "Shared Functional
   Requirements" subsection preceding the functionality list; (c) use a cross-reference
   table by function, the mechanism 29148 9.6.9 (Apportioning of requirements)
   recommends for the analogous problem of a requirement spanning multiple software
   elements. Whichever is chosen, specifications SHOULD avoid duplicating the same
   requirement text in more than one functionality group.
2. **Disposition of the standalone workflow files.** Both `new_bb_template`
   (`internal_workflows.md`) and the canonical layout (`9-workflows.md`) currently hold
   workflows that do not map 1:1 to a single functionality — e.g. the Information
   Mediator draft's "Federation workflow" and "Administrative change workflow" touch
   more than one Key Functionality. Should the standalone file be removed entirely (all
   workflows redistributed, with genuinely cross-functionality ones duplicated or
   summarized in more than one group), or retained as a residual chapter for
   system-level workflows that do not belong to one functionality? This document takes
   no position.
3. **Do Service APIs and Testing subsections need to be literally 1:1 with Chapter 4,
   or just consistently cross-referenced?** Section 3.6 recommends parallel structure
   by analogy to SRS Figure 8, but does not resolve whether this should be enforced as
   an exact subsection-for-subsection mirror (harder to maintain if functionalities are
   added/removed) or as a looser, machine-checked cross-reference (e.g. every Service
   API operation MUST declare which `4.N` functionality it belongs to, without fixing
   the chapter's own subsection numbers to match).
4. **Operational cost of file-based grouping.** The `new_bb_template` README already
   ties a functionality's ordinal position into its filename (`01. Service Access.md`).
   Reordering or renaming functionalities under this proposal has the same effect the
   `govstack` skill already flags for `KF:` labels ("Renaming a KF means updating every
   `KF:` link that references it") but now at the filesystem level. Whether this is an
   acceptable cost, and whether tooling should decouple display order from filename, is
   unresolved here.
5. **Should Data Structures ever be defined inside a functionality group instead of
   the global chapter?** The Information Mediator draft's `Room`, `Event Type` and
   `Subscription` entities are used only by its Pub/Sub functionality, yet
   `data_structures.md` centralizes all entities regardless of which functionality
   uses them (§3.5). This proposal keeps Data Structures global for now, but does not
   resolve whether functionality-specific entities should instead live with their
   functionality, with only shared/cross-functionality entities remaining central.
