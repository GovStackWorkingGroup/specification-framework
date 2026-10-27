---
title: Requirements
status: draft
references:
  - ISO/IEC/IEEE 29148:2018, Systems and software engineering — Life cycle processes — Requirements engineering
  - IEEE Std 1233-1998, IEEE Guide for Developing System Requirements Specifications
  - IEEE Std 830-1998, IEEE Recommended Practice for Software Requirements Specifications
---

{% hint style="info" %}
This chapter is a draft. *Requirement status* describes the model proposed in ARCH-ADR-5 (`ADR/ARCH-ADR-5.md`), which has not yet been decided by the Architecture Committee. The interaction classifier in *Interaction types* is new and has not been discussed by the Architecture Working Group.
{% endhint %}

The requirements are the normative content of a Building Block specification. A solution is assessed against them, and nothing else in the specification makes a solution conformant or not. This chapter says what a requirement is, how its statement is written, how it is identified, which classifiers it carries, and how to tell what kind of interaction a requirement asks of the system under test.

# What a requirement is

A requirement is a single, verifiable statement of something the system under test does or is. Its subject is always the system under test: the Building Block, as delimited in *System under test* in the overview (`chapters/introductory_chapters.md`). A statement whose subject is a person, an organisation or another system is not a requirement of this specification, because nobody can submit that subject for assessment.

Each requirement belongs to exactly one functionality, and lives in that functionality's file in `requirements/` (`chapters/structure_of_specifications.md`, *Requirement files*). Requirements every Building Block shares, such as transport security or structured logging, are cross-functional requirements. They live in `govstack:spec:cfr` and are inherited, not restated.

# Writing the statement

## Structure

A requirement states a **capability**: what the system under test does, without saying how. The capability may be qualified by a **condition**, a measurable attribute that only has meaning in relation to the capability, and bounded by a **constraint**, a limit imposed from outside, such as an existing interface, a law or a referenced standard (IEEE 1233, 6.1).

The statement follows the sentence pattern of ISO/IEC/IEEE 29148, 5.2.4:

> [Condition] [Subject] [Action] [Object] [Constraint of action]

*When a Verifier requests a presentation* [condition], *the Wallet* [subject] *shows the Holder* [action] *every requested attribute* [object] *before any attribute is sent* [constraint].

Only the subject and the action are always present. Conditions and constraints that do not fit in one sentence go into the `documentation` field of the requirement, labelled "Condition:" or "Constraint:", so that the limits of the requirement are explicit when it is assessed.

## Obligation

The obligation of a requirement is carried by its `level` classifier, not by the verb in its statement. GovMarket and the compliance tooling read the classifier. Write the statement in the plain present tense, "The Wallet keeps …", or with an RFC 2119 keyword, but never rely on the keyword alone to say whether the requirement is mandatory. Where a sentence in the `documentation` needs its own obligation, such as "a solution MAY additionally …", use the RFC 2119 keywords in capitals.

## Words to avoid

These break the requirement's meaning or make it impossible to verify (ISO/IEC/IEEE 29148, 5.2.7):

| Class | Examples | Instead |
|---|---|---|
| Superlatives and comparatives | best, most, better than | A measurable threshold |
| Subjective terms | user friendly, easy, efficient | The observable property that would make it so |
| Vague pronouns | it, this, that | Name the thing |
| Ambiguous combinations | and/or, almost always | Separate requirements, or a stated condition |
| Open-ended terms | provide support for, as a minimum, etc. | The complete list |
| Loopholes | if possible, as appropriate | A condition under which the requirement applies |
| Unqualified totality | all, always, never, every | Keep only if it is meant and testable |
| Incomplete references | "according to the standard" | The standard, its version and the clause |

## Checklist

A requirement is ready for review when it has every property in this table (ISO/IEC/IEEE 29148, 5.2.5; IEEE 830, 4.3).

| Property | Means | How a GovStack requirement shows it |
|---|---|---|
| Necessary | Removing it would leave a capability unfulfilled | It realises its functionality, and no other requirement covers it |
| Appropriate | Its detail matches the level of the Building Block | It names a contract, not a design or a technology |
| Unambiguous | It can be read in only one way | It uses the terms of `terminology.md` and none of the words above |
| Complete | Nothing outside it is needed to understand it | Conditions and constraints are in the statement or in its `documentation` |
| Singular | It states one capability, characteristic or constraint | One subject, one action, one object, one interaction type |
| Feasible | It can be met within known technical limits | The Working Group has seen it met, or can say how it would be |
| Verifiable | A finite, repeatable procedure can show it is met | Its `observability` is set, and a test or a list of evidence follows from it |
| Correct | It is something the system under test must do | It does not contradict an `IMMUTABLE` requirement it inherits |
| Conforming | It follows this chapter | Fields, classifiers and sentence pattern as described here |
| Traceable | Its origin and its uses can be followed | It has an `id`, belongs to a functionality, and names its contract when the contract is defined elsewhere |

The requirements of a specification are also checked as a set. Together with the cross-functional requirements they inherit, they leave no functionality without requirements, contain no two requirements that contradict each other, and use one term for one concept throughout (ISO/IEC/IEEE 29148, 5.2.6).

## Common mistakes

- **The wrong subject.** "A holder MUST be able to add credentials" cannot be assessed, because its subject is a person. Write "The Wallet accepts credentials from …"
- **Two obligations in one.** "The Wallet stores and encrypts credentials" is two requirements, which may have different levels and different tests
- **A goal instead of a requirement.** "The Wallet is secure" has no pass or fail. Either state what makes it so, or leave it to the cross-functional requirements
- **Restating an inherited requirement.** A cross-functional requirement is inherited through `extends`. Restate it only to tighten an `EXTENSIBLE` one, and cite it when you do
- **Naming a product.** "The Wallet uses OpenIAM" binds every solution to a vendor. Name the contract the product implements

# Unique identifier

Each requirement has an identification number, unique within its specification, in its `id` field. The number together with the identifier of the specification identifies the requirement wherever it is cited, for example `govstack:spec:bb:wallet#req-7`. Procurement documents and laws cite requirements this way, and the citation has to resolve to the same rule years later.

A requirement receives its number when it is accepted (see *Requirement status*). Working Groups follow these rules:

1. **A new requirement takes the next free number.** That is one higher than the highest number the specification has ever used, including deprecated and withdrawn requirements. Check the default branch of the repository, not only the last release, because another pull request may already have taken a number.
2. **A requirement is never removed.** A published requirement that no longer applies keeps its number and its text, and its status becomes `DEPRECATED`.
3. **A number is never reused.** If a deprecated rule becomes relevant again, it is reinstated under its own number.
4. **A requirement is never renumbered.** Moving a requirement to another file, or reordering requirements, keeps its number. Numbers do not have to follow the order of the text.
5. **The intent of an `IMMUTABLE` requirement does not change.** Its wording may be clarified.

The GovStack compiler checks rules 1 to 4 on every pull request and every release. A release that removes, reuses or renumbers a requirement fails to compile. For rule 5 the compiler reports every change of wording to an `IMMUTABLE` requirement, and the reviewers judge whether the intent changed.

Until the Architecture Working Group decides otherwise, a specification uses one sequence of numbers for all its requirements.

# Classifiers

Every requirement carries the following fields in the front matter of its functionality file.

| Field | Values | Default | Answers |
|---|---|---|---|
| `id` | A number | none until accepted | Which requirement, see *Unique identifier* |
| `status` | DRAFT, CANDIDATE, ACCEPTED, WITHDRAWN, PUBLISHED, DEPRECATED | DRAFT | Where it stands in its life cycle |
| `level` | REQUIRED, RECOMMENDED | RECOMMENDED | Whether a solution fails without it |
| `observability` | OBSERVABLE, AUDITABLE | AUDITABLE | Whether it is verified from outside the running system, or by inspecting evidence |
| `mutability` | IMMUTABLE, EXTENSIBLE, REPLACEABLE, INAPPLICABLE | REPLACEABLE | What an extending specification may do with it |
| `interaction` | SERVICE_API, CONSUMED_API, PROTOCOL, EVENT, USER_INTERFACE, DEVICE, FILE, INTERNAL | none | Where it is exercised: across which boundary, or at none |

Each classifier takes exactly one value at a time. A requirement without an explicit value takes the default, and writing the default explicitly is not a change. Every other change of a classifier requires a new version of the specification; `chapters/versioning_and_change_documentation.md`, *Classifier changes*, says which one.

## Requirement level

The level says how strongly a conformant solution is bound.

**REQUIRED** - every conformant solution meets the requirement exactly as written. A solution that fails a single published REQUIRED requirement is not GovStack compliant.

**RECOMMENDED** - the requirement improves a solution but is optional. GovMarket reports the share of published RECOMMENDED requirements a solution meets, as an additional signal for buyers.

## Requirement status

The status says where the requirement stands in its approval life cycle. It is independent of the level: a draft already records the level it is intended to have, and approving it does not change that level.

Two events divide the life cycle. **Acceptance** gives the requirement its number. **Publication**, the first release that contains it, makes the record permanent.

| Status | Numbered | Takes part in compliance | Meaning |
|---|---|---|---|
| `DRAFT` | No | No | A proposal still being written. Statement and classifiers may change freely |
| `CANDIDATE` | No | No | A proposal the authors consider complete, submitted to the Working Group for review. Implementations may experiment against it |
| `ACCEPTED` | Yes | No | Approved by the Working Group as well formed and as stating what stakeholders need. Merged, but not yet in a released version |
| `WITHDRAWN` | If it was accepted | No | Abandoned before publication. If it had a number, the number is skipped for good |
| `PUBLISHED` | Yes | Yes | In a released version of the specification |
| `DEPRECATED` | Yes | No | Published once, and no longer applies. It keeps its number, statement and level, so that an audit of a past compliance claim can see what was demanded |

| From | To | When |
|---|---|---|
| `DRAFT` | `CANDIDATE` | the authors submit it for review |
| `DRAFT` | `WITHDRAWN` | the authors abandon it |
| `CANDIDATE` | `DRAFT` | the review returns it for rework |
| `CANDIDATE` | `WITHDRAWN` | the review rejects it |
| `CANDIDATE` | `ACCEPTED` | the review accepts it; the next free number is issued |
| `WITHDRAWN` | `DRAFT` | an unnumbered proposal is reopened |
| `ACCEPTED` | `PUBLISHED` | a version of the specification containing it is released |
| `ACCEPTED` | `WITHDRAWN` | it is abandoned, split, merged or rewritten in intent before release |
| `PUBLISHED` | `DEPRECATED` | it is retired |
| `DEPRECATED` | `PUBLISHED` | it is reinstated under its own number, in a released version |

No other transition is permitted. A numbered requirement never returns to `DRAFT` or `CANDIDATE`, and a published requirement is never withdrawn, only deprecated. A released specification may contain unnumbered requirements so that implementations can experiment with them; they are marked as not normative and have no compliance test.

## Observability

The observability says how compliance with the requirement is confirmed. A Building Block is assessed as a black box wherever possible.

**OBSERVABLE** - compliance can be verified by interacting with the running system, without access to its source code, configuration or infrastructure: through API calls, protocol exchanges or behavioural tests. An observable requirement is verified in one of three ways, in order of preference:

- **Automatically testable.** A script or compliance tool checks it.
- **Manually testable.** A documented procedure with a clear pass or fail outcome, which anyone following the steps reproduces.
- **Operationally observable.** Evidence or behaviour observed at run time, where the outcome may not be binary or exactly reproducible.

**AUDITABLE** - compliance cannot be determined from external behaviour alone, and requires inspecting architecture documentation, source code, deployment configuration, access control policies or third-party audit reports. Each AUDITABLE requirement lists in its `documentation` the evidence a vendor produces, so that the assessment stays deterministic even when it is manual.

Prefer an observable formulation where one exists. "The Wallet keeps credentials across a restart" is observable; "The Wallet stores credentials in a database" is not, and it also names a design.

## Mutability

Every Building Block specification extends the cross-functional requirements, and may itself be extended by a national or sectoral specification. The mutability says what an extending specification may do with an inherited requirement.

**IMMUTABLE** - the requirement is frozen. Later versions may clarify its wording but not change its intent, scope, inputs or outputs. Integrations built against it keep working across all minor and patch releases.

**EXTENSIBLE** - an extending specification may add constraints or features without changing the original text. An extension may tighten the requirement, never broaden it, and the tests of the extension still confirm the base behaviour.

**REPLACEABLE** - an extending specification may replace how the requirement is met, as long as its external contract, its inputs, outputs and behaviour, stays the same. This allows a technology to be substituted where policy or the local ecosystem demands it.

**INAPPLICABLE** - used only in an extending specification. It switches off an inherited `EXTENSIBLE` or `REPLACEABLE` requirement for that specification, without altering the parent text, and records why.

An extending requirement cites the requirement it extends, replaces or switches off, for example `govstack:spec:bb:wallet#req-1 extends govstack:spec:cfr:data#req-3`.

# Interaction types

The interaction type says where a tester has to stand to see the requirement met. A requirement that asks the system under test to offer an endpoint is checked by calling it. One that asks it to call another system's API is checked by standing in for that system. One that concerns how the system keeps its data is checked at no boundary at all. Recording this in each requirement tells the reader which contract the requirement depends on, tells the assessor what the test set-up needs, and lets a specification be checked for interfaces that no requirement realises.

| Type | The system under test … | Contract usually lives in | A test needs |
|---|---|---|---|
| `SERVICE_API` | offers an API operation that others call | This specification, `resources/api.yml` | A client that calls the system under test |
| `CONSUMED_API` | calls an API operation of another Building Block or system | The counterpart's specification | A stand-in for the counterpart that records the calls |
| `PROTOCOL` | takes a role in a multi-party exchange defined for a domain | A Domain Reference Architecture or an external standard | A simulator of the other roles |
| `EVENT` | publishes or subscribes to a message | This specification, or the publishing Building Block's | A broker, or a stand-in publisher or subscriber |
| `USER_INTERFACE` | presents something to a person, or takes input from one | This specification, as far as it is specified at all | A documented manual procedure, or a scripted user |
| `DEVICE` | interacts with hardware, such as a card reader, a biometric sensor or a secure element | This specification, or an external standard | Reference hardware, or a device simulator |
| `FILE` | reads or writes a file or data package in a defined format | This specification, or an external standard for the format | Sample files, and a validator for the files produced |
| `INTERNAL` | has a characteristic that does not cross any boundary | This specification | Indirect observation, or evidence artefacts |

The first seven types are the interface types of *Interfaces* in the overview (`chapters/introductory_chapters.md`). A requirement of any type except `INTERNAL` realises one of the interfaces listed there.

## Deciding the type

Ask in order, and stop at the first yes:

1. Is the requirement an obligation of a role in an exchange defined by a Domain Reference Architecture or an external standard? Then `PROTOCOL`, even if the exchange is carried over API calls. The protocol is the contract, and the calls are part of it.
2. Does another party call the system under test? Then `SERVICE_API`.
3. Does the system under test call another party? Then `CONSUMED_API`.
4. Does the exchange happen through published messages rather than calls? Then `EVENT`.
5. Is the other party a person? Then `USER_INTERFACE`.
6. Is the other party a piece of hardware? Then `DEVICE`.
7. Does the requirement concern a file or data package that the system under test reads or produces? Then `FILE`.
8. Otherwise, `INTERNAL`.

Rules:

1. Every requirement has exactly one interaction type. A requirement that seems to need two usually holds two requirements: one for the exchange, and one for what the system under test does with its result.
2. A requirement of type `SERVICE_API` names its operation in `api_operation_id`, and that operation exists in `resources/api.yml`.
3. A requirement of type `CONSUMED_API`, `PROTOCOL`, `DEVICE` or `FILE` whose contract is defined elsewhere names that contract in `contract`: a publication identifier with the operation, role or section, or a document listed in `references.md`.
4. Every interface in the overview is realised by at least one requirement of the matching type, and every requirement that is not `INTERNAL` realises an interface listed in the overview.
5. Interfaces inherited from the cross-functional requirements, such as logging and monitoring, are not given an interaction type here. They belong to the requirements of `govstack:spec:cfr`.

Guidance:

- A file exchanged over a network, for example uploaded through an API operation, is two requirements: one `SERVICE_API` or `CONSUMED_API` for the transfer, and one `FILE` for the format. The format can change without the operation changing, and the reverse
- `INTERNAL` does not mean `AUDITABLE`. Persistence across a restart is internal, but a tester can see it by restarting the system and reading the credential back. Choose `observability` separately
- `USER_INTERFACE` requirements state what a person must be shown or asked, not how the screen looks. Layout, wording and accessibility of the screen belong to the cross-functional requirements or to the deployment
- `DEVICE` is for hardware the system under test interacts with. Hardware reached through an operating system service, such as a platform key store, is still `DEVICE` when the requirement depends on the hardware's properties, and `INTERNAL` when it does not
- When a protocol is realised by an extension functionality, its `PROTOCOL` requirements all sit in that extension (`chapters/functionalities_and_extensions.md`)

## Examples

Illustrative requirements for the Wallet Building Block, one per type.

| Type | Statement | `contract` or `api_operation_id` |
|---|---|---|
| `SERVICE_API` | The Wallet returns the list of credentials it holds, with their issuer and expiry date. | `api_operation_id: listCredentials` |
| `CONSUMED_API` | The Wallet checks the revocation status of a credential with the Trust Registry before presenting it. | `govstack:ra:credentials`, status lookup |
| `PROTOCOL` | The Wallet requests a credential from an Issuer in the pre-authorised code flow of OpenID4VCI. | OpenID4VCI, section 3.5 |
| `EVENT` | The Wallet publishes an event when a credential is deleted. | `resources/events.yml`, `credential.deleted` |
| `USER_INTERFACE` | The Wallet shows the Holder every attribute a Verifier requests before the Holder approves the presentation. | |
| `DEVICE` | The Wallet generates and keeps the private key of a credential in a hardware-backed key store, where the device has one. | |
| `FILE` | The Wallet exports the credentials it holds as an encrypted backup file in the format defined in *Data structures*. | |
| `INTERNAL` | The Wallet keeps every stored credential across a restart. | |

## How the type is used elsewhere

| Where | Use |
|---|---|
| *Interfaces* in the overview | Each interface is realised by requirements of its type. A row with none is missing its requirements |
| Compliance tests | The type tells the test author which harness the requirement needs: client, stand-in, simulator, reference hardware, sample files or evidence |
| Versioning | The rules in *Interface changes* (`chapters/versioning_and_change_documentation.md`) apply to `SERVICE_API` requirements. Changes to the contract of a `CONSUMED_API`, `PROTOCOL` or `FILE` requirement are judged by the same question: does an existing solution or counterpart break? |
| Extensions | A solution that claims only the core never needs a harness for the types used only in extensions |

# In the front matter

```yaml
requirements:
  - id: '4'
    status: ACCEPTED
    level: REQUIRED
    observability: OBSERVABLE
    mutability: IMMUTABLE
    interaction: CONSUMED_API
    contract: govstack:ra:credentials#status-lookup
    statement: The Wallet checks the revocation status of a credential with the Trust Registry before presenting it.
    documentation: |
      Condition: the credential carries a status reference.
      Constraint: a status that cannot be retrieved is treated as unknown, and the credential is not presented as valid.
    api_operation_id:
    test:
```

# Questions for the Architecture Working Group

1. Should `interaction` be required on every requirement, or only recommended until the existing specifications are migrated?
2. Some requirement files carry a `type` field with the values `FUNCTIONAL`, `WEBSERVICE` and `INTERFACE`. Should `interaction` replace it? `WEBSERVICE` would become `SERVICE_API`, `INTERFACE` would split into the other boundary types, and `FUNCTIONAL` would become `INTERNAL`.
3. Is `EVENT` needed as a type of its own, or is it a `SERVICE_API` or `CONSUMED_API` over a messaging channel?
4. Should `contract` be a free reference, or a publication identifier with a defined anchor syntax, like requirement ids?
5. Is an interface requirement a separate type of requirement, or a functional requirement with an interaction type? This chapter takes the second view.
6. Do the cross-functional requirements need interaction types of their own, for instance for logging or configuration files?
7. Do Service API and cross-functional requirements share the sequence of numbers of the functional requirements, or have their own, such as `#req-` and `#cfr-`?
