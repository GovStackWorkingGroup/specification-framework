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

A software solution is assessed against the requirements of GovStack specifications, and against nothing else (`chapters/requirements.md`). This chapter answers three questions in turn: what a software solution can claim, how strongly each part of the claim is evidenced, and how GovStack's marketplace presents the result to buyers.

| Term | Describes | Values |
|---|---|---|
| Functional conformance class | Which requirements a software solution meets | Compatible Software, Building Block Software |
| API conformance class | How far a software solution's API follows GovStack's API rules, and maps to a Building Block's contract | API Ruleset conformance, API operations conformance |
| Assurance level | How strongly a single result is evidenced | 0 to 3 |
| Specification maturity | How far a specification is developed | See *Specification Maturity* (`chapters/specification_maturity.md`) |

A software solution claims one functional conformance class, and may claim one API conformance class in addition. Both are claims about a set of results, and the chapter describes them first. The assurance level of each result then says how well the claim is backed, and the maturity of the specification limits which assurance levels are possible.

# Functional conformance class

A functional conformance class is a named set of requirements that a software solution claims to meet as a whole. The term follows the OGC Specification Model, on which the core and extensions of `chapters/functionalities_and_extensions.md` are also based. There are two classes, and the second builds on the first. Every software solution listed on GovStack's marketplace holds one of them.

| Class | Claimed by | The software solution meets |
|---|---|---|
| **Compatible Software** | A software solution that is to work in a GovStack stack without being an instance of a Building Block, such as a sector application that uses Building Blocks | Every `REQUIRED` published requirement of the cross-functional requirements, `govstack:spec:cfr` |
| **Building Block Software** | A software solution that claims to be an instance of a Building Block, in a named version | The Compatible Software class, and every `REQUIRED` published requirement of the Building Block's core and of each extension the software solution claims |

Rules:

1. Only `PUBLISHED` requirements take part (ARCH-ADR-5). Requirements with another status are not assessed.
2. Every requirement of the class is met at assurance level 1 or higher (see *Assurance levels*). Higher levels are optional, and GovStack's marketplace shows them.
3. A software solution that does not meet one `REQUIRED` requirement of a class does not hold that class.
4. The share of `RECOMMENDED` requirements met is reported, and does not decide the class.
5. A software solution of the Building Block Software class declares the extensions it claims. For each claimed extension, and each extension that one requires, it meets every `REQUIRED` requirement.
6. The requirements of an extension that is not claimed are not applicable: neither passed nor failed, and left out of the shares reported.
7. A cross-functional requirement whose condition does not hold for a software solution, such as an API rule where the software solution offers no API, is not applicable in the same way. The condition is part of the requirement's statement or `documentation` (`chapters/requirements.md`, *Structure*).
8. A software solution may hold the Building Block Software class for several Building Blocks. It holds the Compatible Software class once.

# API conformance class

The functional conformance class says what a software solution does. The API conformance class says how far integrators and AI agents can rely on its API. An API conformance class is a named set of API checks that a software solution claims to pass as a whole. There are two classes, and the second builds on the first. Unlike a functional conformance class, an API conformance class is optional: it is not a condition for being listed on GovStack's marketplace, which shows it next to the functional conformance class.

| Class | Claimed by | The software solution meets |
|---|---|---|
| **API Ruleset conformance** | A software solution of either functional conformance class that offers an API | Every `REQUIRED` rule of the API Ruleset of the cross-functional requirements. Every one of these rules that the lint ruleset checks is met at assurance level 2 |
| **API operations conformance** | A software solution of the Building Block Software class, when the Building Block's specification has its own contract at maturity 2 or higher | The API Ruleset conformance class, and a mapping of every operation of the contract required for its claim, checked at assurance level 2 |

Rules:

1. Both classes are checked on the OpenAPI document that a software solution publishes. The API Ruleset requires every software solution that offers an API to publish one.
2. The API Ruleset is one set of rules for Building Block contracts and for the APIs of software solutions. A contract at specification maturity 2 passes the same lint ruleset that checks a software solution.
3. At assurance level 2, both classes are checked by linting: a static analysis of the published OpenAPI document. Linting checks the interface as described, not the behaviour of the running software solution. The behaviour is the subject of the requirements and their test scenarios.

## API Ruleset conformance

The API Ruleset sets the common rules for APIs: naming, versioning, data formats, errors, pagination and security. Each rule is a cross-functional requirement with its own `level`. A software solution that holds a functional conformance class therefore already meets every `REQUIRED` rule, at least by self-declaration. API Ruleset conformance adds stronger evidence: every `REQUIRED` rule that the lint ruleset checks is met at assurance level 2. A rule that the lint cannot check, such as hiding implementation details, stays at the assurance level it has.

This class shows that a software solution's API follows the common conventions, so that an integrator or an AI agent that knows one GovStack API can call this one without learning new conventions.

## API operations conformance

A software solution keeps its own paths and operation identifiers. It marks in its OpenAPI document which of its operations implements which operation of the Building Block's contract, with the extension field `x-govstack-operation` defined in the API Ruleset:

```yaml
paths:
  /v2/wallet/items:
    get:
      operationId: getItems
      x-govstack-operation: listCredentials
```

The lint check compares these marks with the contract in `resources/api.yml`:

- Every operation required for the claim appears at least once. An operation is required when a `REQUIRED` requirement of the core, or of a claimed extension, names it in `api_operation_id`.
- Every mark names an operation that exists in the contract. Typing errors and invented operations are reported.
- The share of the other operations of the contract that are mapped is reported.

The mapping shows broadly which of the software solution's operations serves the purpose of each operation of the contract, and that none required is missing. It does not show that requests and responses match the contract, nor that an operation behaves as the contract describes. On the strength of the mapping alone, an integrator still needs an adapter to reach functional interoperability. Evidence that an operation takes the requests and gives the responses of the contract, and behaves as required, comes from the `SERVICE_API` requirements that name it, assessed as in *Assurance levels*.

The marks make that assessment easier: a test scenario can be run against the software solution's own endpoint, and an AI agent can find the same operation in every conformant software solution.

A specification that reaches maturity 2 by referencing a domain standard has no contract of its own to map against. Its software solutions cannot hold API operations conformance; the standard's own conformance suite applies instead.

# Assurance levels

Every result carries one assurance level: the result for a requirement, for an API rule, or for the operation mapping. The levels test the same thing with stronger evidence, so a result has exactly one level, and a higher level includes everything a lower one shows.

| Assurance | Procedure | Evidence |
|---|---|---|
| 0 Not assessed | — | None |
| 1 Self-declared | The vendor marks the requirement as met or not met, and attaches proof: a screenshot or log file for an `OBSERVABLE` requirement, the evidence listed in the requirement for an `AUDITABLE` one | A supplier's declaration (ISO/IEC 17050-1), checked for completeness by the GovStack team |
| 2 Manually tested | The vendor receives the test scenarios of the requirement with their steps and input data, runs them on its own test instance, and enters the results. The testing application compares them with the expected results. The GovStack team repeats a sample of the scenarios | Reproducible results against a published procedure |
| 3 Automatically tested | The testing application runs the test against the vendor's test instance, or, for the API conformance classes, lints the vendor's published OpenAPI document. It attaches the log to the compliance report | Results produced and recorded by GovStack |

What each level needs:

| Assurance | Possible for | Needs from the specification |
|---|---|---|
| 1 Self-declared | Every requirement | Specification maturity 1 |
| 2 Manually tested | A requirement with at least one test scenario | The requirement's scenarios in `resources/tests/` |
| 3 Automatically tested | A requirement whose outcome a script can check | The requirement's scenarios; for a `SERVICE_API` requirement also the contract of specification maturity 2. For an API rule, a check in the lint ruleset; for the operation mapping, the contract |

A result can reach a higher assurance level as soon as its requirement has what that level needs, even before the specification reaches maturity 4. Where nothing more exists, the result stays at self-declaration. As the Working Groups add scenarios and contracts, more requirements can be tested at higher levels, without any change to what the requirements demand.

The interaction type of a requirement tells the test author which harness an automated test needs: a client, a stand-in for a counterpart, a simulator, reference hardware or sample files (`chapters/requirements.md`, *Interaction types*). A requirement of type `USER_INTERFACE` is usually tested manually.

# GovStack's marketplace

GovStack's marketplace lists software solutions by functional conformance class:

- **Compatible Software:** software solutions that hold the Compatible Software class and no Building Block Software class.
- **Building Block Software:** software solutions that hold the Building Block Software class, listed under each Building Block and version, with the extensions they claim.

The claimed extensions are shown as facts about the software solution, not as a score: "Wallet 2.0: core, Issuance, Presentation". A buyer can filter on them, and cite them in a procurement.

Each listed software solution shows three markers.

| Marker | Shows | Example |
|---|---|---|
| Requirements met | All `REQUIRED` requirements of the class met, and the share of `RECOMMENDED` ones | REQUIRED: all · RECOMMENDED: 64 % |
| Requirement assurance | The share of `REQUIRED` results tested manually or automatically. The count per assurance level is on the detail page | 83 % of REQUIRED results tested |
| API conformance class | The class held, if any; otherwise the share of linted `REQUIRED` API rules met at assurance level 2 | API operations conformance |

Each Building Block list also shows the maturity of the specification, so that a buyer can tell what conformance means for that Building Block. Below maturity 2 it means that a software solution meets the requirements in its own API design. From maturity 2, its required operations must also take the requests and give the responses of a common contract, under the software solution's own paths; the requirement assurance marker shows how well that is evidenced.
