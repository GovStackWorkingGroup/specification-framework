---
title: Model options and conditional requirements
status: proposed
date: 2026-10-27
decision-makers:
  - Architecture Committee
consulted:
  - Architecture Working Group
  - Wallet Working Group
  - GovStack Technical Committee
informed:
  - All Working Groups
  - GovMarket
---
<!-- markdownlint-disable-next-line MD025 -->
# Model options and conditional requirements

## Context and Problem Statement

Some requirements cannot be stated as "every conformant solution does X". They say "every conformant solution does at least one of X, Y or Z". The solution cannot skip the requirement, but it chooses how to meet it. Once it has chosen, further requirements apply that depend on the choice.

*Requirements* (`chapters/requirements.md`) cannot say this. Its classifiers describe one requirement at a time: `level` says whether a solution fails without it, and nothing says that a requirement applies only to some solutions, or that it is one of several ways of meeting another.

ISO/IEC 14143-2:2011, 3.6 names the missing concept:

> **optional requirement.** requirement… fulfilled in order to comply with a particular option permitted by that document. *Note: An optional requirement is either: a) one of two or more alternative requirements, or b) an additional requirement that is fulfilled only if applicable and can otherwise be disregarded.*

The two cases behave differently in a conformance assessment. Case (a) is a choice the solution makes. Case (b) is a requirement whose applicability follows from that choice.

### Worked example: the Wallet

A wallet must accept credentials the Holder supplies. That is the core functionality *Credential Intake* of the Wallet Building Block (`resources/examples/wallet/introduction/overview.md`). But wallets hold different kinds of credential:

- **holder-bound verifiable credentials**, in SD-JWT VC, ISO mdoc or W3C VCDM, bound to the Holder through a key or a claim;
- **bearer credentials**, signed by an issuer but bound to no one, such as ICAO Visible Digital Seals or signed QR certificates;
- **passes** in the PKPASS format, such as tickets and membership cards.

A conformant wallet must accept at least one of these, and which ones is the implementer's choice. A national identity wallet may hold only verifiable credentials; a transport operator's wallet may hold only passes. Both are wallets the specification should recognise.

The choice decides which other requirements apply:

| If the wallet accepts… | …then these apply | …and these do not |
|---|---|---|
| verifiable credentials | a choice of format, holder binding, status checks | — |
| bearer credentials | a warning that anyone with a copy can present it, display as a code | holder binding |
| passes | signature and manifest checks, display as a code, pass updates | holder binding |

Choices nest. A wallet that accepts verifiable credentials supports at least one of three formats, and at least one of cryptographic or claim-based holder binding. Choices also reach across functionalities: a wallet that accepts bearer credentials or passes must display them as a scannable code in *Credential Presentation*, a different functionality from the one where the choice is made.

### What the current model offers, and why it fails here

| Mechanism | Why it does not fit |
|---|---|
| `RECOMMENDED` on each kind of credential | A solution may skip all of them. And GovMarket would score a wallet that accepts all three above one that accepts only verifiable credentials, which is not what the Working Group means |
| `REQUIRED` on each kind of credential | Excludes every wallet that does not accept all three |
| A condition in the statement: "When the Wallet accepts passes, it verifies…" | The condition is prose. The compiler cannot see it, GovMarket cannot tell "not applicable" from "failed", and a test harness would have to parse English. *Requirements* already lists this under *Common mistakes* |
| Extensions (ARCH-ADR-7, `chapters/functionalities_and_extensions.md`) | An extension is a set of functionalities a solution may skip entirely. Here the solution cannot skip the choice, and the choice sits inside one functionality. *Other mechanisms, and when to use them instead* already names a variation point as the mechanism for this case |
| The file-level `cardinality` field (`ALL`, `ONE_OF`, `ONE_OR_MORE`) in `bb-template` and the publications compiler | Not defined anywhere in the framework. It applies to a whole file, so the requirements every wallet meets must move to another file. An option is one requirement, so it cannot bring in the requirements that follow from it. It has no conditional applicability, and it cannot express "at least two" |

### Why it matters beyond the Wallet

Any Building Block that admits several formats, channels or protocols for the same job meets this pattern: Payments with several payment rails, Digital Registries with several import formats, Identity with several authentication factors. Product-line engineering names it a **variation point** (ISO/IEC 26550, ISO/IEC 26551). An extending specification, such as a national profile, needs the same mechanism to narrow a choice: "this country's wallets accept mdoc credentials".

## Decision Drivers

* A requirement that admits several ways of meeting it is still non-discardable: a solution that meets none of them fails
* Which option a solution chose is recorded and visible, but not scored as quality
* Requirements that depend on a choice are machine-readable as such, so that tests and GovMarket report them as not applicable rather than failed
* A functionality file is organised by meaning, not by the needs of the model
* The model fits *Requirements*: the status model and numbering of ARCH-ADR-5, the interaction classifier, and the mutability rules for extending specifications
* The model fits *Functionalities and Extensions*: it composes with extensions, uses the same conformance declaration, and does not duplicate them
* Version changes are classified by the same two promises as everything else in *Versioning and Change Documentation*
* The model uses established vocabulary (ISO/IEC 14143-2, ISO/IEC 26550 family) rather than inventing its own

## Considered Approaches

* **Approach 1:** Keep the file-level `cardinality` field, and define it in *Requirements*
* **Approach 2:** Variation points with options, and an `applies_when` field for conditional requirements
* **Approach 3:** Conformance profiles: the specification defines named profiles, each listing the requirements it includes
* **Approach 4:** No new mechanism: `RECOMMENDED` and conditions in prose

## Decision Outcome

*To be completed by the Architecture Committee.*

The Wallet Working Group recommends **Approach 2**. It is the only approach that keeps a choice non-discardable, attaches the requirements that follow from a choice to that choice wherever they sit, and lets a functionality file hold both the requirements every solution meets and the choices it makes.

### Vocabulary

* **Variation point**: a requirement that a solution meets by meeting a number of its options. It has a `level` like any requirement. A `REQUIRED` variation point fails if the solution does not meet enough of its options.
* **Option**: a requirement that is one of the choices a variation point permits. ISO/IEC 14143-2, 3.6 calls the choice an *option* and the requirement that realises it an *alternative requirement* (note a); this framework uses one word for both. An option has its own statement, observability, interaction type and test. It has no `level` of its own: its obligation comes from the variation point. A solution chooses among the options of a variation point, but it cannot decline them all.
* **Conditional requirement**: a requirement that applies only when the solution meets a given option or claims a given extension (ISO/IEC 14143-2, 3.6, note b). It has a `level`, which binds only when the condition holds.
* **Conformance declaration**: the options and extensions a solution claims. It is the same declaration ARCH-ADR-7 defines for extensions, and is supplied with the solution when it is assessed.

"Option" is used only as a noun, for one of the choices of a variation point, and always together with its variation point. The adjective "optional" is avoided: in GovStack usage it has meant `RECOMMENDED`, something a solution may skip, which is exactly what an option is not. An option is also not an extension. A solution may claim an extension or not; it must meet at least `min` options of every variation point that applies to it.

### Keys

Options are referred to by a **key**, not by their number. Under ARCH-ADR-5 a requirement receives its number only when it is accepted, so a `DRAFT` option has none, yet conditional requirements and conformance declarations must be able to name it from the start.

A key is a short lowercase identifier, such as `vc`, `bearer` or `pass`. It is unique in the specification, across options and extension ids, so that `applies_when` can name either without ambiguity. Like a requirement number, a key is never reused and never changed once the option is published. A variation point may also carry a key, for readability; it is not used in conditions.

### Fields

| Field | On | Content |
|---|---|---|
| `key` | an option; a variation point, where it helps the reader | See *Keys* |
| `select` | a variation point | `min`, the number of options a solution meets, default 1; `max`, the most it may declare, default no maximum |
| `option_of` | an option | The key of its variation point |
| `applies_when` | any requirement; a functionality | Keys of options, or ids of extensions. The requirement applies if the solution meets **any** of them |
| `applies_when_all` | any requirement; a functionality | As `applies_when`, but the requirement applies only if the solution meets **all** of them |

`applies_when` set in a functionality's front matter applies to every requirement in the file.

A variation point says how many of its options a solution meets, and names them by key in its statement:

```yaml
  - id: '3'
    key: credential-kinds
    status: PUBLISHED
    level: REQUIRED
    observability: OBSERVABLE
    mutability: EXTENSIBLE
    statement: The Wallet accepts credentials of at least one of the kinds vc, bearer and pass.
    select:
      min: 1
```

An option names its variation point and has no `level`:

```yaml
  - id: '4'
    key: vc
    option_of: credential-kinds
    status: PUBLISHED
    observability: OBSERVABLE
    interaction: FILE
    statement: The Wallet accepts a holder-bound verifiable credential, issued to the Holder and bound to them through a key or a claim.
    test: tests/credential-intake/vc.feature
  - id:
    key: bearer
    option_of: credential-kinds
    status: DRAFT
    interaction: FILE
    statement: The Wallet accepts a bearer credential from a scanned code or an imported file.
  - id:
    key: pass
    option_of: credential-kinds
    status: DRAFT
    interaction: FILE
    statement: The Wallet accepts a pass from a PKPASS file.
```

Choices nest through conditions. A variation point may itself be conditional: "the Wallet accepts verifiable credentials in at least one of the formats sd-jwt-vc, mdoc and vcdm" is a variation point with `applies_when: [vc]`, and so is "at least one of cryptographic or claim-based holder binding". An option is never itself a variation point, because an option states behaviour and a variation point does not (rule 2).

A conditional requirement names the options that make it apply. It sits in the functionality it belongs to by meaning, which may not be the one where the choice is made:

```yaml
# requirements/credential-intake.md
  - id:
    status: DRAFT
    level: REQUIRED
    applies_when: [pass]
    interaction: FILE
    statement: The Wallet verifies the signature and the manifest hashes of a pass before storing it.

# requirements/credential-presentation.md
  - id:
    status: DRAFT
    level: REQUIRED
    applies_when: [bearer, pass]
    interaction: USER_INTERFACE
    statement: The Wallet displays a bearer credential or a pass as a code a verifier can scan.
```

`select` replaces the file-level `cardinality`. `ONE_OF` becomes `min: 1, max: 1`, `ONE_OR_MORE` becomes `min: 1`, and `min: 2` expresses "at least two".

### Rules

1. A variation point has at least two options, and `select.min` is at least 1. A choice a solution may skip entirely is not a variation point: it is a set of `RECOMMENDED` requirements or an extension.
2. A variation point states the choice and nothing else. It carries no `interaction`, `api_operation_id` or `test`: it is verified by counting the options that pass. Its `observability` is `OBSERVABLE` only if every option is. This is the one exception to the rule in *Requirements* that every requirement has exactly one interaction type.
3. An option has no `level`. It has its own `status`, `observability`, `interaction` and `test`, and follows every rule of *Writing the statement*.
4. A requirement that applies to every solution, whatever it chose, carries no condition, and sits in the same file as the variation point if that is where it belongs. The Wallet verifies the issuer's signature of every credential before storing it, whichever kinds it accepts.
5. A condition never appears in prose. A statement that begins "When the Wallet supports…" or "If…" is rewritten without the condition, and the condition goes into `applies_when`.
6. A variation point may sit in an extension functionality. It then binds only solutions that claim the extension (ARCH-ADR-7, rule 7).
7. `applies_when` may name an extension id, so that a requirement in a core functionality applies only to solutions that claim the extension (ARCH-ADR-7, rule 6).
8. Requirement numbers follow *Unique identifier* in *Requirements*. An option takes the next free number when it is accepted, like any other requirement. Its key does not replace its number in citations from outside the specification.

### In the overview and scope

* The *Functionalities* table of the overview is unchanged: a variation point is part of its functionality. The *Description* of a functionality with a variation point says what the choice is and why the specification leaves it to the implementer.
* An actor or an interface that a solution meets only through an option is marked with its key in *Actors* and *Interfaces*, as an extension is: "Pass issuer (`pass`)". A solution that does not declare the option never deals with it.
* The *Covered* list of the scope names each variation point and its options, so that a reader sees what kinds of solution conform.

### Conformance

* A solution's conformance declaration lists the options it claims, by key, alongside the extensions it claims.
* A `REQUIRED` variation point fails if fewer than `min` of the declared options pass, or if more than `max` are declared.
* Only `PUBLISHED` options count towards `min`. A `DRAFT` or `CANDIDATE` option may be declared, and is shown as information, but it is not assessed and does not count. This is how a roadmap option enters a released specification without changing what conformance demands, as ARCH-ADR-5 allows for unnumbered requirements.
* A conditional requirement whose condition does not hold is reported as **not applicable**, neither passed nor failed. A `RECOMMENDED` one is left out of the share GovMarket reports.
* GovMarket shows the declared options as facts about the solution, with its extensions: "Wallet 2.0: core (vc: sd-jwt-vc, mdoc), Issuance". They are not a score. A buyer can filter on them, and cite them in a procurement.

### Mutability and extending specifications

The rules of *Mutability* in *Requirements* apply to the variation point as a whole.

* An extending specification may narrow an `EXTENSIBLE` variation point: raise `min`, lower `max`, or mark options `INAPPLICABLE`, as long as at least `min` options remain. A national profile that marks `bearer` and `pass` `INAPPLICABLE` leaves `vc` as the only way to meet requirement 3, which then binds every wallet under that profile.
* An extending specification may not add options. That would broaden the base, which `EXTENSIBLE` forbids.
* A `REPLACEABLE` variation point may be replaced by one with the same options and the same `select`.
* An `IMMUTABLE` variation point cannot be narrowed.
* Each option carries its own mutability, for how it is met, as any requirement does.

### Versioning

Changes are classified by the two promises of *Versioning and Change Documentation*: a solution that conformed keeps conforming (implementers), and what every conformant solution was guaranteed to do stays guaranteed (dependents). For a `REQUIRED` variation point:

| Change | Release | Why |
|---|---|---|
| Publish a new option | MAJOR | Dependents lose a guarantee: before, every conformant solution met one of the earlier options |
| Deprecate a published option | MAJOR | Solutions that relied on it no longer conform |
| Raise `min`, or lower `max` | MAJOR | Existing solutions may no longer conform |
| Lower `min`, or raise or remove `max` | MAJOR | Dependents lose a guarantee |
| Add a condition to a published `REQUIRED` requirement | MAJOR | Solutions that do not meet the condition lose an obligation that dependents relied on |
| Remove a condition from a published `REQUIRED` requirement | MAJOR | Solutions that did not meet the condition gain an obligation |
| Any of the above on a `RECOMMENDED` variation point or requirement | MINOR | Conformance is unaffected |
| Any change to an option that is not yet published | PATCH | It takes no part in conformance |

The first row departs from the earlier Wallet draft of this proposal, which made publishing an option a minor change. It follows from the two-sided promise: once a pass-only wallet can conform, a verifier can no longer assume every conformant wallet accepts verifiable credentials. If the Architecture Working Group protects implementers only (question 1 of *Versioning and Change Documentation*), publishing an option and lowering `min` become minor.

The practical consequence matches *Recommend first, require later*: publish the options a variation point is expected to need together, in a major release, and keep the rest as `DRAFT` options until the next one.

### Compiler checks

* `option_of` names the key of a requirement in the same specification that has `select`.
* An option has no `level`. A variation point has at least two options, no `interaction` and no `test`.
* `select.min` is at least 1 and no greater than the number of options. `select.max`, if set, is at least `min`.
* Keys are unique across the options and extensions of the specification, and are never reused or changed once published.
* Every key in `applies_when` and `applies_when_all` is an option or a declared extension.
* An option has no `select`.
* No cycles: a requirement is not conditional on itself or on an option of itself, directly or through nested conditions.
* Warn when a `REQUIRED` variation point has fewer than `min` published options: no solution can conform.
* Warn when a statement holds a condition in prose ("When the Wallet supports…", "If…") and `applies_when` is empty.
* During migration, accept a file-level `cardinality` and warn that it is replaced by `select`.

### Consequences

* *Requirements* (`chapters/requirements.md`) gains a section, *Options and conditions*, with the vocabulary, fields and rules above, and its *Classifiers* table notes that an option has no `level` and a variation point no `interaction`.
* The requirement file template in *Structure of Specifications* and `bb-template`, and the publications compiler's requirements parser, gain `key`, `select`, `option_of`, `applies_when` and `applies_when_all`, and drop `cardinality`.
* *Introductory Chapters* marks actors and interfaces reached only through an option, and the scope's *Covered* list names variation points.
* *Versioning and Change Documentation* gains the table above under *Classifier changes*.
* The rendered specification shows a variation point with its options beneath it, and the condition next to each conditional requirement.
* The test harness and GovMarket take the conformance declaration as input. Its format is defined with the compliance process, outside this ADR and ARCH-ADR-7.
* Specifications already using `cardinality` (none released) are migrated by hand.

### Confirmation

Confirmed by the Architecture Committee following Architecture Working Group discussion. Implemented in `chapters/requirements.md`, in the template in `chapters/structure_of_specifications.md` and `bb-template`, and in the publications compiler's requirements parser. The Wallet Building Block 2.0 is the first specification to use it.

## Pros and Cons of the Approaches

### Approach 1: Keep file-level `cardinality`

Define the existing field as it is implemented.

* Good, because the template and the compiler already carry it
* Good, because it is one field and easy to read
* Bad, because common requirements and options cannot share a file, so functionalities are split for modelling reasons
* Bad, because an option is a single requirement and cannot bring in the requirements that follow from it
* Bad, because it has no conditional applicability, so conditions stay in prose
* Bad, because it cannot express "at least two"

### Approach 2: Variation points with options and conditional requirements

* Good, because a choice is a requirement, so it is non-discardable, citable and testable like any other
* Good, because the requirements that follow from a choice sit in the functionality they belong to, linked to the choice by `applies_when`
* Good, because "not applicable" is distinct from "failed" in tests and in GovMarket
* Good, because it shares the conformance declaration, `applies_when` and the "not applicable" outcome with extensions, so the two mechanisms compose instead of overlapping
* Good, because keys let a `DRAFT` option be named before it has a number, as ARCH-ADR-5 requires
* Good, because it follows ISO/IEC 14143-2 and the variation-point vocabulary of ISO/IEC 26550
* Good, because extending specifications get a precise way to narrow a choice, consistent with `EXTENSIBLE`
* Neutral, because a solution must supply a conformance declaration, which ARCH-ADR-7 already requires
* Bad, because it adds five fields to the template and several checks to the compiler
* Bad, because nested and conditional variation points can be misused to build complex dependency graphs. The compiler catches cycles but not poor design; Working Group review has to

### Approach 3: Conformance profiles

The specification defines named profiles, such as "Credential wallet" and "Pass wallet", each listing the requirements it includes. A solution conforms to one or more profiles. This is the *RUR Collection* of ISO/IEC TR 14143-4.

* Good, because a buyer can cite a profile by name
* Good, because each profile is a flat list, simple to test
* Bad, because profiles multiply with every independent choice: three kinds of credential, three formats and two kinds of binding give a dozen combinations, each to be named and maintained
* Bad, because the reason a requirement is in one profile and not another is lost
* Bad, because "profile" already means an extending specification in GovStack (`chapters/functionalities_and_extensions.md`, *Terms that sound alike*)
* Neutral, because named combinations can still be defined later on top of Approach 2, as procurement shortcuts that fix a set of options

### Approach 4: No new mechanism

* Good, because nothing changes
* Bad, because choices marked `RECOMMENDED` can all be skipped, and are scored as quality
* Bad, because conditions remain prose that no tool can read
* Bad, because the template and compiler keep an undefined `cardinality` field

## Questions for the Architecture Working Group

1. **Publishing an option.** This ADR makes it a major change under the two-sided promise. Is that acceptable for roadmap options such as bearer credentials and passes, or should options be an exception?
2. **Extensions that need an option.** The Wallet's Issuance extension only makes sense for verifiable credentials. Should an extension's `requires` in `metadata.yml` be allowed to name option keys, as well as other extensions?
3. **Keys for every requirement.** Keys solve the naming of unnumbered options. Should every requirement have one, to make all unnumbered requirements referable, which ARCH-ADR-5 leaves open?
4. **`max`.** Is there a real case for limiting how many options a solution may declare, or should `select` have only `min`?

## More information

* `chapters/requirements.md`, *Classifiers*, *Unique identifier*, *Mutability* and *Interaction types*
* `chapters/functionalities_and_extensions.md`, *Other mechanisms, and when to use them instead*, *Conformance* and *Terms that sound alike*
* `chapters/versioning_and_change_documentation.md`, *What the version promises* and *Classifier changes*
* `chapters/introductory_chapters.md`, *Actors*, *Interfaces* and *Covered*
* ARCH-ADR-5, *Separate a requirement's obligation level from its lifecycle status*
* ARCH-ADR-7, *Organise a specification's functionalities into a core and extensions*
* `bb-template/requirements/functionality-name.md` and `publications-compiler/requirements/requirements_parser.md` req-3, for the current `cardinality` field
* ISO/IEC 14143-2:2011, 3.5 *exclusive requirement* and 3.6 *optional requirement*; ISO/IEC TR 14143-4, *reference user requirements*; ISO/IEC 26550:2015 and ISO/IEC 26551:2016, *variation point* and *variability in requirements*
