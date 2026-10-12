---
title: Worked example, Wallet and a Credentials Reference Architecture
description: How a Building Block specification sits against a Domain Reference Architecture, and how to tell an interface requirement from the other kinds.
date: 2026-09-21
status: draft
---

# Worked example: Wallet and a Credentials Reference Architecture

This example shows how to apply three rules from `chapters/structure_of_specifications.md` §3.3.1
and §3.3.2 to a real case:

- the system is described, the software is specified;
- a constraint that matters at system level is projected onto the Building Block's boundary;
- an actor outside the boundary still has its interface specified.

It is grounded in the current Wallet drafting work in
`bb-remakes/wallet-2.0/bb-wallet/spec/requirements/`, which covers credential discovery, wallet
attestation, key attestation, OpenID4VCI and OpenID4VP.

## How the Wallet interacts with the Reference Architecture

`govstack:ra:credentials` defines the domain's cast and the contracts between them: Credential
Issuer, Credential Holder, Credential Verifier, Trust Registry Operator. It names which protocols
carry each exchange, which credential formats and schemas are in play, which trust anchors a
participant must recognise, and what a conformant actor in each role does. It carries no
requirements, because nothing can be assessed against it.

`govstack:spec:bb:wallet` occupies the Holder role and specifies only the software occupying it.
Where the Reference Architecture defines a role, the Wallet specification cites it rather than
restating it, and the actor table in Chapter 2 records `govstack:ra:credentials` in the
**Specified where** column for Issuer, Verifier and Trust Registry.

The practical payoff is conformance testing. A test harness stands up a reference-conformant
Issuer and Verifier whose behaviour the Reference Architecture defines, and exercises the Wallet
against them. No vendor has to build an ecosystem to certify one component.

## Interface requirements the Wallet holds

These sit at the boundary. Their subject is the Wallet, they derive from contracts the Reference
Architecture defines, and a harness with an emulated counterpart can verify each one.

- The Wallet accepts a Credential Offer from an Issuer over at least one of the mechanisms the
  Reference Architecture names, and initiates issuance in response.
- The Wallet initiates issuance against Issuer metadata without a prior Offer.
- The Wallet accepts a credential in each format the Reference Architecture designates for the
  domain, and rejects one whose signature does not verify against a recognised trust anchor.
- The Wallet presents only the attributes named in a Verifier's presentation request.
- The Wallet produces a key attestation when a Verifier's request requires one.
- The Wallet checks revocation status before presenting, and treats an unreachable revocation
  endpoint as *status unknown* rather than as valid.
- The Wallet refuses a presentation request from a Verifier that cannot be resolved against the
  trust registry.

### The projection rule

Each of these is a projection of a system-level constraint onto the Building Block's boundary.

| System-level constraint | Projected onto the Wallet |
|---|---|
| The Issuer signs credentials with a designated algorithm | The Wallet accepts a credential signed with that algorithm |
| The Verifier requests only the attributes it needs | The Wallet presents only the attributes named in the request |
| The trust registry publishes revocation status | The Wallet treats an unreachable revocation endpoint as *status unknown* |

The subject moves to the component, the constraint survives intact, and a tester can check the
statement against one implementation. Whatever resists projection was never the Wallet
specification's to impose.

## What is actually a different kind of requirement

| Category | Examples | Where it belongs |
|---|---|---|
| **Autonomous functional** | Storing a credential across restarts; credential lifecycle and deletion; backup and recovery; key generation and rotation; holder consent gating before any presentation; showing the holder what a Verifier asked for before answering | `govstack:spec:bb:wallet` |
| **Cross-functional, inherited** | Transport security, container packaging, structured logging, data handling, observability | `govstack:spec:cfr`, inherited through `extends`. The Wallet specification must not restate these |
| **Reference Architecture content** | Who may operate as an Issuer; trust registry governance and admission; credential schema definitions and versioning; revocation registry operation; the legal basis for issuance | `govstack:ra:credentials`. No wallet implementation can satisfy them |
| **Not a requirement** | "A wallet holder MUST support at least one mechanism for adding credentials" | Nowhere. The subject is a person, and nobody submits a holder for assessment |

### Why the autonomous group matters most

Key management, storage durability, deletion and consent gating are what make a Wallet a Wallet.
They hold whatever the domain does, and they would survive the domain swapping OpenID4VCI for
something else. That is the test for whether a requirement belongs to the component or to the
domain: **ask whether it would still be true if the Reference Architecture were rewritten.**

This group is also what keeps the Building Block autonomous, which is a criterion the Building
Block approach imposes and not a stylistic preference. A specification whose requirements are all
interface projections has described an adaptor rather than a component.

## A subject error to watch for

The current draft at `bb-remakes/wallet-2.0/bb-wallet/spec/requirements/credential-discovery.md`
opens:

> A wallet holder MUST support AT LEAST ONE mechanism for adding credentials from an issuer.

As written this cannot be tested, because its subject is the person rather than the software.
Rewriting it as *the Wallet supports at least one mechanism for accepting credentials from an
Issuer* makes it verifiable and preserves the meaning.

The slip is worth hunting for across the draft. "Holder" is the role name in the three-party
model, so the temptation to use it as the grammatical subject is constant, and every instance
produces a requirement that no conformance process can assess.

## How to use this

When classifying a candidate requirement, ask in order:

1. Can one conformant Wallet implementation satisfy it alone? If not, it is not a Wallet
   requirement.
2. Is its subject the software, or a person, an institution or another actor? Only the first can
   conform.
3. Would it still hold if the Reference Architecture were rewritten? If yes, it is autonomous
   functional. If no, it is an interface requirement and should cite the contract it derives from.
4. Is it already in `govstack:spec:cfr`? If so, inherit it and do not restate it.
