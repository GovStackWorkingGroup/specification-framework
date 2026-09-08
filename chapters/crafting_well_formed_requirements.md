---
title: Crafting Requirements
references:
  - IEEE Std 1233-1998, IEEE Guide for Developing System Requirements Specifications, IEEE Computer Society, 1998.
  - ISO/IEC/IEEE 29148:2018(E) Systems and software engineering — Life cycle processes — Requirements engineering, International Organization for Standardization, International Electrotechnical Commission, 2018.
  - IEEE Std 830-1998, IEEE Recommended Practice for Software Requirements Specifications, IEEE Computer Society, 1998.
claude_prompt: |
  From the content of the references propose a way to craft requirements for govstack that also uses GovStack Architechture. From the references use these sections as input:
    - ISO/IEC/IEEE 29148:2018 sections 5.2.3, 5.2.4, 5.2.5 and 5.2.6
    - IEEE Std 1233-1998, section 6 on well formed requirements
    - IEEE Std 830-1998, section 4.8 and 5.3
---

# Introduction

## 0.1 Overview

Every GovStack Building Block specification is, at its core, a set of Functional Requirements (FR) and Cross-Functional Requirements (CFRs) that a solution must satisfy to be considered GovStack compliant. This document addresses the requirement statements themselves — the individual `#req-N` items that GovMarket scores solutions against, that procurement documents cite by identifier, and that test suites are built from.

Requirements written informally, inconsistently, or ambiguously undermine everything downstream: they cannot be reliably tested (defeating the OBSERVABLE/AUDITABLE model), they cannot be compared across Building Blocks, and they weaken the legal and procurement value that a `govstack-bb-*#req-N` reference is meant to carry. 

Because Working Groups draft requirements independently of one another, GovStack needs one shared, standards-based method for crafting a requirement statement, independent of which domain the Building Block covers. This document proposes that method.

<!-- This is not a new invention. GovStack's requirement model — the identifier scheme, the requirement-level classifier (REQUIRED/RECOMMENDED/DRAFT/DEPRECATED), the observability classifier (OBSERVABLE/AUDITABLE), and the mutability classifier (IMMUTABLE/EXTENSIBLE/REPLACEABLE/INAPPLICABLE) described in the GovStack Architecture Specification Framework — already encodes many of the characteristics that requirements engineering literature has established for decades. This document makes those connections explicit and gives Working Groups a concrete, repeatable procedure for writing a requirement, grounded in ISO/IEC/IEEE 29148:2018, IEEE Std 1233-1998, and IEEE Std 830-1998. -->

## 1. Scope

This document applies to the crafting of individual Functional Requirements and Cross-Functional Requirements within GovStack specifications, as defined by the GovStack Architecture Specification Framework (`5.2-building-block-specification.md` and `5.3-specification-template.md`). It has the following objectives:

1. Define what a well-formed GovStack requirement statement looks like, structurally.
2. List the characteristics a single requirement and a set of requirements MUST or SHOULD exhibit, drawn from the normative references.
3. Map those standards-based characteristics onto GovStack's existing identifier, requirement-level, observability and mutability classifiers, so authors understand how the classifiers already discharge (or extend) the obligations the standards describe.
4. Identify common pitfalls when writing requirements, and how to avoid them in a GovStack context.

This document does NOT cover:

- How Key Functionalities are identified or described — see `5.3-specification-template.md §5.3.4` in the GovStack Architecture Specification.
- Terminology and definitions — see `crafting_terminology.md`.
- Working Group lifecycle, consensus-building, or how a specification (and therefore its requirements) is approved, versioned, or retired — see `working_groups.md` and `publication_tracks.md`.
- API contract design (OpenAPI) — see `5.3-specification-template.md §5.3.5.1`.

This document uses RFC2119 and RFC8174 keywords (MUST, SHOULD, MAY, etc., in uppercase) as defined in `docs/index.md`.

## 2. Normative References

The following documents contain the concepts and vocabulary used as the foundation for requirements work in GovStack:

- ISO/IEC/IEEE 29148:2018(E), Systems and software engineering — Life cycle processes — Requirements engineering, International Organization for Standardization, International Electrotechnical Commission, 2018. (Sections used: 5.2.3 Transformation of needs into requirements, 5.2.4 Requirements construct, 5.2.5 Characteristics of individual requirements, 5.2.6 Characteristics of a set of requirements, and 5.2.7 Requirement language criteria, which 5.2.5/5.2.6 depend on.)
- IEEE Std 1233-1998, IEEE Guide for Developing System Requirements Specifications, IEEE Computer Society, 1998. (Section used: Clause 6, Well-formed requirements — 6.1 Definition of a well-formed requirement, 6.2 Properties of a requirement, 6.3 Categorization, 6.4 Pitfalls.)
- IEEE Std 830-1998, IEEE Recommended Practice for Software Requirements Specifications, IEEE Computer Society, 1998. (Sections used: 4.3 Characteristics of a good SRS, 4.7 Embedding design in the SRS, 4.8 Embedding project requirements in the SRS, and 5.3 Specific requirements.)

## 3. From Need to Requirement

A GovStack requirement does not start life as a requirement. ISO/IEC/IEEE 29148:2018 §5.2.3 describes requirements engineering as the transformation of stakeholder needs, goals, and objectives — initially latent, undefined, and possibly inconsistent — into "objectively adequate, structured and more formal statements of stakeholder requirements and goals" that meet the characteristics of §5.2.4 through §5.2.6. This transformation is recursive: it happens again at every lower level of the system structure (29148 §5.2.3, second paragraph).

The GovStack Specification Template operationalizes exactly this transformation, in this order:

1. **Key Functionalities** (`5.3-specification-template.md §5.3.4`) are the GovStack equivalent of the stakeholder need or business-level concern from 29148 §5.2.3: a descriptive, outcome-level statement of what the Building Block must be able to do for a stakeholder, deliberately *not yet* written as a requirement.
2. **Functional Requirements** (`§5.3.5`) are the well-formed, verifiable transformation of a Key Functionality into a testable statement about the system: exactly the move that 29148 §5.2.3 describes as leading stakeholders "from those initial, often latent needs to a set of objectively adequate, structured and more formal statements."
3. **Cross-Functional Requirements** (`§5.3.6`) are the technical, quality, and constraint layer — GovStack's parallel to what 29148 §5.2.4 calls constraints that "restrict the design solution or implementation" and to IEEE 830 §5.3.5/§5.3.6 (design constraints and software system attributes).

A requirement that cannot be traced back to a Key Functionality (for an FR) or to the CFR category it belongs to (for a CFR) has skipped this transformation and is not well-formed under 29148 §5.2.3 — it should be either attached to an existing Key Functionality, or the Key Functionality list should be revised first. Every `#req-N` a Working Group writes SHOULD be traceable, in the specification text itself, to the Key Functionality it realizes.

## 4. Structure of a GovStack Requirement Statement

### 4.1 The generic construct

ISO/IEC/IEEE 29148:2018 §5.2.4 defines a well-formed specified requirement as one that contains one or more of:

- it shall be met or possessed by a system to solve a problem, achieve an objective, or address a stakeholder concern;
- it is qualified by measurable conditions;
- it is bounded by constraints;
- it defines the performance of the system for a specific stakeholder or capability — not a capability of the user, operator, or other stakeholder;
- it can be verified.

IEEE Std 1233-1998 §6.1 arrives at the same construct from a different name: a well-formed requirement is "a statement of system functionality (a capability) that can be validated, that must be met or possessed by a system to solve a customer problem or to achieve a customer objective, and that is qualified by measurable conditions and bounded by constraints." §6.1.1–§6.1.3 decompose this into three parts:

- **Capability** — the fundamental function or feature, stated solution-independently (what the system does, not how).
- **Condition** — a measurable qualitative or quantitative attribute that qualifies the capability (it is meaningless on its own, only in relation to a capability).
- **Constraint** — a limit imposed on the solution by circumstance, force, or compulsion (existing interfaces, physical limits, law, budget, pre-existing platform, etc.), which limits the designer's options absolutely.

§6.1.4 gives a worked example: "Move people from New York to California at a maximum speed of 5300 km/hr" decomposes into Capability ("move people between Los Angeles and New York"), Condition ("cruising speed of 2500 km/hr"), and Constraint ("maximum speed of 5300 km/hr").

29148 §5.2.4 additionally recommends a syntax for the requirement sentence: **[Condition] [Subject] [Action] [Object] [Constraint of Action]**, e.g. *"When signal x is received [Condition], the system [Subject] shall set [Action] the signal x received bit [Object] within 2 seconds [Constraint of Action]."* Not every requirement needs every bracket — the minimal form is **[Subject] [Action] [Constraint of Action]**.

### 4.2 How this maps onto a GovStack requirement

`5.2-building-block-specification.md §5.2.10` already shows the shape of a GovStack requirement:

```
#7 Must return current weather for the API request /api/v1/weather
RECOMMENDED REPLACEABLE
```

Read against 29148 §5.2.4 and IEEE 1233 §6.1, this maps as follows:

| Standards concept | GovStack element |
|---|---|
| Capability (1233 §6.1.1) / Subject+Action+Object (29148 §5.2.4) | The requirement title line, one sentence |
| Condition, Constraint (1233 §6.1.2/6.1.3; 29148 §5.2.4) | The elaboration text below the title, and the preconditions/dependencies GovStack already requires (`§5.3.5`: preconditions, dependencies, data readiness, operational prerequisites) |
| "Can be verified" (29148 §5.2.4) / Validatable (1233 §6.2(d)) | The Observability classifier (OBSERVABLE/AUDITABLE) |
| Ranking by priority (29148 §5.2.4, last paragraph) / Priority (1233 §6.3(b)) | The Requirement Level classifier (REQUIRED/RECOMMENDED/DRAFT/DEPRECATED) |
| Requirement is met by "a system" (29148 §5.2.4) | Every GovStack FR/CFR is scoped to the Building Block, never to a service or business process (`5.1-specification-scope.md §5.1.2`) |

Authors SHOULD therefore write each requirement title as a single **Subject + Action + Object** sentence (a Capability, per IEEE 1233 §6.1.1), stated solution-independently ("what," not "how" — 29148 §5.2.7, first paragraph; IEEE 830 §4.7). Any Condition (a measurable qualifier) and Constraint (a bounding limit) that the Capability needs SHOULD then be spelled out explicitly in the elaboration text, using the preconditions/dependencies structure GovStack's template already asks for. Where an elaboration paragraph is doing the work of a Condition or a Constraint, authors are encouraged to say so plainly (e.g. "Condition: ..." / "Constraint: ...") rather than leaving the distinction implicit — this makes the requirement's boundaries auditable later, per 1233 §6.1.2–§6.1.3.

### 4.3 Obligation language: GovStack's classifier line replaces the modal verb

29148 §5.2.4 recommends agreeing in advance on the keywords that signal a requirement's binding force: `shall` for mandatory provisions, `will` for statements of fact/futurity, `should` for non-mandatory preferences, `may` for non-mandatory suggestions, and it explicitly warns against `must` because of "potential misinterpretation," against negative requirements ("shall not"), against passive voice ("it is required that"), and against "shall be able to."

GovStack has already made this choice once, at the specification level, by adopting RFC2119/RFC8174 (`docs/index.md`). Within a requirement, GovStack narrows this further: **the classifier line beneath the title (REQUIRED / RECOMMENDED / DRAFT / DEPRECATED) is the canonical, machine-readable carrier of the requirement's obligation strength**, not the verb used in the title sentence. This is consistent with, but stricter than, 29148 §5.2.4: where the standard is satisfied by consistent use of "shall/should/may" in prose, GovStack additionally requires that strength to be re-stated as a discrete, versionable classifier token, because GovMarket and compliance tooling read the classifier, not the sentence.

Practical consequence for authors:

- The requirement title MAY use a plain declarative form ("Returns current weather...") or an RFC2119 keyword ("Must return current weather...") — both are acceptable, but the title text MUST NOT be the sole indicator of whether the requirement is mandatory. The classifier line is authoritative.
- Within the elaboration text, when sub-clauses need their own binding strength (e.g. "implementations SHOULD additionally support..."), authors SHOULD use RFC2119 keywords in uppercase, per `docs/index.md`.
- Avoid the specific unbounded/ambiguous term classes 29148 §5.2.7 lists, because these break the Unambiguous and Verifiable characteristics discussed in §5 below: superlatives ("best," "most"); subjective language ("user friendly," "cost effective"); vague pronouns ("it," "this," "that"); ambiguous adverbs/adjectives and logical statements ("almost always," "or," "and/or" — 29148 §5.2.7 Note 1 recommends splitting these into separate requirements); open-ended terms ("provide support," "as a minimum"); comparative phrases ("better than"); loopholes ("if possible," "as appropriate"); terms implying totality ("all," "always," "never," "every"); and incomplete references (a reference without its date/version, or without the applicable clause).

## 5. Characteristics of a Well-Formed GovStack Requirement

The three references converge on an overlapping — not identical — list of characteristics. This section merges them into one practical checklist for GovStack Working Groups, and states which GovStack element already discharges each obligation.

### 5.1 Necessary and Appropriate

29148 §5.2.5: a requirement is **Necessary** if omitting it leaves a capability or characteristic unfulfilled by any other requirement, and it is currently applicable (not obsolete). It is **Appropriate** if its level of detail matches the level of the entity it describes, avoiding unnecessary constraints on architecture or design.

*GovStack mapping:* Necessity is enforced procedurally, through Working Group review and consensus (`working_groups.md`) rather than through a classifier field — this document does not prescribe a new classifier for it and flags this as an open point (see §10). A requirement that is no longer necessary MUST be reclassified DEPRECATED rather than deleted, per `5.2-building-block-specification.md §5.2.5`, which mirrors 29148 §5.2.5's instruction that obsolete requirements be "clearly identified" rather than silently dropped.

### 5.2 Unambiguous

29148 §5.2.5: stated so it "can be interpreted in only one way," simply and easy to understand. IEEE 1233 §6.2(b) states the identical property. IEEE 830 §4.3.2 adds that each characteristic of the product must be described using a single, unique term, and that ambiguity is best caught by independent review of natural-language text (§4.3.2.1).

*GovStack mapping:* apply the §4.3 term-list above during drafting and again during Working Group review; keep a single term per concept, reusing `crafting_terminology.md` definitions rather than inventing synonyms inside a requirement.

### 5.3 Complete

29148 §5.2.5: sufficiently describes the capability/constraint/quality factor without needing other information. IEEE 830 §4.3.3 defines Complete for a whole SRS as including all significant requirements and defining system responses to both valid and invalid inputs, and treats any remaining TBD ("to be determined") as making the document incomplete unless the TBD states why it is unresolved and how/when it will be resolved (§4.3.3.1).

*GovStack mapping:* GovStack's **DRAFT** requirement-level classifier is the structural answer to 830 §4.3.3.1's TBD problem — a DRAFT requirement reserves an identifier and documents an unresolved proposal explicitly, rather than leaving a silent gap, and (per `5.2-building-block-specification.md §5.2.5`) does not fail compliance while unresolved. This is a genuine GovStack refinement of the standard: instead of an informal TBD annotation, GovStack gives the "not yet resolved" state its own first-class, versioned classifier.

### 5.4 Singular

29148 §5.2.5: "states a single capability, characteristic, constraint or quality factor" (though it may have multiple conditions under which it applies — Note 2).

*GovStack mapping:* this is already explicit GovStack guidance — `5.3-specification-template.md §5.3.5` instructs authors to "not bundle multiple obligations into one requirement." The [Subject][Action][Object] sentence form from §4.1 above naturally enforces this: one subject, one action, one object per requirement.

### 5.5 Feasible

29148 §5.2.5 (individual) and §5.2.6 (set): realizable within constraints (cost, schedule, technical) with acceptable risk; feasibility "includes the concept of 'affordable'" (§5.2.6, Note 4). IEEE 1233 §6.3(d) treats Feasibility as a categorization attribute assigned jointly by customer and analyst.

*GovStack mapping:* GovStack does not currently carry an explicit feasibility classifier or attribute; feasibility is judged during Working Group consensus-building. Authors SHOULD document feasibility reasoning (e.g. reliance on an existing standard, a proof of concept, or vendor input) in the requirement's elaboration text or in the specification's decision log (`5.3-specification-template.md §5.3.10`), since GovStack has no dedicated field for it today (see §10).

### 5.6 Verifiable

29148 §5.2.5: structured and worded so realization can be proven to the customer's satisfaction; verifiability is enhanced when the requirement is measurable. IEEE 1233 §6.2(d) calls this **Validatable** ("the requirement should have the means to prove that the system satisfies the requirement"). IEEE 830 §4.3.6 is the most operational of the three: an SRS is verifiable "if, and only if, every requirement stated therein is verifiable... there exists some finite cost-effective process with which a person or machine can check that the software product meets the requirement," gives "works well," "good human interface," and "shall usually happen" as non-verifiable examples, and contrasts a vague requirement ("An operator shall not have to wait for the transaction to complete") with a verifiable one ("Output of the program shall be produced within 20 s of event × 60% of the time; and shall be produced within 30 s of event × 100% of the time" — §5.3.3).

*GovStack mapping:* this is precisely what the **Observability classifier** (OBSERVABLE/AUDITABLE) formalizes (`5.2-building-block-specification.md §5.2.6`). GovStack goes further than the base standards by requiring authors to name *how* a requirement is verified: OBSERVABLE requirements must be automatically testable, manually testable with a documented pass/fail procedure, or operationally observable; AUDITABLE requirements must be paired with a list of evidence artefacts. Every requirement title SHOULD therefore be checked against the IEEE 830 §5.3.3 test: could it be rewritten with a concrete, measurable pass/fail condition? If not, it is not yet a well-formed requirement — it is still a goal (29148 §5.2.4's "should"/preference category) and belongs in the Key Functionalities section, not in Functional/Cross-Functional Requirements (`5.3-specification-template.md §5.3.4` explicitly separates the two for this reason).

### 5.7 Correct

29148 §5.2.5 lists Correct alongside Verifiable, Feasible, and Conforming. IEEE 830 §4.3.1: an SRS is correct "if, and only if, every requirement stated therein is one that the software shall meet," checked by comparing against any superior specification and against actual customer need; traceability makes this easier (§4.3.8).

*GovStack mapping:* for a Building Block specification, the "superior specification" IEEE 830 refers to is `govstack-cfr` — every Building Block specification is defined as extending it (`5.2-building-block-specification.md §5.2.2`, §5.2.9). A Building Block requirement that conflicts with an IMMUTABLE core CFR is, by 830 §4.3.1's definition, not correct.

### 5.8 Conforming

29148 §5.2.5: "the individual items conform to an approved standard template and style for writing requirements, when applicable."

*GovStack mapping:* this document *is* that standard template and style for GovStack. A requirement conforms when it follows the structure in §4 and uses the identifier/classifier syntax defined in `5.2-building-block-specification.md`.

### 5.9 Traceable

IEEE 830 §4.3.8: an SRS is traceable if the origin of each requirement is clear (**backward** traceability, to earlier documents) and each requirement can be referenced from later documents (**forward** traceability, via a unique name or reference number). IEEE 1233 §6.2(c) states the same property directly: "for each requirement it should be feasible to determine a relationship between specific documented customer statement(s) of need and the specific statements in the definition of the system... as evidence of the source of a requirement."

*GovStack mapping:* forward traceability is the **Unique Identifier** (`govstack-bb-<name>-fr#req-N`, `5.2-building-block-specification.md §5.2.4`) — never reused, even when a requirement is deprecated. Backward traceability is the **Key Functionality link** described in §3 above: every FR SHOULD name the Key Functionality it realizes, and every extending/replacing CFR MUST cite the base requirement it extends or replaces (`5.3-specification-template.md §5.3.6`, e.g. `govstack-bb-wallet-cfr#req-1 extends govstack-cfr-data#req-3`).

### 5.10 Set-level characteristics (29148 §5.2.6)

29148 §5.2.6 states that these apply to the whole set of requirements, not to any individual one: **Complete** (no unresolved TBx and all requirement types/life-cycle stages covered), **Consistent** (no conflicting requirements, homogeneous units, one term per concept throughout), **Feasible** (realizable within constraints), **Comprehensible** (clear what is expected and why), and **Able to be validated** (satisfying the set is practicable and leads to the stakeholder need being met).

*GovStack mapping:* a GovStack Building Block specification's set-level completeness and consistency are structurally supported by the fact that every specification extends `govstack-cfr` (so the "set" is never just the Building Block's own requirements — it always includes the inherited core baseline, `5.2-building-block-specification.md §5.2.2`), and by the Mutability classifier, which formalizes *how* a Building Block's set of requirements may diverge from the core set without becoming inconsistent (IMMUTABLE items cannot be touched at all; EXTENSIBLE items can only be tightened; REPLACEABLE items can be swapped if the external contract is preserved). This mutability model is a GovStack extension beyond what 29148 §5.2.6 or IEEE 830 describe — neither standard addresses a *portfolio* of specifications inheriting from a shared baseline, only a single SRS. "Able to be validated" is operationalized by the specification's Testing section (`5.3-specification-template.md §5.3.9`), which requires every REQUIRED requirement to be mapped to at least one verification item.

## 6. Requirement Attributes and Categorization

IEEE 1233 §6.3 and 29148 §5.2.8 both recommend attaching descriptive attributes to requirements to support analysis: identification, priority/stakeholder priority, criticality, feasibility, risk, source, type, version number, owner, and rationale. IEEE 830 §5.3 (introductory paragraph) similarly asks that specific requirements be "cross-referenced to earlier documents" and "uniquely identifiable."

GovStack does not require a full attribute set per requirement (owner, risk, rationale, etc. are not currently tracked fields). It requires, at minimum, the subset that its classifier model already encodes:

| Standards attribute | GovStack equivalent | Where defined |
|---|---|---|
| Identification (1233 §6.3(a); 29148 §5.2.8.2) | `#req-N`, unique and never reused | `5.2-building-block-specification.md §5.2.4` |
| Priority (1233 §6.3(b)); Ranked for importance/stability (830 §4.3.5) | Requirement Level classifier (REQUIRED/RECOMMENDED/DRAFT/DEPRECATED) | `§5.2.5` |
| Feasibility (1233 §6.3(d)); Feasible (29148 §5.2.5/5.2.6) | Not classified — documented in prose (see §5.5) | — |
| Type (1233 §6.3(g)) | FR vs. CFR, and CFR category (development/deployment/architecture/quality/security/data) | `5.3-specification-template.md §5.3.6` |
| Version Number (29148 §5.2.8.2) | Specification version (major.minor.patch) — individual requirements are not separately versioned | `5.2-building-block-specification.md §5.2.2` |

A Working Group that finds it needs Risk, Criticality, or Rationale tracking for a specific Building Block MAY document these informally in the requirement's elaboration text or in the "Other Resources" section (`5.3-specification-template.md §5.3.10`); GovStack does not currently define a formal field for them (see §10).

## 7. Organizing a Requirement Set

IEEE 830 §5.3.7 lists several ways of organizing the specific-requirements section of an SRS when the flat list becomes too large to read: by **system mode**, **user class**, **object**, **feature**, **stimulus**, **response**, or **functional hierarchy** (§5.3.7.1–§5.3.7.7), and notes these can be combined (§5.3.8).

GovStack's template has already made this choice for Functional Requirements: they are organized by **Key Functionality** — closest to IEEE 830's "feature" organization (§5.3.7.4: "an externally desired service by the system... described in a sequence of stimulus-response pairs"), since a Key Functionality is exactly that externally desired, business-value-bearing service. Cross-Functional Requirements are organized by **category** (development, deployment, architecture, quality, security, data — `5.3-specification-template.md §5.3.6`), which is closer to IEEE 830's "system attributes" grouping (§5.3.6 of 830: reliability, availability, security, maintainability, portability).

Working Groups SHOULD NOT invent a third, competing organizational scheme (e.g. by user class or by API endpoint) for the same requirement set; if endpoints or user classes need to be cross-referenced, do so via the Service API's section (`§5.3.5.1`) linking back to the Key-Functionality-organized FRs, not by re-ordering the requirements themselves.

## 8. Common Pitfalls

IEEE 1233 §6.4 lists five pitfalls in building well-formed requirements. Restated for GovStack authors:

1. **Design and implementation** (1233 §6.4(a); 830 §4.7). A requirement specifies an externally visible function or attribute, not a design. Do not write "the system shall use a PostgreSQL database" when the actual need is "the system shall persist X" — 830 §4.7 lists partitioning software into modules, allocating functions to modules, describing information/control flow, and choosing data structures as design items that normally do not belong in requirements at all. 830 §4.7.1 allows an exception only where a constraint (e.g. security or safety) genuinely restricts the design.
2. **Overspecification** (1233 §6.4(b)). Do not write a requirement that names an exact commercial product to be bought rather than built, states tolerances too deep within the conceptual system, or states a solution instead of a need.
3. **Overconstrained** (1233 §6.4(c)). Do not add constraints beyond what is actually needed — 1233's example is requiring a 3-hour recharge time when 12 hours would have been sufficient; this needlessly eliminates otherwise valid solutions.
4. **Unbounded** (1233 §6.4(d)). Avoid relative statements that cannot be verified ("minimize noise" instead of "noise levels should not exceed..."), open-ended lists ("including but not limited to..." / "etc."), and subjective or vague terms ("user friendly," "quick response time," "cost effective"). This overlaps directly with the term list in §4.3 above (29148 §5.2.7).
5. **Undocumented assumptions** (1233 §6.4(e)). If a requirement depends on an assumption (e.g. that a particular external standard or system will exist), document the assumption alongside the requirement, and document an alternative if the assumption might not hold.

Two further pitfalls come from IEEE 830, both about scope creep into the wrong document:

6. **Embedding project requirements in a specification** (830 §4.8). Cost, delivery schedules, reporting procedures, development methods, quality assurance process, validation/verification criteria, and acceptance procedures are project requirements, not product requirements, and MUST NOT appear inside a GovStack requirement. These belong in a Working Group's own process documentation, not in the specification (`working_groups.md`).
7. **"How to implement" masquerading as a requirement.** `5.3-specification-template.md §5.3.5` already states this GovStack-specific version of 830 §4.7/§4.8: a precondition that is not required for actual interoperability belongs in an Implementation Guide, not in the Functional Requirement.

## 9. Worked Example

Starting from a Key Functionality such as "citizens can retrieve the current weather for their location," an early, un-well-formed draft might read:

> The system should provide good weather information to users quickly.

This fails Unambiguous (29148 §5.2.5 — "good," "quickly" are unbounded per §5.2.7), Verifiable (830 §4.3.6 — no finite check exists for "good"), and Singular is unclear (does "provide" cover retrieval, formatting, and delivery as one obligation, or several?). It also states an obligation on "users" rather than performance of the system for a stakeholder, which 29148 §5.2.4 excludes ("not a capability of the user, operator or other stakeholder").

Applying the Subject-Action-Object construct (§4.1), separating Capability from Condition/Constraint (IEEE 1233 §6.1), and attaching the GovStack classifier line, this becomes:

```
govstack-bb-weather-fr#req-7

#7 Returns current weather for the API request GET /api/v1/weather
REQUIRED OBSERVABLE REPLACEABLE

Realizes Key Functionality: "Provide current weather information."

Preconditions: a valid location parameter (ISO 3166-1 country code plus
postal code, or lat/long pair) is supplied in the request.

The response shall include temperature, condition and observation
timestamp for the requested location, returned within 2 seconds for
95% of requests under normal load.
```

Here the title carries the Capability (Subject: the system; Action: returns; Object: current weather for a defined API request — 29148 §5.2.4); the classifier line carries obligation strength (REQUIRED) and verification method (OBSERVABLE) instead of a modal verb (§4.3 above); the Condition (response contents) and Constraint (response time, expressed measurably per 830 §5.3.3's own worked example) are stated in the elaboration text; the Key Functionality link supplies backward traceability (§5.9); and the identifier `govstack-bb-weather-fr#req-7` supplies forward traceability (830 §4.3.8).

## 10. Relationship to Other GovStack Processes and Open Questions

This document only covers how to craft a single requirement and organize a set of requirements. It deliberately does not restate:

- How a specification (and the requirements inside it) moves through drafting, review, and consensus — see `working_groups.md`.
- How a specification and its requirements are published, versioned, and retired across major/minor/patch releases — see `publication_tracks.md` and `5.2-building-block-specification.md §5.2.2`.
- How Key Functionalities themselves are identified — see `5.3-specification-template.md §5.3.4`.

Two points raised while cross-referencing the standards against the current GovStack model could not be resolved from the source material alone and are flagged here rather than answered speculatively:

- **Feasibility (§5.5) and Necessity (§5.1)** have no dedicated GovStack classifier or attribute today, unlike Priority (→ Requirement Level) and Verifiability (→ Observability). Whether GovStack should formalize these as attributes, or continue to treat them as matters for Working Group consensus and prose documentation, is a decision for the GovStack Architecture Working Group, not this document.
- **Risk, Criticality, Owner, and Rationale** (29148 §5.2.8.2; IEEE 1233 §6.3) are attributes the standards recommend tracking per requirement. GovStack's current template has no dedicated field for any of them; this document recommends documenting them informally (elaboration text or `§5.3.10 Other Resources`) until/unless the Architecture Specification Framework decides otherwise.
