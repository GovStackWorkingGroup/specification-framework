---
title: Infer how a requirement is verified from its interaction type
status: proposed
date: 2026-11-23
decision-makers:
  - Architecture Committee
consulted:
  - Architecture Working Group
  - Document Management Working Group
  - GovStack Technical Committee
informed:
  - All Working Groups
  - GovMarket
---
<!-- markdownlint-disable-next-line MD025 -->
# Infer how a requirement is verified from its interaction type

## Context and Problem Statement

Every requirement of a Building Block specification carries two classifiers that describe how it is checked (`chapters/requirements.md`, *Classifiers*):

| Classifier | Values | Answers |
|---|---|---|
| `observability` | `OBSERVABLE`, `AUDITABLE` (default) | Whether it is verified from outside the running system, or by inspecting evidence |
| `interaction` | `SERVICE_API`, `CONSUMED_API`, `PROTOCOL`, `EVENT`, `USER_INTERFACE`, `DEVICE`, `FILE`, `INTERNAL` | Where a tester has to stand to see it met: across which boundary, or at none |

The two overlap. A requirement whose interaction crosses a boundary is checked at that boundary, by a client, a stand-in, a simulator, a scripted user, reference hardware or a validator: it is observable. A requirement whose interaction is `INTERNAL` "does not cross any boundary", and the chapter's test column for it says "Indirect observation, or evidence artefacts". So `interaction` already says almost everything `observability` says, in more detail, and authors set both by hand.

### What the overlap costs

* **Two fields that can disagree.** Nothing checks that a `SERVICE_API` requirement is `OBSERVABLE`, so a specification can say a requirement is checked by calling an API and also that it can only be checked by audit.
* **A default that hides missing work.** `observability` defaults to `AUDITABLE`, so a requirement nobody classified is treated as one that needs evidence. `interaction` has no default, and the compiler warns when it is missing (`requirements_parser#req-35`), because "a guessed one would mislead". The two fields treat the same missing decision in opposite ways.
* **More to write and review.** Each requirement carries one more classifier, and each review checks one more value that follows from another.

### What the Document Management draft shows

The Document Management draft (`bb-file-management`, v0.6) sets both fields on all of its 150 requirements:

| `interaction` | `OBSERVABLE` | `AUDITABLE` |
|---|---|---|
| `SERVICE_API` | 113 | 1 |
| `USER_INTERFACE` | 10 | |
| `FILE` | 10 | |
| `EVENT` | 5 | |
| `CONSUMED_API` | 1 | |
| `PROTOCOL` | 1 | |
| `INTERNAL` | 3 | 6 |

146 of the 150 follow one rule: `INTERNAL` is `AUDITABLE`, every other type is `OBSERVABLE`. The four exceptions are classification slips that the rule would correct:

| Requirement | Classified as | Where it is actually checked |
|---|---|---|
| Verifies the checksums of finalized Versions at a configured interval and reports every mismatch | `INTERNAL`, `OBSERVABLE` | At the report: the interface the mismatch is read from |
| Applies configured rules when a File enters a Container | `INTERNAL`, `OBSERVABLE` | At the Service API: upload a File, then read its metadata or location |
| Deletes Files automatically when their expiry period has passed | `INTERNAL`, `OBSERVABLE` | At the Service API: the File can no longer be retrieved |
| Returns the metadata of an item mapped to the Dublin Core elements | `SERVICE_API`, `AUDITABLE` | At the Service API: call the operation and compare the fields. Nothing about it needs evidence |

In each case the author knew where the requirement is checked, and recorded it in the field that did not match the other.

### Why the chapter says otherwise today

*Interaction types* currently advises: "`INTERNAL` does not mean `AUDITABLE`. Persistence across a restart is internal, but a tester can see it by restarting the system and reading the item back. Choose `observability` separately." That advice is what keeps the two fields apart. But the example it gives is checked at a boundary: the item is read back through the Service API, after a restart. Typed by where the tester stands, it is a `SERVICE_API` requirement, and the advice is no longer needed.

### Cross-functional requirements are different

The cross-functional requirements (`govstack:spec:cfr`) set `observability` on all 78 of their requirements and `interaction` on none. *Interaction types*, rule 5, says interfaces inherited from the cross-functional requirements "are not given an interaction type here". Removing `observability` there would leave those requirements with no record of how they are verified.

## Decision Drivers

* Each fact about a requirement is recorded once, so that two fields cannot disagree
* An author records where a requirement is checked, a decision they already make for `interaction`
* GovMarket and the compliance process still know, for every requirement, whether it is tested or audited
* An unclassified requirement is reported as unclassified, not given a guessed verification method
* The cross-functional requirements, which have no `interaction`, keep working
* Existing specifications stay valid, with warnings
* The vocabulary of `chapters/requirements.md` changes as little as possible

## Considered Options

* **Option 1:** Keep both classifiers, and add a compiler check that they agree
* **Option 2:** Remove `observability` from Building Block specifications, and infer it from `interaction`: `INTERNAL` is audited, every other type is observed
* **Option 3:** Remove `observability`, and split `INTERNAL` into an observable and an auditable value
* **Option 4:** Remove `observability` everywhere, with no inference

## Decision Outcome

*To be completed by the Architecture Committee.*

The author recommends **Option 2**. It records the verification method once, in the field authors already think hardest about, keeps the distinction GovMarket needs, and needs no new value. The cost is a sharper definition of `INTERNAL`, which the Document Management data shows authors already apply in 146 of 150 cases.

### Rule

In a Building Block specification, how a requirement is verified follows from its `interaction`:

| `interaction` | Verified | A test needs |
|---|---|---|
| `SERVICE_API`, `CONSUMED_API`, `PROTOCOL`, `EVENT`, `USER_INTERFACE`, `DEVICE`, `FILE` | By observation, at that boundary | As in *Interaction types* today |
| `INTERNAL` | By audit: inspecting the evidence a vendor produces | The evidence listed in the requirement's `documentation` |

The words *observable* and *auditable* stay in the chapter, as descriptions of these two groups. They are no longer values a requirement carries.

### `INTERNAL`, redefined

`INTERNAL` means that compliance cannot be seen at any boundary of the Building Block software, and is shown only by evidence: architecture documentation, source code, deployment configuration, access control policies or third-party audit reports.

A requirement about an internal characteristic whose effect can be seen at a boundary takes the type of that boundary. Persistence across a restart is checked by restarting and reading the item back through the Service API, so it is `SERVICE_API`, and names the operation it is read back with. Automatic deletion, rules applied on arrival and periodic checks that produce a report follow the same rule.

A requirement that involves hardware but can only be shown by evidence, such as keeping a key in a hardware-backed store when the device offers no attestation, is `INTERNAL`. It is `DEVICE` when a test exercises the hardware.

### Fields

* Building Block specifications drop `observability` from the requirement template, in `chapters/structure_of_specifications.md` and `bb-template`.
* `documentation` lists, for every `INTERNAL` requirement, the evidence a vendor produces. Today it does so for every `AUDITABLE` one.
* The cross-functional requirements keep `observability`, because they carry no `interaction`. If they later adopt `interaction`, this decision applies to them too.

### Compiler checks

* When a requirement has `interaction`, the compiler derives its verification method from it, and passes it to GovMarket and the compliance tooling as `observable` or `auditable`.
* When a requirement in a Building Block specification still has `observability`, warn that it is no longer read. When it disagrees with the derived method, name both, so that the Working Group corrects the `interaction`.
* When a requirement has no `interaction`, apply no default and warn, as `requirements_parser#req-35` does today. It is reported to GovMarket as *unclassified*, not as auditable.
* `requirements_parser#req-14` becomes: warn if `interaction` is not `INTERNAL` and `test` is empty.
* Add: warn if `interaction` is `INTERNAL` and `documentation` lists no evidence.
* In a specification whose requirements carry no `interaction`, such as `govstack:spec:cfr`, read `observability` as today.

### Versioning

The *Classifier changes* table in `chapters/versioning_and_change_documentation.md` loses its `observability` row, and its `interaction` row splits in two:

| Classifier | Change | Release |
|---|---|---|
| `interaction` | between `INTERNAL` and any other type, statement unchanged | MINOR: the verification method changes, and GovMarket re-verifies the requirement for software solutions already listed |
| `interaction` | between two types other than `INTERNAL`, statement unchanged | PATCH |

Removing `observability` from a specification changes no requirement's demand, and on its own is a **patch** change. Correcting an `interaction` that the derived method shows to be wrong is classified by the table above.

### Consequences

* *Requirements* (`chapters/requirements.md`):
  * drops `observability` from the *Classifiers* table;
  * turns *Observability* into a short section on how the verification method follows from `interaction`;
  * replaces the advice "`INTERNAL` does not mean `AUDITABLE`" with the redefinition above;
  * updates the *Verifiable* row of the quality checks to "Its `interaction` is set, and a test or a list of evidence follows from it";
  * drops `observability` from the front-matter example.
* *Structure of Specifications* drops `observability` from the functionality file template, and says that `documentation` lists the evidence for `INTERNAL` requirements.
* *Versioning and Change Documentation* updates its *Classifier changes* table as above, and question 4 at its end becomes a question about `interaction` changes to and from `INTERNAL`.
* `bb-template` drops `observability` from `functionality-name.md`.
* The `publications-compiler` requirements parser and requirement id checker implement the checks above.
* Specifications that already set `observability` stay valid, with a warning per requirement. The Document Management draft corrects the `interaction` of the four requirements listed in the context.
* The cross-functional requirements are unchanged.
* The Specification Framework's own requirement files keep `observability` until they adopt `interaction`, like the cross-functional requirements.

### Confirmation

Confirmed by the Architecture Committee following Architecture Working Group discussion. Implemented in `chapters/requirements.md`, `chapters/structure_of_specifications.md`, `chapters/versioning_and_change_documentation.md`, `bb-template`, and the `publications-compiler` requirements parser and requirement id checker. The Document Management Building Block is the first specification to use it.

## Pros and Cons of the Options

### Option 1: Keep both, and check that they agree

* Good, because nothing is removed, and no definition changes
* Good, because a check would have caught the four mismatches in the Document Management draft
* Bad, because if the check is strict, `observability` is fully determined by `interaction` and carries no information of its own
* Bad, because if the check allows exceptions, there is no rule for which exceptions are legitimate, and the two fields drift
* Bad, because authors still write and review a field that follows from another

### Option 2: Remove `observability` from Building Block specifications, and infer it from `interaction`

* Good, because the verification method is recorded once, where the author decides where a requirement is checked
* Good, because GovMarket and the compliance process still receive *observable* or *auditable* for every classified requirement
* Good, because an unclassified requirement is reported as such, instead of defaulting to `AUDITABLE`
* Good, because it matches how authors already classify: 146 of 150 Document Management requirements
* Good, because the cross-functional requirements keep working unchanged
* Neutral, because Building Block specifications and the cross-functional requirements describe verification differently until the latter adopt `interaction`
* Bad, because `INTERNAL` must be redefined, and requirements typed `INTERNAL` under the old advice must be reviewed
* Bad, because a requirement whose effect is checked at an interface is typed by that interface even when its subject is an internal characteristic, which can read oddly at first ("persistence across a restart" as `SERVICE_API`)

### Option 3: Remove `observability`, and split `INTERNAL`

* Good, because an internal characteristic seen at a boundary keeps the type `INTERNAL`, and its verification method is still recorded
* Bad, because it moves `observability` into `interaction` under another name, so one field again answers two questions
* Bad, because the observable half of `INTERNAL` has no boundary, so no test set-up follows from it, which is what `interaction` exists to say
* Bad, because every tool that reads `interaction` learns a new value

### Option 4: Remove `observability` everywhere, with no inference

* Good, because it is the simplest change to the templates
* Bad, because GovMarket and the compliance process lose whether a requirement is tested or audited
* Bad, because the cross-functional requirements lose the only record of how their requirements are verified

## More information

* `chapters/requirements.md`, *Classifiers*, *Observability* and *Interaction types*
* `chapters/structure_of_specifications.md`, *Functionality files*
* `chapters/versioning_and_change_documentation.md`, *Classifier changes* and question 4
* `publications-compiler/requirements/requirements_parser.md`, req-8, req-14 and req-35
* `publications-compiler/requirements/requirement_id_checker.md`, req-11
* `bb-file-management`, v0.6, the requirement files
* `cross-functional-requirements/requirements/`
* ARCH-ADR-5, *Separate a requirement's obligation level from its lifecycle status*
