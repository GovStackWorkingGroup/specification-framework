
---
description: >-
  This page covers the recommended template and their contents for GovStack
  specifiations.
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/dU03bcgqfvodP4msaMaL/5-specification-framework/5.3-specification-template
---

# 5.4 Specification Template

The core aspects of what a Building Block is and what a specification for GovStack is expected to be is listed under parent page 5 Specification Framework and 4 Interoperability Architecture pages.

The following is the recommended content and document tree for a GovStack specification, with examples and other details that might be of interest.

**Most notably a specification should consist of:**

* Title and authors page with general description for a specification
* Version history of notable changes
* Terminology (exclusive to this specification)
* Key Functionalities (what are the business value provided by solutions following this specification)
* Functional Requirements and their related Service API's
* Cross-Functional Requirements (if any new CFR's are defined or core specification requirements are extended or replaced)
* Data structures (overview of required data structures for the internals of the Builind Block)
* Internal workflows (overview of internal flows of the Building Block)
* Testing (and how to test to assure compliance of solutions against this specification as well as how to test that the functions work as expected for business value)
* Other resources

**The structure is not mandatory, but it is highly recommended to be followed. New sub-pages can be added or adjusted per need.**

#### 5.4.1 Building Block Title and Description

GovStack specification must follow GovStack Specification Framework (the parent document of this Template document).

The first page of the specification should include the visual title page and a list of authors and the latest version number of the specification as well as the abstract and general description of what this specification is about. If needed the Description can be on its own sub-page.

A building block specification should have a technical name and identifier following this format: **govstack-bb-\[name]** where \[name] is a unique lowercase and alphanumeric business function describing keyword for the function, such as _govstack-bb-workflow_.

Specifications are expected to be versioned using semantic versioning.

> _Specific version of a spec can be thus referred to as govstack-bb-workflow-2.3.0_

**Description**

Use this section to set the context and purpose of the Building Block without going into detailed requirements or APIs. A good description covers:

* **Purpose and scope:** Explain why the Building Block exists, what problems it solves and where it sits in the ecosystem. It is most important to define the value of the Building Block. Keep it high‑level - save technical details for later sections.
* **Context of use:** Describe typical scenarios or sectors where the Building Block is needed.
* **Approach and design choices:** Note any overarching models or philosophies. State clearly if the description is focused on a particular approach and whether alternatives will be addressed in future revisions.
* **Sub‑components overview:** List the major internal modules or services the Building Block consists of and their roles, if any. This helps readers understand the scope before they dive into detailed functional requirements.
* **Out of scope:** Briefly mention what is explicitly not covered in this specification to manage expectations and avoid misinterpretation.

Keep paragraphs short and avoid normative language. The description is explanatory, not prescriptive. It should leave the reader with a clear understanding of the block’s purpose, its main parts and the context in which it operates.

#### 5.4.2 Version History

This should be a simple table that records the major changes between published versions.

**Recommended table format**

Use a 3-column table:

* Version
* Contributor(s)
* Comment

**What to write in “Comment”**

Keep each entry to 1-2 lines:

* What changed (one clear sentence)
* Impact: Breaking or Non-breaking

**Practical rule**

If a change affects APIs, data schemas, or REQUIRED requirements, it must be listed here and must state whether it is Breaking or Non-breaking. **Remember that major version number of specification must be changed for any breaking changes.**

#### 5.4.3 Terminology

**Purpose:** Give clear definitions for terms that are specific to this Building Block so that readers understand exactly what is meant in later sections. It is also recommended to give a link of reference to general GovStack Architecture Specification Common Terminology page without re-defining the same terms.

**What to include:**

* **Building block–specific concepts:** List only the terms that are unique to the block’s domain or have a special meaning within this specification.&#x20;
* **Short, plain-language definitions:** Use a simple two-column table (Term/Description). Keep each description to a sentence or two - long explanations belong elsewhere.

**What to avoid:**

* **Common terminology:** Do not repeat definitions that are already in the GovStack common glossary (e.g., API, Building Block, Use Case). Link to the Common Terminology page instead.

By following these pointers, the Terminology section stays focused on what makes this Building Block unique and helps implementers and reviewers share a common understanding of its vocabulary.

#### 5.4.4 Key Functionalities

**Purpose:** Provide a descriptive list of core functional description of what the Building Block does. Every building block serves a specific business-functional need for a digital government ecosystem Keep this section implementation-agnostic. Describe capabilities as outcomes, not components. Each capability should be testable later through Functional Requirements and Testing, if possible.

> Key functionalities are **not requirements**, but are a description of what the requirements achieve when implemented to the solution. Each requirement may link back to a requirement.

Imagine key functionalities as a descriptive list of capabilities provided by a Building Block.

**What to include:**

* Title of a Key Functionality (may be a separate sub-page or sub-chapter). This should be easy to reference (requirements may link to KF's based on this title).
* A short intro definition that describes the core functions this Building Block is able to perform.
* Core function and capability list, each is recommended to include:
  * What is this key functionality about and what value and outcome it brings.
  * Primary actor(s) and boundries (who benefits from it and how).
* Optional out-of-scope capabilities (what could be in scope but is not).

**What to avoid:**

* “The system uses X technology” statements.
* Requirements language _("must do this")_ - which belongs in requirements sections.
* Duplicating functional or cross-functional requirements - this should stay high-level.

Key Functionalities should be achieved through technical functions provided by Functional and Cross-Functional requirements.

#### 5.4.5 Functional Requirements

**Purpose:** Define the technical behaviors an implementation must provide. Functional requirements are actual functions of the digital component. They are most commonly functions that can be used over the API (either directly or with the UI/UX component using the API).

> How to define and classify a single Functional Requirement is described in 5.3 Requirements Model.

Functional requirements are requirements that are business-only, as in tied to Key Functionalities and core value and capability provided by the Building Block. Functional requirements should either be observeable (and potentially automatically testable) or at least auditable to assure that the solution works as expected by the specification.

**What to include:**

* Each requirement should describe what this requirement is about and what value it brings to the organization.
* Requirement name or title should be a single sentence, as brief yet as accurate as possible.
  * After the title, identifier and classifiers you can explain the requirement and its details further, include references, sources, links and other reading materials if needed.
* Each functional requirement should follow GovStack Specification Framework and identification and classification rules. These are listed in the parent document of this template.
* Include a reference tag to the requirement, such as `govstack-bb-wallet-fr#req-1`
* If this requirement serves only a specific Key Functionality, add a reference such as `KF: Identity Verification` _(multiple can be added)_

**Assumptions and prerequisites**

For each functional requirement, explicitly state:

* Preconditions: what must be true, ideally what can be validated.
* Required dependencies: external dependencies or conditions that must be available (if any)
* Data readiness: required identifiers, registrations or reference data
* Operational prerequisites: configuration, keys, environment setups etc.

If a prerequisite is not required for actual interoperability, it is recommended to be moved to Implementation Guides or other documentation instead. _In other words, "how to implement" is not a good functional requirement._

**What to avoid:**

* Do not bundle multiple obligations into one requirement.

Remember that internal design mandates do not directly affect functional interoperability. Functional requirements should focus on actual business functions and capabilities.

**5.4.5.1 Service API's**

This section defines a subset of Functional Requirements. They are an external API surface that implementations of this Building Block must expose. The APIs listed here form the minimum contract for interoperability - other Building Blocks and consuming applications depend on these endpoints to integrate with the Building Block's functional capabilities. Implementations may offer additional endpoints, but every endpoint defined here must be present and must behave as specified.

All APIs must be defined using the OpenAPI (Swagger) standard. The canonical OpenAPI definition files (YAML) should be maintained in the Building Block's GitHub repository and linked from this section. A live API documentation portal hosted by GovStack should serve as the browsable reference for implementers and testers.

_**A Functional Requirement may itself be a Service API.** In this case a defined the Service API model must follow Requirements Model, except with the added features listed below._

**What to include:**

An introductory paragraph that states the purpose of the API surface. **Every API endpoint listed here either is by itself already or should trace back to at least one Functional Requirement** - if an endpoint cannot be linked to a requirement, it either belongs in an implementation guide or the requirement is missing.

It is recommended to group endpoints by functional domain. Each group should have a short description of the domain it covers.

For each endpoint, provide:

* HTTP method and path (e.g. GET /resources/{resourceId})
* A brief description of what the endpoint does and what value it provides to the caller
* Path parameters, query parameters and request body with types and constraints
* Response codes with meaning (at minimum: success, not found, server error)
* A reference to which functional requirement(s) this endpoint satisfies

End the section with a summary table or numbered list of all endpoints for quick reference. This gives readers a single place to scan the full API without scrolling through individual definitions.

**What to avoid:**

* It is not recommended to describe internal implementation details behind the endpoint. The API contract defines external behavior only.
* Do not introduce new behavioral requirements here that are not already covered in section Functional Requirements.
* Do not mandate specific technology stacks or frameworks for serving the API.
* Do not duplicate cross-functional requirements that are already covered in govstack-cfr. Reference them instead.

**Testing:**

Every endpoint defined here should have corresponding test cases in Testing section.

#### 5.4.6 Cross-Functional Requirements

**Purpose:** Define the internal technical and quality behaviors an implementation must provide, including non-feature constraints that affect security, privacy, performance and operations of the building block. Cross-functional requirements are technical requirements.

**It is mandatory that all GovStack building blocks comply with the all central cross-functional requirements of GovStack (govstack-cfr). In the Building Block specification you should not duplicate or create conflicts with requirements that are already in govstack-cfr.**

> Cross-functional requirements of a building block are not required to be defined for a specification, there may be building blocks that have no cross-functional requirements at all! However if a Building Block specification EXTENDS or REPLACES core cross-functional requirements then it is highly recommended to have a separate sub-page for cross-functional requirements instead of mixing them among Functional Requirements.

Cross-functional requirements of a building block specification must be considered as an extension and to be compatible with core GovStack cross-functional requirements. If cross-functional requirements are defined for a specification then it is recommended to categorize them similarly (though you do not need to have subpages for each, they can be just on the same page):

* development
* deployment
* architecture
* quality
* security
* data

**You must not define new cross-functional-requirements that are a repeat of an already existing cross-functional requirement of GovStack.** It is also important not to overwrite IMMUTABLE requirements. But you can extend requirements that are EXTENSIBLE and you can replace requirements that are REPLACEABLE as described in the Specification Framework.

In case of extending or replacing a requirement, reference to the original requirement is required, for example your CFR can define a new CFR that changes govstack-cfr-data#req-3 as follows

```
govstack-bb-wallet-cfr#req-1 extends govstack-cfr-data#req-3
```

And in case you replace a requirement (for example #6) entirely:

<pre><code><strong>govstack-bb-wallet-cfr#req-2 replaces govstack-cfr-data#req-6
</strong></code></pre>

In every other way the cross-functional requirements are to be defined with similar content recommendation and structure with Functional Requirements.

Also remember to include the reference tag for each requirement if you define it, such as `govstack-bb-wallet-cfr#req-7`

#### 5.4.7 Data Structures

**Purpose:** This section describes the minimum information model the Building Block uses internally. It defines the required resources and fields so implementations can exchange data consistently and evolve schemas without breaking consumers.

This section is optional, but it can be helpful for providing understanding with more complicated data structures of a building block.

Goal of this section is also to define the minimum information model needed for the building block and provide a stable reference for schemas and data compatibility.

**What to include:**

* Resource model overview: entities and relationships at a conceptual level.
* Minimum required fields for each resource: name, type.
* Validation rules: required fields, formats, enums and constraints.
* Extension rules: how to add fields safely and how consumers must handle unknown fields.
* References to canonical schema artifacts if you maintain them elsewhere.

**What to avoid:**

* Optional fields that are treated as mandatory without stating it.
* Ambiguous identifiers or unspecified uniqueness constraints.

#### 5.4.8 Internal Workflows

This section describes the main end-to-end flows the Building Block must support, especially where it interacts with other Building Blocks or external systems. The goal is to make the expected sequence of steps clear and to remove ambiguity about who does what, when and with what inputs and outputs.

Document a small set of workflows that cover the common path and at least one failure and recovery path. For each workflow list actors, preconditions, numbered steps, key decisions (authz, validation, routing) and postconditions. Include what must be logged and what errors must be returned. Diagrams are fine but the text must stand on its own.

#### 5.4.9 Testing

This section defines how conformance is verified. It links requirements to testable criteria and provides the basis for validating candidate implementations consistently. Goal of this is to make compliance verifiable and repeatable and ensure that at least every REQUIRED rule has a way to test it.

**What to include:**

* Conformance definition: what it means to be compliant.
* Every REQUIRED requirement mapped to at least one verification item.&#x20;
  * RECOMMENDED requirements are also good to be covered, if possible.
* Test types: contract tests, negative tests, security checks, performance checks and observability checks as well as required test artifacts: fixtures, sample data and environment assumptions.

**What to avoid:**

* Test descriptions without expected outcomes.
* Dependence on a single vendor toolchain.
* Non-deterministic tests that cannot be repeated reliably.

#### 5.4.10 Other Resources

This section contains supporting material that is useful but not part of the requirements. It is where you put context and references that help readers understand decisions and future direction without cluttering functional and cross-functional requirements.

**This includes links to relevant Implementation Guides and Use-Case documents and references. Additionally it can provide a key decision logs or other supplemental material that has impacted the creation and lifecycle of this specification.**

Include links to future considerations and out-of-scope assumptions. Add pointers to example schemas, reference configurations, known limitations and issue trackers if they help implementers. Do not introduce new rules here. If something affects compliance or interoperability it belongs in the requirements or testing sections.
