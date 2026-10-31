# Source Engineering -- Compatibility Matrix

> Which released versions of each artefact work together, as of each floor.

This document is the discipline's published compatibility matrix. It answers "what works with what" in one place: for each released floor, the versions of the specification, reference implementation, conformance suite, and tools that were validated together. It is updated with every tagged release (per the Advancement Plan's cross-cutting rule: tag and release at each floor).

The field-level, entity-level, cross-reference, and temporal guarantees that a consumer can rely on within a version are normative in [`compatibility.md`](compatibility.md). This matrix records which released artefact sets satisfy which guarantees.

## Matrix

| Floor | COTS schema version | specifications | reference-implementation | conformance | tools | Conformance test cases | Validated together |
|---|---|---|---|---|---|---|---|
| v0.1 (Layer 1 typed-model floor) | 1.0.0 | v0.1.0 | v0.1.0 | v0.1.0 | v0.1.0 | TC-FF-01..02, TC-SI-01..07, TC-LI-01..05, TC-CI-01..02, TC-DI-01, TC-EI-01 | Yes: the reference implementation's `example:legal-summarization` output validates PASS (0 errors, 0 warnings) against the conformance suite, and the tools suite reads the same COTS (5/5 tests) |
| v0.2 (Layer 2 runtime-validation floor; RFC-0001) | 1.1.0 | v0.2.0 | v0.2.0 | v0.2.0 | v0.2.0 (practice handbook ch. 9) | adds TC-L2-01..06 | Yes: the reference implementation's Layer 2 module attaches Validation records to its example COTS; the conformance suite's Layer 2 fixtures (conformant, bad flag type, bad decision, flags/decision mismatch, bad timestamp) pass/fail TC-L2-01..06 as specified; `se-trace validate-runtime` reads the same records (7/7 tools tests, 18/18 ref-impl tests, 23/23 conformance tests, all CI-green) |

## How to read the matrix

- A row is a **floor**: a set of released artefact versions that were validated against each other as one vertical slice. The `Validated together` column states the concrete cross-check that was run.
- Within a row, the conformance suite of that row is the authority: a COTS produced by that row's reference implementation is conformant if and only if that row's conformance suite says so.
- Producers and consumers on **different rows** have no compatibility claim unless stated. The compatibility guarantees in [`compatibility.md`](compatibility.md) apply within the same major COTS schema version.
- The matrix is regenerated as part of each floor's release process (Advancement Plan Phase 0 item), then committed here with the release PR.

## Version pinning policy

1. Every floor tags all four code-bearing repos (`specifications`, `reference-implementation`, `conformance`, `tools`) at the same time.
2. The tags share the floor number (`v0.1.0`, `v0.2.0`, ...) even though per-repo semver may advance independently between floors; per-repo versions appear in the matrix as tagged.
3. The COTS schema version advances per [`versioning.md`](versioning.md); the matrix records which schema version each floor emits.
4. A floor is only added to the matrix after the cross-check in `Validated together` has been run and recorded.

## Current status

- **v0.2.0** is the current floor: Layer 2 (runtime validation), COTS schema 1.1.0, RFC-0001 Implemented.
- The next work is semantic conformance (Advancement Plan Phase 2); Layer 3 (guardrails DSL) follows per the Advancement Plan.
