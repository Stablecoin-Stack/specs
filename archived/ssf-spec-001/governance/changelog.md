# Change Log — SS-SPEC-001

This file records all changes to the SS-SPEC-001 specification group (Instant Payment With Permitted Token Transfer — Submission). Changes to companion specifications (SS-SPEC-000, SS-SPEC-002, etc.) are recorded in their respective change logs.

---

## [1.0.0] — 2026-03-04

**Status:** Draft
**Authors:** Adalton Reis \<reis@stablecoinstack.org\>
**Summary:** Initial release of SS-SPEC-001 and its supporting documentation under the Layered Docs structure.

**Files introduced:**

- `overview/ss-spec-001/payment-submission.md` — What the submission flow is, who it involves, and the two submission flows.
- `core-concepts/ss-spec-001/permit-based-payments.md` — ERC-2612 permits, gasless transfers, EIP-712 signing, and nonce-based replay protection.
- `specifications/ss-spec-001/ss-spec-001.md` — Normative specification: data structures, cryptographic conventions, validation rules, submission flows, error handling, and security considerations.
- `reference/ss-spec-001/payload-fields.md` — Flat field-level reference for all structures and payload types, encoding rules, and error categories.
- `guides/ss-spec-001/submit-a-payment.md` — Step-by-step guide for constructing, signing, and submitting a `TransferRequest`.
- `guides/ss-spec-001/buy-acquiring-pack.md` — Step-by-step guide for constructing, signing, and submitting a `BuyAcquiringPackRequest`.
- `governance/ss-spec-001/changelog.md` — This file.

---

## License

Copyright © 2026 Stablecoin Stack Foundation. All rights reserved.

All specifications and documentation in this repository are published under the Apache License, Version 2.0. Unless required by applicable law or agreed to in writing, material distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
