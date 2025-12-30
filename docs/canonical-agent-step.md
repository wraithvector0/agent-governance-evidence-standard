# Canonical Agent Step (AGES v1)

Normative definition of a single, hash-verifiable governance decision.

## 1. Purpose

This document defines the Canonical Agent Step (CAS) for the
Agent Governance Evidence Standard (AGES) v1.

A Canonical Agent Step represents a single governance decision
made by a system that controls or supervises AI agents.

Each step is:
- Deterministic
- Fail-closed
- Cryptographically verifiable
- Independently auditable offline

## 2. Core Principle

AGES governs **decisions**, not intelligence.

The standard does not attempt to measure, score, or explain AI behavior.
It records whether an action was allowed or blocked, under which rules,
and provides immutable evidence of that decision.

## 3. Definitions

**Agent**  
Any autonomous or semi-autonomous system capable of taking actions.

**Governance Gateway**  
A system that intercepts agent actions and produces an ALLOW or BLOCK decision.

**Canonical Agent Step (CAS)**  
The minimal, atomic record of a single governance decision.

**Evidence Chain**  
An ordered sequence of Canonical Agent Steps linked by cryptographic hashes.

## 4. Normative Language

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**,
**SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL**
in this document are to be interpreted as described in RFC 2119.

## 5. Canonicalization Rules

A Canonical Agent Step MUST be serialized and hashed in a fully
deterministic manner.

Any deviation from these rules results in an INVALID step and
MUST cause verification to fail.

## 6. Encoding

A Canonical Agent Step MUST be encoded using UTF-8.

- Byte Order Mark (BOM) MUST NOT be used.
- Newline character MUST be `\n`.
- Any other encoding or newline form results in an INVALID step.

## 7. JSON Canonical Form

A Canonical Agent Step MUST be represented as a JSON object
serialized in Canonical JSON form.

The following rules are REQUIRED:

- Object keys MUST be sorted lexicographically by byte order.
- No trailing commas are allowed.
- No insignificant whitespace is allowed.
- Numbers MUST be represented in base-10 notation.
- Scientific notation MUST NOT be used.
- Boolean values MUST be `true` or `false`.
- Null values MUST be `null`.

If a non-canonical JSON serializer is used, the output MUST be
canonicalized before hashing.

## 8. Hash Algorithm

AGES v1 uses the SHA-256 cryptographic hash function.

- Hash output MUST be represented as lowercase hexadecimal.
- The hash MUST be computed over the canonical byte representation
  of the Canonical Agent Step.

## 9. Canonical Agent Step Schema

A Canonical Agent Step (CAS) MUST be represented as a single JSON object
with the exact fields defined in this section.

- All fields listed as REQUIRED MUST be present.
- No additional fields are allowed unless explicitly defined as extensions.
- Any unknown or missing field results in an INVALID step.

### 9.1 Required Top-Level Fields

The following top-level fields are REQUIRED and MUST appear exactly
as defined below.

#### 9.2 schema_version

- Type: string
- REQUIRED

The version identifier of the AGES schema.

For AGES v1, the value MUST be:

"ages.v1"

#### 9.3 tenant_id

- Type: string
- REQUIRED

A stable identifier representing the tenant or organizational boundary
under which the governance decision was made.

This value MUST NOT contain secrets or personally identifiable information.

#### 9.4 request_id

- Type: string
- REQUIRED

A correlation identifier representing a single logical execution or run
across multiple Canonical Agent Steps.

#### 9.5 step_id

- Type: string
- REQUIRED

A unique identifier for this step within a given request_id.

#### 9.6 step_index

- Type: integer
- REQUIRED

A monotonically increasing index starting at 0.

Gaps, duplicates, or reordering result in verification failure.

#### 9.7 timestamp

- Type: string
- REQUIRED

An ISO 8601 timestamp in UTC, terminated with 'Z'.

Example:
2025-12-30T12:34:56.789Z

#### 9.8 kind

- Type: string
- REQUIRED

The semantic type of the step.

Allowed values:
- "GENESIS"
- "GOVERNANCE_DECISION"
- "EXPORT"

#### 9.9 actor

- Type: object
- REQUIRED

Identifies the entity responsible for initiating the action
subject to governance.

Required fields:

- `type` (string, REQUIRED)
  Allowed values:
  - "agent"
  - "user"
  - "system"

- `id` (string, REQUIRED)
  A stable identifier for the actor.

The actor object MUST NOT contain secrets or personally identifiable information.

#### 9.10 subject

- Type: object
- REQUIRED

Describes what is being governed by this step.

Required fields:

- `type` (string, REQUIRED)
  Allowed values:
  - "prompt"
  - "tool"
  - "action"

- `name` (string, REQUIRED)
  A human-readable identifier for the subject.

Examples:
- Prompt: { "type": "prompt", "name": "primary" }
- Tool: { "type": "tool", "name": "ocr_id_check" }

#### 9.11 input

- Type: object
- REQUIRED

Represents the input material evaluated by the governance gateway.

Required fields:

- `input_class` (string, REQUIRED)
  Allowed values:
  - "raw"
  - "sanitized"
  - "redacted"

- `content_hash` (string, REQUIRED)
  SHA-256 hash (hex) of the canonicalized input payload.

- `content_type` (string, REQUIRED)
  MIME type of the input (e.g. "application/json", "text/plain").

The input object MUST NOT include raw content.
Only hashes and safe metadata are permitted.

#### 9.12 policy

- Type: object
- REQUIRED

Describes the policy evaluation performed by the governance gateway.

Required fields:

- `mode` (string, REQUIRED)
  Allowed values:
  - "enforcing"
  - "monitoring"

- `policy_set_id` (string, REQUIRED)
  Identifier of the active policy set version.

- `rules_evaluated` (array, REQUIRED)
  An ordered list of evaluated rules.

Each element of `rules_evaluated` MUST be an object with:

- `rule_id` (string, REQUIRED)
- `result` (string, REQUIRED)
  Allowed values:
  - "PASS"
  - "FAIL"
  - "ERROR"
- `reason_code` (string, REQUIRED)
  A short machine-readable code.
- `reason_detail` (string, REQUIRED)
  A short human-readable explanation.

The policy object MUST NOT include raw data, secrets, or model internals.

#### 9.13 decision

- Type: object
- REQUIRED

Represents the final governance outcome for this step.

Required fields:

- `outcome` (string, REQUIRED)
  Allowed values:
  - "ALLOW"
  - "BLOCK"

- `fail_closed` (boolean, REQUIRED)
  MUST be `true` when `policy.mode` is "enforcing".

- `latency_ms` (integer, REQUIRED)
  End-to-end decision latency in milliseconds.

- `error` (object or null, REQUIRED)

If `error` is not null, it MUST contain:

- `type` (string, REQUIRED)
  Examples: "TIMEOUT", "EXCEPTION", "UPSTREAM_DOWN"
- `message` (string, REQUIRED)
  A safe, non-sensitive description.
- `retryable` (boolean, REQUIRED)

If `error` is not null, the `outcome` MUST be "BLOCK".

#### 9.14 outputs

- Type: object
- REQUIRED

Represents the output produced after the governance decision.

Required fields:

- `sanitized_output_hash` (string or null, REQUIRED)
  SHA-256 hash (hex) of the sanitized output payload.
  MUST be null if `decision.outcome` is "BLOCK".

- `evidence_ref` (string, REQUIRED)
  Identifier referencing the exported evidence pack.

#### 9.15 chain

- Type: object
- REQUIRED

Defines the cryptographic linkage of this step within an evidence chain.

Required fields:

- `prev_step_hash` (string or null, REQUIRED)
  SHA-256 hash of the previous step.
  MUST be null if this step is GENESIS.

- `step_hash` (string, REQUIRED)
  SHA-256 hash of the canonical representation of this step.

- `genesis` (boolean, REQUIRED)
  MUST be true if and only if `kind` is "GENESIS".

Rules:

- If `genesis` is true:
  - `prev_step_hash` MUST be null
  - `step_index` MUST be 0
- If `genesis` is false:
  - `prev_step_hash` MUST be a valid SHA-256 hex string

## 10. Hash Computation Rules

The `step_hash` MUST be computed over the canonical byte representation
of the entire Canonical Agent Step as defined in Section 9,
with the following rule:

- The `chain.step_hash` field itself MUST be excluded from the hash input.

Procedure:

1. Set `chain.step_hash` to an empty string ("").
2. Serialize the Canonical Agent Step using the canonicalization rules
   defined in Sections 6 and 7.
3. Compute SHA-256 over the resulting byte sequence.
4. Encode the hash as lowercase hexadecimal.
5. Write the resulting value into `chain.step_hash`.

Verification MUST recompute the hash using the same procedure.
Any mismatch results in verification failure.

## 11. Minimal Complete Example

The following is a minimal, valid Canonical Agent Step example
shown in canonical JSON form.

```json
{
  "actor": { "id": "system", "type": "system" },
  "chain": { "genesis": true, "prev_step_hash": null, "step_hash": "" },
  "decision": { "error": null, "fail_closed": true, "latency_ms": 0, "outcome": "ALLOW" },
  "input": {
    "content_hash": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
    "content_type": "application/json",
    "input_class": "sanitized"
  },
  "kind": "GENESIS",
  "outputs": { "evidence_ref": "evp_0000", "sanitized_output_hash": null },
  "policy": { "mode": "enforcing", "policy_set_id": "polset_v1", "rules_evaluated": [] },
  "request_id": "req_2025_12_30_001",
  "schema_version": "ages.v1",
  "step_id": "step_0000",
  "step_index": 0,
  "subject": { "name": "run", "type": "action" },
  "tenant_id": "tnt_123",
  "timestamp": "2025-12-30T12:34:56.789Z"
}
```

After canonicalization and hashing, the computed value MUST be written
to chain.step_hash.
