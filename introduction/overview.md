---
title: Overview
---

GovStack Specifications are publications that identify in a complete, precise and verifiable manner, the requirements, design, behavior, or other expected characteristics of the entity that can be verified against[^spec-definition]. GovStack organises its publications into classes, and the class records what a publication asserts requirements about:

| Class | Asserts requirements about | Examples |
|---|---|---|
| `govstack:spec` | specifications | this publication |
| `govstack:spec:cfr` | software | Cross-Functional Requirements: protocol interoperability and the minimum practices interoperability is built upon |
| `govstack:spec:bb:*` | software | Building Block Specifications: cross-functional interoperability for common technology components, prioritising replaceability of the minimum and essential characteristics across compliant implementations |

This publication sits at `govstack:spec` and holds the rules every GovStack specification follows, together with the machine-readable requirements and conformance artifacts that verify them. Rules particular to one specification type are chapters of this publication rather than separate publications.

More information can be found at the chapters of [GovStack Interoperability](/ra/architecture/chapters/govstack-interoperability), at [Building Block Approach](/ra/architecture/chapters/building-block-approach) of the core GovStack Architecture.  

What separates GovStack Specifications from other types of publications is that every specification contains requirements that can be tested against, which makes them evaluation instruments.

Requirements are an essential part of GovStacks' value proposition. They allow a builder to precisely identify what is expected from a system, an implementer to identify whether a software product performs the minimum functionalities required, or a procurer to have an instrument to measure offers or write into contracts, that offers tangible ways to evaluate. When applied as a policy at the organisational level or accross organisations, it makes interoperability between software systems possible.

This publication describes how to build a GovStack Specification whose requirements are well formed: unambiguous enough that a reader can tell what conformance demands, and structured enough that a tool can check it. A requirement that cannot be verified against either a software solution or a specification document does not belong in a GovStack publication.

Because this publication governs every GovStack specification, it governs itself. `govstack:spec` conforms to `govstack:spec`. ISO/IEC Directives Part 2 has the same property, for the same reason: a document setting out how normative documents are written is itself a normative document.

## Footnotes
[^spec-definition]: from ./terminology#specification and originally from ./references#SEVOCAB
- (1) information item that identifies, in a complete, precise, and verifiable manner, the requirements, design, behavior, or other expected characteristics of a system, service, or process (ISO/IEC/IEEE 15289:2019 Systems and software engineering--Content of life-cycle information items (documentation), 5.24) 
- (2) detailed formulation, in document form, which provides a definitive description of a system for the purpose of developing or validating the system (ISO/IEC 2382:2015 Information technology -- Vocabulary) 
- (3) information part that identifies, in a complete, precise and verifiable manner, the requirements, design, behavior, or other expected characteristics of an entity (ISO/IEC/IEEE 42010:2022, Software, systems and enterprise  Architecture description, 3.16) 
- (4) concrete representation of a model in some notation (ISO/IEC 10746-2:2009 Information technology -- Open Distributed Processing -- Reference Model: Foundations, 7.4) 
- (5) attribute that is necessary to be present in a project deliverable to help meet or exceed a target business objective (A Guide to the Project Management Body of Knowledge (PMBOK(R) Guide) -- Eighth Edition)