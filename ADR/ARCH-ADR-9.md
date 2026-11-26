---
title: Model conditional requirements
status: proposed
date: 2026-11-25
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
# Model conditional requirements

## Context and Problem Statement

Some requirements bind only some conformant solutions: those that made a given choice, or that claim a given extension. "The Wallet verifies the manifest hashes of a pass before storing it" binds a wallet that accepts passes, and means nothing for one that does not. Such a requirement is neither `REQUIRED` of every solution nor `RECOMMENDED`: where it applies it binds, and where it does not apply it is neither met nor failed.

*Requirements* (`chapters/requirements.md`) cannot say this. Its classifiers describe one requirement at a time, and nothing says that a requirement applies only to some solutions. Authors write the condition into the statement ("When the Wallet accepts passes, it verifies…"), where no tool can read it.

ISO/IEC 14143-2:2011, 3.6 names the concept:

> **optional requirement.** requirement… fulfilled in order to comply with a particular option permitted by that document. *Note: An optional requirement is either: a) one of two or more alternative requirements, or b) an additional requirement that is fulfilled only if applicable and can otherwise be disregarded.*

Case (a), a choice among options, is proposed in ARCH-ADR-6. This ADR decides case (b): a requirement whose applicability follows from what a solution declares. It was first part of ARCH-ADR-6, and was separated so that the two can be decided on their own. The conditions here can name the extensions of ARCH-ADR-7 without ARCH-ADR-6, and can also name options if ARCH-ADR-6 is accepted.

### Worked example: the Wallet

Under ARCH-ADR-6, a wallet adds credentials of at least one type: bearer credentials (`bearer`), passes (`pass`) or verifiable credentials (`vc`, met by claiming the `verifiable-credentials` extension). The choice decides which other requirements apply:

| If the wallet accepts… | …then these apply | …and these do not |
|---|---|---|
| bearer credentials | a warning that anyone with a copy can present it, copying to a new device, display as a code | holder binding, status lists |
| passes | signature and manifest checks, voided passes, pass updates, copying to a new device, display as a code | holder binding, status lists |
| verifiable credentials | issuance over OpenID4VCI, a choice of format, a choice of holder binding, status lists, reissuance on a new device | copying to a new device |

These requirements reach across functionalities: the pass manifest check sits in *Credential acquisition*, voided passes in *Identifying revoked credentials*, and copying passes in *Portability*. Each belongs in its functionality by meaning. Moving them next to the choice would split *Identifying revoked credentials* and *Portability* by credential type, which is a modelling need, not a reader's.

Extensions meet the same pattern. The Wallet's request-based presentation requirements (checking the verifier, minimising disclosure) are needed by both `remote-presentation` and `proximity-presentation`. Written in each, they are duplicated. Written in an extension of their own, they form an extension no product would claim by itself. They belong in `verifiable-credentials`, which both require, but bind only wallets that also claim one of the two presentation extensions. And a core *Transaction log* may need "the Wallet logs the verifier's identity for each presentation", which only means something for a wallet that presents to verifiers.

### What the current model offers, and why it fails here

| Mechanism | Why it does not fit |
|---|---|
| A condition in the statement: "When the Wallet accepts passes, it verifies…" | The condition is prose. The compiler cannot see it, GovMarket cannot tell "not applicable" from "failed", and a test harness would have to parse English |
| `RECOMMENDED` | A wallet that accepts passes may then skip the manifest check, and GovMarket scores it as quality |
| `REQUIRED` with no condition | Fails every wallet that does not accept passes |
| Moving the requirement into an extension (ARCH-ADR-7) | Works only where the condition is an extension. A plain option has no extension to hold its requirements, and an extension made only to hold them is one no product would claim. It also moves the requirement out of the functionality it belongs to |
| ARCH-ADR-7, rule 6 | Already anticipates `applies_when` naming an extension, but leaves it undefined |

## Decision Drivers

* A requirement that binds only some solutions is machine-readable as such, so that tests and GovMarket report it as not applicable rather than failed
* A requirement sits in the functionality it belongs to by meaning, whatever condition makes it apply
* Requirements shared by several extensions are written once
* A condition reads as what it means, and does not change when a `DRAFT` option or requirement is numbered
* Conditions use the same conformance declaration as extensions (ARCH-ADR-7) and options (ARCH-ADR-6), and add nothing new to it
* The model works with ARCH-ADR-7 alone, and also with ARCH-ADR-6 if it is accepted
* Conditions stay simple enough for a Working Group to review: no expression language
* Version changes are classified by the same two promises as everything else in *Versioning and Change Documentation*

## Considered Approaches

* **Approach 1:** No new mechanism: conditions in prose, and conditional requirements moved into extensions where possible
* **Approach 2:** Conditions on functionalities only: a conditional requirement goes into a functionality file whose front matter carries the condition
* **Approach 3:** `applies_when` and `applies_when_all` on any requirement or functionality, naming option keys and extension ids
* **Approach 4:** A boolean expression in one field: `applies_when: "pass or (vc and not mdoc)"`

## Decision Outcome

*To be completed by the Architecture Committee.*

The Wallet Working Group recommends **Approach 3**. It keeps each requirement in the functionality it belongs to, makes every condition visible to the compiler, the harness and GovMarket, and covers both the Wallet's credential types and its shared presentation requirements with two fields that hold lists, not expressions.

### Vocabulary

* **Condition**: a list of option keys (ARCH-ADR-6) or extension ids (ARCH-ADR-7) that a solution may declare. It holds for a solution when the solution declares any of them (`applies_when`), or all of them (`applies_when_all`).
* **Conditional requirement**: a requirement with a condition. It has a `level`, which binds only when the condition holds. When the condition does not hold, the requirement is **not applicable**. A conditional requirement is ISO/IEC 14143-2, 3.6, note b.
* **Conformance declaration**: the options and extensions a solution claims, as ARCH-ADR-7 and ARCH-ADR-6 define it. This ADR adds nothing to it; a condition is evaluated against it.

A conditional requirement is not an option. An option is one of several ways of meeting a variation point, and counts towards it. A conditional requirement is a requirement in its own right, which binds when its condition holds.

### Fields

| Field | On | Content |
|---|---|---|
| `applies_when` | any requirement; a functionality | Keys of options, or ids of extensions. The requirement applies if the solution declares **any** of them |
| `applies_when_all` | any requirement; a functionality | As `applies_when`, but the requirement applies only if the solution declares **all** of them |

`applies_when` set in a functionality's front matter applies to every requirement in the file. A requirement in that file may add a condition of its own; both must hold. A requirement may carry `applies_when` and `applies_when_all` together; both must hold.

A condition names an option by its **key** (ARCH-ADR-6, *Writing a variation point*), never by its id, so that it reads as what it means and does not change when a `DRAFT` option is numbered. Without ARCH-ADR-6, a condition names only extension ids.

A conditional requirement names the options that make it apply. It sits in the functionality it belongs to by meaning, which may not be the one where the choice is made:

```yaml
# requirements/credential-acquisition.md
  - id: '31'
    status: ACCEPTED
    level: REQUIRED
    observability: OBSERVABLE
    mutability: EXTENSIBLE
    applies_when: [pass]
    interaction: FILE
    statement: The Wallet verifies the signature and the manifest hashes of a pass before storing it.
    test: resources/tests/credential-acquisition/31-pass-manifest.feature

# requirements/identifying-revoked-credentials.md
  - id: '36'
    status: ACCEPTED
    level: REQUIRED
    observability: OBSERVABLE
    mutability: EXTENSIBLE
    applies_when: [pass]
    interaction: USER_INTERFACE
    statement: The Wallet shows the Holder which passes have been voided.
    test: resources/tests/identifying-revoked-credentials/36-voided-pass.feature

# requirements/portability.md
  - id: '38'
    status: ACCEPTED
    level: RECOMMENDED
    observability: OBSERVABLE
    mutability: EXTENSIBLE
    applies_when: [bearer, pass]
    interaction: USER_INTERFACE
    statement: The Wallet lets the Holder copy bearer credentials and passes to another device.
    test: resources/tests/portability/38-copy.feature
```

A requirement in an extension may name another extension, so that requirements several extensions share are written once, in the extension they all require:

```yaml
# requirements/answering-a-verifiers-request.md
conformance: EXTENSION
extension: verifiable-credentials
applies_when: [remote-presentation, proximity-presentation]
requirements:
  - id: '52'
    status: ACCEPTED
    level: REQUIRED
    observability: OBSERVABLE
    mutability: EXTENSIBLE
    interaction: USER_INTERFACE
    statement: The Wallet shows the Holder the identity of the Verifier and every requested attribute before any attribute is sent.
    test: resources/tests/answering-a-verifiers-request/52-show-request.feature
```

A variation point (ARCH-ADR-6) may itself be conditional. The choice of holder binding could sit in the core with `applies_when: [vc]`, instead of in the `verifiable-credentials` extension. It then binds only wallets that declare `vc`, and its options count only for them.

A test for a conditional requirement is written as for any requirement, and carries the condition. The harness runs it only when the condition holds, and otherwise reports the requirement as not applicable:

```gherkin
# resources/tests/credential-acquisition/31-pass-manifest.feature
@requirement:31 @applies_when:pass
Feature: The Wallet verifies the signature and the manifest hashes of a pass before storing it

  Scenario: A pass with a valid signature and manifest is stored
    Given the Holder opens the file "fixtures/passes/valid.pkpass" with the Wallet
    When the Holder approves adding the pass
    Then the Wallet lists the pass

  Scenario: A pass whose manifest does not match its content is rejected
    Given the Holder opens the file "fixtures/passes/tampered-manifest.pkpass" with the Wallet
    Then the Wallet does not store the pass
    And the Wallet tells the Holder the pass could not be verified
```

### Views by key

All the requirements whose condition names a key describe what follows from that option or extension, across every functionality. For the Wallet, the key of a credential type gathers how that type is acquired, checked, kept, moved and shown. The specification does not store this as a unit; the compiler renders it as one.

For every key, the compiler renders the requirements whose `applies_when` or `applies_when_all` names it, grouped by functionality, with the option or extension that defines the key at the top. For the Wallet's `pass` this is one page: how a pass is added, checked, updated, shown as voided, copied and displayed. It is generated, so it never disagrees with the functionality files, and it is informative: conformance is still assessed per requirement.

### Rules

1. A condition never appears in prose. A statement that begins "When the Wallet supports…" or "If…" is rewritten without the condition, and the condition goes into `applies_when` or `applies_when_all`.
2. A conditional requirement sits in the functionality it belongs to by meaning, which need not be the one where the option is chosen or the extension defined.
3. A requirement that applies to every solution, whatever it chose, carries no condition. The Wallet verifies the issuer's signature of every credential before storing it, whichever types it accepts.
4. `applies_when` may name an extension id, so that a requirement in a core functionality applies only to solutions that claim the extension (ARCH-ADR-7, rule 6). It may also do so in another extension, for requirements several extensions share. This keeps shared requirements in one place without an extension of their own that no product would claim.
5. A requirement in an extension functionality already applies only to solutions that claim the extension. Its condition does not name its own extension.
6. A condition names only keys and ids the solution can declare. It does not negate: "applies unless the solution declares `vc`" is written as `applies_when` naming the other options.

### In the overview and scope

* The *Description* of a functionality that holds conditional requirements says, in a sentence, which options or extensions they follow from.
* The scope needs no change: a conditional requirement is covered with its functionality.

### Conformance

* A conditional requirement whose condition holds is assessed like any requirement of its level.
* A conditional requirement whose condition does not hold is reported as **not applicable**, neither passed nor failed. A `RECOMMENDED` one is left out of the share GovMarket reports.
* A condition is evaluated against the conformance declaration, and against `PUBLISHED` options and extensions only. A `DRAFT` option that a solution declares does not make a requirement apply.
* A conditional variation point that does not apply is not applicable as a whole, and its options are not assessed.

### Mutability and extending specifications

*Mutability* in *Requirements* already lets an extending specification switch off an inherited `EXTENSIBLE` or `REPLACEABLE` requirement with `INAPPLICABLE`, recording why. Adding a condition switches the requirement off for some solutions only, so it is treated as a scoped `INAPPLICABLE`, on the same terms:

* An extending specification may add a condition to an inherited `EXTENSIBLE` or `REPLACEABLE` requirement. It cites the requirement, as for any extending requirement, and records why the requirement does not apply to solutions outside the condition: "requirement 31 applies in this profile only to wallets that declare `vc`, because passes here are signed under a national scheme specified in requirement 12".
* It may not add a condition to an `IMMUTABLE` requirement, just as it may not switch one off.
* It may remove a condition from an inherited `EXTENSIBLE` requirement, or narrow the keys a condition names. Either one binds more solutions, so it only tightens the base.
* A condition it adds names only keys and ids that the base specification or the extending specification declares, under rule 6.
* An extending specification that narrows a variation point to some of its options (ARCH-ADR-6) makes the requirements conditional on the other options not applicable to every solution under it. The compiler marks them so in the extending specification's views.

### Versioning

Changes are classified by the two promises of *Versioning and Change Documentation*: a solution that conformed keeps conforming (implementers), and what every conformant solution was guaranteed to do stays guaranteed (dependents).

| Change | Release | Why |
|---|---|---|
| Add a condition to a published `REQUIRED` requirement | MAJOR | Solutions that do not meet the condition lose an obligation that dependents relied on |
| Remove a condition from a published `REQUIRED` requirement | MAJOR | Solutions that did not meet the condition gain an obligation |
| Change the keys or ids in a condition of a published `REQUIRED` requirement | MAJOR | Some solutions gain an obligation, others lose one |
| Publish a new `REQUIRED` requirement with a condition | MAJOR | As for any new `REQUIRED` requirement: solutions that meet the condition gain an obligation |
| Any of the above on a `RECOMMENDED` requirement | MINOR | Conformance is unaffected |
| Any change to a requirement that is not yet published | PATCH | It takes no part in conformance |

### Compiler checks

* Every key in `applies_when` and `applies_when_all` is the key of an option or the id of a declared extension.
* A requirement in an extension functionality does not name its own extension in its condition.
* A condition added in an extending specification is on an inherited `EXTENSIBLE` or `REPLACEABLE` requirement, cites it, and records a reason.
* No cycles: a requirement is not conditional on itself or on an option of itself, directly or through nested conditions.
* Warn when a statement holds a condition in prose ("When the Wallet supports…", "If…") and has no condition field.
* Warn when an option has a key and nothing names it, in a condition or elsewhere.
* Warn when a condition names an option of a conditional variation point without the condition of that variation point, as the requirement then also depends on it.

### Consequences

* *Requirements* (`chapters/requirements.md`) gains a section *Conditional requirements* with the vocabulary, fields and rules above, and its *Common mistakes* adds conditions written in the statement. Its *Mutability* section notes that adding a condition in an extending specification is a scoped `INAPPLICABLE`.
* *Functionalities and Extensions* (`chapters/functionalities_and_extensions.md`) and ARCH-ADR-7 point to this ADR for `applies_when`, in rule 5 and rule 6 respectively. *Other mechanisms, and when to use them instead* adds requirements shared by several extensions, and *Warning signs* adds an extension no product would claim on its own.
* The requirement file template in *Structure of Specifications* and `bb-template`, and the publications compiler's requirements parser, gain `applies_when` and `applies_when_all`.
* The compiler renders the condition next to each conditional requirement, and a view for each key.
* *Versioning and Change Documentation* gains the table above under *Classifier changes*, and lists adding or removing a condition among the breaking changes.
* The test harness and GovMarket evaluate conditions against the conformance declaration. Tests carry the requirement id and the condition, so that the harness can report a requirement as not applicable.

### Confirmation

Confirmed by the Architecture Committee following Architecture Working Group discussion. Implemented in `chapters/requirements.md`, in the template in `chapters/structure_of_specifications.md` and `bb-template`, and in the publications compiler's requirements parser. The Wallet Building Block 2.0 is the first specification to use it (`bb-wallet/research/2.0-requirements-reorganization-proposal.md`).

## Pros and Cons of the Approaches

### Approach 1: No new mechanism

* Good, because nothing changes
* Bad, because conditions remain prose that no tool can read, and GovMarket cannot tell "not applicable" from "failed"
* Bad, because requirements that follow from a plain option have nowhere to go but `RECOMMENDED` or prose
* Bad, because shared requirements are either duplicated across extensions or put in an extension no product would claim

### Approach 2: Conditions on functionalities only

A functionality file may carry a condition in its front matter, and every requirement in it is conditional. Individual requirements carry none.

* Good, because there is one place to look for a condition, and one field
* Good, because it fits the shared presentation requirements well: a whole functionality of `verifiable-credentials` applies when a presentation extension is claimed
* Bad, because requirements that follow from an option must leave the functionality they belong to: the voided pass check moves out of *Identifying revoked credentials* into a "pass" file, and functionalities are split by credential type
* Bad, because one file per combination of conditions multiplies files as options are added

### Approach 3: `applies_when` and `applies_when_all`

* Good, because each requirement stays in the functionality it belongs to by meaning
* Good, because "not applicable" is distinct from "failed" in tests and in GovMarket
* Good, because the same fields serve options and extensions, and shared requirements are written once
* Good, because a functionality-level condition covers Approach 2's case without forcing it on every requirement
* Good, because the key of an option gathers what follows from it, and the compiler can show that as one view without the specification storing it twice
* Good, because it works with ARCH-ADR-7 alone, naming extension ids, and with ARCH-ADR-6 if accepted
* Neutral, because a solution must supply a conformance declaration, which ARCH-ADR-7 already requires
* Bad, because a reader of a requirement must check both the requirement and its file's front matter to see its full condition; the compiler renders the combined condition to help
* Bad, because conditions and conditional variation points can be misused to build complex dependency graphs. The compiler catches cycles but not poor design; Working Group review has to

### Approach 4: Boolean expressions

* Good, because any condition can be written, including negation and nesting
* Bad, because it is a small language the compiler, the harness, GovMarket and every reviewer must parse
* Bad, because negation lets a requirement depend on what a solution does not declare, which changes meaning when a new option is published in a minor release
* Bad, because the Wallet, the most varied specification so far, needs no more than "any of" and "all of"

## Questions for the Architecture Working Group

1. **`applies_when_all`.** The Wallet needs only "any of" so far. Is there a case for "all of", or should it be left out until a specification needs it?
2. **Functionality-level conditions.** Should `applies_when` be allowed in a functionality's front matter, or only on requirements, so that every condition is read in one place?
3. **Conditions on core requirements naming extensions.** ARCH-ADR-7, rule 6 allows a core requirement to apply only when an extension is claimed. Is that a requirement of the core or of the extension, for citation and for the extension's tests?
4. **Implied conditions.** A requirement conditional on `mdoc` also depends on `vc`, since `mdoc` is an option of a variation point that applies only to `vc` wallets. Should the compiler require the condition to name `vc` too, or infer it?
5. **Conditions added by extending specifications.** This ADR treats a condition added by an extending specification as a scoped `INAPPLICABLE`: allowed on `EXTENSIBLE` and `REPLACEABLE` requirements, with a recorded reason, and never on `IMMUTABLE` ones. Does the Architecture Working Group accept this, or should a partial switch-off need more than a full one, such as review by the Working Group that owns the base specification?

## More information

* `chapters/requirements.md`, *Common mistakes*, *Classifiers* and *Conditional requirements*
* `chapters/functionalities_and_extensions.md`, *Rules*, *Other mechanisms, and when to use them instead* and *Warning signs*
* `chapters/versioning_and_change_documentation.md`, *What the version promises* and *Classifier changes*
* ARCH-ADR-6 (proposed), *Model options*
* ARCH-ADR-7 (proposed), *Organise a specification's functionalities into a core and extensions*
* `bb-wallet/research/2.0-requirements-reorganization-proposal.md`, sections *Options* and *Credential types*
* ISO/IEC 14143-2:2011, 3.6 *optional requirement*, note b
