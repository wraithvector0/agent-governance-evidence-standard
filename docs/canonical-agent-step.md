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
