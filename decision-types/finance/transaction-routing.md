# Decision Types -- Finance

> The finance-domain sub-registry of the Source Engineering decision-types registry.

This sub-registry is at v0.1. The first decision-type is `transaction-routing-v0.1.0`, which captures the financial transaction routing use case.

## Decision-type: transaction-routing

**ID.** `urn:source-engineering:decision-type:transaction-routing:0.1.0`

**Name.** Transaction Routing

**Version.** 0.1.0

**Domain.** finance

**Description.** A typed question that asks the model to route a financial transaction to one of a declared set of destinations, with a COTS that supports the routing decision.

### Question schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "urn:source-engineering:decision-type:transaction-routing:0.1.0:question",
  "title": "Transaction Routing Question",
  "type": "object",
  "required": ["transaction", "destinations"],
  "properties": {
    "transaction": {
      "type": "object",
      "required": ["id", "amount", "currency"],
      "properties": {
        "id": {"type": "string"},
        "amount": {"type": "number"},
        "currency": {"type": "string"},
        "origin": {"type": "string"},
        "metadata": {"type": "object"}
      }
    },
    "destinations": {
      "type": "array",
      "minItems": 2,
      "items": {
        "type": "object",
        "required": ["id"],
        "properties": {
          "id": {"type": "string"},
          "name": {"type": "string"},
          "constraints": {"type": "object"}
        }
      }
    }
  }
}
```

### Answer schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "urn:source-engineering:decision-type:transaction-routing:0.1.0:answer",
  "title": "Transaction Routing Answer",
  "type": "object",
  "required": ["cots", "destination_id"],
  "properties": {
    "cots": {"$ref": "https://source-engineering.github.io/specifications/cots/schemas/v1/cots.schema.json"},
    "destination_id": {
      "type": "string",
      "description": "The id of the destination chosen."
    }
  }
}
```

### Evidence requirements

A conformant transaction-routing answer must include:

- At least one evidence item per constraint the routing decision considered.
- The destinations' constraints are recorded in the COTS's `evidence.evidence_chain[*].claims[*].supporting_evidence`.
- The counterevidence (the constraints that would have routed to a different destination) is recorded.

### Validation rules

A conformant transaction-routing answer must satisfy:

- The chosen destination is one of the declared destinations.
- The chosen destination satisfies the transaction's constraints.
- The COTS's `validation.feedback_surfaces` includes an `appeal` surface.

### Author

Emmanuel A. Otchere

### Maintainer

Emmanuel A. Otchere

## Related documents

- `../README.md` -- the registry overview.
- `../../cots/README.md` -- the COTS spec.
- `Source-Engineering/source-engineering-architecture` -- the four-layer stack that processes transaction-routing decisions.