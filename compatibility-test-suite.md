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

## Test case TC-SEM-01 -- URN grammar

**Description.** Every identifier in the COTS is a well-formed Source Engineering URN.

**Input.** A COTS whose `cots_id`, source, decision, evidence, claim, and basis identifiers all match the `urn:source-engineering:<type>:<id>` grammar.

**Expected outcome.** Pass.

## Test case TC-SEM-02 -- Evidence chain resolution

**Description.** Every reference in the evidence chain resolves within the same COTS.

**Input.** A COTS whose every evidence item's `source_id` resolves to an entry in `inputs.source_artifacts`, every `claim_id` resolves to a claim, every claim's `supporting_evidence`/`contradicting_evidence` URNs resolve to evidence items, and every `decision_id` in the evidence chain resolves to a DecisionRecord.

**Expected outcome.** Pass. A dangling reference fails with error `unresolved-reference` (per the cross-reference guarantees in `compatibility.md`).

## Test case TC-SEM-03 -- Temporal ordering

**Description.** Timestamps in the COTS are internally consistent.

**Input.** A COTS whose `produced_at` is not before any source artifact's `issued_at`, whose decision `created_at` values are not before the source artifacts they rely on, and whose Layer 2 `validated_at` is not before `produced_at`.

**Expected outcome.** Pass. An impossible ordering (evidence from the future) fails with error `temporal-inconsistency`.

## Test case TC-RC-01 -- Recursive chain flagged

**Description.** A COTS whose evidence includes a `model-generated` source_type item.

**Input.** A COTS with `source_type: "model-generated"` on at least one evidence item.

**Expected outcome.** Pass with warning `recursive-evidence` if the chain termination policy admits recursion; fail if the policy is `terminal` (TC-RC-04).

## Test case TC-RC-02 -- Bounded recursion declares its bound

**Description.** A COTS with `chain_termination.policy: "bounded_recursion"`.

**Input.** A COTS declaring bounded recursion with `max_levels` present and at least 1.

**Expected outcome.** Pass; a bound greater than 1 additionally warns `high-recursion-bound`.

## Test case TC-RC-03 -- Unbounded recursion warned

**Description.** A COTS with `chain_termination.policy: "unbounded_recursion"`.

**Input.** A COTS declaring unbounded recursion.

**Expected outcome.** Pass with warning `unbounded-recursion`. The suite records the position; the user decides.

## Test case TC-RC-04 -- Chain termination policy violated

**Description.** A COTS whose evidence violates its declared chain termination policy.

**Input.** A `terminal` COTS containing a `model-generated` evidence item; or a `bounded_recursion` COTS whose model-generated depth exceeds its declared `max_levels`.

**Expected outcome.** Fail with error `chain-termination-violated`.

## Test case TC-RC-05 -- Legacy COTS without RFC-0002 fields

**Input.** A v1.1.0 COTS without `source_type` and `chain_termination`.

**Expected outcome.** Pass with warning `legacy-cots` (the suite accepts pre-RFC-0002 COTSs and records their legacy status).
