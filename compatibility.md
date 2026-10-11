# Source Engineering -- Compatibility Guarantees

> What a v1 COTS consumer must accept from a v1.x COTS producer.

This document is normative for the discipline's specifications. It states, in concrete terms, what a consumer of a given version can rely on when it receives a COTS from a producer of the same major version.

## The guarantees

### Field-level guarantees

- Every field declared as **mandatory** in the schema MUST be present in every COTS of the corresponding version. The conformance suite verifies this.
- Every field declared as **optional** in the schema MAY be absent. Consumers must not throw on missing optional fields; they must treat the absence as "the producer did not record this datum."
- Every field declared as **enum** in the schema MUST take one of the declared values. A producer MUST NOT emit an unknown enum value.
- Every field declared as **URI** in the schema MUST be a syntactically valid URI.

### Entity-level guarantees

- Every `SourceArtifact` MUST be uniquely identified by its `id`. Two COTSs from the same producer MUST NOT share an `id`.
- Every `DecisionRecord` MUST be uniquely identified by its `id`.
- Every `EvidenceItem` MUST be uniquely identified by its `id` and MUST reference at least one `SourceArtifact` via `source_id`.

### Cross-reference guarantees

- Every `claim_id` referenced by an `EvidenceItem` MUST resolve to a `Claim` in the same COTS.
- Every `basis_id` referenced by a `DecisionRecord` MUST resolve to a `DecisionBasis` in the same COTS.
- Every `evidence_chain_ref` referenced by a `DecisionRecord` MUST resolve to an entry in the `evidence.evidence_chain` array.
- Every `source_id` referenced by an `EvidenceItem` MUST resolve to an entry in the `inputs.source_artifacts` array.

### Temporal guarantees

- Every `produced_at` MUST be a valid ISO-8601 timestamp.
- Every `valid_from` MUST be before or equal to `valid_until`.
- Every `valid_until` that is not null MUST be after the `produced_at` of the COTS that references the artifact (the artifact must be valid at the time of the decision).

## What the guarantees do not cover

- The **content** of fields. A producer can put any text in a `decision_text` field. The discipline does not assert that the text is correct; it asserts that the text is recorded.
- The **correctness** of provenance. A producer can record a Provenance that is wrong. The discipline records the Provenance; the conformance suite verifies the Provenance is well-formed.
- The **completeness** of evidence. A producer can record a partial evidence chain. The discipline records what the producer recorded; the conformance suite verifies that the recorded evidence satisfies the schema.

## Versioning interactions

The compatibility guarantees are themselves versioned. The current guarantees apply to v0.1 and later pre-release versions. When v1.0 is released, the guarantees are tightened (the wire format becomes normative; the conformance suite is promoted to a release-gate).

## Related documents

- `cots/README.md` -- the COTS spec.
- `versioning.md` -- the versioning policy.
- `compatibility-test-suite.md` -- the test cases the conformance suite runs against this spec.