---
title: Scope
---

<!-- Worked example for chapters/introductory_chapters.md. Illustrative: the Credentials Reference Architecture (govstack:ra:credentials) and the requirement file names are examples, not published artefacts. -->

# Scope

## Subject and applicability

This document specifies the Wallet Building Block, a software component that holds credentials on behalf of one person, together with the keys bound to them, and presents them at that person's direction.

It is applicable to any software solution claiming conformance to `govstack:spec:bb:wallet`.

A conformant solution meets the core of this document. It may also claim one or both of these extensions, and meets every requirement of each extension it claims:

- Issuance: obtaining credentials from a Credential Issuer through the issuance exchange.
- Presentation: answering a Credential Verifier's request through the presentation exchange, with holder binding.

## Relationships

This document extends `govstack:spec:cfr`. A conformant solution meets the requirements of both.

The Wallet Building Block performs the Credential Holder role in `govstack:ra:credentials`, in full when it claims both extensions.

## Covered

**Core**

- **Credential Intake**: receiving credentials the Holder supplies, recognising their format, and checking them before storage
- **Credential Management**: keeping, displaying, deleting, backing up and recovering credentials
- **Key Management**: generating, protecting and rotating keys bound to credentials, and attesting to them
- **Holder Consent**: showing the Holder each request and recording their decision before disclosure
- **Credential Presentation**: presenting a credential with only the attributes the Holder agreed to disclose

**Issuance extension**

- **Credential Issuance**: the Wallet's side of the issuance exchange and of the issuer trust lookup defined in `govstack:ra:credentials`

**Presentation extension**

- **Presentation Exchange**: the Wallet's side of the presentation exchange and of the verifier trust and status lookups defined in `govstack:ra:credentials`

## Not covered

| Excluded | Owned by |
|---|---|
| Behaviour of Credential Issuers and Credential Verifiers | `govstack:ra:credentials` |
| Trust registry operation, and admission of Issuers and Verifiers to it | `govstack:ra:credentials` |
| Credential schemas, and which credentials a domain uses | `govstack:ra:credentials` |
| Identity proofing of a person before a credential is issued to them | `govstack:spec:bb:identity` |
| Records of consent given to an organisation to process personal data | `govstack:spec:bb:consent` |
| Who may act as an Issuer, and the legal effect of a presented credential | The adopting government. Guides cover particular regulations |
| Government services that issue or accept credentials | The country's Digital Service System |

## Ownership

Owned by the Credentials and Identity Working Group (`govstack:wg:cfid`). Change requests are raised as issues in the specification's repository.
