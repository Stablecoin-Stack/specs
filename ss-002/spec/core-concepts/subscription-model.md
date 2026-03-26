# Subscription Model

The wallet-gateway can push real-time notifications to connected clients without polling. This document explains how the subscription model works and what clients should expect.

---

## Two Channels

Two push notification channels are available:

**Balance updates** — the gateway notifies the client whenever the balance of a subscribed token changes for the connected wallet address. The notification carries the new balance.

**Transfer notifications** — the gateway notifies the client whenever a new confirmed transfer involving the connected wallet address is indexed for a subscribed token. The notification carries the full transfer record, including direction, amount, counterparty, and block timestamp.

Both channels can be active simultaneously. A client may subscribe to any combination of tokens on either or both channels.

---

## Subscriptions Are Connection-Scoped

Subscriptions exist only for the lifetime of the connection on which they were created. They are not persisted, not transferred to a new connection, and not recoverable after a disconnect.

This has practical consequences:

- When the gateway closes a connection due to idle timeout, all subscriptions on that connection are cancelled immediately.
- When a connection is superseded by a new connection from the same wallet, the old connection's subscriptions are cancelled along with the old connection.
- When a client reconnects after any disconnection — planned or unplanned — it starts with no subscriptions and must re-register the ones it needs.

This design keeps the gateway stateless between connections and ensures there is no ambiguity about which connection should receive a notification.

---

## Subscribing

To subscribe, the client sends a `SUBSCRIBE_BALANCE` or `SUBSCRIBE_TRANSFERS` message specifying one or more domain separators (token identifiers). The gateway confirms the subscription with an acknowledgement message listing the tokens that are now active.

A client may subscribe to as many tokens as needed in a single message, or add tokens incrementally across multiple messages. If a subscription message references an unsupported token, the entire request is rejected with an `UNSUPPORTED_TOKEN` error — no partial subscriptions are created.

---

## Unsubscribing

A client may cancel a subscription for specific tokens at any time by sending an `UNSUBSCRIBE` message specifying the channel and the tokens to remove. The remaining subscriptions on that connection are unaffected.

---

## Keeping Connections Active

Because subscriptions are tied to the connection, a client that wants to maintain long-running subscriptions must keep the connection alive. The gateway closes idle connections after a configurable timeout (recommended: 5 minutes). The gateway sends a `ping` frame before closing; clients SHOULD respond to `ping` frames with `pong` to reset the idle timer.

A client that wants to maintain subscriptions over an extended period should respond to pings or send periodic messages to prevent the connection from being considered idle.

---

## After Reconnection

After reconnecting, a client should:

1. re-register all subscriptions it needs by sending fresh `SUBSCRIBE_BALANCE` and/or `SUBSCRIBE_TRANSFERS` messages;
2. query current balances via `GET_BALANCE` to resynchronise, since notifications that arrived during the disconnection window were not delivered; and
3. optionally query recent history via `GET_HISTORY` to recover any transfers that occurred while disconnected.

---

## Relationship to Settlement Confirmation

The `TRANSFER_NOTIFICATION` push from the transfer channel is the definitive signal of final settlement. A `SUBMISSION_STATUS` notification with status `SUCCESS` indicates that a submitted transaction was accepted by the network without reverting — but this is not final settlement. Final settlement is confirmed only after the transfer-history service observes the corresponding on-chain event with sufficient block confirmations and delivers it as a `TRANSFER_NOTIFICATION`.

Wallet applications MUST NOT present a payment as complete based on `SUCCESS` alone.

---

## Related Documents

- [Connection Model](./connection-model.md)
- [Formal Specification, Section 9](../SPEC.md#9-subscription-operations)
- [Formal Specification, Section 10 — Asynchronous Status Notifications](../SPEC.md#10-asynchronous-status-notifications)
- [Guide: Subscribe to Updates](../guides/subscribe-to-updates.md)
