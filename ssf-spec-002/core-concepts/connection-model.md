# Connection Model

The wallet-gateway uses WebSocket as its sole transport. This document explains how connections are established, managed, and closed — and what wallet clients should expect at each stage.

---

## Why WebSocket

The gateway uses WebSocket exclusively because the core operations it serves are inherently asynchronous. When a payment is submitted, processing involves queuing, off-chain validation, network submission, and eventually on-chain confirmation. The time between submission and final outcome may span multiple network block intervals.

A request–response model over stateless HTTP would require the client to poll for status, introducing latency and unnecessary load. WebSocket allows the gateway to push status updates to the client as they occur, without the client needing to ask.

All other operations — balance queries, history retrieval, nonce lookups — are also served over the same WebSocket connection, keeping the client interface uniform.

---

## Establishing a Connection

Connections MUST use WSS (WebSocket over TLS). Plaintext WS connections are refused.

After the WebSocket handshake, the client has a short window (the gateway's configured authentication timeout, recommended 30 seconds) to send a valid signed message. Any connection that does not produce an authenticated message within this window is closed by the gateway.

There is no separate authentication handshake. The first message the client sends — whatever operation it is — serves as authentication. If the signature on that message is valid, the connection is established. If not, the connection is closed with an `AUTHENTICATION_ERROR`.

---

## One Connection Per Wallet

At any moment, only one active connection is permitted per wallet address. If a wallet opens a second connection — for example, after a network interruption before the first connection's idle timeout fires — the gateway accepts the new connection and terminates the previous one.

The previous connection is closed with code `4001` and reason `"superseded"`. Any push notifications that were queued for it are discarded.

This rule means that a client opening a fresh connection can be confident it is the only active session for its address. There is no risk of another device or session receiving its notifications.

---

## Idle Timeout and Connection Cleanup

Connections are not held open indefinitely. The gateway enforces an **idle timeout**: if no message is received from the client within the configured duration (recommended 5 minutes), the gateway closes the connection.

Before closing, the gateway sends a WebSocket `ping` frame. If the client responds with a `pong`, the idle timer resets and the connection remains open. A wallet client that wishes to maintain a long-lived connection without sending application messages SHOULD respond to ping frames. It MAY also send periodic low-cost messages to keep the connection active.

When a connection closes — for any reason — the gateway immediately releases all resources associated with it:

- all subscriptions registered on that connection are cancelled;
- any queued notification state is discarded; and
- the wallet address becomes available for a new connection immediately.

**Subscription state is never persisted across connections.** A client that reconnects after a timeout or network interruption must re-register any subscriptions it needs.

---

## Reconnection

There is no reconnection protocol at the gateway level. From the gateway's perspective, every new WebSocket handshake is a fresh connection. The client simply reconnects, sends a valid signed message, and is ready to operate.

Wallet clients SHOULD implement reconnection logic with appropriate backoff, particularly when a connection is lost unexpectedly. After reconnecting, the client SHOULD:

1. re-register any balance or transfer subscriptions it requires; and
2. query current balances and any pending submission statuses to resynchronise its local state.

---

## Related Documents

- [Message Authentication](./message-authentication.md)
- [Subscription Model](./subscription-model.md)
- [Formal Specification, Section 4](../specifications/ssf-spec-002.md#4-transport-and-connection-model)
- [Guide: Connect and Authenticate](../guides/connect-and-authenticate.md)
