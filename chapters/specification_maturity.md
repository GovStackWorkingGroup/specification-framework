---
title: Specification Maturity
status: draft
authors:
  - name: Nico Lueck
references:
  - ISO/IEC/IEEE 29148:2018, Systems and software engineering — Life cycle processes — Requirements engineering
  - OpenAPI Specification 3.1.0, OpenAPI Initiative
  - Gherkin Reference, Cucumber <https://cucumber.io/docs/gherkin/reference/>
---

A Building Block specification is written by volunteers who know their domain, and not every Working Group can specify everything at once. This chapter lets a specification grow in steps. Specification maturity says how far a specification is developed, on one scale from 0 to 4. Each level adds artefacts to the levels below it, so nothing is written twice.

Maturity describes the **specification**, not a software solution. What a software solution claims is its *functional conformance class* and, optionally, its *API conformance class*. How strongly each result is evidenced is its *assurance level*. All three are described in *Conformance* (`chapters/conformance.md`).

Always write "specification maturity 2", or "maturity 2" where the context is clear. On its own, "level" means the `level` classifier of a requirement, `REQUIRED` or `RECOMMENDED` (`chapters/requirements.md`).

# The levels

| Maturity | Adds | Lives in |
|---|---|---|
| 0 None | No requirement is defined, or the requirements carry no classifiers | — |
| 1 Statements with classifiers | Every published requirement has a statement that passes the checklist of *Writing the statement*, and sets `level`, `observability`, `mutability` and `interaction` explicitly. A requirement whose contract is defined elsewhere names it in `contract`; an `AUDITABLE` requirement lists the evidence a vendor produces in its `documentation` | `requirements/` |
| 2 OpenAPI contract | An OpenAPI contract for the Service API, or a reference to a well-established domain standard that defines it. Every `SERVICE_API` requirement names its operation in `api_operation_id` | `resources/api.yml` |
| 3 API examples and mock | Examples for every outcome of every operation, and a mock that answers with them | `resources/api.yml`, `resources/mock/` |
| 4 Test scenarios | Every published `REQUIRED` `OBSERVABLE` requirement has at least one test scenario, referenced in its `test` field | `resources/tests/` |

Rules:

1. The maturity of a specification is the highest level whose conditions it meets together with those of every level below.
2. A specification with no `SERVICE_API` requirement has nothing to add at levels 2 and 3. It meets them as soon as it meets level 1, and moves from level 1 to level 4 by writing test scenarios.
3. Maturity is declared for the specification as a whole, not per functionality or per requirement.
4. Maturity does not limit the testing of a single requirement. A requirement that has a test scenario can be tested before the specification reaches level 4 (`chapters/conformance.md`, *Assurance levels*).
5. Writing a classifier's default value explicitly does not change the requirement (`chapters/requirements.md`, *Classifiers*).

At maturity 1 there is no common API. A conformant software solution meets the requirements in its own API design, and is not drop-in replaceable. From maturity 2, conformant software solutions take the requests and give the responses of a common contract.

# Maturity 1: Statements with classifiers

Maturity 1 is the baseline. Every specification reaches it before it is published.

The checklist in *Writing the statement* (`chapters/requirements.md`) already asks for `observability` before a requirement is ready for review, so a new specification starts at maturity 1. A specification written before the classifiers existed stays at maturity 0 until its requirements carry them.

At maturity 1, a `SERVICE_API` requirement states what the Building Block software offers for others to call, without naming an operation. Its `api_operation_id` stays empty until the contract exists.

# Maturity 2: OpenAPI contract

The contract in `resources/api.yml` is one OpenAPI document for the whole Service API of the specification. It follows the API Ruleset of the cross-functional requirements (`govstack:spec:cfr`), and passes the lint ruleset that comes with it.

The API Ruleset sets the minimum elements of a contract. They include:

- the version of the OpenAPI Specification;
- an `operationId` for every operation;
- a summary and a description in plain language for every operation, written so that a domain expert can check them against the requirements;
- which fields are required and which are optional, and the format of each field;
- the error responses, in the format the API Ruleset prescribes;
- at least one example response for every operation.

Every `SERVICE_API` requirement names its operation in `api_operation_id`, and that operation exists in `resources/api.yml`. The link is made only in the requirement; the compiler shows it in both directions when the specification is rendered.

The contract binds the operations and their requests and responses. A software solution may offer them under its own paths and operation identifiers, and maps each to the contract with `x-govstack-operation` (`chapters/conformance.md`, *API operations conformance*).

A technical contributor usually drafts the contract with AI, from the requirements and the API Ruleset. A domain expert of the Working Group checks that each operation's description says what its requirements say.

## Referencing a domain standard instead

Where a well-established domain standard already defines the API, such as OpenID for Verifiable Credential Issuance for the Wallet, a specification reaches maturity 2 by referencing the standard instead of writing its own contract. It then states:

- the standard and its version;
- the options, formats and subsets of the standard a software solution supports;
- which operation of the standard each `SERVICE_API` requirement names.

The standard's own schema is referenced, never copied.

## Maturity 2 is done when

- The contract passes the lint ruleset of the API Ruleset.
- Every `SERVICE_API` requirement names an operation that exists in the contract, and every operation is named by at least one requirement.
- A domain expert of the Working Group has signed off the operation descriptions.

# Maturity 3: API examples and mock

- Every operation has at least one request and response example for each of its outcomes: success, each refusal and each pending result.
- Examples use synthetic data only.
- A mock in `resources/mock/` answers every example, and is labelled as not for production use.

Maturity 3 is done when every example validates against the contract and the mock returns it.

Integrators use the mock to build and test before a software solution is procured or deployed, and to test AI agents safely. Automated testing of software solutions does not need it: a contract at maturity 2 is enough to run a test scenario against a software solution's test instance.

# Maturity 4: Test scenarios

A test scenario says, in plain language, how to show that a software solution meets one requirement. Scenarios are written in Given/When/Then form, so that a domain expert can approve them without reading code, and a test harness can run them where a script can check the outcome.

```gherkin
# govstack:spec:bb:wallet#req-1 (illustrative)
Feature: Listing credentials

  Scenario: The Wallet lists every credential it holds
    Given the Wallet holds a credential issued by "Transport Authority" that expires on 2027-01-31
    When a client acting for the Holder requests the list of credentials
    Then the list contains that credential with issuer "Transport Authority" and expiry date 2027-01-31

  Scenario: The Wallet refuses a client the Holder has not authorised
    Given the Holder has not authorised the client
    When the client requests the list of credentials
    Then the request is refused as not permitted
```

Rules:

1. One scenario tests exactly one requirement. A requirement usually needs several: one for success, one for each way it can be refused, and one for each pending result.
2. The scenarios of a requirement sit in `resources/tests/`, and the `test` field of the requirement lists them.
3. A scenario for a `SERVICE_API` requirement names the operation of the contract it calls. Before maturity 2, it describes the call in words.
4. `AUDITABLE` requirements need no scenario. The evidence listed in their `documentation` already says what is inspected.
5. `RECOMMENDED` requirements may have scenarios. Maturity 4 does not need them.

Maturity 4 is done when every published `REQUIRED` `OBSERVABLE` requirement has at least one scenario, and a domain expert of the Working Group has approved the scenarios.

# Moving up

- **Maturity 1** is reached by every specification before publication.
- **Maturity 2 and 3** are reached for a foundational Building Block, or when there is a demand signal. The Technical Committee names the foundational Building Blocks once. A demand signal names a country or funder, a timeline and a contributor. A Working Group that cannot write a contract of the required quality leaves the Service API to its requirements, and stays at maturity 1.
- **Maturity 4** is reached as the Working Group writes test scenarios.

A specification declares its maturity in `metadata.yml`:

```yaml
maturity: 1 # 0, 1, 2, 3, 4
```

`resources/examples/wallet/metadata.yml` shows the field in a complete file.

How raising the maturity affects the version of a specification is set out in *Versioning and Change Documentation* (`chapters/versioning_and_change_documentation.md`, *Maturity changes*).

# Existing specifications

An existing specification takes the maturity its content supports:

- **Requirements without classifiers:** maturity 0, until every published requirement carries them.
- **An OpenAPI document that passes the lint ruleset of the API Ruleset:** maturity 2, once every `SERVICE_API` requirement names an operation in it.
- **An OpenAPI document that does not pass:** maturity 1. The document is kept as input for a revised contract.
