# Changelog

All notable changes to the Source Engineering specifications repo are recorded here.

## 2026-10-11

### Added

- `cots/README.md` -- the COTS v0.1 specification. The five-part structure (Inputs, Context, Decisions, Evidence, Validation) and the JSON wire format.
- `versioning.md` -- the versioning policy. Major / minor / patch semantics; pre-release rules; compatibility rules.
- `compatibility.md` -- the backward-compatibility guarantees. Field-level, entity-level, cross-reference, and temporal guarantees.
- `compatibility-test-suite.md` -- the canonical test cases for the conformance suite. Twelve test cases covering the five integrity questions and the five-part structure.
- `decision-types/README.md` -- the registry overview.
- `decision-types/legal/legal-summarization.md` -- the legal-summarization decision-type (v0.1.0). Question schema, answer schema, evidence requirements, validation rules.
- `decision-types/finance/transaction-routing.md` -- the transaction-routing decision-type (v0.1.0). Question schema, answer schema, evidence requirements, validation rules.
- `CHANGELOG.md` -- this file.

### Notes

- v0.1 is a pre-release. Per the versioning policy, pre-release versions are not required to maintain backwards compatibility.
- The decision-types registry is at v0.1 with two initial decision-types. New decision-types can be added via RFC without breaking compatibility, per the minor-version rules.
- The compatibility-test-suite.md is the canonical list of test cases; the conformance suite in `Source-Engineering/source-engineering-conformance` implements them.
## [Unreleased]

### Added

- `compatibility-matrix.md` - the published per-floor compatibility matrix (specification x reference-implementation x conformance x tools), with the version pinning policy and the v0.1.0 floor's validated cross-check

### Added (RFC-0001 Layer 2 floor, COTS schema v1.1.0)

- `cots/README.md` Part 3: the optional `decisions[*].validation` field (ValidationFlags, `decision_on_validation`, runtime metadata) per RFC-0001; optional per the minor-version rules
- `compatibility-test-suite.md`: test cases TC-L2-01..06 covering the Layer 2 Validation record (well-formedness, enum enforcement, flags/decision consistency, timestamp validity, v1.0.0 optionality)

### Changed

- `compatibility-matrix.md`: the v0.2 row recorded with the floor's real tags (v0.2.0 across specifications, reference-implementation, conformance, tools, practice) and the validated cross-check; current floor is v0.2.0
