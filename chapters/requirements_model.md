---
title: Requirements Model
---

Requirements of a Building Block specification are the most important part of a specification. Requirements are what are used to validate compliance of software solutions on GovMarket as well as to assure interoperability and quality of GovStack labeled solutions.

Each specification includes clearly defined requirements. Each requirement must have a unique identifier and then a set of classifiers based on the classification defined in this document.

There are two types of requirements for a GovStack specification:

* **Functional Requirements** - these are technical requirements that are providing functional business value for the Building Block.
  * Subset of these requirements are Service API's, specific techical integration interfaces that can be used to trigger functionalities within the building block
  * Functional Requirements may be tied to one or more Key Functionalities. If Functional Requirement does not define a link to a Key Functionality then it is considered a "global" Functional Requirement that is required for all Key Functionalities.
* **Cross-Functional Requirements** are technical functionalities that are expected from all compliant solutions of all Building Block software. These are optional for a Building Block specification, however all Building Blocks are considered to extend their Cross-Functional Requirements from core GovStack Cross-Functional Requirements and to be compliant with them.

_An easier way to think about these two requirements is that Functional Requirements are often about "WHAT" and Cross-Functional Requirements are often about "HOW". While this is not exclusively so, this may be a valuable starting point._

#### 5.3.1 **Unique Identifier**

Each requirement has a unique **identification number** within its own specification. While most specifications will seem to have requirements in sequental numbers, it is expected that numbers under the same unique specification will never be reused for another purpose within that specification. If a requirement becomes outdated or irrelevant, its classification is updated to deprecated and its number is never reused.

> For example, wallet specification requirement number 1 could be referred to as govstack-bb-wallet#req-1

This is important for external dependencies as a procudement requirement may refer to a specific requirement and this needs to be fundamentally unique when referred back years from now.

#### **5.3.2 Link to Key Functionalities**

Each requirement may be linked to a specific Key Functionality of a Building Block specification. If a requirement is not linked to a Key Functionality then it is considered within the scope of a single Building Block to be a global requirement that applies to all solutions, regardless of their Key Functionalities.

To define that a requirement serves a specific Key Functionality within the Building Block the body of the requirement should just carry the label named exactly the same as the Key Functionality in the specification itself.

`KF: Payment Orchestration`

A single requirement may apply to multiple Key Functionalities of a Building Block or even all of them (in which case it is recommended not to define links to any KF's, since this would be assumed already).

**Make sure to change the names of KF links whenever Building Block Key Functionality names themselves are changed. Consider updating version numbers of specifications whenever notable changes are made to Key Functionalities.**

_It is not recommended for Cross-Functional Requirements of a Building Block to have a link to Key Functionalities (none of the central CFR's of GovStack do). Similarly to Functional Requirements without a defined Key Functionality they are all considered by default GLOBAL (this does not need to be defined within the requirement)._

#### 5.3.3 Requirement Level Classifiers

GovStack specifications are a combination of descriptions and technical requirements. Each requirement is classified following this model and only one of the keywords applies at the time. If this classification changes, then the specification must update its version number as well.

**REQUIRED -** marks a rule that every conformant solution must satisfy exactly as written. Failure to meet a single REQUIRED element disqualifies the product from GovStack compliance, thus all REQUIRED requirements have to be met for a solution to be GovStack compliant.\
\
**RECOMMENDED -** designates a rule that improves quality but remains optional. GovMarket will calculate the proportion of RECOMMENDED rules a solution meets and expose that figure to buyers as an additional decision signal.
\
\
**DRAFT -** captures a proposal that has reached public view yet still needs evidence or consensus. It reserves an identifier and lets implementations experiment, but no compliance test will fail if a DRAFT rule is absent.
\
\
**DEPRECATED -** freezes a rule that once applied but is now retired. The identifier persists for audit and traceability, but future versions will never reuse it, preventing confusion in historical records (unless it becomes relevant again and gets any of the above classifiers).

If none of these classifiers is defined for a requirement, it is considered **RECOMMENDED** by default.

_Older versions of specifications apply previous versions of keywords and definitions:  "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL". When specifications will be updated to match 2.0.0 core specification then the new classifiers have to be defined, however the wording can still be used within the specification requirement text itself._

#### 5.3.4 Observability Classifiers

Each requirement may carry a verification classifier that defines how compliance can be confirmed. Exactly one of the following keywords applies at a time.&#x20;

<figure><img src="../.gitbook/assets/Govstack observeable.png" alt="" width="329"><figcaption><p>Building Blocks are like a black box</p></figcaption></figure>

**OBSERVABLE** - compliance can be verified through external interaction with the running system. Observable means capable of being verified, measured, or proven true or false through testing, observation or experimentation. This includes API calls, protocol exchanges, behavioral tests or any method that does not require access to source code, configuration or internal infrastructure.

Observable requirements should be observable in one of the three following methods:

* **Automatically testable.** Specifications could be tested automatically with a script or a compliance tool (like web API tests or Robotic Process Automation etc.).
* **Manually testable.** If automatic testing is not possible: there is a defined test procedure and there is a clear pass/fail outcome - anyone following the documented steps should reach the same result.
* **Operationally observable.** If neither automatic testing nor test procedure is possible, it should be operationally observable: verification relies on observing the runtime evidence or behavior. Outcome may not be binary or reproducible in identical conditions.

**AUDITABLE** - If compliance cannot be determined from external behavior alone and requires inspection of internals such as architecture documentation, source code, deployment configuration, access control policies or third-party audit reports. Specifications should pair each AUDITABLE requirement with a list of evidence artefacts the implementer must produce - for example architecture decision records, deployment diagrams or signed audit statements - so that the verification process remains deterministic even when manual.

If this classification changes for a given requirement, the specification must update its version number accordingly.

If neither of these classifiers is defined for a requirement, it is considered **AUDITABLE** by default.

#### 5.3.5 Mutability Classifiers


GovSpecs adopts an object-oriented philosophy: each building-block specification behaves like a class, inheriting common traits from the cross-functional layer and exposing controlled variation points. To make that inheritance explicit, every requirement now carries a mutability tag that determines how child specifications or local overlays may treat it:

**IMMUTABLE** - the requirement is considered frozen and static. Later versions may refine wording for clarity but must not alter its intent, scope, input, or output. Integrations built against an immutable requirement are guaranteed to keep working across all minor and patch releases. For example, govstack-cfr-security#req-1 (“#1 Transport security is enforced”) would be immutable and breaking this guarantee would demand a new major version of the entire specification that depends on it.
\
\
**EXTENSIBLE** - additional constraints or features can be layered on without changing the original text. The base wording stays immutable, ensuring upward compatibility, while overlays can tighten or elaborate the rule or add additional functionalities to the same rule. Tests for the overlay must still confirm that all base behaviour passes unchanged. Extended requirements can tighten the rule, but never broaden it.
\
\
**REPLACEABLE** - the rule may be swapped out wholesale, provided the external contract - inputs, outputs, behaviour - remains identical. This enables technology substitution where policy or local ecosystems demand it. A requirement like this can change the execution of the rule without changing the rule itself.

**INAPPLICABLE -** appears only in an extended specification. It formally switches off an inherited extensible or replaceable rule for a given context, without altering the parent text, and records the rationale so automated validators understand why the rule no longer applies for that specific building block.

If none of these classifiers is defined for a requirement, it is considered **REPLACEABLE** by default.

_**Important!** Each specification can only have one of these extensability and replaceability classifications similarly to the requirement general classification. If this classification changes, then the specification must update the version number as well._

#### 5.3.6 Example of a requirement

To define a new requirement for example for a new "weather" building block and I want to require that there is an API call which returns current weather based on some parameters (not shown here in the example) then it can be defined as a requirement like this:

```
#7 Must return current weather for the API request /api/v1/weather
RECOMMENDED REPLACEABLE
```

... in which case it can be referred to as _govstack-bb-weather-fr#req-7_ requirement.

Or in case you wish to refer to specific version of the specification:

```
govstack-bb-example-fr-2.3.0#req-7
```

Additionally the Functional Requirements can define links to Key Functionalities like this: `KF: Payment Orchestration`
