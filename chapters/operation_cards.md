---
title: Operation Cards
status: draft
authors:
  - name: Nico Lueck
---

Operation cards are the artefact of API maturity 1, the baseline for every specification with `SERVICE_API` requirements (`chapters/specification_maturity.md`, *API maturity*). An operation card describes one operation of the Service API in plain words, with no code. A domain expert writes a card in about ten minutes, without AI. Every higher API maturity builds on the cards: the OpenAPI contract of API maturity 2 is generated from them, and the examples of API maturity 3 cover the outcomes they list.

A card does not list the requirements it serves. The link is made once, in the `api_operation_id` field of each `SERVICE_API` requirement (`chapters/requirements.md`, *Interaction types*), and the compiler shows it in both directions when the specification is rendered.

All cards of a specification sit in `resources/operations.md`, one after another, each under its operation name as a heading. The file has no header of its own: the name, purpose and actors of the Service API are those of the specification, in `metadata.yml` and `introduction/overview.md`.

**Entry condition:** the requirements the Service API serves have well-formed statements (requirement maturity 1), and the actors are listed in the overview (`chapters/introductory_chapters.md`, *Actors*).

# Fields

Each card starts with the operation name as its heading, such as "List credentials". The name is for people; the operation ID is the stable key that requirements, software solutions and the contract point to.

| Field | What to write | Used for |
|---|---|---|
| Operation ID | A short, stable name in camelCase, such as `listCredentials`. Never changed once published | Traceability: `api_operation_id` of requirements points to it, software solutions mark their operations with it, and API maturity 2 keeps it as `operationId` |
| What it does | One sentence | Intended use |
| Use it when / Do not use it when | One line each | Intended use and its limits |
| Who calls it, and for whom | The actor from the overview, and whether it acts for someone else | Actors and delegation |
| What goes in / What comes out | Each item with its meaning in a few words; where the data comes from and how current it is, if known | Semantics |
| What changes | "Nothing", or what changes, and whether a person must approve first | Effect and human approval |
| Possible outcomes | Done; refused, with each reason; pending, and how the caller learns the result; what happens if the same request arrives twice | Behaviour, and the source of test scenarios |
| Standard | The standard that governs the exchange, or "none" | No conflict with domain standards |
| Example | One realistic sentence (recommended) | Illustration |

"What changes" separates operations that only read from operations that have consequences. A caller can call, repeat and explore a reading operation freely. A changing operation, such as one that moves money or deletes a credential, has effects the caller must be sure of and may not be able to undo. An agent acting for a person uses the field to know when it must ask that person before calling, and the card says so explicitly when a person must approve first. The field also drives what follows: it gives the operation its effect in the operation index, the operation-coverage check compares a software solution's marks against that effect (`chapters/conformance.md`, *Operation coverage*), and at API maturity 2 it decides the HTTP method.

"Possible outcomes" matters most for testing: each outcome becomes at least one test scenario at requirement maturity 3.

# Template

```markdown
## <Operation name>

Operation ID: <camelCase>

**What it does:**
**Use it when:**
**Do not use it when:**
**Who calls it, and for whom:**
**What goes in:**
-
**What comes out:**
-
**What changes:**
**Possible outcomes:**
- Done:
- Refused:
- Pending:
- Same request twice:
**Standard:**
**Example:**
```

# Example

An illustrative card for the Wallet, based on the `SERVICE_API` example in `chapters/requirements.md`, *Interaction types*.

```markdown
## List credentials

Operation ID: listCredentials

**What it does:** Returns the credentials the Wallet holds, with their issuer and expiry date.
**Use it when:** a client shows the Holder's credentials, or lets the Holder choose one.
**Do not use it when:** a Verifier asks for a presentation; that is the presentation exchange.
**Who calls it, and for whom:** a client application, acting for the Holder.
**What goes in:**
- Credential type (optional): narrows the list to one type, as named by the Issuer
**What comes out:**
- For each credential: its identifier in the Wallet, its type, its Issuer and its expiry date
**What changes:** nothing.
**Possible outcomes:**
- Done: the list is returned; it may be empty.
- Refused: the Holder has not authorised the client.
- Pending: none.
- Same request twice: the same list, unless a credential was added or removed in between.
**Standard:** none.
**Example:** The Holder's app shows a driving licence from the Transport Authority, valid until 31 January 2027.
```

# The operation index

The compiler derives an index from the cards. For each operation it records:

| Entry | Derived from |
|---|---|
| Operation ID | The card |
| Effect | `READ` when "What changes" says nothing, otherwise `CHANGE` |
| Obligation | `REQUIRED` when at least one `REQUIRED` requirement names the operation in `api_operation_id`, otherwise `RECOMMENDED` |
| Extension | The extension of the requirements that name the operation, when all of them belong to one (`chapters/functionalities_and_extensions.md`). Such an operation binds only software solutions that claim the extension |

The operation-coverage check compares software solutions against this index (`chapters/conformance.md`, *Operation coverage*).
