---
title: Separate a requirement's obligation level from its lifecycle status
status: proposed
date: 2026-10-12
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
# Separate a requirement's obligation level from its lifecycle status

## Context and Problem Statement

The *Requirement Level Classifiers* section of the Requirements Model
(`chapters/requirements_model.md`) gives every requirement
exactly one of four keywords:

| Keyword | What *Requirement Level Classifiers* says it means |
|---|---|
| `REQUIRED` | every conformant solution must satisfy it; failing one disqualifies the product |
| `RECOMMENDED` | improves quality but is optional; GovMarket reports the share a solution meets |
| `DRAFT` | a proposal in public view that still needs evidence or consensus; it reserves an identifier, and no compliance test fails if it is absent |
| `DEPRECATED` | a retired rule whose identifier persists for audit and is never reused |

The four keywords answer two different questions. `REQUIRED` and `RECOMMENDED` say **how strongly
a conformant solution is bound** by the requirement. `DRAFT` and `DEPRECATED` say **where the
requirement stands in its approval lifecycle**. The classifier mixes these two independent
dimensions in one enumeration:

| Dimension | Values | Canonical anchor |
|---|---|---|
| Obligation (conformance strength) | `REQUIRED`, `RECOMMENDED` | *exclusive* vs *optional requirement* (ISO/IEC 14143-2) |
| Lifecycle (approval status) | `DRAFT`, …, `DEPRECATED` | *established requirement* (IEEE 730-2014, 3.2) |

The analysis is recorded in `research/defining_a_requirement.md` §10.1, as open decision D8.

### A draft requirement has an intended obligation

A Working Group drafting a requirement already knows whether it intends the requirement to be
mandatory. The single enumeration cannot record that intent: a requirement is either `DRAFT` or
`REQUIRED`, never a draft of a required rule. On promotion its classifier changes from `DRAFT` to
`REQUIRED` or `RECOMMENDED`, so the act of approving it also looks like a change to what it
demands. Reviewers then cannot tell from the classifier alone whether approval altered the
obligation.

The same holds at the other end. A deprecated requirement loses the record of whether it was
mandatory, which is exactly what an audit of a past compliance claim needs.

### A draft that reserves an identifier burns it

The *Unique Identifier* section makes requirement numbers permanent: a new requirement takes the next free number, a
requirement is never removed, and a number is never reused. *Requirement Level Classifiers* says a `DRAFT` requirement
reserves an identifier. Together these mean every proposal that is floated and then abandoned
consumes a number for the life of the specification, and must be kept as a `DEPRECATED`
requirement that never applied. The record of retired rules fills with rules that were never in
force, and a procurement document can cite a number whose rule was never agreed.

A number is a promise that the rule it names will still resolve years later. That promise should
be made when a Working Group has agreed the rule, not when someone proposes it, and the record
should become permanent when the rule is released, which is when *Unique Identifier* itself says a number
"belongs to" its requirement. `DEPRECATED` should then mean only what it says: a rule that was once
in force and has been retired.

### The approval gate has no name

IEEE 730-2014 defines an *established requirement* as one the project has verified as well formed
and validated against stakeholder needs, and has accepted as the basis of development. That is the
gate a GovStack requirement passes on its way out of drafting. With `DRAFT` folded into the
obligation scale, the gate is not modelled as a transition of its own.

### Downstream artefacts have already diverged

The requirement file template has already split the dimensions, ahead of any decision:

```yaml
status: DRAFT # DRAFT, WITHDRAWN, CANDIDATE, ACCEPTED, PUBLISHED, DEPRECATED
level: RECOMMENDED # RECOMMENDED, REQUIRED
```

This appears in `chapters/structure_of_specifications.md`, `requirements/*.md`,
`bb-template/requirements/functionality-name.md`, and the `publications-compiler` requirements
parser, which defaults a missing `status` to `DRAFT` and a missing `level` to `RECOMMENDED`. The
Requirements Model still describes the single enumeration, and none of `WITHDRAWN`, `CANDIDATE`,
`ACCEPTED` or `PUBLISHED` is defined anywhere, so the normative text and the tooling disagree. The
parser records two open questions this ADR answers: what `WITHDRAWN` means for identifier checks,
and whether `DRAFT` is the right default status.

## Decision Drivers

* Approving a requirement should not appear to change what it demands
* A requirement's intended obligation should be visible while it is still under review, so
  implementers experimenting with it know what to expect
* An audit of a past compliance claim needs both the obligation and the status a requirement had
* A requirement number is permanent, so it should be issued only to a rule the Working Group has
  agreed, and kept as a retired rule only if it was ever in force
* GovMarket and compliance tooling read classifiers, not prose, so each classifier must carry one
  meaning
* The normative text and the template, parser and published requirement files must agree
* Requirements are not individually versioned (`chapters/specification_versioning.md`); whatever
  carries status must work within a single specification version

## Considered Options

* **Option 1:** Keep the single four-value Requirement Level classifier
* **Option 2:** Two classifiers: `level` for obligation, `status` for lifecycle, with numbers
  issued on acceptance
* **Option 3:** `level` only, with lifecycle carried by the specification's version and changelog

## Decision Outcome

*To be completed by the Architecture Committee.*

The work that produced this ADR recommends **Option 2**. It is the only option in which a draft
requirement can carry its intended obligation and approval leaves `level` untouched. It also stops
abandoned proposals, and accepted requirements abandoned before release, from filling the record
of retired rules, and it matches what the template and parser
already do, so adopting it brings the normative text into line with existing practice.

### Requirement status

Under Option 2 every requirement carries exactly one `status`. Two events divide the lifecycle.
**Acceptance** issues the requirement a number, so that no two pull requests take the same one.
**Publication** makes the record permanent: from the first release that contains a requirement,
*Unique Identifier* forbids removing, reusing or renumbering it. This follows that section's own
wording, which binds a number to its requirement "once a requirement has been released".

**Unnumbered statuses** hold no number. The `id` field is empty. The requirement can be discussed,
reviewed and experimented with, but it cannot be cited from outside the specification and it never
takes part in compliance.

**DRAFT -** a proposal still being written. Its statement, `level` and other classifiers may change
freely, and the Working Group has not yet judged it. This is the default when `status` is missing.

**CANDIDATE -** a proposal the authors consider complete and have submitted for the Working
Group's acceptance review. The review checks that the requirement is well formed (requirements
verification, ISO/IEC/IEEE 29148 4.1.23) and that it states what stakeholders need (requirements
validation, 29148 4.1.22). Implementations may experiment against a candidate, knowing its
`level` is the one the Working Group is being asked to approve. A candidate that needs rework
returns to `DRAFT`.

**Accepted but unreleased**

**ACCEPTED -** a requirement the Working Group has approved as an *established requirement*
(IEEE 730-2014, 3.2): verified as well formed and validated against stakeholder needs. On
acceptance it takes the next free number. It is merged to the default branch but is not yet in a
released version of the specification, so it does not yet take part in compliance. Between
acceptance and release the requirement may still be found wanting: a compliance test cannot be
derived from it, it turns out not to be atomic and must be split, it overlaps another accepted
requirement and the two are merged, the release review rejects it, or the base requirement it
extends changes. A requirement that must be abandoned, split, merged or rewritten in intent is
withdrawn, and its replacements are accepted under new numbers. A requirement held back for a
later release stays `ACCEPTED`.

**Ended before publication**

**WITHDRAWN -** a requirement abandoned before it was ever published: a draft its authors drop, a
candidate the review rejects, or an accepted requirement abandoned before release. It never took
part in compliance, so there is no obligation to record.

* A requirement withdrawn as a draft or candidate never held a number. It may be deleted from the
  requirement file or kept for the discussion it records, and it may be reopened as `DRAFT`.
* A requirement withdrawn after acceptance keeps its `id` and stays in the requirement file. Its
  number is skipped for good: *Unique Identifier* rule 1 counts it when finding the next free number, and it is
  never issued again. It is never reopened; if the rule is wanted again it starts as a new draft.

**Published and after**

**PUBLISHED -** an accepted requirement that appears in a released version of the specification.
Only published requirements take part in compliance: a `REQUIRED` one must be met, and a
`RECOMMENDED` one counts towards the share GovMarket reports. From here on every rule of *Unique
Identifier* applies.

**DEPRECATED -** a published requirement that no longer applies. It keeps its number, statement and
`level`, so an audit of a past compliance claim can see what was demanded. If the rule becomes
relevant again it is reinstated as `PUBLISHED` under its own number. Only a requirement that has
been published can be deprecated: `DEPRECATED` records a rule that was once in force.

### Transitions

| From | To | When |
|---|---|---|
| `DRAFT` | `CANDIDATE` | the authors submit it for acceptance review |
| `DRAFT` | `WITHDRAWN` | the authors abandon it |
| `CANDIDATE` | `DRAFT` | the review returns it for rework |
| `CANDIDATE` | `WITHDRAWN` | the review rejects it |
| `CANDIDATE` | `ACCEPTED` | the review accepts it; the next free number is issued |
| `WITHDRAWN` | `DRAFT` | an unnumbered proposal is reopened |
| `ACCEPTED` | `PUBLISHED` | a version of the specification containing it is released |
| `ACCEPTED` | `WITHDRAWN` | it is abandoned, split, merged or rewritten in intent before release; its number is skipped |
| `PUBLISHED` | `DEPRECATED` | it is retired |
| `DEPRECATED` | `PUBLISHED` | it is reinstated under its own number, in a released version |

No other transition is permitted. In particular, a numbered requirement never returns to `DRAFT` or
`CANDIDATE`, which would release its number, and a published requirement is never withdrawn, only
deprecated.

### Consequences

*To be completed once the option is chosen.* Under Option 2:

* *Requirement Level Classifiers* becomes obligation only, with `REQUIRED` and `RECOMMENDED`. A new section, *Requirement
  Status*, carries the definitions and transitions above
* The statement in *Requirement Level Classifiers* that a `DRAFT` requirement reserves an identifier is removed. A draft reserves
  nothing
* *Unique Identifier* is restated around the two events. Rule 1 issues the number at acceptance and counts
  numbers held by withdrawn requirements. Rules 2 to 4 apply from publication. Rule 2 ("never
  removed") also covers a withdrawn requirement that holds a number, since its entry is what keeps
  the number from being issued again
* A released specification may contain unnumbered requirements, so that implementations can
  experiment with them. They are marked as not normative and have no compliance test. An external
  document can cite only a numbered requirement, and should cite only a published one
* While unnumbered, a requirement is referred to by its file and its statement, or by a review
  link. Whether it needs a provisional key that does not look like a number is left to the
  Working Group process
* The compiler checks `id` against `status`. It fails a `DRAFT` or `CANDIDATE` requirement that has
  an `id`, and an `ACCEPTED`, `PUBLISHED` or `DEPRECATED` requirement that has none. A `WITHDRAWN`
  requirement may have either
* The compiler also checks transitions. Against the last released version it fails a published
  requirement that has become `WITHDRAWN` or disappeared. Against the default branch it fails a
  numbered requirement that has disappeared or returned to an unnumbered status, which is how an
  `ACCEPTED` requirement withdrawn before release keeps its number out of reuse
* The rule that any classifier change forces a version bump is restated per dimension. A change of
  `level`, or a transition into or out of `PUBLISHED`, changes what conformance demands and
  follows `requirements/use_semantic_versioning.md`. A change among unreleased statuses, including
  `ACCEPTED` to `WITHDRAWN`, does not
* Specifications written to the single enumeration are migrated. A numbered `DRAFT` requirement has
  already appeared in a release, so its number may have been cited: it is either accepted, keeping
  its number, or withdrawn, keeping its `id` so the number is skipped and a citation still
  resolves. Every migrated requirement receives an explicit `level`, chosen by the owning Working
  Group
* The acceptance review itself, including who may accept a candidate, is defined in the GovStack
  Process

### Confirmation

Confirmed by the Architecture Committee following Architecture Working Group discussion. The outcome
is implemented in the *Unique Identifier* and *Requirement Level Classifiers* sections of
`chapters/requirements_model.md`, in the requirement file
template in `chapters/structure_of_specifications.md` and `bb-template`, and in the
`publications-compiler` requirements parser.

## Pros and Cons of the Options

### Option 1: Keep the single four-value classifier

* Good, because no text, template or tooling definition changes
* Good, because one keyword per requirement is simple to read
* Bad, because a draft requirement cannot record whether it is intended to be mandatory
* Bad, because approval changes the classifier, so it looks like a change in obligation
* Bad, because a deprecated requirement loses the record of its former obligation
* Bad, because a draft reserves a permanent number, so every abandoned proposal is kept for the
  life of the specification as a deprecated rule that never applied
* Bad, because the template and parser already use two fields, so this option requires reverting
  them or leaving the normative text in disagreement with practice

### Option 2: Two classifiers, with numbers issued on acceptance

* Good, because each classifier answers one question, which is what tooling that reads classifiers
  needs
* Good, because a requirement's obligation is stable across its whole lifecycle, from draft to
  deprecation
* Good, because a number is issued only to a rule the Working Group has agreed, so every number
  that can be cited names an established requirement
* Good, because `DEPRECATED` records only rules that were once in force, so an audit never has to
  tell a retired obligation from one that was never released
* Good, because it gives the IEEE 730 *established requirement* gate a place in the model
* Good, because the template, parser and existing requirement files already carry both fields
* Neutral, because pre-published requirements have no stable external reference, which is
  intended but means review discussions must point at files or links
* Bad, because every requirement carries one more field, and specifications already written to
  the single enumeration need migrating

### Option 3: `level` only, lifecycle from specification version

`DRAFT` and `DEPRECATED` are removed from requirements altogether. A requirement is in force if it
appears in a published version of the specification; drafts live only in pre-release versions,
and retired requirements are recorded in the changelog.

* Good, because it is consistent with requirements not being individually versioned
* Good, because each requirement carries the fewest classifiers
* Bad, because a published specification can no longer contain a draft requirement that invites
  experimentation, which *Requirement Level Classifiers*
  values
* Bad, because a retired number is visible only in the changelog, not where the requirement stood,
  which weakens the never-reuse rule
* Bad, because it gives no point at which a number is issued, leaving *Unique Identifier* rule 1 to be
  read
  against unreleased work
* Bad, because the template, parser and existing requirement files already carry `status`, and
  would have to drop it

## More information

* `research/defining_a_requirement.md` §10.1 (orthogonality defect), §8 decisions D8 and D13
* `chapters/requirements_model.md`, sections *Unique Identifier* and *Requirement Level
  Classifiers*
* `research/requirement-id-scheme.md`, on how the next free number is allocated
* `chapters/crafting_well_formed_requirement_statements.md`, which maps the classifier to
  *priority* in ISO/IEC/IEEE 29148 and IEEE 1233, and `DRAFT` to the TBD problem in IEEE 830
  §4.3.3.1. Both mappings need updating once the classifier is split
* `publications-compiler/requirements/requirements_parser.md` req-8 and its open questions
* IEEE 730-2014, 3.2, *established requirement*; ISO/IEC/IEEE 29148:2018, 4.1.22 and 4.1.23;
  ISO/IEC 14143-2, *exclusive* and *optional requirement*
