---
title: Conformance
status: draft
authors:
  - name: Nico Lueck
references:
  - ISO/IEC 17000:2020, Conformity assessment — Vocabulary and general principles
  - ISO/IEC 17050-1:2010, Conformity assessment — Supplier's declaration of conformity — Part 1 General requirements
  - OGC 08-131r3, The Specification Model — A Standard for Modular Specifications, Open Geospatial Consortium, 2009
---

A software solution is assessed against the requirements of GovStack specifications, and against nothing else (`chapters/requirements.md`). This chapter says which sets of requirements a software solution can claim, how strongly each result is evidenced, what the API checks add, and how GovStack's marketplace presents the outcome to buyers.

| Term | Describes | Values |
|---|---|---|
| Conformance class | The set of requirements a software solution claims to meet | Integration Software, Building Block Software |
| Assurance level | How strongly a result is evidenced | 0 to 3 for requirements, 0 to 2 for API checks |
| Maturity | How far a specification is developed | See *Specification Maturity* (`chapters/specification_maturity.md`) |

A conformance class is what a software solution claims. An assurance level is how well the claim is backed. Maturity is a property of the specification, and it limits which assurance levels are possible.

# Conformance classes

A conformance class is a named set of requirements that a software solution claims as a whole. The term follows the OGC Specification Model, on which the core and extensions of `chapters/functionalities_and_extensions.md` are also based. There are two classes, and the second builds on the first.

| Class | Claimed by | The software solution meets |
|---|---|---|
| **Integration Software** | Any software solution that is to work in a GovStack stack, whether or not it is an instance of a Building Block | Every `REQUIRED` published requirement of the cross-functional requirements, `govstack:spec:cfr` |
| **Building Block Software** | A software solution that claims to be an instance of a Building Block, in a named version | The Integration Software class, and every `REQUIRED` published requirement of the Building Block's core and of each extension the software solution claims, including cross-functional requirements as the Building Block tightens them |

Rules:

1. Only `PUBLISHED` requirements take part (ARCH-ADR-5). `DRAFT` and `CANDIDATE` requirements of a release are not assessed.
2. A software solution that fails one `REQUIRED` requirement of a class does not hold that class.
3. The share of `RECOMMENDED` requirements met is reported, and does not decide the class.
4. Requirements of an extension the software solution does not claim are not applicable: neither passed nor failed, and left out of the shares reported (`chapters/functionalities_and_extensions.md`, *Conformance*).
5. A software solution may hold the Building Block Software class for several Building Blocks. It holds the Integration Software class once.

# Assurance of requirement results

Every requirement result carries one assurance level. The levels test the same thing with stronger evidence, so a result has exactly one level, and a higher level includes everything a lower one shows.

| Assurance | Procedure | Evidence |
|---|---|---|
| 0 Not assessed | — | None |
| 1 Self-declared | The vendor marks the requirement as met or not met, and attaches proof: a screenshot or log file for an `OBSERVABLE` requirement, the evidence listed in the requirement for an `AUDITABLE` one | A supplier's declaration (ISO/IEC 17050-1), checked for completeness by the GovStack team |
| 2 Manually tested | The vendor receives the steps and input data of the test scenarios, runs them on its own test instance, and enters the results. The testing application compares them with the expected results | Reproducible results against a published procedure |
| 3 Automatically tested | The vendor provides the endpoint of its test instance. The testing application runs the test scenarios and attaches the log to the compliance report | Results produced and recorded by GovStack |

Self-declaration is the default, and the only level available for every requirement. Higher levels are optional for a vendor, and possible only where the specification provides what they need:

| Assurance | Possible for | Needs from the specification |
|---|---|---|
| 1 Self-declared | Every requirement | Requirement maturity 1; for an `AUDITABLE` requirement, the evidence list of requirement maturity 2 |
| 2 Manually tested | `OBSERVABLE` requirements | Requirement maturity 3: test scenarios |
| 3 Automatically tested | `OBSERVABLE` requirements that a script can check | Requirement maturity 3; for a `SERVICE_API` requirement also API maturity 3, whose examples serve as fixtures |

The interaction type of a requirement tells the test author which harness an automated test needs: a client, a stand-in for a counterpart, a simulator, reference hardware or sample files (`chapters/requirements.md`, *Interaction types*). A requirement of type `USER_INTERFACE` is usually tested manually.

When a test scenario does not exist, or cannot be run, the requirement falls back to self-declaration. As the Working Groups add scenarios, more requirements can be tested at higher levels, without any change to what the requirements demand.

# API checks

Two checks examine the OpenAPI document that a software solution publishes. They test different things, and each is reported on its own.

| Check | Tests | Against | Applies to |
|---|---|---|---|
| **API style** | Whether the API follows the API design rules | The lint ruleset of `govstack:spec:cfr` | Both conformance classes |
| **Operation coverage** | Whether the software solution offers an operation for every operation of the Building Block | The operation index of the Building Block specification | The Building Block Software class, when the specification has API maturity 1 or higher |

Both checks need the software solution to publish an OpenAPI document. The cross-functional requirements make this an obligation of every software solution that offers an API.

## API style

Each rule of the lint ruleset carries the `level` of the cross-functional requirement it checks. A rule for a `REQUIRED` requirement reports an error, and a rule for a `RECOMMENDED` requirement reports a warning. The check passes when there is no error. The share of warning rules met is reported, as for `RECOMMENDED` requirements.

## Operation coverage

A software solution keeps its own API design: its own paths, field names and versioning. It marks in its OpenAPI document which of its operations implements which operation of the Building Block:

```yaml
paths:
  /v2/wallet/items:
    get:
      operationId: getItems
      x-govstack-operation: listCredentials
      x-govstack-effect: READ
```

The check compares these marks with the operation index of the specification (`chapters/operation_cards.md`, *The operation index*):

- Every `REQUIRED` operation appears at least once. The check fails if one is missing.
- Every mark names an operation that exists. Typing errors and invented operations are reported.
- The effect of each mark matches the effect in the index. An operation that changes something cannot be marked `READ`.
- The share of `RECOMMENDED` operations covered is reported.

The check proves that the vendor has declared, publicly and precisely, which of its operations fulfils which operation of the Building Block, and that none is missing. It does not prove that the operation behaves as its card describes. That is the work of the `SERVICE_API` requirements that name the operation, assessed as in *Assurance of requirement results*. The marks make that assessment easier: test instructions can name the software solution's own endpoint, and an agent can find the same operation in every conformant software solution without configuration for each one.

## Assurance of API checks

| Assurance | Procedure |
|---|---|
| 0 Not assessed | — |
| 1 Self-declared | The vendor declares that its API follows the design rules, or that it covers the operations |
| 2 Automatically linted | The testing application runs the check on the software solution's published OpenAPI document |

Each check carries its own assurance level. The API checks are reported next to the conformance class. They do not decide it.

# GovStack's marketplace

GovStack's marketplace lists software solutions by conformance class:

- **Integration Software:** software solutions that hold the Integration Software class and no Building Block Software class.
- **Building Block Software:** software solutions that hold the Building Block Software class, listed under each Building Block and version, with the extensions they claim.

Each listed software solution shows four markers.

| Marker | Shows | Example |
|---|---|---|
| Requirements met | All `REQUIRED` requirements of the class met, and the share of `RECOMMENDED` ones | REQUIRED: all · RECOMMENDED: 64 % · Extensions: Issuance |
| Requirement assurance | How many results stand at each assurance level | 18 automatically tested · 25 manually tested · 9 self-declared |
| API checks met | The result of each API check | API style: passed, 37 of 40 recommended rules · Operation coverage: 12 of 12 required, 3 of 5 recommended |
| API check assurance | The assurance level of each API check | API style: automatically linted · Operation coverage: automatically linted |

Each Building Block list also shows the maturity of the specification, so that a buyer can tell what conformance means for that Building Block. At API maturity 1 it means that the software solution follows the API style and behaviour, and covers the operations. From API maturity 2 it means that the software solution implements a common contract.
