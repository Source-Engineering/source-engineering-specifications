# Source Engineering -- Compatibility Test Suite

> Canonical test cases for the COTS schema. Used by the conformance suite in `Source-Engineering/source-engineering-conformance`.

This document is normative for the discipline's conformance testing. The test cases here are the canonical test cases for the COTS spec; the conformance suite in `source-engineering-conformance` runs them.

Each test case is named, has a description, has an input COTS (or a description of how to construct one), and has an expected outcome (pass or fail with a specific error). The test cases are organised by the integrity question they address.

## Test case TC-SI-01 -- Source identity

**Integrity question.** Source integrity.

**Description.** A COTS with a source artifact that has a stable identifier and an addressable version.

**Input.** A COTS with one source artifact whose `id` is a URI and whose `version.content_hash` is a SHA-256.

**Expected outcome.** Pass. The conformance suite verifies the URI is well-formed and the SHA-256 is the correct length.

## Test case TC-SI-02 -- Unresolved source

**Integrity question.** Source integrity.

**Description.** A COTS that references a source whose `id` is malformed.

**Input.** A COTS with one source artifact whose `id` is not a URI.

**Expected outcome.** Fail with error `source-id-not-uri`. The conformance suite must report the source id and the malformed value.

## Test case TC-CI-01 -- Context recorded

**Integrity question.** Contextual integrity.

**Description.** A COTS whose context records the runtime parameters in effect at the call.

**Input.** A COTS with a complete `context.parameters` object.

**Expected outcome.** Pass. The conformance suite verifies all required parameters are present.

## Test case TC-CI-02 -- Source validity

**Integrity question.** Contextual integrity.

**Description.** A COTS whose source artifact is valid at the decision's timestamp.

**Input.** A COTS with one source artifact whose `temporal_validity.valid_until` is after the `produced_at`.

**Expected outcome.** Pass. The conformance suite verifies the temporal validity.

## Test case TC-CI-03 -- Expired source

**Integrity question.** Contextual integrity.

**Description.** A COTS that cites a source whose validity has expired.

**Input.** A COTS with one source artifact whose `temporal_validity.valid_until` is before the `produced_at`.

**Expected outcome.** Fail with warning `source-validity-expired`. The COTS is still valid (the discipline records the decision); the conformance suite flags it for reviewer attention.

## Test case TC-EI-01 -- Evidence chain

**Integrity question.** Evidential integrity.

**Description.** A COTS whose every claim carries at least one evidence item, and each evidence item has a provenance back to a source.

**Input.** A COTS with one decision, one claim, and one evidence item with a transformation and a source_id.

**Expected outcome.** Pass. The conformance suite verifies the chain.

## Test case TC-EI-02 -- Unsupported claim

**Integrity question.** Evidential integrity.

**Description.** A COTS whose claim has no supporting or contradicting evidence.

**Input.** A COTS with one decision and one claim with empty `supporting_evidence` and `contradicting_evidence` arrays.

**Expected outcome.** Fail with error `claim-has-no-evidence`. The discipline's evidential integrity requires every claim to have at least one evidence item.

## Test case TC-DI-01 -- Decision basis

**Integrity question.** Decision integrity.

**Description.** A COTS whose decision carries a basis with supporting evidence, counterevidence, constraints, and known gaps.

**Input.** A COTS with one decision whose `basis_id` resolves to a DecisionBasis with non-empty supporting evidence, non-empty counterevidence, at least one constraint, and at least one known gap.

**Expected outcome.** Pass. The conformance suite verifies the basis structure.

## Test case TC-DI-02 -- Decision without counterevidence

**Integrity question.** Decision integrity.

**Description.** A COTS whose decision has no counterevidence.

**Input.** A COTS with one decision whose basis has empty `counterevidence`.

**Expected outcome.** Fail with error `decision-has-no-counterevidence`. The discipline's decision integrity requires every decision to consider counterevidence.

## Test case TC-LI-01 -- Audit history

**Integrity question.** Lifecycle integrity.

**Description.** A COTS whose decision record carries a complete audit trail.

**Input.** A COTS with one decision whose `audit_history` contains a `created` entry as the first entry.

**Expected outcome.** Pass.

## Test case TC-LI-02 -- Audit history append-only

**Integrity question.** Lifecycle integrity.

**Description.** A COTS whose audit history is appended.

**Input.** A COTS with one decision whose `audit_history` is re-emitted with an additional entry after the decision was created.

**Expected outcome.** Pass. The conformance suite verifies the new entry's `action` is one of the declared values, its `performed_at` is later than the previous entry, and its `id` is unique.

## Test case TC-FF-01 -- Five parts present

**Description.** A COTS with all five parts present.

**Input.** A COTS with non-empty `inputs`, `context`, `decisions`, `evidence`, and `validation`.

**Expected outcome.** Pass.

## Test case TC-FF-02 -- Missing validation

**Description.** A COTS with empty `validation`.

**Input.** A COTS with empty `validation.feedback_surfaces`.

**Expected outcome.** Fail with warning `no-feedback-surfaces`. The discipline records the absence but does not block the COTS.

## Related documents

- `cots/README.md` -- the COTS spec.
- `versioning.md` -- the versioning policy.
- `compatibility.md` -- the compatibility guarantees.
- `Source-Engineering/source-engineering-conformance` -- the conformance suite that runs these test cases.
## Test case TC-L2-01 -- Validation record well-formed

**Description.** A DecisionRecord carrying a Layer 2 `validation` field that satisfies the v1.1.0 shape.

**Input.** A COTS whose `decisions[0].validation` has a well-formed `flags` array (valid `flag_type` enum values), a `decision_on_validation` of `passed`, `flagged`, or `escalated`, a valid ISO-8601 `validated_at`, and a non-empty `validated_by`.

**Expected outcome.** Pass.

## Test case TC-L2-02 -- Unknown flag_type

**Description.** A Validation record carrying a `flag_type` outside the declared enum.

**Input.** A COTS whose `decisions[0].validation.flags[0].flag_type` is `made-up-flag`.

**Expected outcome.** Fail with error `unknown-flag-type`. Producers must not emit unknown enum values.

## Test case TC-L2-03 -- Unknown decision_on_validation

**Description.** A Validation record carrying a `decision_on_validation` outside the declared enum.

**Input.** A COTS whose `decisions[0].validation.decision_on_validation` is `rejected`.

**Expected outcome.** Fail with error `unknown-decision-on-validation`. Layer 2 does not reject; it flags.

## Test case TC-L2-04 -- Flags inconsistent with decision_on_validation

**Description.** A Validation record whose `decision_on_validation` contradicts its `flags`.

**Input.** A COTS whose `decisions[0].validation` has a non-empty `flags` array but `decision_on_validation` of `passed`.

**Expected outcome.** Fail with error `flags-decision-mismatch`. A non-empty flags array means the decision cannot be `passed`; an empty flags array means it cannot be `flagged` or `escalated`.

## Test case TC-L2-05 -- Malformed validated_at

**Description.** A Validation record whose `validated_at` is not a valid ISO-8601 timestamp.

**Input.** A COTS whose `decisions[0].validation.validated_at` is `yesterday`.

**Expected outcome.** Fail with error `invalid-validated-at`.

## Test case TC-L2-06 -- Validation field optional at v1.0.0

**Description.** A v1.0.0 COTS whose DecisionRecords carry no `validation` field.

**Input.** A conformant v1.0.0 COTS.

**Expected outcome.** Pass. The field is optional; its absence means the producer has not floored Layer 2 (minor-version rules in `versioning.md`).
