---
specification_name: # Example: Wallet Building Block Specification
owner_group: # Example: govstack:wg:cfid
target_version: # Example: 2.0.0
---

# Preparatory Work

This guide leads a Working Group through the conversations that come before writing a Building Block specification. Work through the sections in order: each one builds on the last, and each ends with an output that lands in a named file of the specification.

Fill in the tables as the conversations happen, and keep this document with the specification. When a scope question comes back months later, the answer, and the reason for it, should be here.

| Section | Output | Lands in |
|---|---|---|
| 1. Audience | Who the specification is written for | Calibrates every chapter |
| 2. Groundwork reading | What already exists, and what we take from it | `introduction/references.md` |
| 3. Use cases and workflows | Where the Building Block appears in real services | Workflows in `requirements/` |
| 4. Capabilities and functionalities | What the Building Block provides, and how it is organised | `introduction/overview.md`, one `requirements/` file per functionality |
| 5. Actors and interfaces | Who and what the Building Block deals with | `introduction/overview.md`, `resources/tests/` |
| 6. Boundary | What this specification leaves to others | `introduction/scope.md` |
| 7. Scope statement | The draft scope | `introduction/scope.md` |
| 8. Terminology | The terms to define | `introduction/terminology.yml` |

# 1. Audience

Decide who the specification is written for before anything else. The audience decides how much needs explaining, which terms need defining, and what an example should look like. Now, normally because Building Blocks are often used by subject matter experts, it helps identifying the audience further. For example, GIS Building Block might be read by a Software Developer but also by a Geographer or Urbanist.

Here's a list of audience examples from the GovStack Architecture document, chapter *Specifications Use*. Use it as a base and specify further:

- Government adopter
- Procurement officer
- Donor or funding organisation
- Solution vendor or implementer
- Software architect
- GovMarket compliance assessor
- GovStack specification developer
- Adjacent Working Group
- Academia and researchers
- AI-assisted development tooling

**Primary audience, and why:** <!-- One or two sentences. Where audiences differ in expertise, write for the less expert one. -->

# 2. Groundwork reading

Find out what already exists before deciding what to write. A specification that restates another publication will drift from it; one that ignores an established standard will not be adopted.

Look in this order:

1. **The previous version** of this specification, if there is one, and its open issues
2. **GovStack publications**: the Cross-Functional Requirements, Building Block specifications in adjacent domains, and any Domain Reference Architecture in which this Building Block has a role
3. **International standards and specifications** from ISO, IEC, ITU-T, IETF, W3C, OASIS and comparable bodies
4. **Regional and national regimes** that already govern the domain
5. **Country implementations and existing products**, to learn what works in practice. Learn from them; do not specify them

For each source, decide what the specification does with it:

- **Adopt**: the specification relies on it, and cites it
- **Reference**: the specification points readers to it, without depending on it
- **Contrast**: it informed the work, and nothing is taken from it

| # | Source | What it gave us | Verdict |
|---|---|---|---|
| 1 | GovStack Architecture, `govstack:ra:architecture` | Common terminology; the Building Block criteria | Adopt |
| 2 | Wallet Building Block Specification 1.2, `govstack:spec:bb:wallet` | Credential Holder, Holder Binding: terms and scope of the previous version | Adopt |
| 3 | European Digital Identity Wallet Architecture and Reference Framework, <https://eudi.dev/1.4.0/arf/> | Requirements of one regional regime, used to test our functionalities against | Contrast |
| | | | |

**Vocabulary regime.** Where a regional or national regime already names the domain, decide whether the specification uses that regime's terms or regime-neutral ones, with the regime as a worked example. A specification meant for every country usually chooses neutral terms.

**Decision:** <!-- Regime in play, the choice, and why. -->

# 3. Use cases and workflows

A Building Block is never the service. It is the software part of services that governments deliver. Start from those services, then find the Building Block inside them.

Collect real scenarios: from countries, from the GovStack Reference Use Cases (<https://govstack.gitbook.io/use-cases>), and from Domain Reference Architectures. For each one, walk the workflow end to end and mark the steps the Building Block performs. The marked steps are the raw material for functionalities in section 4.

Aim for scenarios that differ: a different country, a different sector, a different actor starting the workflow. If every scenario marks the same steps, the functionalities are likely stable. If one scenario marks steps no other does, ask whether it belongs to this Building Block.

| # | Scenario | Source | Steps the Building Block performs |
|---|---|---|---|
| 1 | A person receives a professional licence as a credential and shows it to an employer | Country practice | Accept the credential; store it; ask the person's consent; present the licence attributes |
| | | | |

# 4. Capabilities and functionalities

Turn the marked steps into the shape of the specification.

**Capabilities** say what the Building Block makes possible, as outcomes. Name each one with a noun phrase ("Credential Custody", not "stores credentials"), and name no technology. Give each a short id. Capabilities are declared in `metadata.yml` and appear in the Purpose of `overview.md`.

| Id | Capability | Outcome, for whom |
|---|---|---|
| `custody` | Credential Custody | A person keeps the credentials issued to them and can use them whenever they need to |
| | | |

**Functionalities** organise the requirements. Group the marked steps from section 3 into functionalities. Each functionality becomes one file in `requirements/`, and each must realise at least one capability. A functionality may realise several capabilities, and a capability is often realised by several functionalities.

| Functionality | Steps from section 3 | Capabilities it realises |
|---|---|---|
| Credential Intake | Accept the credential | `custody` |
| | | |

Check the result:

- A capability no functionality realises is either aspirational or misplaced
- A functionality no actor ever reaches is an internal mechanism, and may be a requirement of another functionality instead
- A functionality that only appeared in one scenario may belong to another Building Block

Then answer the question the Purpose must answer: **what would go wrong if every government and vendor built this component to their own design?** If the answer is weak, revisit whether this needs to be a GovStack specification at all.

**Answer:** <!-- Interoperability, replaceability, choice or assurance; see chapters/introductory_chapters.md. -->

# 5. Actors and interfaces

List everyone and everything the Building Block deals with in the scenarios of section 3. Mark each one inside or outside the boundary of the Building Block.

An actor **inside** is part of the software being specified. An actor **outside** belongs to the environment: this specification describes its interface with the Building Block, never its behaviour. Record the interface of every outside actor the Building Block exchanges anything with. Without it, the actor cannot be emulated in a test, and the Building Block cannot be tested on its own.

| Actor | Kind | Inside or outside | Interface with the Building Block | Behaviour specified by | Test needs |
|---|---|---|---|---|---|
| Credential Issuer | System | Outside | Credential issuance | `govstack:ra:credentials` | An emulated Issuer that offers and issues credentials |
| | | | | | |

The *Test needs* column is what the test environment must supply in place of each outside actor: an emulator, a stub, sample data. It is what makes `resources/tests/` writable later.

# 6. Boundary

The conversations so far will have raised things this specification should not cover. Record each one with the publication or party that owns it. An exclusion with an owner is a boundary; without one, it is a gap.

Building Block specifications are technical. Legal and organisational matters belong to PAERA, to Guides, or to the government adopting the specification. Requirements shared by every Building Block belong to the Cross-Functional Requirements, and are inherited, not excluded.

| Excluded | Raised because | Owned by |
|---|---|---|
| Trust registry operation | Every scenario needs one | `govstack:ra:credentials` |
| | | |

**Reference architectures.** Name each Domain Reference Architecture in which the Building Block performs a role, and the role. If none exists for the domain, record that, and tell the Architecture Working Group.

| Reference architecture | Role |
|---|---|
| | |

# 7. Scope statement

Draft the scope from sections 4 to 6, using the skeleton in `chapters/introductory_chapters.md`. It should answer "what does this document cover, and what does it leave to others", and a reader should be able to settle a scope question by reading it.

<!-- Draft here. -->

# 8. Terminology

Actor names and functionality names are terms, and they are the terms most likely to be shared with adjacent publications. Collect them here, then follow the *Process for Crafting Terminologies* chapter of `govstack:terminology` to decide which to define, which to take from an existing definition, and which to propose to the GovStack common terminology.

| Candidate term | From section | Already defined in |
|---|---|---|
| Credential Holder | 5 | `govstack:spec:bb:wallet` 1.2 |
| | | |
