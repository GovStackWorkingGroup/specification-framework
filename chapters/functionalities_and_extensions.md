---
title: Functionalities and Extensions
---

{% hint style="info" %}
This chapter describes the mechanisms proposed in ARCH-ADR-7 (`ADR/ARCH-ADR-7.md`) for core and extensions, in ARCH-ADR-6 (`ADR/ARCH-ADR-6.md`) for options, in ARCH-ADR-9 (`ADR/ARCH-ADR-9.md`) for conditional requirements, and in ARCH-TDR-1 (`ADR/ARCH-TDR-1.md`) for capabilities. None has yet been decided by the Architecture Committee.
{% endhint %}

A Building Block specification is organised by functionalities, and each functionality realises one or more capabilities. This chapter says what a capability is, what a functionality is, how the two are linked, and how a specification sorts its functionalities into a **core**, which every conformant solution meets, and **extensions**, which a solution may claim in addition to the core.

# Capabilities

A capability is an outcome the Building Block makes possible for its beneficiaries: "Credential Custody", "Selective Disclosure", "Holder Control". Capabilities are named in the *Purpose* of the overview, for a reader deciding whether the Building Block is relevant to them (`chapters/introductory_chapters.md`, *Value*).

A capability states **what**, never **how**. It names no product, vendor, protocol or technology, and it carries no requirements. Capabilities are informative: a solution does not claim a capability and is not assessed against one. It is assessed against the functionalities that realise them.

Each capability is declared once, in `metadata.yml`:

```yaml
capabilities:
  - id: custody
    title: Credential Custody
    description: A person keeps the credentials issued to them and can use them whenever they need to.
  - id: disclosure
    title: Selective Disclosure
    description: A person shares only the attributes of a credential that a party needs to see.
```

| Field | Content |
|---|---|
| `id` | A short identifier, unique in the specification and never reused |
| `title` | A noun phrase, used in the *Purpose* of the overview |
| `description` | One sentence: the outcome, from the point of view of the beneficiary |

## Capabilities and functionalities

Capabilities and functionalities answer different questions, for different readers, and are cut along different lines:

| | Capability | Functionality |
|---|---|---|
| Answers | What the Building Block makes possible | What a conformant solution does, and for which actor |
| Written for | A decision-maker or buyer reading the *Purpose* | An implementer or assessor reading the requirements |
| Carries requirements | No | Yes, in its file in `requirements/` |
| Core or extension | Neither. It follows from the functionalities that realise it | Exactly one of the two |
| Declared in | `metadata.yml`, `capabilities` | Its own file in `requirements/` |

A functionality names the capabilities it realises in its `realises` field. The link is many-to-many. One capability is often realised by several functionalities, and these may sit on both sides of the line between the core and an extension: in the Wallet, Credential Acquisition (core) and Verifiable Credential Acquisition (Verifiable Credentials extension) both realise Credential Custody. One functionality may also realise several capabilities: Credential Management realises both Credential Custody and Holder Control.

A capability is therefore not a container for requirements. If it were, each requirement would belong to exactly one capability, and a capability that spans the core and an extension would have to be split in two, along a line drawn for conformance rather than for value.

Rules:

1. Every capability is realised by at least one functionality.
2. Every functionality realises at least one capability. A functionality that realises none serves no outcome the *Purpose* names: either the *Purpose* is missing a capability, or the functionality belongs elsewhere.
3. A capability that at least one core functionality realises is provided by every conformant solution. A capability that only extension functionalities realise is provided only by solutions that claim one of those extensions, and the *Purpose* says so.
4. When the core and an extension both realise a capability, the *Description* says what a solution that claims the extension provides beyond the core.

A Domain Reference Architecture has capabilities of its own, in its capability map, and maps each one to a Building Block in a role (`govstack-process/chapters/reference_architectures.md`, Part 3). That mapping may cite the capabilities of a Building Block specification by their ids. The relationship runs one way, as for extensions: a Building Block's capabilities never reference a reference architecture.

# Functionalities

A functionality is a service the Building Block provides to an actor outside it, realised by a set of requirements. It is the unit a specification is organised by: each functionality has its own file in `requirements/`, and the functionalities named in the overview are those files, with the same names (`chapters/structure_of_specifications.md`).

This follows the "feature" organisation of requirements in ISO/IEC/IEEE 29148: a feature is a service the system provides externally, which may need a sequence of inputs to produce its result. A functionality is such a feature, and the sequence of inputs and responses that realises it is its workflow, described in the body of its file.

A functionality file holds:

| Part | Where | Content |
|---|---|---|
| Title | front matter, `title` | The name of the functionality, as in the overview |
| Description | front matter, `description` | What the functionality provides, from the point of view of the business. No requirements language. The *Functionalities* table of the overview repeats it |
| Realises | front matter, `realises` | The ids of the capabilities, declared in `metadata.yml`, that the functionality realises |
| Conformance | front matter, `conformance` and `extension` | Whether the functionality is in the core or in an extension, and which one |
| Requirements | front matter, `requirements` | The requirements that realise the functionality |
| Body | below the front matter | Description, workflows and data structures. Free text that does not embed requirements |

A functionality is reached by an actor, directly or through another functionality. A functionality that no actor reaches is usually an internal mechanism, and may be better expressed as requirements of another functionality (`chapters/introductory_chapters.md`, *Functionalities*).

# Core and extension functionalities

Every functionality carries one classifier for conformance:

| Value | Meaning |
|---|---|
| `CORE` | Every conformant solution meets this functionality |
| `EXTENSION` | A solution meets this functionality if it claims the extension the functionality belongs to |

```yaml
---
title: Verifiable Credential Acquisition
description: |
   The holder obtains verifiable credentials from an issuer over OpenID4VCI.
realises: [custody] # ids of capabilities declared in metadata.yml
conformance: EXTENSION # [CORE, EXTENSION]
extension: verifiable-credentials # only when conformance is EXTENSION
requirements:
  - ...
---
```

`conformance` defaults to `CORE`. A specification with no extensions is all core.

## The core

The core is what makes a solution an instance of the Building Block. A solution that meets the core is a useful, conformant product on its own, even if it claims no extension.

The core of a specification is never empty, and never depends on an extension. A core variation point may offer an option that is met by claiming an extension (see *Options met through an extension*). That is not a dependency: the variation point is still met without the extension, through its other options.

## Extensions

An extension is a named set of one or more functionalities that a solution may claim in addition to the core. A solution that claims an extension meets every `REQUIRED` requirement in it, as it does for the core. An extension is therefore all or nothing: a solution cannot claim half of one.

Each extension is declared once, in `metadata.yml`:

```yaml
extensions:
  - id: verifiable-credentials
    title: Verifiable Credentials
    description: Obtaining verifiable credentials from an Issuer over OpenID4VCI and keeping them bound to the Holder.
    role: Credential Holder, towards the Credential Issuer and the Credential Verifier
    reference_architecture: govstack:ra:credentials # optional
    requires: [] # ids of other extensions this one depends on
```

| Field | Content |
|---|---|
| `id` | A short identifier, unique in the specification and never reused |
| `title` | The name used in the overview, the scope and GovMarket |
| `description` | One sentence: what a solution that claims the extension can do that the core alone cannot |
| `role` | The role the Building Block performs through the extension, and towards whom. Optional when the extension serves no particular role |
| `reference_architecture` | The Domain Reference Architecture that describes that role. Optional |
| `requires` | Other extensions a solution must also claim when it claims this one |

Rules:

1. A functionality is either core or in exactly one extension. Its requirements are not split between the two.
2. An extension may add to the behaviour of a core functionality when it is claimed. It may not weaken or replace a core requirement.
3. An extension may require other extensions. The core never depends on an extension, and there are no cycles. An option met by claiming an extension is not a dependency, as long as the core meets `min` of that variation point's options without one.
4. A variation point (ARCH-ADR-6) may sit in an extension. It then binds only the solutions that claim the extension.
5. With ARCH-ADR-9, `applies_when` may name an extension id, so that a requirement applies only to solutions that claim the extension. In a core functionality, use it when the requirement belongs there by meaning, for example "the wallet logs the verifier's identity for each presentation" in a transaction log that is core. In an extension, use it for requirements that several other extensions share: the Wallet's verifier checks sit in `verifiable-credentials` with `applies_when: [remote-presentation, proximity-presentation]`, written once, rather than in an extension of their own. Without ARCH-ADR-9, write such a requirement in a functionality of the extension.

# Deciding what is core and what is an extension

Start from the core. For each functionality, ask whether a product without it would still be an instance of the Building Block. If it would not, the functionality is core.

Make a functionality an extension when one of these holds:

| Reason | Example |
|---|---|
| **Some conformant products legitimately lack it.** The capability adds to the Building Block, and a product without it still does the Building Block's job | A Payments Hub that does not issue vouchers |
| **It is an obligation towards a role in a domain.** The functionality exists to work with a counterpart that follows the protocol of a particular domain, and not every deployment has that counterpart | A wallet's exchange with an Issuer that follows the issuance protocol of a credentials reference architecture |
| **It depends on infrastructure that not every deployment has.** | Checking issuers against a trust registry that a country may not operate |

The second reason follows from how reference architectures work. A Domain Reference Architecture carries no requirements. The obligations a Building Block has towards a role in it belong in the Building Block specification (`govstack-process/chapters/reference_architectures.md`, Part 5). When those obligations are not part of what makes a solution an instance of the Building Block, they are an extension. This keeps the Building Block autonomous: a solution can conform to the core without any counterpart following a domain's protocol.

## Options met through an extension

Every solution may have to do something that some solutions do through a domain's protocol and others do alone. The Wallet must add credentials of at least one type: bearer credentials and passes it handles alone, verifiable credentials only within an ecosystem of issuers and trust infrastructure. Making every type an extension splits small, autonomous sets of requirements into extensions every wallet would claim. Making every type a plain option pulls a whole protocol stack into the core.

The answer is a core variation point whose options are mostly plain, with an option met by claiming an extension where the option needs one (`chapters/requirements.md`, *Options*). The extension holds what needs the ecosystem. Requirements specific to a plain option stay in the core functionalities they belong to, conditional on the option's key (`chapters/requirements.md`, *Conditional requirements*).

Use an extension for an option when one of the reasons in the table above holds for that option. Otherwise use a plain option.

An extension names the role it serves and the reference architecture that describes the role, for the reader. Its requirements cite the standards they use directly, and are complete without the reference architecture. The relationship still runs one way: a reference architecture references the specification, never the reverse.

## Other mechanisms, and when to use them instead

| The need | Use | Not an extension because |
|---|---|---|
| Every solution does something, but may do it in one of several ways | A variation point with options (ARCH-ADR-6) | The solution cannot skip it |
| As above, but one of the ways needs a domain's protocol or infrastructure | A variation point with an option met by claiming an extension | The choice cannot be skipped; only the protocol-bound way is an extension |
| Several extensions need the same requirements | Requirements in the extension they all require, with `applies_when` naming the extensions that use them (ARCH-ADR-9) | No product would claim the shared requirements on their own |
| A behaviour improves quality and a solution may skip it | A `RECOMMENDED` requirement | Nothing is claimed; GovMarket reports the share met |
| A whole specification is narrowed for one context, such as a country | A profile: a separate specification that extends this one | The narrowing is decided by someone other than the Working Group, for one context |
| The part can be assessed as a component on its own, with its own system under test | A separate Building Block specification | It does not need this Building Block's core |

## Warning signs

- **Every product would claim the extension.** It is core in disguise. Move it to the core.
- **The extension has no `REQUIRED` requirement.** Claiming it guarantees nothing. Write its requirements as `RECOMMENDED` requirements in the core instead.
- **The core cannot stand alone.** A solution that meets only the core would not be useful. A functionality the core needs has been placed in an extension.
- **The extension can be assessed without the core.** It may be a separate Building Block.
- **Extensions multiply with each combination.** Two extensions that differ only in how they do the same thing are usually a variation point. If each way needs its own protocol, keep them as extensions and make them options of one variation point; otherwise make them plain options inside one functionality or extension.
- **No product would claim the extension on its own.** It exists only to be required by other extensions. Move its requirements into an extension they already require, conditional on the extensions that use them.
- **Every option of a core variation point is an extension.** The core cannot stand alone. Move a way that needs no counterpart into the core as a plain option.

# Conformance

- Every conformant solution meets the core.
- A solution declares the extensions it claims and the options it meets, in its conformance declaration. For each claimed extension, and each extension it requires, the solution meets every `REQUIRED` requirement. Declaring an option met by an extension also claims the extension.
- The requirements of an extension that is not claimed are **not applicable**, neither passed nor failed. A `RECOMMENDED` requirement in an unclaimed extension is left out of the share GovMarket reports.
- GovMarket shows the claimed extensions and declared options as facts about the solution, not as a score: "Wallet 2.0: core (pass, bearer; code)" or "Wallet 2.0: core (vc; remote), Verifiable Credentials, Remote Presentation". A buyer can filter on them, and cite them in a procurement.
- An extension takes part in compliance through its `PUBLISHED` requirements (ARCH-ADR-5). An extension can be introduced with `DRAFT` requirements, so that implementations can experiment with it before it binds anything.

# Versioning

An extension's requirements follow the rules of *Versioning and Change Documentation*, judged against the solutions that claim the extension. Changes to the extensions themselves are classified as follows:

| Change | Release | Reason |
|---|---|---|
| Adding an extension | minor | No existing claim changes |
| Moving a functionality from the core into an extension | major | Every citation of the specification loses a guarantee |
| Moving a functionality from an extension into the core | major | Every solution gains obligations |
| Deprecating an extension | major | Solutions that claim it lose their claim |
| Adding an extension to another one's `requires` | major | Solutions that claim the first must now claim the second |

Capabilities are informative. Adding, rewording or removing a capability, or changing the functionalities that realise it, is on its own a **patch** change: no solution's conformance changes. A capability id is never reused, so a citation of a capability in a reference architecture never comes to mean something else.

# Terms that sound alike

| Term | Means | Unit |
|---|---|---|
| **extends** | One specification inherits another, and may tighten it. Every Building Block specification extends the cross-functional requirements | A whole specification |
| **Profile** | A separate specification that extends a Building Block specification and narrows it for one context, such as a national profile | A whole specification |
| **Extension** | A named set of functionalities inside one Building Block specification, which a solution may claim in addition to the core | Functionalities |
| **Variation point** | A requirement a solution meets by meeting at least `min` of its options (ARCH-ADR-6) | A requirement |
| **Option** | One of the choices a variation point permits, with level `OPTION` and an id such as 30.2. A solution must meet at least `min` options, unlike an extension, which it may leave unclaimed. An option may be met by claiming an extension (ARCH-ADR-6) | A requirement |

Do not use "optional" for an extension or for an option. In GovStack usage, "optional" already means `RECOMMENDED`.

# Example: the Wallet Building Block

The Wallet performs the Credential Holder role in the Issuer-Holder-Verifier model. What makes a product a wallet is that it holds credentials issued to its holder and presents them at the holder's direction. That is the core. Some credential types and some ways of presenting need counterparts that follow the protocols of the credentials domain. Those are extensions, reached through options of the core.

The Wallet declares three capabilities: Credential Custody (`custody`), Selective Disclosure (`disclosure`) and Holder Control (`control`).

| Functionality | Realises | Conformance | Extension |
|---|---|---|---|
| Credential Acquisition: adding credentials of at least one type. Options: bearer credentials (`bearer`) and passes (`pass`) in the core, verifiable credentials (`vc`) met by claiming `verifiable-credentials` | `custody` | `CORE` | |
| Credential Management: storage, expiry, revocation, removal and portability, with conditional requirements for `bearer` and `pass` | `custody`, `control` | `CORE` | |
| Credential Presentation: presenting with the holder's consent, in at least one way. Options: showing the code a credential was issued with (`code`) in the core, OpenID4VP and ISO/IEC 18013-5 met by claiming their extensions | `disclosure`, `control` | `CORE` | |
| Verifiable Credential Acquisition, Holder Binding, Verifiable Credential Lifecycle | `custody` | `EXTENSION` | `verifiable-credentials` |
| Answering a Verifier's Request, Privacy-preserving Presentation (conditional on `remote-presentation` or `proximity-presentation`) | `disclosure` | `EXTENSION` | `verifiable-credentials` |
| Remote Presentation over OpenID4VP | `disclosure` | `EXTENSION` | `remote-presentation` (requires `verifiable-credentials`) |
| Proximity Presentation over ISO/IEC 18013-5 | `disclosure` | `EXTENSION` | `proximity-presentation` (requires `verifiable-credentials`) |

Every capability is realised by at least one core functionality, so every conformant Wallet provides all three. Each core variation point has a plain option, so the core stands alone: a wallet for tickets and passes conforms as "core (pass, bearer; code)" without claiming any extension.

A product that holds keys but no credential issued by anyone, such as a crypto hardware wallet, fails the core: it meets no option of Credential Acquisition. A wallet built for a credentials ecosystem claims `verifiable-credentials` and one or both presentation extensions.

A complete worked example of the introductory chapters is in `resources/examples/wallet/introduction/`. It still shows the earlier split into Issuance and Presentation extensions, and will be updated when the Wallet 2.0 structure is agreed.

# References

- ARCH-ADR-5, *Separate a requirement's obligation level from its lifecycle status*
- ARCH-ADR-6 (proposed), *Model options*
- ARCH-ADR-7 (proposed), *Organise a specification's functionalities into a core and extensions*
- ARCH-ADR-9 (proposed), *Model conditional requirements*
- ARCH-TDR-1 (proposed), *Declare capabilities and link functionalities to them*
- ISO/IEC/IEEE 29148:2018, *Systems and software engineering — Life cycle processes — Requirements engineering*
- OGC 08-131r3, *The Specification Model — A Standard for Modular Specifications*, Open Geospatial Consortium, 2009
