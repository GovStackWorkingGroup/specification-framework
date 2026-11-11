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

A Building Block specification is written by volunteers who know their domain, and not every Working Group can specify everything at once. This chapter lets a specification grow in steps. It defines two maturity scales: one for how far the requirements are developed, and one for how far the Service API is specified. Each level adds artefacts to the level below it, so nothing is written twice.

Maturity describes the **specification**, not a software solution. How strongly the results of a software solution are evidenced is its *assurance level*, and which set of requirements it claims is its *conformance class*. Both are described in *Conformance* (`chapters/conformance.md`).

| Scale | Describes | Levels |
|---|---|---|
| Requirement maturity | How far the requirements of a specification are developed | 0 to 3 |
| API maturity | How far the Service API of a specification is specified | 0 to 4 |

Always qualify the word: "requirement maturity 2", "API maturity 1". On its own, "level" means the `level` classifier of a requirement, `REQUIRED` or `RECOMMENDED` (`chapters/requirements.md`).

# Requirement maturity

Requirement maturity says how much of what a software solution is assessed against is written down.

| Maturity | Adds | Lives in |
|---|---|---|
| 0 None | No requirement is defined | — |
| 1 Statements | Every published requirement has a statement that passes the checklist of *Writing the statement* (`chapters/requirements.md`) | `requirements/`, field `statement` |
| 2 Classifiers | Every published requirement sets `level`, `observability`, `mutability` and `interaction` explicitly. A `SERVICE_API` requirement names its operation in `api_operation_id`; a requirement whose contract is defined elsewhere names it in `contract`; an `AUDITABLE` requirement lists the evidence a vendor produces in its `documentation` | `requirements/`, front matter |
| 3 Test scenarios | Every published `OBSERVABLE` requirement has at least one test scenario in Given/When/Then form, referenced in its `test` field | `resources/tests/` |

Rules:

1. The requirement maturity of a specification is the highest level that every one of its published requirements reaches. A Working Group may also report it per functionality, to show where work is missing.
2. The checklist of `chapters/requirements.md` requires `observability` to be set before a requirement is ready for review. A new specification therefore starts at requirement maturity 2. Requirement maturity 1 occurs only in specifications written before the classifiers existed.
3. Writing a classifier's default value explicitly does not change the requirement (`chapters/requirements.md`, *Classifiers*).
4. A requirement usually needs several scenarios: one for success, one for each way it can be refused, and one for each pending outcome. One scenario tests exactly one requirement.

A scenario is written in plain language, so that a domain expert can approve it without reading code:

```gherkin
# govstack:spec:bb:wallet#req-1
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

# API maturity

API maturity applies to the Service API: the operations a software solution offers for others to call, realised by requirements of interaction type `SERVICE_API`. A specification with no `SERVICE_API` requirement has no API maturity. Consumed APIs, protocols and the other interaction types take their contract from where `contract` points.

| Maturity | Adds | Lives in |
|---|---|---|
| 0 None | No Service API is specified. Software solutions follow the API design rules of the cross-functional requirements only | — |
| 1 Operation cards | One plain-language card per operation (`chapters/operation_cards.md`) | `resources/operations.md` |
| 2 OpenAPI contract | A contract generated from the cards and the API design rules, or a reference to a well-established domain standard with a GovStack profile | `resources/api.yml` |
| 3 Examples and mock | Examples for every outcome of every operation, and a mock that answers with them | `resources/api.yml`, `resources/mock/` |
| 4 Usage history (experimental) | A field report from an operator, and anonymised call sequences that serve as a regression set | `resources/history/` |

Each level includes the levels below it. API maturity 1 is the baseline: a specification with `SERVICE_API` requirements reaches it before publication. Levels 2 to 4 are reached on demand (see *Moving up*).

At API maturity 1 there is no common API. A conformant software solution follows the API style and behaviour of the specification and covers its operations, but it is not drop-in replaceable. From API maturity 2, conformant software solutions implement a common contract.

## API maturity 1: Operation cards

An operation card describes one operation of the Service API in plain words, with no code. *Operation Cards* (`chapters/operation_cards.md`) gives the fields, a template, an example, and the operation index the compiler derives from the cards.

### API maturity 1 is done when

- Every card has all fields filled in. The example is recommended.
- Every `SERVICE_API` requirement names an existing card in `api_operation_id`, and every card is named by at least one requirement.
- A second member of the Working Group has read the cards and found no unclear field.
- The cards contain no paths, status codes or schemas. Those belong to API maturity 2.

## API maturity 2: OpenAPI contract

The contract in `resources/api.yml` is an OpenAPI 3.1 document generated from the operation cards and the API design rules of `govstack:spec:cfr`. Every element traces to something a domain expert wrote. A technical contributor drafts it, usually with AI, and the card author checks that the contract says what the card says.

| Card field | Becomes in the contract |
|---|---|
| Operation ID | `operationId`, unchanged |
| What it does | `summary` |
| Use it when / Do not use it when | `description` |
| Who calls it, and for whom | The security requirement prescribed by the design rules, and the delegation fields they define |
| What goes in / What comes out | Schema properties with descriptions. Identifiers and codes reference shared vocabularies where they exist |
| What changes | The HTTP method, and `x-govstack-effect` |
| Possible outcomes | Responses: the error format of the design rules for refusals, the asynchronous pattern for pending results, an idempotency key where the card says a repeated request takes effect once |
| Standard | `externalDocs`; the standard's own schema is referenced, never copied |

### Referencing a domain standard instead

Where a well-established domain standard already defines the API, such as OpenID for Verifiable Credential Issuance for the Wallet, a specification reaches API maturity 2 by referencing it instead of writing its own contract. It then states:

- the standard and its version;
- the GovStack profile: which options, formats and subsets a software solution supports;
- which operation card each operation of the standard fulfils.

The operation cards are still written, so that traceability and the operation index stay the same as for any other specification.

### API maturity 2 is done when

- The contract passes the lint ruleset of the API design rules.
- Every operation traces to an operation card, and every operation card has an operation.
- The contract has been checked against at least two existing software solutions, where they exist, to confirm they could implement it.
- The card author has signed off.

## API maturity 3: Examples and mock

- Every operation has at least one request and response example for each outcome on its operation card: done, each refusal, and pending.
- Examples use synthetic data only.
- A mock in `resources/mock/` answers every example, and is labelled as not for production use.

API maturity 3 is done when every example validates against the contract and the mock returns it.

The examples double as fixtures for automated tests. Automated testing of `SERVICE_API` requirements therefore needs API maturity 3 (`chapters/conformance.md`, *Assurance of requirement results*).

## API maturity 4: Usage history (experimental)

API maturity 4 is experimental. Its content and the way it is reviewed may change before it is used in a release.

Usage history records what happens when the contract meets reality. It comes from at least one operator of a software solution that implements the contract:

- **A field report:** where the software solution deviates from the contract and why, which errors occur most often, and which call sequences are typical.
- **Call sequences:** recorded sequences of calls and responses, anonymised or replayed with synthetic data, that serve as a regression set for clients and agents.

Usage history contains no personal data. An operator removes it before sharing, or records the sequences again with synthetic data, under the data protection rules that apply to the deployment.

Usage history is not the changelog. Every specification keeps a changelog at every maturity (`chapters/versioning_and_change_documentation.md`, *The changelog*).

API maturity 4 is done when the Working Group has reviewed the field report and the call sequences replay successfully against the mock.

# Moving up

- **Requirement maturity** rises as the Working Group writes statements, classifiers and scenarios. No decision is needed.
- **API maturity 1** is reached by every specification with `SERVICE_API` requirements.
- **API maturity 2 and above** are reached for a foundational Building Block, or when there is a demand signal. The Technical Committee names the foundational Building Blocks once. A demand signal names a country or funder, a timeline and a contributor.

A specification declares the maturity it has reached in `metadata.yml`, under `maturity`:

```yaml
maturity:
  requirements: 2
  api: 1
```

| Field | Values | Content |
|---|---|---|
| `requirements` | 0, 1, 2, 3 | The requirement maturity of the specification |
| `api` | 0, 1, 2, 3, 4 | The API maturity of the specification. Omitted when the specification has no `SERVICE_API` requirement |

`resources/examples/wallet/metadata.yml` shows the field in a complete file.

## Maturity and versions

| Change | Release | Why |
|---|---|---|
| Requirement maturity rises from 0 to 1 | as for the requirements that are added | |
| Requirement maturity rises from 1 to 2 | none on its own; each classifier change follows *Classifier changes* | Writing a default explicitly is not a change |
| Requirement maturity rises from 2 to 3 | patch | Adding a test for a requirement that had none is a patch |
| API maturity rises from 0 to 1 | as for the requirements that introduce the operations | |
| API maturity rises from 1 to 2 | major | `REQUIRED` requirements now bind software solutions to an exact contract. Software solutions that implemented an operation in their own design no longer conform |
| API maturity rises to 3 or 4 | patch | Examples, mock and history inform; they do not change what a software solution must do |

# Existing specifications

Every existing specification writes operation cards at its next version, because the operation cards are the baseline that API maturity 2 builds on. Its API maturity then depends on what it already has:

- **An OpenAPI document that passes the lint ruleset:** API maturity 2, once every operation traces to an operation card.
- **An OpenAPI document that does not pass:** API maturity 1. The document is kept as input for a revised contract.
- **No OpenAPI document:** API maturity 0 until the operation cards are written, then 1.

Its requirement maturity is assessed in the same way, from the requirements it publishes.
