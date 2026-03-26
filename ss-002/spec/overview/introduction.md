# Introduction

The wallet-gateway is the entry point of the Stablecoin Stack for wallet client applications. It is the single interface through which a wallet retrieves balances and history, fetches the data required to construct a payment authorisation, submits that authorisation for processing, and receives real-time confirmation of the outcome.

Wallet developers do not interact with the payment network directly. They interact with the wallet-gateway. This boundary is intentional: it delegates all network-layer complexity to the processor's infrastructure and provides wallet implementers with a clean, well-defined interface.

---

## What the wallet-gateway Does

The wallet-gateway provides five categories of service to connected wallet clients:

**Data retrieval** — current token balances, transfer history, permit nonces, and fee breakdowns are available on demand. All data is scoped to the requesting wallet's address and the set of tokens supported by the processor.

**Payment submission** — the wallet submits a fully signed payment payload. The gateway accepts it, routes it to the broadcast-service for validation and processing, and delivers status updates as the operation progresses through to network confirmation.

**Acquirer registration submission** — the same asynchronous submission model applies to acquirer registration requests.

**Real-time notifications** — a wallet may subscribe to receive push notifications for balance changes and new confirmed transfers on any subset of supported tokens. Notifications are delivered over the same WebSocket connection without polling.

**Wallet initialisation** — on a wallet's first connection, the gateway transparently collects its complete transfer history and initial balance snapshot, making this data immediately available for subsequent queries.

---

## Who This Is For

**Wallet developers** building a conformant client application should start here, then read [Connection Model](../core-concepts/connection-model.md) and [Message Authentication](../core-concepts/message-authentication.md) to understand the transport and security model, then follow the [guides](../guides/) for step-by-step implementation. The [formal specification](../SPEC.md) is the normative reference for all interface details.

**Processor operators** implementing or deploying a wallet-gateway instance should read the formal specification in full, paying particular attention to Sections 4 (transport and connection model), 5 (authentication), and 15 (conformance requirements).

**Auditors** should go directly to the [formal specification](../SPEC.md), Sections 5 and 13.

---

## Relationship to SS-SPEC-001

This specification is a companion to SS-SPEC-001 (The Stablecoin Stack). SS-SPEC-001 defines the overall system architecture, the data structures used in payment payloads, and the conformance requirements for the wallet-gateway at a system level (Section 5.3). This document provides the full interface specification for that component.

All data structures referenced here — `TransferRequest`, `BuyAcquiringPackRequest`, `PermitParams`, and related types — are defined normatively in SS-SPEC-001 and referenced here by section number.

---

## Related Documents

- [Connection Model](../core-concepts/connection-model.md)
- [Message Authentication](../core-concepts/message-authentication.md)
- [Subscription Model](../core-concepts/subscription-model.md)
- [Formal Specification — SS-SPEC-004](../SPEC.md)
- [SS-SPEC-001 — The Stablecoin Stack](../../ss-001/SPEC.md)
