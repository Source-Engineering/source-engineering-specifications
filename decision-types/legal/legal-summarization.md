# Decision Types -- Legal

> The legal-domain sub-registry of the Source Engineering decision-types registry.

This sub-registry is at v0.1. The first decision-type is `legal-summarization-v0.1.0`, which captures the legal-document summarisation use case from the 2025 essay and the first experimental protocol in `Source-Engineering/source-engineering-research/protocols/legal-summarization-evaluation.md`.

## Decision-type: legal-summarization

**ID.** `urn:source-engineering:decision-type:legal-summarization:0.1.0`

**Name.** Legal Document Summarisation

**Version.** 0.1.0

**Domain.** legal

**Description.** A typed question that asks the model to summarise a legal document and produce a COTS that supports the summary.

### Question schema

The question schema describes the input the model accepts.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "urn:source-engineering:decision-type:legal-summarization:0.1.0:question",
  "title": "Legal Document Summarisation Question",
  "type": "object",
  "required": ["document"],
  "properties": {
    "document": {
      "type": "object",
      "required": ["uri", "media_type"],
      "properties": {
        "uri": {"type": "string", "format": "uri"},
        "media_type": {"type": "string"},
        "language": {"type": "string"},
        "jurisdiction": {"type": "string"}
      }
    },
    "summary_intent": {
      "type": "string",
      "enum": ["executive", "clause-by-clause", "risk-focused"]
    },
    "audience": {
      "type": "string",
      "enum": ["attorney", "paralegal", "in-house-counsel", "business-stakeholder"]
    }
  }
}
```

### Answer schema

The answer schema describes the output the model produces.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "urn:source-engineering:decision-type:legal-summarization:0.1.0:answer",
  "title": "Legal Document Summarisation Answer",
  "type": "object",
  "required": ["cots", "summary_text"],
  "properties": {
    "cots": {"$ref": "https://source-engineering.github.io/specifications/cots/schemas/v1/cots.schema.json"},
    "summary_text": {
      "type": "string",
      "minLength": 50,
      "description": "The summary text the reviewer reads."
    }
  }
}
```

### Evidence requirements

A conformant legal-summarization answer must include:

- At least one evidence item per material claim in the summary.
- At least one piece of counterevidence considered (a counterargument or a clause that contradicts the summary's conclusion).
- The source artifact (the document being summarised) is recorded in the COTS's `inputs.source_artifacts`.

### Validation rules

A conformant legal-summarization answer must satisfy:

- The summary text does not introduce facts not supported by the document.
- The COTS's evidence chain reconstructs from each summary claim to a clause in the document.
- The COTS's `validation.feedback_surfaces` includes an `appeal` surface.

### Author

Emmanuel A. Otchere

### Maintainer

Emmanuel A. Otchere

## Related documents

- `../README.md` -- the registry overview.
- `../../cots/README.md` -- the COTS spec.
- `Source-Engineering/source-engineering-research/protocols/legal-summarization-evaluation.md` -- the experimental protocol that produces findings about this decision-type.
- `Source-Engineering/source-engineering-case-studies/2026-legal-summarization/` (forthcoming, P4) -- the case study derived from the experimental protocol.