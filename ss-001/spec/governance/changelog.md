---
document: SS-SPEC-001/governance/changelog.md
spec: SS-SPEC-001
version: 1.0.0
status: Draft
date: 2026-03-04
author: "Adalton Reis <reis@stablecoinstack.org>"
organization: Stablecoin Stack Foundation
license: Apache License 2.0
---

# Changelog

All notable changes to the Stablecoin Stack specification (SS-SPEC-001) are recorded here.

This log follows [Semantic Versioning 2.0.0](https://semver.org). For the versioning policy see [versioning-policy.md](./versioning-policy.md).

---

## [1.0.0] — 2026-03-04

**Authors:** Adalton Reis \<reis@stablecoinstack.org\>

### Added

- Initial release of the unified Stablecoin Stack specification.
- Unification of three predecessor documents into a single specification under the Layered Docs structure:
  - SS-SPEC-000 (System Overview and Architecture)
  - SS-SPEC-001 pre-unification (Instant Payment With Permitted Token Transfer — Submission)
  - SS-SPEC-002 pre-unification (The Settlement Contract — Canonical)
- Formal specification: design philosophy, system architecture, component responsibilities, cryptographic conventions, data structures, submission payloads, acquiring model, Settlement Contract interface (state variables, events, fee model, functions), validation rules, end-to-end payment flow, security model, conformance requirements.
- Full Layered Docs layer set: overview, core-concepts, specifications, reference, guides, governance.
- Planned companion specification registry (SS-SPEC-002 through SS-SPEC-009) covering individual microservice interfaces.

### Notes

- The `ref` field currently concatenates two logically independent values (Order Reference and Acquirer ID). Separation into independent fields is identified as future work and will be a MINOR version change.
- Offline transaction signing is identified as future work and is explicitly out of scope for this version.

---

*Entries for future versions will be added above this line in reverse chronological order.*
