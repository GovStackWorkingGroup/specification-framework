---
title: Model options
status: proposed
date: 2026-10-27
updated:
  - date: 2026-11-22
    message: "Recommends nested options with level OPTION and n.m ids, options met by claiming an extension, and publishing an option as a MINOR change."
  - date: 2026-11-25
    message: "Conditional requirements move to ARCH-ADR-9."
  - date: 2026-11-26
    message: "Shortened. Unreleased explorations (file-level cardinality, flat options, max, presented_by, options in requires) move to the end. Options are numbered when accepted, as in ARCH-ADR-5. Six questions become two."
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

Some requirements say "every conformant solution does at least one of X, Y or Z". The solution cannot skip the requirement, but it chooses how to meet it. *Requirements* (`chapters/requirements.md`) cannot express this: `level` says whether a solution fails without a requirement, and nothing says that a requirement is one of several ways of meeting another.

ISO/IEC 14143-2:2011, 3.6 calls this an *optional requirement* of the first kind: "one of two or more alternative requirements". Its second kind, a requirement that applies only if a choice was made, is ARCH-ADR-9.

### Worked example: the Wallet

A wallet must accept credentials, but wallets hold different types:

- **bearer credentials**, such as signed QR certificates, added from a scanned code or a file;
- **passes** in the PKPASS format, such as tickets, added from a file or a link;
- **verifiable credentials**, obtained from an issuer over OpenID4VCI and bound to the Holder.

A conformant wallet accepts at least one type, and which ones is the implementer's choice. A national identity wallet may hold only verifiable credentials; a transport operator's wallet only passes.

The types are not alike. A wallet handles bearer credentials and passes by itself. Verifiable credentials need an ecosystem of issuers, verifiers and trust infrastructure, with protocols of their own. That part fits an extension (ARCH-ADR-7), but only as one way of meeting the choice, not as the choice itself.

### Why the current model does not fit

| Mechanism | Why it does not fit |
|---|---|
| `RECOMMENDED` on each type | A solution may skip all of them, and GovMarket would score a wallet with three types above one with one |
| `REQUIRED` on each type | Excludes every wallet that does not accept all three |
| An extension per type (ARCH-ADR-7) | A solution may skip an extension; it cannot skip the choice. And bearer credentials and passes need no ecosystem |

The same pattern appears wherever a Building Block admits several formats, channels or protocols for one job: payment rails, import formats, authentication factors. Product-line engineering calls it a **variation point** (ISO/IEC 26550).

## Decision Drivers

* A requirement with several ways of meeting it is still non-discardable
* Which option a solution chose is visible, but not scored as quality
* A reader sees which requirement an option belongs to, from the file and from its id
* An option that needs a whole ecosystem can be met through an extension, without the core depending on it
* The model fits ARCH-ADR-5, ARCH-ADR-7 and the mutability and versioning rules, and uses established vocabulary

## Considered Approaches

* **Approach 1:** Nested options: options sit beneath their variation point, with level `OPTION`, and may be met by claiming an extension
* **Approach 2:** Conformance profiles: named lists of requirements, such as "Pass wallet"
* **Approach 3:** No new mechanism

## Decision Outcome

*To be completed by the Architecture Committee.*

The Wallet Working Group recommends **Approach 1**.

### Vocabulary

* **Variation point**: a requirement that a solution meets by meeting at least `min` of its options. A `REQUIRED` variation point fails if the solution meets fewer.
* **Option**: one of the choices a variation point permits. It has level `OPTION`: it does not bind on its own, and counts towards its variation point.

"Option" is only ever a noun. "Optional" is avoided, because in GovStack it has meant `RECOMMENDED`, which an option is not. An option is also not an extension, though it may be met by claiming one.

### Writing a variation point

```yaml
# requirements/credential-acquisition.md
conformance: CORE
requirements:
  - id: '30'
    status: ACCEPTED
    level: REQUIRED
    mutability: EXTENSIBLE
    statement: The Wallet adds credentials of at least one of the credential types in requirements 30.1 to 30.3.
    select:
      min: 1
    options:
      - id: '30.1'
        key: bearer
        status: ACCEPTED
        level: OPTION
        interaction: FILE
        statement: The Wallet adds a bearer credential from a scanned QR code or barcode, or from an imported file.
        test: resources/tests/credential-acquisition/30.1-bearer.feature
      - id: '30.2'
        key: pass
        status: ACCEPTED
        level: OPTION
        interaction: FILE
        statement: The Wallet adds a pass from a PKPASS file or from a link to one.
        test: resources/tests/credential-acquisition/30.2-pass.feature
      - id: '30.3'
        key: vc
        status: ACCEPTED
        level: OPTION
        extension: verifiable-credentials
        statement: The Wallet obtains verifiable credentials from issuers as the Verifiable Credentials extension specifies.
```

| Field | On | Content |
|---|---|---|
| `select.min` | a variation point | How many options a solution meets, default 1 |
| `options` | a variation point | Its options, indented beneath it |
| `level: OPTION` | an option | Every option has it, and nothing else does |
| `key` | an option | A short lowercase name, used in a conformance declaration, GovMarket, an extending specification, or a condition (ARCH-ADR-9). Unique across options and extensions, never reused or changed once published |
| `extension` | an option | The id of an extension. The option is met by claiming it |

An option's id is its variation point's id, a full stop and its own number. It is assigned when the option is accepted, like any requirement's (ARCH-ADR-5), and is never reused or changed.

### Options met by claiming an extension

An option with `extension` is met when the solution claims the extension and passes its `PUBLISHED` requirements. It has no `interaction` or `test`: the extension's tests are its tests. Its statement still says in one sentence what the solution does.

Use an extension for an option only when the option needs counterparts that follow a given protocol, or brings workflows, data structures and APIs of its own. Otherwise use a plain option.

This does not break ARCH-ADR-7, rule 5 ("the core never depends on an extension"), because the core still meets the variation point through its plain options (rule 5 below).

### Rules

1. A variation point has at least two options, and `min` is between 1 and the number of options. A choice a solution may skip entirely is a set of `RECOMMENDED` requirements or an extension.
2. A variation point states the choice and nothing else. It has no `interaction`, `api_operation_id` or `test`: it is verified by counting the options that pass.
3. An option has level `OPTION` and an id of the form `n.m`, and sits in the `options` list of its variation point. It has its own `status`, `interaction` and `test`, unless it is met by claiming an extension, and no `select` or `options` of its own.
4. An option with `extension` names an extension declared in `metadata.yml`.
5. In a core functionality, at least `min` options of each variation point have no `extension`, so that the core stands alone.
6. A variation point may sit in an extension, and then binds only solutions that claim it. This is how choices nest: the choice of credential format sits in the `verifiable-credentials` extension, never as options inside option 30.3.

The compiler enforces these rules, and warns when a `REQUIRED` variation point has fewer than `min` published options.

### Conformance

* A solution's conformance declaration (ARCH-ADR-7) lists the options it meets, by key or id, with the extensions it claims. Declaring an option met by an extension also claims the extension.
* A `REQUIRED` variation point fails if fewer than `min` of the declared options pass. Only `PUBLISHED` options count.
* An option that is not declared is neither passed nor failed, and is left out of the share GovMarket reports.
* GovMarket shows declared options as facts, not as a score: "Wallet 2.0: core (pass, bearer)", or "Wallet 2.0: core (vc), Verifiable Credentials". A buyer can filter on them and cite them.

### Extending specifications

* An extending specification may narrow an `EXTENSIBLE` variation point: raise `min`, or list the options it **accepts**, as long as at least `min` remain. Listing accepted options, not rejected ones, keeps an option the base publishes later out of the profile until the profile adopts it.
* It may not add options, which would broaden the base.
* A `REPLACEABLE` variation point may be replaced by one with the same options and `min`. An `IMMUTABLE` one cannot be narrowed.

### Versioning

| Change to a `REQUIRED` variation point | Release |
|---|---|
| Publish a new option | MINOR |
| Deprecate a published option | MAJOR |
| Change `min` | MAJOR |
| Any change to a `RECOMMENDED` variation point | MINOR |
| Any change to an option that is not yet published | PATCH |

Publishing an option is minor because a variation point is an **open set**. No solution stops conforming: each still meets the options it declared. And a dependent never relies on "every conformant solution meets one of the options listed today", only on the options a solution declares. A verifier that needs verifiable credentials asks for `vc`. *Versioning and Change Documentation* already treats an open value set the same way. Extending specifications are not widened either, because they list the options they accept.

### Consequences

* *Requirements* gains a section *Options*, adds `OPTION` to `level`, and adds the `n.m` id form to *Unique identifier*.
* *Functionalities and Extensions* and ARCH-ADR-7 add options met by claiming an extension to *Other mechanisms, and when to use them instead*.
* The requirement file template, `bb-template` and the publications compiler gain `select`, `options`, `OPTION`, `key` and `extension`, and drop their unreleased file-level `cardinality` field.
* *Introductory Chapters*: a functionality's description says what its choice is; actors and interfaces reached only through an option are marked with its key; the scope's *Covered* list names each variation point and its options.
* *Versioning and Change Documentation* gains the table above, and states that a variation point is an open set.
* The test harness and GovMarket take the conformance declaration as input. Tests carry the option key, so that the harness runs them only for declared options.

### Confirmation

Confirmed by the Architecture Committee following Architecture Working Group discussion. Implemented in `chapters/requirements.md`, the template in `chapters/structure_of_specifications.md`, `bb-template` and the publications compiler. The Wallet Building Block 2.0 is the first specification to use it.

## Pros and Cons of the Approaches

### Approach 1: Nested options

* Good, because the choice is a requirement: non-discardable, citable and testable
* Good, because each option sits beneath its variation point, and its id shows it: 30.2 is an option of 30
* Good, because an option can be met through an extension, so a choice can mix options a solution meets alone with options that need an ecosystem, and the core still stands alone
* Good, because extending specifications get a precise way to narrow a choice
* Good, because it follows ISO/IEC 14143-2 and ISO/IEC 26550
* Neutral, because a solution supplies a conformance declaration, which ARCH-ADR-7 already requires
* Bad, because the `n.m` id form must be handled by the compiler and the publication identifiers
* Bad, because it adds five fields or values to the template

### Approach 2: Conformance profiles

The specification defines named profiles, each listing the requirements it includes (ISO/IEC TR 14143-4, *RUR Collection*).

* Good, because a buyer can cite a profile by name, and each profile is a flat list
* Bad, because profiles multiply with every independent choice: three types, three formats and two kinds of binding give a dozen combinations
* Bad, because "profile" already means an extending specification in GovStack
* Neutral, because named combinations of options can still be added later, as procurement shortcuts

### Approach 3: No new mechanism

* Good, because nothing changes
* Bad, because choices marked `RECOMMENDED` can all be skipped, and are scored as quality

## Questions for the Architecture Working Group

1. **Publishing an option as MINOR.** Does the Working Group accept that every variation point is an open set, so that dependents rely only on declared options? Is there a case for a closed variation point, where publishing an option would be MAJOR?
2. **Options met by claiming an extension.** Does the Working Group accept `extension` on an option, and the reading of ARCH-ADR-7, rule 5 above?

## More information

* `chapters/requirements.md`, *Classifiers*, *Unique identifier*, *Mutability* and *Options*
* `chapters/functionalities_and_extensions.md` and `chapters/versioning_and_change_documentation.md`
* ARCH-ADR-5, ARCH-ADR-7, and ARCH-ADR-9 (proposed), *Model conditional requirements*
* `bb-wallet/research/2.0-requirements-reorganization-proposal.md`
* ISO/IEC 14143-2:2011, 3.6; ISO/IEC TR 14143-4; ISO/IEC 26550:2015 and ISO/IEC 26551:2016

## Explorations not taken forward

Earlier drafts of this ADR, none of them released, considered the following.

* **File-level `cardinality`** (`ALL`, `ONE_OF`, `ONE_OR_MORE`), as in `bb-template` and the publications compiler. It applies to a whole file, so requirements every solution meets must move to another file, and it cannot say "at least two". Replaced by `select.min`.
* **Flat options**: options as top-level requirements without a `level`, linked by `option_of`. Options could be scattered through the file, their numbers said nothing about their variation point, and every option needed a key. Replaced by nesting.
* **`max`**, a limit on how many options a solution may declare. No specification has needed it. It can be added to `select` if one does.
* **Publishing an option as MAJOR.** It made every new type, format or channel wait for a major release, the change a variation point exists to make easy.
* **`presented_by`**, letting an option name the options of another variation point it works with, so that "every type acquired can be presented" is checked from the declaration. Left for a later ADR if the Wallet needs it.
* **Options in `requires`**, so that an extension such as `proximity-presentation` can require option `mdoc`. Left for later; until then the extension states the need as a requirement of its own.
