---
title: Specification Model
---

Specification consists of a description and one or more (mostly technical) requirements. A Building Block specification is an extension of GovStack Architecture Specification's cross-functional requirements, which means that every specification (and thus every resulting software solution) must also comply with core cross-functional requirements.

<figure><img src="../.gitbook/assets/Govstack extension.png" alt="" width="320"><figcaption><p>Each building block includes all core requirements</p></figcaption></figure>

In other words, even if a new Building Block may only have 5 requirements, it will also automatically have all of the requirements of GovStack core Architecture Specification.

#### 5.2.1 Specification Identifiers and Naming Conventions

Identifiers are important for referencing within GovStack as well as outside GovStack (for example in tender documents). The names of specifications for Building Blocks need to be unique and never re-used. When a new specification is created, make sure it does not replicate an existing one.

The schema used is **govstack-\[document-type]-\[name]** for the specification. When referring to a version, then the schema is, **govstack-\[document-type]-\[name]-\[version]**. When referring to a requirement then the schema is similarly: **govstack-\[document-type]-\[name]-\[requirement-type]#req-\[requirement-number]** and when together with version it is **govstack-\[document-type]-\[name]-\[requirement-type]-\[version]-#req-\[requirement-number]**.

> The \[document-type] for a Building Block specification would be "bb", for example govstack-bb-wallet.

Already existing cross-functional requirements (the core overarching requirements of GovStack) have the following identifiers:

* **govstack-cfr** (main central specification)
* **govstack-cfr-development**
* **govstack-cfr-deployment**
* **govstack-cfr-architecture**
* **govstack-cfr-quality**
* **govstack-cfr-security**
* **govstack-cfr-data**

_The older verisons of specifications used conventions bb-\* and until they are updated to 2.0.0, may still be referenced by their older name._

**Reserved Name Tags/Nodes**

The following keywords have a specific meaning in GovStack namespace and should only be used in their own context. _The following keywords cannot be a name or one of the words in a name of a Building Block specification._

* **govstack** - This is the prefix node for every GovStack specification name. It is used always only at the very start of the specification name.
* **bb** - This is a shorthand for a "building block". Every Building Block specifcation of GovStack has thus a full name prefix of "govstack-bb-", to differentiate core specifications, guides and other materials from Building Block specifications.
* **fr** - This means _functional requirements_, a specific level of business functional requirements. These requirements are specific to a Building Block (such as _govstack-bb-wallet-fr-\*_). _Each "fr" can have its own sub-names (such as govstack-bb-wallet-fr-credentials)._
  * _Between 'bb' and 'fr' would be the building block specification name, with or without dashes (e.g. govstack-bb-**information-mediator**-fr)._
* **cfr** - This means _cross-functional requirements_, a specific level of technical requirements. CFR's can be GovStack core requirements (such as _govstack-cfr-\*_) or Building Block cross-functional requirements (such as _govstack-bb-wallet-cfr-\*_). _Each "cfr" can have its own sub-names (such as govstack-bb-wallet-cfr-data)._&#x20;
  * _Between 'bb' and 'cfr' would be the building block specification name, with or without dashes (e.g. govstack-bb-**wallet**-fr)._
  * _Cross-functional requirements are not required to be defined for Building Blocks, but govstack-cfr is always expected to apply to Building Blocks._
* **"numbers"** - when separated with dash - are reserved only for version numbering, not for naming. _For example govstack-bb-wallet-2 is not allowed, however govstack-bb-wallet2 is._

#### 5.2.2 Specification Versioning (major.minor.patch)

Every GovSpecs specifications document (which includes the whole document, all of its individual requirements) adopts a three-part semantic version number. The label appears in the header of the human-readable text, in the machine-readable schema, and in the metadata that GovMarket can use.

* A **major** increment signals a change that can break an existing integration. Examples include removal or fundamental rewriting of a REQUIRED rule, alteration of an immutable interface signature, or a change in the data model that forces re-migration. When the major digit changes, dependent specifications must assess and, if necessary, revise their own versions before release. The workgroup provides a migration note that lists breaking points, affected test cases, and a recommended upgrade path.
* A **minor** increment adds new capability while preserving full backward compatibility. This can involve new OPTIONAL or RECOMMENDED rules, extensions to an existing enumerated value set, or clarifications that broaden but do not narrow acceptable behaviour. Implementations that follow earlier minor versions continue to pass validation without code changes, though they may adopt new tests to display improved coverage in GovMarket.
* A **patch** increment corrects errors or omissions that prevent a rule from working as intended. Typical patches fix typographical mistakes, update references to external standards, or align an example with normative text. No behaviour visible to an integration partner may change in a patch; therefore, automated test suites should continue to pass without modification.

Requirements themselves are individually not versioned. If a requirement needs to be updated, the whole specification document has to be updated.

> For example, version 2.0.0 of the Workflow building block could be referred to as **govstack-bb-workflow-2.0.0**

_Older versions of specifications use YYQN version naming scheme applied to all specifications. These will be updated to match new expectations laid out here. 25Q2 is expected to be the last version number of this scheme._

_Since every specification is essentially an extension of core Architecture Specification of GovStack, its correct definition would be for example: **govstach-bb-workflow-2.0.0 extends govstack-cfr-2.0.0** (this is further covered below)_

#### _5.2.4 Key Functionalities_

_Every building block serves a specific business-functional need for a digital government ecosystem. Every Key Functionality should have its own section or sub-chapter explaining what it is about. **These are detailed in 5.4 Specification Template.**_

#### _5.2.3 Building Block Requirements_

_Each GovStack Building Block specification includes a combination of requirements: functional requirements and cross-functional requirements. **These are detailed in section 5.3 Requirement Specification.**_

#### 5.2.4 Extensibility and Replaceability

GovStack specifications can be extended and forked for various contexts. To implement variation safely, GovSpecs introduces machine-readable extended specifications, which must explain:

* the parent specification version it extends (with its own unique specification name);
* a list of requirement reclassifications (e.g., RECOMMENDED→REQUIRED, REQUIRED→INAPPLICABLE);
* new requirements with unique identifiers;
* dependency statements that show why each change is legal (immutable rules cannot be altered, extensible rules can be tightened, replaceable rules can be made inapplicable or replaced).

A specification that is extended from an existing GovStack specification it must define in the header that it extends an existing specification, such as:

```
govstack-bb-wallet-europe extends govstack-bb-wallet
```

_This approach somewhat mirrors profiling in HL7 FHIR and extension taxonomies in XBRL. Validators first enforce the base spec, then apply overlay rules, ensuring deterministic outcomes and auditability._

#### 5.2.5 Example of a specification definition

To define a new specification for a Building Block that returns weather data, it can be defined like this:

```
govstack-bb-weather extends govstack-cfr
```

Or in case to be specific with versions:

```
govstack-bb-weather-2.2.5 extends govstack-cfr-2.0.0
```

This "extends govstack-cfr" is not necessarily required. If this is not defined for a specification which is still considered GovStack building block specification, then it is assumed by default. _No GovStack building block can avoid compliant extension of core cross-functional requirements._
