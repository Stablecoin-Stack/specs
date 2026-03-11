# Gateway Role

The wallet-gateway sits at the boundary between wallet client applications and the internal services of the Stablecoin Stack. Understanding what it does — and explicitly what it does not do — is essential for both wallet developers and processor operators.

---

## What the wallet-gateway Is

The wallet-gateway is a **mediation and delivery layer**. It:

- accepts authenticated WebSocket connections from wallet clients;
- validates every inbound message before forwarding it to any internal service;
- routes requests to the appropriate internal service: the broadcast-service for payment submissions, the balance-and-history service for queries, and the data layer for nonces and fee calculations;
- delivers responses and push notifications back to the connected client; and
- enforces connection management rules, including the single-connection-per-wallet constraint and idle timeout.

---

## What the wallet-gateway Is Not

**It is not an execution engine.** The gateway does not broadcast transactions, verify on-chain signatures, or interact with the Settlement Contract. When a wallet submits a payment, the gateway enqueues it with the broadcast-service and relays status updates. All on-chain activity originates from the broadcast-submitter.

**It is not a custodian.** The gateway holds no token balances and has no authority over any on-chain state. A compromise of the gateway does not expose user funds. The cryptographic integrity of every payment is enforced by the Settlement Contract, independently of the gateway.

**It is not a source of truth for settlement.** The gateway can report that a transaction was accepted by the network without error. It cannot confirm final settlement. Final settlement is determined by the transfer-history service after a configurable number of block confirmations, and is delivered to the wallet as a `TRANSFER_NOTIFICATION` push notification.

---

## Position in the Broadcast Layer

Within SSF-SPEC-001's component model, the wallet-gateway is one of four components in the Broadcast Layer:

| Component | Role |
|---|---|
| **wallet-gateway** | External-facing entry point. Wallet connections, message validation, request routing, notification delivery. |
| **broadcast-service** | Internal submission lifecycle management. Validates payloads, manages state transitions, publishes updates. |
| **broadcast-submitter** | Holds the Relayer account. The only component that issues on-chain transactions. |
| **balance-and-history** | Maintains real-time balance views and transfer event records. |

The wallet-gateway is the only component in the Broadcast Layer that wallet clients communicate with directly.

---

## Related Documents

- [Introduction](./introduction.md)
- [Connection Model](../core-concepts/connection-model.md)
- [SSF-SPEC-001, Section 5.3 — Broadcast Layer](../../ssf-spec-001/specifications/ssf-spec-001.md#53-broadcast-layer)
