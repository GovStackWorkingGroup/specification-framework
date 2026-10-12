---
Title: Introduce the concept of Reference Architectures
status:
date: 2026-06-02
decision-makers:
  - Kristo Vaher
  - Ali González-García
  - David Higgins
  - Aleksander Reitsakas
  - Yuliia Kravchenko
consulted:
  - Registries Working Group
  - CFID Working Group
informed:
---
<!-- markdownlint-disable-next-line MD025 -->
# Introduce the concept of Reference Architectures

## Context and Problem Statement

GovStack Building Block Specifications operate in the Technical layer, and are scoped to be autonomous and generic to provide interoperability and composability at designing Digital Service Systems. However the organizational, semantic and infrastructure level are intentionally left out to permit this agnosticity. Hoevever, some Building Blocks only achieve their intended purpose when operating together with other Building Blocks, and when the organizations that operate them have agreed on shared rules, semantic standards, and governance. A proposal was prepared [here](https://github.com/GovStackWorkingGroup/cfr-architecture/issues/4) for the introduction of the Term "Domain Frameworks" to address this gaps, and discussed on the Architecture Working Group on may 12 and june 2, 2026.

## Decision Drivers

* Will this constrain implementations or make them vendor-locked?
* Concerns about being super prescriptive or needing infinite framework for domains that need definition
* Who will be in charge of a Framework? Will they need their own working grup? Who will fund them? How specific should we be?
* A Framework is formally prescriptive, leaving small room for sovereign decisions

## Considered Options

* Leave them at the level of Implementation Guides
* Rename them with another concept that is more appropriate

## Decision Outcome

After the team completed their discussion, it was agreed the need is present but that "Framework" is unsuitable. Given the term Framework is more formally prescriptive, the term Reference Architecture was chosen as a more flexible tool for describing the different moving parts an implementation at the business level of a Building Block may or may not have.

### Consequences

* Working Groups will own the process of creating and updating a Reference Architecture whenever they deem necessary.

### Confirmation

A draft for change will be prepared by the Working Group, with first draft provided by Kristo Vaher and Ali González-García; following Working Group discussions and open feedback, it shall be integrated to either the GovStack Architecture document, PAERA, or a separate publication.

## Pros and Cons of the Options

###  Leave this at the level of Implementation Guides

<!-- This is an optional element. Feel free to remove. -->
Implementation Guides were addressed at the [GovSpecs 2.0 strategy](https://govstack.global/app/uploads/2025/08/GovSpecs-2.0-Strategy_2025-2027.pdf)  document on section 7.4.3, Creation of implementation guidelines. These are instruments that describe how to implement a specification in a specific regional context or under a particular regulation. This option states we don't need a different instrument, but we can rather create implementation guides for this.

* Good, because no other changes need to be made
* Good, because there is already a number of guides available
* Neutral, implementation guides still lack a formal description or definition
* Bad, because as compared to existing guides, the concerns discussed about what the proposal should include, such as largely identifying actors, moving pieces, semantic and organizational necessities, the proposal seems to require a more formal structure and a broader scope

### Domain Frameworks

More information in the [original proposal](https://github.com/GovStackWorkingGroup/cfr-architecture/issues/4). However it would identify the roles, building blocks involved, governance concerns, and high level steps to implement in one or more scenarios.

* Good, because it builds neatly on top of the [Generic Interoperability Framework](https://specs.govstack.global/architecture/4-interoperability-architecture) available on our [Interoperability Architecture](https://specs.govstack.global/architecture/4-interoperability-architecture) chapter 
* Good, it is a natural inheritor to [PAERA](https://paera.govstack.global/).
* Good, because CFID, Payments and Registries WG are already working on this sense.
* Neutral, because its formality still needs to be defined
* Bad, because the scope of which Frameworks to publish could be infinite



