# Agent-Governance-Evidence-Standard (AGES) V1
Fail-closed agent governance with offline-verifiable cryptographic evidence.


## Authorship and Maintenance

**Author:** Fran Martos  

**Initial Maintainer:** Wraithvector AI Systems  

**Reference implementation & verifier:** Apache 2.0 licensed  

**Enterprise runtime (non-open-source):** https://wraithvector.com


AGES is an open, vendor-neutral specification.
Wraithvector AI Systems provides the initial reference implementation.

## Purpose

The Agent Governance Evidence Standard (AGES) defines a canonical and
vendor-neutral format for recording and verifying governance decisions
made over AI agents and automated systems.

The standard focuses exclusively on:
- Deterministic decisions (ALLOW / BLOCK)
- Fail-closed enforcement
- Offline-verifiable cryptographic evidence

  ## Explicitly Out of Scope

AGES does NOT define or standardize:
- Risk scoring, probabilities, or severity levels as it is heuristic and interchangeable
- AI reasoning or model explanations
- Detection heuristics or classifiers
- Dashboards, monitoring, or observability UX
- Compliance certifications (SOC 2, ISO, etc.)

These concerns are implementation-specific and intentionally excluded.

## Scope Freeze (v1)

The following elements are frozen for AGES v1 and MUST NOT change:

- Canonical step structure
- Field ordering and canonicalization rules
- Hash computation method
- GENESIS semantics
- ALLOW / BLOCK decision model
- Fail-closed requirement

Any modification requires a new major version (v2).

## Vendor Extensions

AGES allows optional vendor-specific metadata under a non-hashed
`vendor_extensions` object.

Vendor extensions:
- MUST NOT affect verification outcomes
- MUST NOT be required to validate evidence
- MUST NOT alter the canonical decision

Reference implementations MAY define proprietary extensions.



