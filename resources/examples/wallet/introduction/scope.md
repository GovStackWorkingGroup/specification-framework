---
title: Scope
---

<!--
Worked example for chapters/introductory_chapters.md. Illustrative: the Credentials Reference
Architecture (govstack:ra:credentials) and the requirement file names are examples, not published
artefacts.
-->

# Scope

## Subject and applicability

This document specifies the Wallet Building Block, a software component that holds credentials on
behalf of one person, together with the keys bound to them, and presents them at that person's
direction.

It is applicable to any software solution claiming conformance to `govstack:spec:bb:wallet`.

## Relationships

This document extends `govstack:spec:cfr`. A conformant solution meets the requirements of both.

The Wallet Building Block performs the Credential Holder role in `govstack:ra:credentials`.

## Covered

- **Credential Intake**: receiving issued and bearer credentials, recognising their format, and
  checking them before storage
- **Credential Management**: keeping, displaying, deleting, backing up and recovering credentials
- **Key Management**: generating, protecting and rotating keys bound to credentials, and attesting
  to them
- **Holder Consent**: showing the Holder each request and recording their decision before
  disclosure
- **Credential Presentation**: answering presentation requests with only the attributes the Holder
  agreed to disclose
- The Wallet's side of the issuance, presentation and trust lookup exchanges defined in
  `govstack:ra:credentials`

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

Owned by the Credentials and Identity Working Group (`govstack:wg:cfid`). Change requests are raised
as issues in the specification's repository.
