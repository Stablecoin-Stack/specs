# Message Authentication

Every message sent to the wallet-gateway must be signed by the private key of the wallet address it claims to be from. There are no sessions, tokens, or cookies. The signature on the message is the credential.

---

## The Model

Authentication in the gateway is per-message and cryptographic. Each message carries:

- the wallet address making the request (`callerAddress`);
- a deadline after which the message is no longer valid;
- the operation and its parameters (`payload`); and
- an ECDSA signature over the message content.

The gateway verifies that the signature was produced by the private key corresponding to `callerAddress`. If it was, the message is accepted. If it was not — for any reason — the message is rejected.

This model has two important properties. First, there is nothing to steal: there are no session tokens that would grant an attacker access if intercepted. Second, it is stateless from an authorisation perspective: the gateway does not need to maintain a session table or issue credentials.

---

## The Message Envelope

All messages from the client share a common envelope structure:

```typescript
interface GatewayMessage {
  type: string;          // The operation being requested.
  callerAddress: string; // The wallet's EVM address (0x-prefixed, 42 characters).
  deadline: number;      // Unix timestamp in seconds. The message is invalid after this time.
  payload: object;       // Operation-specific parameters.
  signature: MessageSig;
}

interface MessageSig {
  hash: string; // The EIP-712 digest of the message (bytes32 hex string).
  v: number;    // ECDSA recovery parameter. Must be 27 or 28.
  r: string;    // bytes32 hex string.
  s: string;    // bytes32 hex string.
}
```

The `hash` field is not self-reported and trusted — the gateway recomputes the digest from the message contents and checks that it matches. A mismatch results in rejection.

---

## What the Gateway Checks

For every received message, the gateway performs these checks in order:

1. **Structure** — all required fields are present and correctly formatted.
2. **Deadline** — the `deadline` has not passed. The gateway allows a small tolerance for clock differences between client and server.
3. **Signature format** — `v` is 27 or 28 (values 0 and 1 are normalised automatically; all other values are rejected). `r` and `s` are valid 32-byte hex strings.
4. **Hash match** — the gateway recomputes the EIP-712 digest and verifies it equals `signature.hash`.
5. **Signature recovery** — the signing address is recovered from the signature.
6. **Address match** — the recovered address equals `callerAddress`.

Failure at any step results in immediate rejection and an `AUTHENTICATION_ERROR` response. No partial processing occurs.

---

## Setting the Deadline

The `deadline` is a Unix timestamp in seconds. It should be set to a point in the near future — typically one to five minutes from the time the message is constructed. This limits the window during which an intercepted signed message could be replayed.

The gateway also maintains a short-lived cache of processed message hashes to detect and reject exact duplicates within the validity window.

---

## What This Means for Wallet Developers

- Every message you send must be signed. There are no unsigned requests.
- Signing must be performed locally on the device. Private keys must never be sent to the gateway or any other remote service.
- Each message needs a fresh `deadline`. Reusing a signed message after its deadline has passed will result in rejection.
- The `hash` field in `signature` must be the EIP-712 digest of the message you are sending — not a cached or reused value.

---

## Related Documents

- [Connection Model](./connection-model.md)
- [Formal Specification, Section 5](../specifications/ssf-spec-002.md#5-authentication-and-message-security)
- [Guide: Connect and Authenticate](../guides/connect-and-authenticate.md)
- [SSF-SPEC-001, Section 6 — Cryptographic Conventions](../../ssf-spec-001/specifications/ssf-spec-001.md#6-cryptographic-conventions)
