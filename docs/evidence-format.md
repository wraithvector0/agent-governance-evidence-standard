# Evidence Format (AGES v1)

Normative definition of the exported evidence container for AGES v1.

## 1. Purpose

This document defines the canonical structure of an AGES Evidence Pack.

An Evidence Pack bundles one or more Canonical Agent Steps together
with the metadata required for independent, offline verification.

## 2. Core Requirements

An Evidence Pack MUST:
- Be self-contained
- Be verifiable offline
- Not require access to the originating system
- Preserve step ordering and integrity

Any deviation results in INVALID evidence.

## 3. Evidence Pack Structure

An Evidence Pack MUST be a single JSON object with the following
top-level fields.

### 3.1 schema_version

- Type: string
- REQUIRED
- Value MUST be: "ages.evidence.v1"

### 3.2 exported_at

- Type: string
- REQUIRED
- ISO 8601 UTC timestamp with 'Z'

### 3.3 tenant_id

- Type: string
- REQUIRED
- MUST match the tenant_id of all included steps

### 3.4 request_id

- Type: string
- REQUIRED
- MUST match the request_id of all included steps

### 3.5 steps

- Type: array
- REQUIRED

An ordered array of Canonical Agent Steps as defined in
`canonical-agent-step.md`.

Rules:
- MUST be ordered by step_index ascending
- MUST include the GENESIS step
- MUST include all steps without gaps

### 3.6 root_hash

- Type: string
- REQUIRED

The SHA-256 hash (hex) of the final step in the chain.

This value serves as the verification anchor.

## 4. Verification Rules

An Evidence Pack is VALID if and only if:

- All steps validate individually
- step_index values are contiguous
- Each step_hash matches recomputation
- Each prev_step_hash matches the previous step_hash
- The final step_hash equals root_hash

Any failure results in TAMPERED evidence.

## 5. Prohibited Content

An Evidence Pack MUST NOT contain:
- Raw PII
- Raw prompts or documents
- Secrets or credentials
- Model internals or weights

Only hashes and safe metadata are permitted.

## 6. Minimal Example

```json
{
  "schema_version": "ages.evidence.v1",
  "exported_at": "2025-12-30T13:00:00.000Z",
  "tenant_id": "tnt_123",
  "request_id": "req_2025_12_30_001",
  "steps": [ /* Canonical Agent Steps */ ],
  "root_hash": "ab12cd34ef56..."
}
