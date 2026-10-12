# Specifications

> Normative technical requirements. The COTS spec is the headline sub-artefact; the decision-types registry is the second.

This repository holds the discipline's normative technical requirements. Every claim a Source Engineering system makes about being conformant is checked against this repository. The specifications are versioned; the versioning policy is in `versioning.md`.

## What is in this repository

- `cots/` - the headline sub-artefact: the Chain of Orchestrable and Traceable Sources specification. JSON Schemas, YAML schemas, and TOML schemas for the COTS and its parts.
- `decision-types/` - the second sub-artefact: a shared, versioned registry of typed questions and declared answer shapes. Each domain (legal, finance, healthcare, ...) writes its own sub-registry.
- `versioning.md` - the versioning policy. How schemas are versioned; what counts as a breaking change; what the compatibility guarantees are.
- `compatibility.md` - the backward-compatibility guarantees. What a v1 COTS consumer must accept from a v1.x COTS producer.
- `compatibility-test-suite.md` - the canonical test cases for the COTS schema. Used by the conformance suite in `Source-Engineering/source-engineering-conformance`.

## How to engage

- **Read the COTS spec first.** Start at `cots/README.md`.
- **Then the versioning policy.** `versioning.md` is normative for how the specs evolve.
- **Then the compatibility guarantees.** `compatibility.md` is normative for what consumers can rely on.
- **Propose a schema change** by opening an issue and an RFC in `Source-Engineering/source-engineering-governance`.

## Provenance

The COTS spec is the discipline's headline artefact. It is the operational form of the conceptual model in `Source-Engineering/source-engineering-model`. The schema is derived from the entities in the model's data model. The integrity questions in the model are the normative requirements the schema satisfies.

## Organisation

The discipline is published as a set of repositories under the [Source-Engineering](https://github.com/Source-Engineering) GitHub organisation. One authoritative home per artefact:

| Repo | Owns |
|---|---|
| [Source-Engineering/.github](https://github.com/Source-Engineering/.github) | Org profile README |
| [Source-Engineering/project-governance](https://github.com/Source-Engineering/project-governance) | Project-level governance for the governance home |
| [Source-Engineering/source-engineering-governance](https://github.com/Source-Engineering/source-engineering-governance) | Org-wide discipline governance |
| [Source-Engineering/source-engineering-foundations](https://github.com/Source-Engineering/source-engineering-foundations) | Discipline definition, principles, terminology |
| [Source-Engineering/source-engineering-research](https://github.com/Source-Engineering/source-engineering-research) | Research agenda, experimental protocols |
| [Source-Engineering/source-engineering-model](https://github.com/Source-Engineering/source-engineering-model) | Canonical conceptual and data model |
| [Source-Engineering/source-engineering-specifications](https://github.com/Source-Engineering/source-engineering-specifications) (this repo) | Normative technical requirements; COTS spec; decision-types registry |
| [Source-Engineering/source-engineering-architecture](https://github.com/Source-Engineering/source-engineering-architecture) | Reference architecture, blueprint stack |
| [Source-Engineering/source-engineering-reference-implementation](https://github.com/Source-Engineering/source-engineering-reference-implementation) | Working reference code |
| [Source-Engineering/source-engineering-conformance](https://github.com/Source-Engineering/source-engineering-conformance) | Conformance test suite |
| [Source-Engineering/source-engineering-tools](https://github.com/Source-Engineering/source-engineering-tools) | Reusable software utilities |
| [Source-Engineering/source-engineering-case-studies](https://github.com/Source-Engineering/source-engineering-case-studies) | Evaluated demonstrations |
| [Source-Engineering/source-engineering-practice](https://github.com/Source-Engineering/source-engineering-practice) | Practitioner guidance |
| [Source-Engineering/source-engineering-education](https://github.com/Source-Engineering/source-engineering-education) | Curriculum and learning resources |

## Status

This is the specifications slice (Phase 2). The COTS spec is at v0.1 (initial draft). The decision-types registry is at v0.1 with the legal and finance sub-registries seeded. The versioning policy and the compatibility guarantees are normative.

## License

Apache-2.0. See [LICENSE](LICENSE).
