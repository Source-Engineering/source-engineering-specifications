# Source Engineering -- COTS Specification (v0.1)

> The Chain of Orchestrable and Traceable Sources. The headline sub-artefact of the discipline.

This document is the canonical prose specification of the COTS. The JSON, YAML, and TOML schemas live alongside this document under the same version. The COTS is the unit of accountability for the discipline: every audit, every appeal, every re-run, every cross-domain comparison works against the COTS, not against the response alone.

The COTS schema is derived from the data model in `Source-Engineering/source-engineering-model`. Every entity in the COTS maps to an entity in the model. Every relationship in the COTS maps to a relationship in the model. The COTS schema is the operational form of the model.

## What is a COTS

A COTS is the structured, machine-readable bundle that every Source Engineering response carries. A COTS has five named parts:

- **Inputs** -- every data point that shaped the response, with provenance metadata.
- **Context** -- the runtime parameters set for this call, with their rationale.
- **Decisions** -- every decision point the workflow took, with the option set and the rationale.
- **Evidence** -- the chain linking each output to its supporting data and reasoning step.
- **Validation** -- the feedback surfaces exposed back to the user for this response.

If a response does not carry a COTS, it is not a Source Engineering response.

## The COTS top-level structure

```json
{
  "cots_version": "1.0.0",
  "cots_id": "urn:source-engineering:cots:<uuid>",
  "produced_at": "2026-10-11T12:00:00Z",
  "produced_by": {
    "system_name": "<string>",
    "system_version": "<string>"
  },
  "inputs":      { ... SourceUniverse ... },
  "context":     { ... Context ... },
  "decisions":   { ... DecisionRecord[] ... },
  "evidence":    { ... EvidenceChain ... },
  "validation":  { ... ValidationSurfaces ... }
}
```

The five parts are addressed below. The JSON Schemas live at `cots/schemas/v1/`. The wire format is JSON; YAML and TOML serialisations are normative for hand-authored and configuration use, not for the wire.

## Part 1 -- inputs

The `inputs` part is the Source Universe the model considered for this COTS. It is an array of `SourceArtifact` objects as defined in the data model.

```json
"inputs": {
  "source_artifacts": [
    {
      "id": "urn:source-engineering:source:<id>",
      "origin": {
        "system_name": "Anthropic Citations",
        "system_version": "1.2.0",
        "configuration_hash": "<sha256>"
      },
      "version": {
        "content_hash": "<sha256>",
        "issued_at": "2026-10-10T08:00:00Z"
      },
      "authority": {
        "asserted_by": "anthropic-citations",
        "trust_score": 0.95
      },
      "temporal_validity": {
        "valid_from": "2026-10-10T08:00:00Z",
        "valid_until": null
      },
      "content_reference": {
        "uri": "https://example.com/source/<id>",
        "media_type": "application/pdf"
      }
    }
  ]
}
```

## Part 2 -- context

The `context` part is the runtime parameters in effect at the call.

```json
"context": {
  "model": {
    "model_name": "claude-sonnet-4-5",
    "model_version": "2026-09-15"
  },
  "parameters": {
    "temperature": 0.3,
    "top_p": 0.95,
    "prompt_cache_id": "<uuid>",
    "tool_budget": 8,
    "retrieval_depth": 5,
    "additional": {}
  },
  "constraints": [
    {
      "id": "urn:source-engineering:constraint:<id>",
      "type": "jurisdiction",
      "scope": "all_decisions",
      "value": "US-NY"
    }
  ]
}
```

## Part 3 -- decisions

The `decisions` part is an array of `DecisionRecord` objects as defined in the data model.

```json
"decisions": [
  {
    "id": "urn:source-engineering:decision:<uuid>",
    "decision_text": "<the decision itself>",
    "basis_id": "urn:source-engineering:basis:<uuid>",
    "evidence_chain_ref": "urn:source-engineering:evidence-chain:<uuid>",
    "validation_status": {
      "status": "pending",
      "last_changed_at": "2026-10-11T12:00:00Z",
      "last_changed_by": "cots-producer"
    },
    "responsible_authority": {
      "authority_id": "anthropic-claude-sonnet-4-5",
      "authority_type": "automated"
    },
    "audit_history": [
      {
        "id": "urn:source-engineering:audit:<uuid>",
        "action": "created",
        "performed_by": "cots-producer",
        "performed_at": "2026-10-11T12:00:00Z",
        "notes": "Initial creation."
      }
    ],
    "created_at": "2026-10-11T12:00:00Z",
    "validation": {
      "flags": [
        {
          "flag_type": "drift-detected",
          "message": "Calibration drift detected on segment 'contract-law' (0.14 divergence, threshold 0.10).",
          "detected_at": "2026-10-11T12:00:00Z"
        }
      ],
      "decision_on_validation": "flagged",
      "validated_at": "2026-10-11T12:00:00Z",
      "validated_by": "layer2-runtime-validation"
    }
  }
]
```

### The Layer 2 Validation record (v1.1.0)

The optional `validation` field on each `DecisionRecord` carries the Layer 2 (Runtime Validation) record, added in COTS schema v1.1.0 per RFC-0001. The field is **optional**: producers on v1.0.0 do not emit it, and consumers must treat its absence as "the producer has not floored Layer 2" (per the minor-version rules in [`versioning.md`](../versioning.md)). When present, it MUST satisfy:

- `flags` is an array of `ValidationFlag` objects, possibly empty. Each flag carries a `flag_type` from the declared enum (`drift-detected`, `segment-signal-divergence`, `parameter-set-out-of-range`, `temporal-validity-expired`), a human-readable `message`, and a `detected_at` timestamp.
- `decision_on_validation` is one of `passed` (no flags raised), `flagged` (one or more flags raised; the flag is a warning, not a rejection), or `escalated` (validation signalled escalation to a human or organisational authority).
- `validated_at` is a valid ISO-8601 timestamp, and `validated_by` identifies the validating system.
- Layer 2 **does not reject**; it flags. A flagged decision MUST still be exposed to Layer 3 (Business Guardrails), which is the layer that can reject. A decision MUST NOT be exposed to Layer 3 before its Validation record, if emitted, is attached.

The interface contract is normative in `Source-Engineering/source-engineering-architecture/layer-contracts/layer-2-runtime-validation.md`.

## Part 4 -- evidence

The `evidence` part is the EvidenceChain that links each decision to its supporting and contradicting evidence.

```json
"evidence": {
  "chain_termination": {
    "policy": "terminal",
    "max_levels": 1
  },
  "evidence_chain": [
    {
      "decision_id": "urn:source-engineering:decision:<uuid>",
      "supporting_evidence": [
        {
          "id": "urn:source-engineering:evidence:<uuid>",
          "claim_id": "urn:source-engineering:claim:<uuid>",
          "source_id": "urn:source-engineering:source:<id>",
          "transformation": {
            "operation": "extract_clause",
            "inputs": ["<input-uri-1>"],
            "output": "<output-text>",
            "recorded_at": "2026-10-11T12:00:00Z"
          },
          "uncertainty": {
            "calibrated_score": 0.85,
            "basis": "Source is recent and authoritative; transformation is deterministic."
          },
          "polarity": "supports",
          "source_type": "human-authored"
        }
      ],
      "contradicting_evidence": [],
      "claims": [
        {
          "id": "urn:source-engineering:claim:<uuid>",
          "decision_record_id": "urn:source-engineering:decision:<uuid>",
          "claim_text": "<the claim>",
          "claim_type": "factual",
          "supporting_evidence": ["<evidence-id-1>"],
          "contradicting_evidence": []
        }
      ]
    }
  ]
}
```

### The RFC-0002 fields (v1.2.0): source_type and chain_termination

COTS schema v1.2.0 adds two optional fields to the evidence part, per RFC-0002 (recursive evidence chains). Both are optional; a v1.1.0 producer does not emit them, and consumers treat absence as "the producer has not adopted RFC-0002" (minor-version rules in [`versioning.md`](../versioning.md)).

**`EvidenceItem.source_type`** declares what kind of source the evidence item resolves to. Values: `human-authored` (the terminal item is a human-authored source), `human-curated` (a human selected or verified the item), `model-generated` (the item is itself the output of a model; the chain is recursive at this item), `mixed`. A COTS MUST flag any evidence item that is itself a model output with `model-generated`.

**`evidence.chain_termination`** declares the COTS's chain termination policy:

- `terminal`: all chains resolve to human-authored sources. The discipline's default.
- `bounded_recursion`: chains may include up to `max_levels` levels of model outputs, declared per-COTS. The bound is recommended to be 1; bounds greater than 1 are permitted but flagged for the user.
- `unbounded_recursion`: chains may include any depth of model outputs. Conformance flags the COTS in its warning set.

A COTS that violates its declared chain termination policy fails validation (TC-RC-04): e.g. a `terminal` COTS containing a `model-generated` evidence item.

## Part 5 -- validation

The `validation` part is the feedback surfaces the COTS exposes to the user.

```json
"validation": {
  "feedback_surfaces": [
    {
      "id": "urn:source-engineering:surface:<uuid>",
      "type": "appeal",
      "target": "urn:source-engineering:decision:<uuid>",
      "endpoint": "https://example.com/appeal/<uuid>",
      "description": "File an appeal against this decision."
    },
    {
      "id": "urn:source-engineering:surface:<uuid>",
      "type": "rerun",
      "target": "urn:source-engineering:decision:<uuid>",
      "endpoint": "https://example.com/rerun/<uuid>",
      "description": "Re-run the decision with the same evidence under a different model or version."
    }
  ]
}
```

## The integrity questions and the v1 schema

The COTS schema is designed to satisfy the five integrity questions from `Source-Engineering/source-engineering-model/integrity-questions.md`. The mapping is enforced by the conformance suite.

| Integrity question | Schema requirement |
|---|---|
| Source integrity | Every `source_artifacts[*].id` is a URI; every `version.content_hash` is a SHA-256; `content_reference.uri` resolves to the content. |
| Contextual integrity | Every decision's `context.parameters` is a complete record of the runtime parameters; every `temporal_validity` is checked. |
| Evidential integrity | Every `claims[*]` has at least one `supporting_evidence` or `contradicting_evidence`; every evidence item has a `transformation` and a `source_id`; the `polarity` enum is honoured. |
| Decision integrity | Every `decisions[*]` carries a `basis_id`; every `basis_id` resolves to a `DecisionBasis` with supporting evidence, counterevidence, constraints, and known gaps. |
| Lifecycle integrity | Every `decisions[*].audit_history` is append-only; every change records `action`, `performed_by`, and `performed_at`. |

## Versioning

The COTS schema is versioned. The current version is v0.1 (initial draft, pre-release). The versioning policy is at `../versioning.md`.

## How to engage

- **Read this document first.**
- **Read the JSON Schemas** at `cots/schemas/v1/` to see the normative structure.
- **Use the conformance test cases** at `compatibility-test-suite.md` to see how the schema is tested.
- **Propose a schema change** by opening an issue and an RFC.

## Related documents

- `Source-Engineering/source-engineering-model/model.md` -- the conceptual model.
- `Source-Engineering/source-engineering-model/entities.md` -- the data model.
- `Source-Engineering/source-engineering-model/integrity-questions.md` -- the integrity questions.
- `../versioning.md` -- the versioning policy.
- `../compatibility.md` -- the backward-compatibility guarantees.
- `Source-Engineering/source-engineering-conformance` -- the conformance suite that runs against this spec.