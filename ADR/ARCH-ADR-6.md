---
title: Model options
status: proposed
date: 2026-10-27
updated:
  - date: 2026-11-22
    message: "Recommends nested options (Approach 3): level OPTION, options listed beneath their variation point with n.m ids, keys only where they are used, and options met by claiming an extension. The flat model of the first draft becomes Approach 2. Publishing an option becomes MINOR: variation points are open sets and dependents rely on declared options; extending specifications accept options by listing them. Adds rule 4 (the core meets min without extensions), and questions on numbering, presented_by and requires naming options. Worked example updated from the Wallet's credential types and presentation methods."
  - date: 2026-11-25
    message: "Conditional requirements (applies_when, applies_when_all, views by key, and the related rules, checks and version changes) move to ARCH-ADR-9, so that the Architecture Committee can decide the two separately. This ADR keeps variation points, options and keys."
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
# Model options

## Context and Problem Statement

Some requirements cannot be stated as "every conformant solution does X". They say "every conformant solution does at least one of X, Y or Z". The solution cannot skip the requirement, but it chooses how to meet it.

*Requirements* (`chapters/requirements.md`) cannot say this. Its classifiers describe one requirement at a time: `level` says whether a solution fails without it, and nothing says that a requirement is one of several ways of meeting another.

ISO/IEC 14143-2:2011, 3.6 names the missing concept:

> **optional requirement.** requirement… fulfilled in order to comply with a particular option permitted by that document. *Note: An optional requirement is either: a) one of two or more alternative requirements, or b) an additional requirement that is fulfilled only if applicable and can otherwise be disregarded.*

The two cases behave differently in a conformance assessment. Case (a) is a choice the solution makes. Case (b) is a requirement whose applicability follows from that choice, or from an extension the solution claims. This ADR decides case (a). Case (b), conditional requirements, is decided separately in ARCH-ADR-9, which can name the options defined here in its conditions.

### Worked example: the Wallet

A wallet must accept credentials. That is the core functionality *Credential acquisition* of the Wallet Building Block. But wallets hold different types of credential:

- **bearer credentials**, signed by an issuer but bound to no one, such as ICAO Visible Digital Seals or signed QR certificates, added from a scanned code or a file;
- **passes** in the PKPASS format, such as tickets and membership cards, added from a file or a link;
- **verifiable credentials**, in SD-JWT VC, ISO mdoc or W3C VCDM, bound to the Holder through a key or a claim, and obtained from an issuer over OpenID4VCI.

A conformant wallet must accept at least one of these, and which ones is the implementer's choice. A national identity wallet may hold only verifiable credentials; a transport operator's wallet may hold only passes. Both are wallets the specification should recognise.

Choices nest: a wallet that accepts verifiable credentials supports at least one of three formats, and at least one of cryptographic or claim-based holder binding. The choice also decides which other requirements apply, such as the pass manifest check or status lists; ARCH-ADR-9 covers those.

The three options are not alike. A wallet handles bearer credentials and passes by itself: a file or a code in, a signature check, a code on screen. Verifiable credentials need an ecosystem of issuers, verifiers and trust infrastructure that follow given protocols, with their own workflows, data structures and APIs. The Wallet Working Group first modelled every type as an extension, and found that it split a handful of autonomous requirements into files of their own and made a wallet claim extensions for things every wallet can do. It then modelled every type as a plain option, and found that the verifiable credential option dragged a whole protocol stack into the core. What fits is a plain option for the first two, and for the third an option that is met by claiming an extension (ARCH-ADR-7).

### What the current model offers, and why it fails here

| Mechanism | Why it does not fit |
|---|---|
| `RECOMMENDED` on each type of credential | A solution may skip all of them. And GovMarket would score a wallet that accepts all three above one that accepts only verifiable credentials, which is not what the Working Group means |
| `REQUIRED` on each type of credential | Excludes every wallet that does not accept all three |
| Extensions (ARCH-ADR-7, `chapters/functionalities_and_extensions.md`) | An extension is a set of functionalities a solution may skip entirely. Here the solution cannot skip the choice, and the choice sits inside one functionality. An extension does fit one option, verifiable credentials, but only as the way that option is met, not as the choice itself |
| The file-level `cardinality` field (`ALL`, `ONE_OF`, `ONE_OR_MORE`) in `bb-template` and the publications compiler | Not defined anywhere in the framework. It applies to a whole file, so the requirements every wallet meets must move to another file. It cannot express "at least two" |

### Why it matters beyond the Wallet

Any Building Block that admits several formats, channels or protocols for the same job meets this pattern: Payments with several payment rails, Digital Registries with several import formats, Identity with several authentication factors. Product-line engineering names it a **variation point** (ISO/IEC 26550, ISO/IEC 26551). An extending specification, such as a national profile, needs the same mechanism to narrow a choice: "this country's wallets accept mdoc credentials".

## Decision Drivers

* A requirement that admits several ways of meeting it is still non-discardable: a solution that meets none of them fails
* Which option a solution chose is recorded and visible, but not scored as quality
* A reader sees which requirement an option belongs to from the file and from the option's id, without following references
* A functionality file is organised by meaning, not by the needs of the model
* An option whose content is a whole ecosystem can be met through an extension, without the core depending on that extension's content
* The model fits *Requirements*: the status model and numbering of ARCH-ADR-5, the interaction classifier, and the mutability rules for extending specifications
* The model fits *Functionalities and Extensions*: it composes with extensions, uses the same conformance declaration, and does not duplicate them
* Options can be named by conditional requirements (ARCH-ADR-9), but the model works if ARCH-ADR-9 is not accepted
* Version changes are classified by the same two promises as everything else in *Versioning and Change Documentation*
* The model uses established vocabulary (ISO/IEC 14143-2, ISO/IEC 26550 family) rather than inventing its own

## Considered Approaches

* **Approach 1:** Keep the file-level `cardinality` field, and define it in *Requirements*
* **Approach 2:** Flat options: options are top-level requirements without a `level`, linked to their variation point by `option_of` and a key
* **Approach 3:** Nested options: options sit beneath their variation point, with level `OPTION` and an id derived from it, and may be met by claiming an extension
* **Approach 4:** Conformance profiles: the specification defines named profiles, each listing the requirements it includes
* **Approach 5:** No new mechanism: `RECOMMENDED`, and the choice described in prose

## Decision Outcome

*To be completed by the Architecture Committee.*

The Wallet Working Group recommends **Approach 3**. Like Approach 2, it keeps a choice non-discardable and lets a functionality file hold both the requirements every solution meets and the choices it makes. It also shows each option where it belongs, gives every requirement a `level`, needs keys only where they are used, and lets an extension be one of the ways a choice is met. Approach 2 was this ADR's earlier recommendation; the Wallet's attempt to model credential types with it led to Approach 3.

### Vocabulary

* **Variation point**: a requirement that a solution meets by meeting a number of its options. It has a `level` like any requirement. A `REQUIRED` variation point fails if the solution does not meet enough of its options.
* **Option**: a requirement that is one of the choices a variation point permits. ISO/IEC 14143-2, 3.6 calls the choice an *option* and the requirement that realises it an *alternative requirement* (note a); this framework uses one word for both. An option has level `OPTION`: it does not bind on its own, and counts towards its variation point. A solution chooses among the options of a variation point, but it cannot decline them all.
* **Conformance declaration**: the options and extensions a solution claims. It is the same declaration ARCH-ADR-7 defines for extensions, and is supplied with the solution when it is assessed.

"Option" is used only as a noun, for one of the choices of a variation point. The adjective "optional" is avoided: in GovStack usage it has meant `RECOMMENDED`, something a solution may skip, which is exactly what an option is not. An option is also not an extension, though it may be met by claiming one. A solution may claim an extension or not; it must meet at least `min` options of every variation point that applies to it.

### Level `OPTION`

`OPTION` joins `REQUIRED` and `RECOMMENDED` as a value of `level`. It is used only on options, and every option has it. It tells the reader, the compiler and GovMarket that the requirement is assessed and reported, but that whether a solution fails depends on its variation point, not on the option alone.

### Ids

An option's id is its variation point's id, a full stop, and its own number: the options of requirement 30 are 30.1, 30.2 and 30.3. Citations name the option the same way: "requirement 30.2". The number after the full stop follows *Unique identifier* in *Requirements*: it is never reused or changed, and a deprecated option keeps its number.

A variation point has a number only once it is accepted (ARCH-ADR-5). Its options take their numbers then, or, if they are added later, when they are added (question 3).

### Keys

An option that is named elsewhere has a **key**: a short lowercase identifier, such as `pass`, `mdoc` or `crypto`. A conformance declaration, GovMarket and an extending specification name options by key, and so do the conditions of ARCH-ADR-9. A key reads as what it means and does not change if a `DRAFT` option is numbered later. It is unique in the specification, across options and extension ids, and is never reused or changed once the option is published. An option that nothing names by key is named by its id.

### Fields

| Field | On | Content |
|---|---|---|
| `select` | a variation point | `min`, the number of options a solution meets, default 1; `max`, the most it may declare, default no maximum |
| `options` | a variation point | The list of its options, indented beneath it |
| `level: OPTION` | an option | See *Level `OPTION`* |
| `key` | an option that is named elsewhere | See *Keys* |
| `extension` | an option | The id of an extension. The option is met by claiming that extension and passing its tests |

`option_of` and `cardinality` are not used.

A variation point says how many of its options a solution meets, and lists them beneath it:

```yaml
# requirements/credential-acquisition.md
conformance: CORE
requirements:
  - id: '30'
    status: ACCEPTED
    level: REQUIRED
    observability: OBSERVABLE
    mutability: EXTENSIBLE
    statement: The Wallet adds credentials of at least one of the credential types in requirements 30.1 to 30.3.
    select:
      min: 1
    options:
      - id: '30.1'
        key: bearer
        status: ACCEPTED
        level: OPTION
        observability: OBSERVABLE
        mutability: EXTENSIBLE
        interaction: FILE
        statement: The Wallet adds a bearer credential from a scanned QR code or barcode, or from an imported file.
        test: resources/tests/credential-acquisition/30.1-bearer.feature
      - id: '30.2'
        key: pass
        status: ACCEPTED
        level: OPTION
        observability: OBSERVABLE
        mutability: EXTENSIBLE
        interaction: FILE
        statement: The Wallet adds a pass from a PKPASS file or from a link to one.
        test: resources/tests/credential-acquisition/30.2-pass.feature
      - id: '30.3'
        key: vc
        status: ACCEPTED
        level: OPTION
        observability: OBSERVABLE
        extension: verifiable-credentials
        statement: The Wallet obtains verifiable credentials from issuers as the Verifiable Credentials extension specifies.
```

Choices nest by placing one variation point where it applies, never by putting options inside options. The choice of format sits in the `verifiable-credentials` extension, so it binds only wallets that claim it:

```yaml
# requirements/vc-acquisition.md
conformance: EXTENSION
extension: verifiable-credentials
requirements:
  - id: '40'
    status: ACCEPTED
    level: REQUIRED
    observability: OBSERVABLE
    mutability: EXTENSIBLE
    statement: The Wallet accepts verifiable credentials in at least one of the formats in requirements 40.1 to 40.3.
    select:
      min: 1
    options:
      - id: '40.1'
        key: w3c-vcdm
        status: ACCEPTED
        level: OPTION
        observability: OBSERVABLE
        interaction: FILE
        statement: The Wallet accepts verifiable credentials in the W3C Verifiable Credentials Data Model.
        test: resources/tests/vc-acquisition/40.1-w3c-vcdm.feature
      - id: '40.2'
        key: sd-jwt-vc
        status: ACCEPTED
        level: OPTION
        observability: OBSERVABLE
        interaction: FILE
        statement: The Wallet accepts verifiable credentials in the SD-JWT VC format.
        test: resources/tests/vc-acquisition/40.2-sd-jwt-vc.feature
      - id: '40.3'
        key: mdoc
        status: ACCEPTED
        level: OPTION
        observability: OBSERVABLE
        interaction: FILE
        statement: The Wallet accepts credentials in the ISO/IEC 18013-5 mdoc format.
        test: resources/tests/vc-acquisition/40.3-mdoc.feature
```

A variation point that follows from a plain option, such as a choice that only wallets accepting passes make, would sit in the core with a condition naming the option. That is a conditional variation point, and needs ARCH-ADR-9.

A test for an option is written as for any requirement. The harness runs it only when the solution declares the option, and otherwise reports the option as not declared:

```gherkin
# resources/tests/credential-acquisition/30.2-pass.feature
@requirement:30.2 @option:pass
Feature: The Wallet adds a pass from a PKPASS file or from a link to one

  Scenario: A pass is added from a file
    Given the Holder opens the file "fixtures/passes/valid.pkpass" with the Wallet
    When the Holder approves adding the pass
    Then the Wallet lists the pass
```

`select` replaces the file-level `cardinality`. `ONE_OF` becomes `min: 1, max: 1`, `ONE_OR_MORE` becomes `min: 1`, and `min: 2` expresses "at least two".

### Options met by claiming an extension

An option with `extension` is met when the solution claims that extension and passes its `PUBLISHED` requirements. The option has no `interaction` or `test` of its own: the extension's tests are its test. Its statement still says what the solution does, in one sentence, so that the variation point reads in full without opening the extension.

Use an extension for an option when the option needs counterparts that follow a given protocol or trust infrastructure, or brings workflows, data structures and APIs of its own. Use a plain option otherwise. A plain option is cheaper: no extension has to be declared, documented or assessed.

This is the one way the core refers to an extension. It does not break ARCH-ADR-7, rule 5 ("The core never depends on an extension"): the variation point is met without the extension, through its other options, so the core depends on the choice, not on the extension. A variation point whose every option is met by an extension does depend on extensions, and is not allowed in the core (rule 4). The Wallet's *Credential presentation* first had only extension options (showing a code, OpenID4VP, ISO/IEC 18013-5); rule 4 moved showing a code, which needs no counterpart, into the core as a plain option.

It is also not Option 4 of ARCH-ADR-7, which made extensions the options of a variation point with `min: 0`. Here `min` is at least 1, so the choice cannot be skipped, and only the options that need an extension have one.

### Rules

1. A variation point has at least two options, and `select.min` is at least 1. A choice a solution may skip entirely is not a variation point: it is a set of `RECOMMENDED` requirements or an extension.
2. A variation point states the choice and nothing else. It carries no `interaction`, `api_operation_id` or `test`: it is verified by counting the options that pass. Its `observability` is `OBSERVABLE` only if every option is. This is the one exception to the rule in *Requirements* that every requirement has exactly one interaction type.
3. An option has level `OPTION`, sits in the `options` list of its variation point, and has an id of the form `n.m`. It has its own `status`, `observability`, `interaction` and `test`, unless it is met by claiming an extension, and follows every rule of *Writing the statement*.
4. In the core, at least `min` options of a variation point are met without an extension. Otherwise the core could not stand alone (ARCH-ADR-7, rule 3).
5. An option has no `select` and no `options`. A choice that follows from an option is a separate variation point, placed where it applies: in the extension that meets the option, or, under ARCH-ADR-9, with a condition naming the option.
6. A variation point may sit in an extension functionality. It then binds only solutions that claim the extension (ARCH-ADR-7, rule 7).

### In the overview and scope

* The *Functionalities* table of the overview is unchanged: a variation point is part of its functionality. The *Description* of a functionality with a variation point says what the choice is and why the specification leaves it to the implementer.
* An actor or an interface that a solution meets only through an option is marked with its key in *Actors* and *Interfaces*, as an extension is: "Pass issuer (`pass`)". A solution that does not declare the option never deals with it.
* The *Covered* list of the scope names each variation point and its options, so that a reader sees what kinds of solution conform.

### Conformance

* A solution's conformance declaration lists the options it claims, by key or id, alongside the extensions it claims. Declaring an option met by an extension also claims the extension.
* A `REQUIRED` variation point fails if fewer than `min` of the declared options pass, or if more than `max` are declared.
* Only `PUBLISHED` options count towards `min`. A `DRAFT` or `CANDIDATE` option may be declared, and is shown as information, but it is not assessed and does not count. This is how a roadmap option enters a released specification without changing what conformance demands, as ARCH-ADR-5 allows for unnumbered requirements.
* An option the solution does not declare is reported as not declared, neither passed nor failed, and is left out of the share GovMarket reports.
* GovMarket shows the declared options as facts about the solution, with its extensions: "Wallet 2.0: core (pass, bearer)", or "Wallet 2.0: core (vc: sd-jwt-vc, mdoc), Verifiable Credentials". They are not a score. A buyer can filter on them, and cite them in a procurement.

### Mutability and extending specifications

The rules of *Mutability* in *Requirements* apply to the variation point as a whole.

* An extending specification may narrow an `EXTENSIBLE` variation point: raise `min`, lower `max`, or restrict it to some of its options, as long as at least `min` remain. It restricts by listing the options it **accepts**, not the ones it rejects, so that an option the base publishes later does not enter the profile until the profile says so (see *Versioning*). A national profile that accepts only `vc` for requirement 30 binds every wallet under that profile to the Verifiable Credentials extension.
* An extending specification may not add options. That would broaden the base, which `EXTENSIBLE` forbids.
* A `REPLACEABLE` variation point may be replaced by one with the same options and the same `select`.
* An `IMMUTABLE` variation point cannot be narrowed.
* Each option carries its own mutability, for how it is met, as any requirement does.

### Versioning

Changes are classified by the two promises of *Versioning and Change Documentation*: a solution that conformed keeps conforming (implementers), and what every conformant solution was guaranteed to do stays guaranteed (dependents). For a `REQUIRED` variation point:

| Change | Release | Why |
|---|---|---|
| Publish a new option | MINOR | No conformant solution stops conforming. A variation point is an open set: dependents rely on the options a solution declares, not on the list being closed (see below) |
| Deprecate a published option | MAJOR | Solutions that relied on it no longer conform |
| Raise `min`, or lower `max` | MAJOR | Existing solutions may no longer conform |
| Lower `min`, or raise or remove `max` | MAJOR | Dependents lose a guarantee |
| Publish a requirement in the extension that meets an option | as for the extension (ARCH-ADR-7) | The option's test is the extension's tests |
| Any of the above on a `RECOMMENDED` variation point | MINOR | Conformance is unaffected |
| Any change to an option that is not yet published | PATCH | It takes no part in conformance |

**Why publishing an option is minor.** It keeps the implementers' promise: every solution that conformed still meets the options it declared. The dependents' promise depends on what a dependent was entitled to rely on. *Versioning and Change Documentation* already answers this for value sets: adding a value a solution may return is minor "if the value set is declared open". Every variation point is declared open by this ADR. A dependent therefore never relies on "every conformant solution meets one of the options listed today"; it relies on the options a solution declares, which GovMarket shows and a procurement cites ("Wallet 2.x: core (vc)"). A verifier that needs verifiable credentials asks for `vc`, and a pass-only wallet never claims to have it.

An earlier revision of this ADR made publishing an option major, reading the list of options as a guarantee to dependents. That made every new credential type, format or channel wait for a major release, which is the change a variation point exists to make easy, and it gave dependents a guarantee they should not use: a verifier that relied on the bare version number would still be unable to tell which of the listed options a wallet met.

What keeps this safe:

* **Dependents cite options.** The conformance declaration and GovMarket show the options a solution meets. *Versioning and Change Documentation* tells dependents that a variation point may gain options in a minor release.
* **Extending specifications accept options by listing them.** A national profile that accepts only `vc` is not widened when the base publishes a new option. It adopts the option, or not, in its own release.
* **The other changes stay major.** Deprecating an option and changing `select` change what solutions must do or what a declared option means.
* **Requirements that relate variation points still hold.** A new acquisition option names the presentation options that carry it (`presented_by`), so a requirement such as the Wallet's "every type acquired can be presented" applies to it from the start.

The remaining cost: a dependent that ignored the declaration and assumed every conformant solution met one of the earlier options may meet a solution that meets only the new one. This ADR accepts that cost and makes the rule explicit, rather than keeping a guarantee that blocks every addition.

### Compiler checks

* An option sits in the `options` list of a requirement that has `select`, and has level `OPTION`. No other requirement has level `OPTION`.
* An option's id is its variation point's id, a full stop and a number. Option numbers are unique within their variation point and never reused.
* A variation point has at least two options, no `interaction` and no `test`.
* `select.min` is at least 1 and no greater than the number of options. `select.max`, if set, is at least `min`.
* An option has no `select` and no `options`.
* An option with `extension` names an extension declared in `metadata.yml`, and has no `interaction` or `test`. Every other option has both.
* In a core functionality, at least `min` options of each variation point have no `extension`.
* Keys are unique across the options and extensions of the specification, and are never reused or changed once published.
* Warn when a `REQUIRED` variation point has fewer than `min` published options: no solution can conform.
* Warn when `option_of` is used.
* During migration, accept a file-level `cardinality` and warn that it is replaced by `select`.

### Consequences

* *Requirements* (`chapters/requirements.md`) gains a section *Options* with the vocabulary, fields and rules above. Its *Classifiers* table adds `OPTION` to `level`, and notes that a variation point has no `interaction`. *Unique identifier* adds the `n.m` form for options.
* *Functionalities and Extensions* (`chapters/functionalities_and_extensions.md`) and ARCH-ADR-7 add options met by claiming an extension to *Other mechanisms, and when to use them instead*, and note under rule 5 that this is not a dependency of the core on the extension. The warning that "two extensions that differ only in how they do the same thing" are a variation point is reworded: such extensions may be the options of one variation point.
* The requirement file template in *Structure of Specifications* and `bb-template`, and the publications compiler's requirements parser, gain `select`, `options`, the `OPTION` level, `key` and `extension`, and drop `cardinality`. The parser reads options as requirements nested in their variation point.
* The compiler renders a variation point with its options indented beneath it.
* *Introductory Chapters* marks actors and interfaces reached only through an option, and the scope's *Covered* list names variation points.
* *Versioning and Change Documentation* gains the table above under *Classifier changes*, lists publishing an option among the non-breaking changes, and says that a variation point is an open set, as it already does for open value sets.
* The test harness and GovMarket take the conformance declaration as input. Its format is defined with the compliance process, outside this ADR and ARCH-ADR-7. Tests carry the requirement id and the option key, so that the harness runs them only for declared options.
* Specifications already using `cardinality` (none released) are migrated by hand.

### Confirmation

Confirmed by the Architecture Committee following Architecture Working Group discussion. Implemented in `chapters/requirements.md`, in the template in `chapters/structure_of_specifications.md` and `bb-template`, and in the publications compiler's requirements parser. The Wallet Building Block 2.0 is the first specification to use it (`bb-wallet/research/2.0-requirements-reorganization-proposal.md`).

## Pros and Cons of the Approaches

### Approach 1: Keep file-level `cardinality`

Define the existing field as it is implemented.

* Good, because the template and the compiler already carry it
* Good, because it is one field and easy to read
* Bad, because common requirements and options cannot share a file, so functionalities are split for modelling reasons
* Bad, because an option is a whole file rather than a requirement, so it cannot be cited, numbered or narrowed on its own
* Bad, because it cannot express "at least two"

### Approach 2: Flat options

Options are top-level requirements with no `level`, each with a mandatory `key` and an `option_of` naming its variation point's key. Each takes the next free number when it is accepted. This was the earlier recommendation of this ADR.

* Good, because a choice is a requirement, so it is non-discardable, citable and testable like any other
* Good, because options are numbered like every other requirement, so ARCH-ADR-5 needs no change
* Neutral, because a solution must supply a conformance declaration, which ARCH-ADR-7 already requires
* Bad, because an option without a `level` is an exception in the *Classifiers* table, and a reader cannot tell an option from a requirement whose level was forgotten
* Bad, because the link from an option to its variation point is a reference, not structure: options may be scattered through the file, and their numbers say nothing about which requirement they belong to
* Bad, because every option needs a key, even when nothing refers to it
* Bad, because it has no way to meet an option through an extension, so an option whose content is a protocol stack either drags it into the core or becomes an extension that a solution may skip

### Approach 3: Nested options

* Good, for every reason Approach 2 is good, except the numbering
* Good, because the file shows each option beneath its variation point, and the id shows it in citations: requirement 30.2 is an option of requirement 30
* Good, because every requirement has a `level`, so the *Classifiers* table has no exception
* Good, because keys are needed only where an option is named elsewhere
* Good, because an option can be met by claiming an extension, so a choice can mix options a solution meets alone with options that need an ecosystem, and the core still stands alone
* Good, because it follows ISO/IEC 14143-2 and the variation-point vocabulary of ISO/IEC 26550
* Good, because extending specifications get a precise way to narrow a choice, consistent with `EXTENSIBLE`
* Neutral, because a solution must supply a conformance declaration, which ARCH-ADR-7 already requires
* Bad, because it adds a new id form, `n.m`, that the compiler, the publications identifiers and ARCH-ADR-5 numbering must handle
* Bad, because it adds five fields or values to the template and several checks to the compiler

### Approach 4: Conformance profiles

The specification defines named profiles, such as "Credential wallet" and "Pass wallet", each listing the requirements it includes. A solution conforms to one or more profiles. This is the *RUR Collection* of ISO/IEC TR 14143-4.

* Good, because a buyer can cite a profile by name
* Good, because each profile is a flat list, simple to test
* Bad, because profiles multiply with every independent choice: three types of credential, three formats and two kinds of binding give a dozen combinations, each to be named and maintained
* Bad, because the reason a requirement is in one profile and not another is lost
* Bad, because "profile" already means an extending specification in GovStack (`chapters/functionalities_and_extensions.md`, *Terms that sound alike*)
* Neutral, because named combinations can still be defined later on top of Approach 3, as procurement shortcuts that fix a set of options

### Approach 5: No new mechanism

* Good, because nothing changes
* Bad, because choices marked `RECOMMENDED` can all be skipped, and are scored as quality
* Bad, because the template and compiler keep an undefined `cardinality` field

## Questions for the Architecture Working Group

1. **Publishing an option.** This ADR makes it a minor change, by declaring every variation point an open set on which dependents rely only through declared options. Does the Architecture Working Group accept this reading of the dependents' promise? Is there a case for a closed variation point, for which publishing an option would be major?
2. **Options met by claiming an extension.** Does the Architecture Working Group accept `extension` on an option, and the reading of ARCH-ADR-7, rule 5 given above? Should the extension's `metadata.yml` entry also name the option it meets, so that the link can be read from both sides?
3. **Numbering options under ARCH-ADR-5.** A requirement receives its number when it is accepted. Does an option added later to an accepted variation point take its `n.m` number when it is added, since its parent already has one, or only when the option itself is accepted? In the second case it is named by its key until then.
4. **Requirements that relate two variation points.** The Wallet requires that "every credential type the Wallet acquires can be presented through at least one of the presentation options it meets". Should an option be able to name the options of another variation point it works with (for example `presented_by`), so that the harness checks such a requirement from the conformance declaration alone?
5. **Extensions that need an option.** The Wallet's `proximity-presentation` extension only works with mdoc credentials, which is option `mdoc` of a variation point inside `verifiable-credentials`. Should `requires` in `metadata.yml` be allowed to name option keys as well as extensions, or should such an extension state the need as a `REQUIRED` requirement of its own?
6. **`max`.** Is there a real case for limiting how many options a solution may declare, or should `select` have only `min`?

## More information

* `chapters/requirements.md`, *Classifiers*, *Unique identifier*, *Mutability*, *Interaction types* and *Options*
* `chapters/functionalities_and_extensions.md`, *Other mechanisms, and when to use them instead*, *Conformance* and *Terms that sound alike*
* `chapters/versioning_and_change_documentation.md`, *What the version promises* and *Classifier changes*
* `chapters/introductory_chapters.md`, *Actors*, *Interfaces* and *Covered*
* ARCH-ADR-5, *Separate a requirement's obligation level from its lifecycle status*
* ARCH-ADR-7, *Organise a specification's functionalities into a core and extensions*
* ARCH-ADR-9 (proposed), *Model conditional requirements*, on the requirements that apply only when a solution meets a given option or claims a given extension
* `bb-wallet/research/2.0-requirements-reorganization-proposal.md`, sections *Options* and *Credential types*
* `bb-template/requirements/functionality-name.md` and `publications-compiler/requirements/requirements_parser.md` req-3, for the current `cardinality` field
* ISO/IEC 14143-2:2011, 3.5 *exclusive requirement* and 3.6 *optional requirement*; ISO/IEC TR 14143-4, *reference user requirements*; ISO/IEC 26550:2015 and ISO/IEC 26551:2016, *variation point* and *variability in requirements*
