# Source Engineering -- Decision Types Registry

> A shared, versioned registry of typed questions and declared answer shapes. Each domain writes its own sub-registry.

This document is normative for how Source Engineering systems declare the shape of a decision. The decision-types registry is the second sub-artefact of the specifications repo (the first is the COTS spec at `cots/`).

A decision-type is a contract: the model's input is a typed question, the model's output is a declared answer shape, and the model operator commits to producing the declared shape. The decision-types registry is the discipline's response to schema fragmentation: every domain's typed questions live in one place, versioned, so a Source Engineering system can be conformant without re-inventing the schema.

## What is a decision-type

A decision-type is a typed question with a declared answer shape. The schema is:

```
DecisionType {
  id:                  URI (unique within the registry)
  name:                string (human-readable)
  version:             major.minor.patch
  domain:              string (the domain the decision-type belongs to)
  question_schema:     TypeSchema (the typed question)
  answer_schema:       AnswerSchema (the declared answer shape)
  evidence_requirements:  EvidenceRequirement[]
  validation_rules:    ValidationRule[]
  author:              string (the decision-type author)
  maintainer:          string (the current maintainer)
}
```

The TypeSchema is a JSON Schema (Draft 2020-12 or later). The AnswerSchema is a JSON Schema for the model's output.

## Sub-registries

The decision-types registry is organised by domain. Each domain has its own sub-registry under `decision-types/<domain>/`. The initial sub-registries are:

- `decision-types/legal/` -- legal domain. The legal-summarization decision type is the v0.1 entry.
- `decision-types/finance/` -- finance domain. The transaction-routing decision type is the v0.1 entry.

Each sub-registry is independently versioned. A breaking change to the legal sub-registry does not require a breaking change to the finance sub-registry.

## Versioning

The decision-types registry follows the versioning policy in `../versioning.md`. The major version of the registry is incremented when a sub-registry is renamed or removed. The minor version is incremented when a new decision-type is added.

## How to add a decision-type

1. Open an issue describing the decision-type.
2. File an RFC under `Source-Engineering/source-engineering-governance/RFC/`.
3. The RFC includes the question schema, the answer schema, the evidence requirements, and the validation rules.
4. If accepted, the decision-type lands under the appropriate sub-registry.
5. The CHANGELOG records the addition.

## What the registry is not

- The decision-types registry is not a workflow engine. It defines the schema for typed questions and the schema for declared answers; it does not define the steps to produce the answer.
- The decision-types registry is not a model registry. The registry names the typed question and the declared answer; it does not name the model that produces the answer.
- The decision-types registry is not a UI library. The registry is the schema, not the rendering.

## Related documents

- `../cots/README.md` -- the COTS spec.
- `../versioning.md` -- the versioning policy.
- `decision-types/legal/` -- the legal sub-registry.
- `decision-types/finance/` -- the finance sub-registry.