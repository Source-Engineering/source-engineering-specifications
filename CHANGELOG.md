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