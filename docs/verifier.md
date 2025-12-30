# Evidence Verification (AGES v1)

Normative description of the offline verification process for AGES evidence.

## 1. Purpose

This document defines the verification procedure for AGES Evidence Packs.

Verification MUST be possible:
- Offline
- Without access to the originating system
- Without trusting the vendor that produced the evidence

## 2. Verification Inputs

A verifier MUST accept:
- A single AGES Evidence Pack JSON file
- No external dependencies or network access

## 3. Verification Outputs

A verifier MUST produce exactly one of the following results:

- VALID
- TAMPERED

No intermediate or probabilistic states are allowed.

## 4. Verification Procedure

A verifier MUST perform the following steps in order:

1. Parse the Evidence Pack JSON.
2. Verify `schema_version` equals "ages.evidence.v1".
3. Verify all required top-level fields are present.
4. For each Canonical Agent Step:
   - Validate schema_version equals "ages.v1".
   - Canonicalize the step according to encoding and JSON rules.
   - Recompute the step_hash.
   - Compare with the provided chain.step_hash.
5. Verify step_index values are contiguous and ordered.
6. Verify each prev_step_hash matches the previous step_hash.
7. Verify the GENESIS step:
   - genesis = true
   - prev_step_hash = null
   - step_index = 0
8. Verify the final step_hash equals root_hash.

If any check fails, verification MUST stop and return TAMPERED.

If all checks pass, verification MUST return VALID.

## 5. Error Handling

Verification errors MUST NOT be silently ignored.

Any error, ambiguity, or malformed input results in TAMPERED.

## 6. Reference Implementation

AGES provides a reference verifier implementation
to demonstrate correct verification behavior.

The reference verifier is:
- Open source
- Vendor-neutral
- Not required to trust any backend

Implementations MAY differ internally but MUST
produce identical verification outcomes.
