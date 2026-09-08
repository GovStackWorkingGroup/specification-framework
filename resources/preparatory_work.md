---
specification_name: # Example: Registries Building Block Specification
working_group: # Example: Registries and Registration
project:
  project_start_date: # In ISO 8601 Date format: YYYY-MM-DD
  project_end_date: # In ISO 8601 Date format: YYYY-MM-DD
  status: # Active, Inactive, Completed, Withdrawn 
current_version: # Example: 1.2-draft
past_version: # Example: 1.1
---

# 1. Target audience

ISO 10241-1:2011 clause 4.3.2 requires that the target group of a publication be clearly defined before terminology work begins, because it governs the delimitation of the domain, the number of concepts to be included, the formulation of definitions, the number of synonymous terms and their normative status, and the number and type of examples. This is step 1 of the procedure in `crafting_terminology.md` §5.1.

Select every audience this publication is written for. If several are selected, it is recommended to identify one of them as your primary audience, and provide a small rationale for the reason why. 

The list of audiences below are derived from the GovStack Architecture, section 5.5 *Specification Use*. A Working Group MAY add an audience not listed here, provided it is named and defined in the same form.

| Audience | Definition |
|---|---|
| Government adopter | A ministry, agency or central digital government team adopting the specification as an architectural baseline for its own systems, selectively or in full, and potentially referencing it from a national interoperability framework. Reads to decide what to adopt and what it commits them to. |
| Procurement officer | A public official who cites requirements by identifier in a tender, terms of reference or contract, and who evaluates bids against them. Reads for unambiguous, quotable obligations, and is typically not a domain engineer. |
| Donor or funding organization | An international development funder — such as the World Bank, the Gates Foundation, GIZ, ITU, the European Commission, USAID, UNICEF or UNDP — that makes GovStack compliance a condition of funding and evaluates deliverables against published requirements. Reads for a testable quality baseline. |
| Solution vendor or implementer | A private-sector or open-source team building new software against the specification, updating existing software to comply with it, or writing an adaptor that maps an existing API onto it. Reads to know exactly what to build and how compliance will be judged. |
| Software architect | A practitioner using the specification as a reference for operational qualities and interoperability decisions rather than as an implementation contract, in public sector, private sector or independent work. Reads for the reasoning and the classifiers, not only the requirement text. |
| GovMarket compliance assessor | A party determining whether a software solution satisfies the REQUIRED requirements of this specification and the cross-functional baseline, and computing its degree of compliance against RECOMMENDED requirements. Reads each requirement as a pass or fail test. |
| GovStack specification developer | An author or Working Group drafting another GovStack specification, who studies this one as a structural example or references it as an upstream dependency instead of restating its capabilities. Reads for interfaces, dependencies and terminology to reuse. |
| Adjacent Working Group | A Working Group whose own specification consumes this one's interfaces or shares part of its domain, and which must agree with it on shared concepts. Reads to detect conflicts and to identify candidates for Common Terminology. |
| Academia and researchers | University courses and researchers using the specification corpus to teach software architecture, systems integration or digital government, or to study compliance testing, interoperability maturity and comparative adoption. Reads without the surrounding institutional context that practitioners have. |
| AI-assisted development tooling | Code generation, automated testing and agent-driven workflows that consume the specification as structured context — requirement identifiers, classifiers, and machine-readable API contracts — to generate or review software. Cannot infer what is left implicit, and is unforgiving of an undefined term used as if it were defined. |

**Primary audience and rationale**

<!-- Name the primary audience and state in one or two sentences why. This is the audience whose level of knowledge sets the calibration for the whole Terminology section. -->

**Additional audiences not listed above**

<!-- Name and define any audience specific to this publication, in the same form as the table. -->

**Calibration notes**

Calibration notes help communicate decisions on whether any term classes are excludable or includable according to the audience defined. A term that a qualified reader in the selected audiences already knows SHOULD NOT be defined, and a term that any one of them would misread SHOULD be. Where the selected audiences differ in expertise, a procurement officer and a systems architect reading the same document, resolve in favour of defining the term.

<!-- Record any term classes the selected audiences make obviously includable or excludable, so the decision is not re-argued later. For example: "the primary audience is implementers, so protocol and transport terms such as NFC or QR Code are common knowledge and are not defined; role names from the trust regime are not, and are defined." -->


# 2. Project scope

ISO 10241-1:2011 clause 4.3.3.1 states that a careful delimitation of the domain SHALL be the starting point of the work. This is step 2 of the procedure in `crafting_terminology.md` §5.2. The output of this section is a written delimitation that makes possible to ask whether any inclusions are or aren't in scope by merely reference this section.

Delimitation is a property of the publication, not of the subject matter or domain. So is the publication scope for a Building Block specification is technical and directed to builders and implementers, the Working Group may choose to not define a term that an Procurement Guide for that Building Block does, due to different audiences being targeted.

## 2.1 Publication type and subdivision axis

The type of publication being prepared is a good guide for how to specify the domain and its subdivisions of the domain are, and therefore how §2.3 below is filled in.

| Publication type | Subdivision axis for §2.3 |
|---|---|
| Building Block specification | Key Functionalities. Each one delimits a portion of the domain whose concepts must be nameable. |
| Cross-Functional Requirement specification | CFR categories (development, deployment, architecture, quality, security, data). |
| Reference Architecture | Interoperability layers covered: governance, legal, organizational, semantic, technical, infrastructure. Name only the layers this publication actually treats. |
| Implementation Guide | Phases or decision areas of the guide. |
| GovStack Architecture | Framework parts. |
| Other | <!-- Name the type and state what its subdivisions are. --> |



## 2.2 Sources consulted to ascertain the domain

Clause 4.3.3.2 a) requires the scope of the domain to be ascertained by consulting the following. Record what each one yielded, or state explicitly that it yielded nothing.

| # | To consult | Finding |
|---|---|---|
| 1 | The scope of the Working Group, and of this publication | |
| 2 | The GovStack Architecture, which fixes the boundary between what this publication defines and what the surrounding architecture already defines | |
| 3 | Existing GovStack publications in adjacent domains, to identify where this domain touches theirs | |
| 4 | General and special subject classifications used in the domain | |
| 5 | Domain literature: international standards, national legal instruments, reference architectures, academic and practitioner sources | |
| 6 | Existing terminological collections (carried into §3) | |

## 2.3 Subdivisions of the domain

Clause 4.3.3.2 b) requires the subdivisions to be selected in light of the purpose of the publication and the needs of the target group recorded in §1. Use the subdivision axis selected in §2.1.

| Subdivision | What it covers | Concepts expected to arise |
|---|---|---|
| | | |
| | | |
| | | |

## 2.4 Actors

List every actor that takes part in the larger process this publication sits inside, whether or not the publication covers it, and mark each one in or out of scope. ISO/IEC/IEEE 29148:2018 clause 9.6.4 requires that where the product is an element of a larger system, the publication relate that larger system to what it covers and identify the interfaces between the two. The actor list is how that boundary is stated in a form a reader can check.

An actor marked **in scope** is specified here, and a conformant solution must implement it. An actor marked **out of scope** belongs to the environment: its interface is specified here, its behaviour is not. An out-of-scope actor with which the subject exchanges anything MUST still have its interface recorded, because an actor whose interface is unspecified cannot be emulated, and a Building Block whose counterparties cannot be emulated cannot be conformance-tested in isolation.

| Actor | Role in the larger process | In scope | Interface to it | Owned or specified by |
|---|---|---|---|---|
| | | ☐ | | |
| | | ☐ | | |
| | | ☐ | | |

Every actor name entered above is a term, and carries into the candidate list produced in step 5. Actor names are among the designations most likely to be shared with adjacent publications, which makes them frequent Common Terminology candidates.

**Environment assumptions arising from out-of-scope actors**

<!-- For each out-of-scope actor, state what the test environment will have to supply in its place — an emulator, a stub, sample data, a fixture. The GovStack Architecture clause 5.4.9 requires the Testing chapter to declare these; recording them here is what makes that chapter writable later. -->

## 2.5 Explicit exclusions and adjacent domains

This table is the working part of the delimitation. Record each concept or area that the surrounding conversation treats as relevant but that this publication will not define, and name the publication that owns it. A concept listed here is not forgotten; it is assigned.

The GovStack Architecture clause 5.1.2, *Specification Scope is Technical*, is the default boundary for a Building Block specification: "GovStack specifications are technical specifications for software components", and "to avoid conflicts of specifications growing out of scope it is important to keep focus on the technical requirements of the specification". Legal and organizational matter belongs in Guides or in a Reference Architecture, not in a Building Block.

| Concept, area or actor excluded | Reason for exclusion | Publication that owns it |
|---|---|---|
| | | |
| | | |
| | | |

## 2.6 Vocabulary regime

Where the domain is already named by a regional or national regime, the Working Group SHALL decide whether to adopt that regime's vocabulary directly or to use regime-neutral designations with the regime documented as a worked instance. This decision belongs here, in step 2, because it determines the entire term-candidate list produced in step 5.

**Regime or regimes in play:**

<!-- For example: eIDAS 2 and the EUDI Architecture and Reference Framework; ICAO 9303; the domestic legal framework of a specific country. -->

**Decision:**

<!-- State whether the publication adopts the regime vocabulary directly, uses regime-neutral designations, or carries both with one marked preferred. Give the reason, since a globally applicable GovStack publication and a national profile will reasonably decide this differently. -->

## 2.7 Scope statement

<!-- Draft the scope statement that will appear in the publication. It should be readable as the answer to "what is this document about, and what is it not about", and be consistent with §2.3 and §2.4 above. -->

# 3. List of references

ISO 10241-1:2011 clause 4.3.5.2 lists the source types to be considered and states that authoritative sources SHALL be given preference. Clause 4.3.6.2 requires that a list of all sources be compiled, including the bibliographic data needed to retrieve them. This is step 3 of the procedure in `crafting_terminology.md` §5.3.

This list is retained with the specification after publication. It is what allows a later Working Group, or a reader disputing a definition, to see what the definition was based on.

## 3.1 Precedence order

Sources SHALL be consulted in this order, and an existing definition SHALL be adopted in preference to writing a new one wherever a suitable one exists. The level recorded in §3.2 is the level the source belongs to, not the order in which it happened to be found.

| Level | Source class | Examples |
|---|---|---|
| 1 | GovStack Common Terminology | The GovStack Architecture, Chapter 2, Common Terminology |
| 2 | Other GovStack publications | Other GovStack Common Terminologies; published Building Block specifications |
| 3 | GovStack foundational vocabularies | ISO/IEC 2382:2015; ISO/IEC/IEEE 24765:2017 (SEVOCAB) |
| 4 | Other international standards and specifications | ISO, IEC, ITU-T, IETF, W3C, OASIS and comparable bodies |
| 5 | Legal and regulatory instruments | Regional and national law relevant to the domain |
| 6 | Domain literature | Textbooks, reference architectures, peer-reviewed work |
| 7 | Working Group expert judgment | Used only where levels 1 to 6 yield nothing |

Clause 4.3.5.2 a) NOTE warns that definitions of concepts in laws and regulations are sometimes broader or narrower than the use of the concept in domain communication. A level 5 source is authoritative about the legal concept, not necessarily about the technical one.

## 3.2 Source register

One row per source consulted, including sources that were consulted and rejected. GovStack Common Terminology and GovStack Architecture should be included by default.

Example:

| # | Level | Full bibliographic reference | Retrieval locator |Assesment notes| Verdict |
|---|---|---|---|---|---|
|1|1| Govstack-Arch - GovStack Architecture Specification - v.2.2 |https://specs.govstack.global/architecture|Registry, Service, are taken from here| Adopted |
|2|2|ID-Related BB Common Terminology v2.0| govstack-bb-id - https://specs.govstack.global/identity/3-terminology/id-related-bb-common-terminology|Credential, Digital Credentials, Legal Entity, are taken from here|Adopted|
|3|2|Wallet Building Block Specification v1.2|govstack-bb-wallet - https://specs.govstack.global/wallet/3-terminology| Credential Holder, Holder Binding, Repudiation, are taken from here.|Adopted|
|4|4|Digital Wallet Conformance Criteria v3.4.1|https://open-credentialing-initiative.github.io/Digital-Wallet-Conformance-Criteria/latest/index.html|Definition fo DID taken from here|Adopted|
|5|5|European Digital Identity Wallet Architecture and Reference Framework|https://eudi.dev/1.4.0/arf/|Consulted|
|6|6|Digital Wallets : A New Paradigm - Convergence of User-Centric Digital Identity, Data Sharing and Payments (English). Digital Wallet Policy Series Note Washington, D.C. : World Bank Group.| http://documents.worldbank.org/curated/en/099051126133542746|Reviewed Architecture chapter| Consulted|

Citation style is to follow 2021 IEEE SA Standards Style Manual, §19. Bibliography, where standards can be referenced by designation and title, and the rest of the document types follow the Chicago Manual of Style.


Verdict values: **Adopted** (definitions taken from it), **Consulted** (informed the work, nothing taken verbatim), **Rejected** (with the reason from the evaluation).

**Translated sources.** Clause 4.3.6.2 requires that where a source is a translation, the reliability of the translation be assessed. A translated document may be used as a starting point when no original exists in the working language, when the translation is itself widely used in the domain and represents the state of the art, or when parallel official versions exist. Record any such assessment in the Evaluation column.

## 3.3 Adopted definitions register

For every definition taken from a source rather than drafted by the Working Group, record the degree of correspondence. ISO 10241-2:2012 clause 3.2 defines three, following ISO/IEC Guide 21-1:

- **Identical** — the entry is identical in technical content, terminological data model and wording, allowing minor editorial change only.
- **Modified** — the entry has clearly identified and explained technical changes or adaptations. The change SHALL be visible to the reader, not silent.
- **Not equivalent** — the content differs and the changes have not been clearly identified. This does not constitute an adoption, and SHALL NOT be published as one.

| Term | Source (# from §3.2) | Clause or entry number in source | Degree of correspondence | What was changed, and why |
|---|---|---|---|---|
| | | | | |
| | | | | |

An entry recorded as *Modified* here MUST carry its source indication and an account of the modification in the published specification. Section 7 of `crafting_terminology.md` covers the formatting.

