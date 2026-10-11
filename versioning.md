# Source Engineering -- Versioning Policy

> How the specifications evolve. What counts as a breaking change. What the compatibility guarantees are.

This document is normative for the discipline's specifications. It applies to the COTS schema, the decision-types registry, and every other specification under this repo. The versioning policy is itself versioned; it evolves via the change process in `Source-Engineering/source-engineering-governance`.

## The version triple

Every normative artefact in this repository carries a version triple: **major.minor.patch**. The three components have different meanings.

### Major version

A change to the major version is a **breaking change**. A producer of major version N+1 is not required to accept consumers of major version N. A consumer of major version N is not required to accept producers of major version N+1. The discipline commits to long-term support of major versions for at least 24 months.

### Minor version

A change to the minor version is a **backwards-compatible additive change**. A producer of minor version N+1 can produce outputs that consumers of minor version N accept (the consumer does not understand the new fields, but it ignores them gracefully). A consumer of minor version N+1 can consume outputs of minor version N (the producer does not emit the new fields).

### Patch version

A change to the patch version is a **clarification or documentation change**. The wire format does not change. The schema does not change. Only the prose is updated. Consumers and producers do not need to change.

## What counts as breaking

A change is breaking if it:

- Removes an entity or attribute.
- Renames an entity or attribute.
- Changes the type of an entity or attribute in a way that does not round-trip.
- Changes the polarity enum values.
- Changes the claim_type enum values.
- Changes the ValidationStatus enum values.
- Changes the AuditEntry action enum values.
- Makes a previously-optional field now mandatory.

A change is **not** breaking if it:

- Adds a new optional entity or attribute.
- Adds a new enum value with a documented meaning (consumers ignore unknown enum values per the minor-version rules).
- Adds a new constraint type.
- Adds a new transformation type.

## The compatibility guarantees

A consumer of major version N, minor version M can consume any producer of major version N, minor version M' >= M. The consumer ignores fields it does not understand.

A producer of major version N, minor version M can produce outputs that any consumer of major version N, minor version M' >= M can consume. The producer does not emit fields the consumer does not understand.

These guarantees apply to the wire format (JSON, YAML, TOML) and to the COTS schema. They do not apply to the prose of this document; the prose can be updated at any patch version.

## Pre-release versions

Versions below v1.0 are pre-release. Pre-release versions:

- Are not required to maintain backwards compatibility.
- Can change the wire format between minor versions.
- Can change the schema between minor versions.

The discipline uses pre-release versions to iterate on the schema before committing to a stable v1.0. Once v1.0 is released, the breaking-change discipline applies.

## Versioning of the JSON Schemas

The JSON Schemas live at `cots/schemas/v<N>/`. Each major version has its own directory. Minor versions are patch-level changes to the same directory. The discipline does not split minor versions into separate files; the schema is the schema.

## Versioning of the decision-types registry

The decision-types registry is versioned separately from the COTS schema. A decision-type is identified by a versioned URI; the URI carries the major version. A decision-type added at minor version M is available at minor version M and later.

## How to propose a versioned change

1. Open an issue describing the change.
2. File an RFC under `Source-Engineering/source-engineering-governance/RFC/`.
3. The RFC specifies the target version (major or minor).
4. If the RFC is accepted, the schema and the prose are updated.
5. The CHANGELOG records the change.

## Related documents

- `cots/README.md` -- the COTS spec.
- `compatibility.md` -- the backward-compatibility guarantees.
- `Source-Engineering/source-engineering-governance/RFC/0000-template.md` -- the RFC template.