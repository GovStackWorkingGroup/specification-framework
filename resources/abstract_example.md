---
title: Abstract Example
---

The examples in the chapters of the Specification Framework describe one fictional Building Block, the **Example Building Block** (`govstack:spec:bb:example`). Its names are placeholders that show the shape of a specification, not the content of any real one. Replace each with the names of your own Building Block.

## Domain

The Example Building Block performs **Role B** in a fictional Domain Reference Architecture, `govstack:ra:example`. The other roles in that domain are:

| Role | Played by | Exchanges with the Building Block |
|---|---|---|
| Role P | System P | Protocol P, defined in `govstack:ra:example` |
| Role Q | System Q | Protocol Q, defined in `govstack:ra:example` |
| Registry operator | Registry R | Lookups that Protocol Q depends on, operated in some deployments only |

A person, the **End User**, uses the Building Block directly.

## Capabilities

| Id | Title | Outcome |
|---|---|---|
| `capability-a` | Capability A | An outcome every conformant software solution provides |
| `capability-b` | Capability B | An outcome the core provides, and Extension Y adds to |
| `capability-c` | Capability C | An outcome only Extension X provides |

## Conformance profiles and functionalities

```text
conformance-profiles/
  core-profile/              # CORE
    introduction.md
    functionalities/
      functionality-a.md     # realises capability-a
      functionality-b.md     # realises capability-a, capability-b
      functionality-c.md     # realises capability-b
  extension-x/               # EXTENSION, towards Role P
    introduction.md
    functionalities/
      functionality-x.md     # realises capability-c
  extension-y/               # EXTENSION, towards Role Q, requires extension-x
    introduction.md
    functionalities/
      functionality-y.md     # realises capability-b
```

| Conformance profile | Type | Functionalities | Requires | Why it is a separate profile |
|---|---|---|---|---|
| Core Profile | Core | Functionality A, Functionality B, Functionality C | | Every conformant software solution needs it |
| Extension X | Extension | Functionality X | | An obligation towards Role P that not every deployment has |
| Extension Y | Extension | Functionality Y | Extension X | An obligation towards Role Q that depends on Registry R, which not every deployment operates |

Real folder names name the profile, as `file-management` or `records-management`. `core-profile` and `extension-x` are placeholders only.
