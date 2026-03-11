# SSF-SPEC-002 — wallet-gateway Interface Specification

| Field | Value |
|---|---|
| **Document ID** | SSF-SPEC-002 |
| **Title** | wallet-gateway Interface Specification |
| **Version** | 1.0.0 |
| **Status** | Draft |
| **Date** | 2026-03-10 |
| **Conforms To** | SSF-SPEC-001 v1.0.0 |
| **Author(s)** | Adalton Reis [reis@stablecoinstack.org](mailto:reis@stablecoinstack.org) |
| **Reviewers** | — |
| **Organization** | Stablecoin Stack Foundation |
| **Contact** | [specs@stablecoinstack.org](mailto:spec@stablecoinstack.org) |
| **Public Address** | `0x0000000000000000000000000000000000000000` |
| **License** | Apache License 2.0 |

---

## Table of Contents

1. [Abstract](#1-abstract)
2. [Introduction](#2-introduction)
   - 2.1 [Purpose](#21-purpose)
   - 2.2 [Scope](#22-scope)
   - 2.3 [Normative References](#23-normative-references)
   - 2.4 [Conventions and Terminology](#24-conventions-and-terminology)
3. [Role Within the Stablecoin Stack](#3-role-within-the-stablecoin-stack)
4. [Transport and Connection Model](#4-transport-and-connection-model)
   - 4.1 [WebSocket-Only Transport](#41-websocket-only-transport)
   - 4.2 [Single-Connection-Per-Wallet Constraint](#42-single-connection-per-wallet-constraint)
   - 4.3 [Connection Lifecycle and Idle Timeout](#43-connection-lifecycle-and-idle-timeout)
5. [Authentication and Message Security](#5-authentication-and-message-security)
   - 5.1 [Signature-Based Authentication](#51-signature-based-authentication)
   - 5.2 [Message Envelope](#52-message-envelope)
   - 5.3 [Signature Verification](#53-signature-verification)
   - 5.4 [Expiration and Replay Protection](#54-expiration-and-replay-protection)
6. [Token and Domain Separator Conventions](#6-token-and-domain-separator-conventions)
7. [Wallet Initialisation](#7-wallet-initialisation)
8. [Request–Response Operations](#8-requestresponse-operations)
   - 8.1 [Retrieve Nonce](#81-retrieve-nonce)
   - 8.2 [Retrieve Fees](#82-retrieve-fees)
   - 8.3 [Retrieve Balance](#83-retrieve-balance)
   - 8.4 [Retrieve Transfer History](#84-retrieve-transfer-history)
   - 8.5 [Submit Payment Request](#85-submit-payment-request)
   - 8.6 [Submit Acquirer Registration Request](#86-submit-acquirer-registration-request)
9. [Subscription Operations](#9-subscription-operations)
   - 9.1 [Subscribe to Balance Updates](#91-subscribe-to-balance-updates)
   - 9.2 [Subscribe to Transfer Notifications](#92-subscribe-to-transfer-notifications)
   - 9.3 [Unsubscribe](#93-unsubscribe)
10. [Asynchronous Status Notifications](#10-asynchronous-status-notifications)
    - 10.1 [Submission Status Lifecycle](#101-submission-status-lifecycle)
    - 10.2 [Status Notification Message](#102-status-notification-message)
11. [Message Type Reference](#11-message-type-reference)
12. [Error Handling](#12-error-handling)
13. [Security Considerations](#13-security-considerations)
14. [Future Work](#14-future-work)
15. [Conformance](#15-conformance)
16. [Change Log](#16-change-log)
17. [License](#17-license)

---

## 1. Abstract

This document specifies the interface of the **wallet-gateway** component of the Stablecoin Stack, as introduced in SSF-SPEC-001. The wallet-gateway is the sole network entry point for wallet client applications. It provides a WebSocket-based messaging interface through which wallets perform balance queries, history retrieval, nonce and fee lookups, payment submissions, and acquirer registration requests. It also delivers real-time notifications on submission status changes, balance updates, and new incoming transfers.

All communication is authenticated by cryptographic signature on every message. No session tokens, cookies, or API keys are used. The gateway does not execute operations directly; it mediates between the wallet client and the internal broadcast and indexing services of the Stablecoin Stack.

---

## 2. Introduction

### 2.1 Purpose

This specification defines the normative interface that a conformant wallet-gateway MUST implement, and that a conformant wallet client MUST be capable of consuming. It is the authoritative reference for:

- wallet application developers implementing client-side communication with the Stablecoin Stack;
- payment processor operators implementing or deploying a conformant wallet-gateway; and
- auditors and reviewers assessing the correctness and security of a gateway implementation.

### 2.2 Scope

This specification covers:

- the transport model and connection management requirements;
- the authentication scheme and message signing requirements;
- all request–response operations exposed by the gateway;
- the subscription model for real-time balance and transfer notifications;
- the asynchronous status notification model for payment and registration submissions;
- error categories and response formats; and
- security requirements specific to the gateway interface.

This specification does not cover:

- the internal protocol between wallet-gateway and broadcast-service (planned: SSF-SPEC-005);
- the internal protocol between wallet-gateway and balance-and-history;
- the on-chain Settlement Contract interface (specified in SSF-SPEC-001, Sections 10–13); or
- wallet key management, signing UX, or device-level security.

### 2.3 Normative References

| Reference | Description |
|---|---|
| SSF-SPEC-001 v1.0.0 | The Stablecoin Stack — foundational specification |
| ERC-20 | Token Standard — Ethereum Improvement Proposal 20 |
| ERC-2612 | Permit Extension for EIP-20 Signed Approvals |
| EIP-712 | Typed Structured Data Hashing and Signing |
| ECDSA | Elliptic Curve Digital Signature Algorithm (FIPS 186-4) |
| RFC 2119 | Key words for use in RFCs to Indicate Requirement Levels |
| RFC 6455 | The WebSocket Protocol |
| RFC 8446 | The Transport Layer Security (TLS) Protocol Version 1.3 |

### 2.4 Conventions and Terminology

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHOULD**, **RECOMMENDED**, **MAY**, and **OPTIONAL** are to be interpreted as described in RFC 2119.

All terms defined in SSF-SPEC-001 Section 2.4 carry the same meaning in this document. Additional terms introduced here:

| Term | Definition |
|---|---|
| Wallet Client | A conformant client application that connects to the wallet-gateway on behalf of a payer. |
| Gateway Message | A JSON object transmitted over the WebSocket connection, conforming to the message envelope defined in Section 5.2. |
| Caller Address | The EVM address that identifies the wallet in a given gateway message. Must match the address recovered from the message signature. |
| Domain Separator | A `bytes32` EIP-712 domain separator hex string, used as the primary identifier for a supported token throughout this specification. See Section 6. |
| Transfer History | The record of ERC-20 Transfer events for supported tokens associated with a given wallet address. Does not include transfers of unsupported tokens or any other on-chain activity. |
| Subscription | A per-connection registration to receive real-time push notifications for a specified set of tokens. Active only while the originating connection remains open and non-idle. |
| Idle Timeout | The gateway-configurable duration after which a connection with no activity is closed. See Section 4.3. |

---

## 3. Role Within the Stablecoin Stack

The wallet-gateway occupies the boundary between external wallet clients and the internal services of the Stablecoin Stack. It holds no token balances and has no authority to initiate on-chain transactions. Its responsibilities are:

- accepting and authenticating inbound WebSocket connections from wallet clients;
- routing validated requests to the appropriate internal service (broadcast-service, balance-and-history, or data layer);
- delivering responses and asynchronous notifications back to the connected client;
- enforcing the single-connection-per-wallet constraint (Section 4.2);
- closing idle connections and cleaning up associated subscription state (Section 4.3); and
- performing first-line validation of all inbound messages before forwarding.

The gateway MUST NOT execute payment operations directly. Upon receiving a valid payment or registration submission, it MUST enqueue the request with the broadcast-service and await the corresponding status updates, which it then relays to the client.

---

## 4. Transport and Connection Model

### 4.1 WebSocket-Only Transport

The wallet-gateway MUST expose its interface exclusively over the **WebSocket protocol** (RFC 6455). No REST, HTTP polling, or alternative transport MUST be offered for any operation defined in this specification.

All WebSocket connections MUST be established over TLS (WSS). Plaintext WebSocket connections (WS) MUST be refused.

The rationale for the WebSocket-only design is the inherently asynchronous nature of on-chain submission and confirmation. Operations such as payment submission require the client to remain connected and receive status updates over a period that may span multiple network block intervals.

### 4.2 Single-Connection-Per-Wallet Constraint

The wallet-gateway MUST enforce a maximum of **one active connection per wallet address** at any time. A wallet address is identified by the `callerAddress` field present in all authenticated messages (Section 5.2).

When a new connection is established and authentication succeeds for a wallet address that already has an active connection, the gateway MUST:

1. accept the new connection;
2. terminate the existing connection for that address with WebSocket close code `4001` and reason `"superseded"`; and
3. discard any pending notifications queued for the previous connection.

This behaviour ensures that the most recently authenticated client is always the active one. Wallet applications SHOULD handle unexpected disconnection gracefully and re-establish the connection when required.

### 4.3 Connection Lifecycle and Idle Timeout

WebSocket connections are **not** maintained indefinitely. The gateway MUST enforce an **idle timeout**: any connection on which no message has been received within a gateway-configurable duration MUST be closed.

The RECOMMENDED idle timeout is **5 minutes**. Processors MAY configure a shorter or longer value appropriate to their operational environment, but the value MUST be documented and consistent for all clients.

The idle timeout is reset on every valid inbound message. The gateway SHOULD send a WebSocket `ping` frame before closing an idle connection, allowing the client a brief window (RECOMMENDED: 30 seconds) to respond. If the client responds with a `pong` frame, the idle timer MUST be reset and the connection MUST remain open.

Upon closing a connection for any reason — idle timeout, supersession (Section 4.2), or client-initiated close — the gateway MUST immediately release all resources associated with that connection, including:

- all active subscriptions registered on that connection;
- any in-memory state tracking pending notification delivery for that connection; and
- the wallet address slot, making it immediately available for a new connection.

Subscription state is not persisted across connections. A client that reconnects MUST re-register any subscriptions it requires.

Upon establishing a new WebSocket connection, the client MUST send an authenticated message within a gateway-configurable timeout (RECOMMENDED: 30 seconds). Connections that do not produce an authenticated message within this window MUST be closed by the gateway.

---

## 5. Authentication and Message Security

### 5.1 Signature-Based Authentication

The wallet-gateway does not use session tokens, API keys, cookies, or any persistent credential. **Every message** sent by a wallet client MUST be individually signed by the private key corresponding to the wallet's address. The gateway MUST verify this signature on every received message, without exception.

Authentication and authorisation are inseparable in this model: a valid signature over a well-formed message constitutes both proof of identity and authorisation for the requested operation.

### 5.2 Message Envelope

All messages sent by a wallet client MUST conform to the following JSON envelope:

```typescript
interface GatewayMessage {
  type: string;            // Message type identifier. See Section 11.
  callerAddress: string;   // EVM address of the sender. 42-char hex string (0x-prefixed).
  deadline: number;        // Unix timestamp (seconds). Message MUST be rejected if expired.
  payload: object;         // Operation-specific parameters. Defined per message type.
  signature: MessageSig;   // ECDSA signature over the canonical message hash.
}

interface MessageSig {
  hash: string;   // bytes32 — 66-char hex string. EIP-712 digest of the message.
  v: number;      // uint8. Must be 27 or 28 (values 0/1 are normalised).
  r: string;      // bytes32 — 66-char hex string.
  s: string;      // bytes32 — 66-char hex string.
}
```

### 5.3 Signature Verification

Upon receiving a message, the gateway MUST perform the following checks in order, rejecting immediately on any failure:

1. **Structural validation** — all envelope fields are present and correctly typed.
2. **Deadline check** — `deadline` is strictly greater than the gateway's current clock. The gateway SHOULD apply a configurable tolerance (RECOMMENDED: 30 seconds) to account for clock skew.
3. **Signature format validation** — `signature.v` is in `{0, 1, 27, 28}`. Values `0` or `1` MUST be normalised to `27` or `28` respectively. All other values MUST be rejected. `r` and `s` MUST be valid 32-byte hex strings.
4. **Hash verification** — the gateway MUST independently recompute the EIP-712 digest of the message contents and verify that it equals `signature.hash`. Mismatches MUST be rejected.
5. **Signature recovery** — the gateway MUST recover the signing address from `(signature.hash, v, r, s)` using ECDSA.
6. **Address match** — the recovered address MUST equal `callerAddress`. If it does not, the message MUST be rejected regardless of whether the signature is otherwise cryptographically valid.

The gateway MUST NOT partially process a message that fails any validation step.

### 5.4 Expiration and Replay Protection

The `deadline` field provides time-bounded validity for each message. Clients SHOULD set deadlines no further than a few minutes in the future for interactive operations.

The gateway SHOULD maintain a short-lived cache of recently processed message hashes to detect and reject duplicate submissions within the validity window. This is a defence-in-depth measure at the gateway layer; the primary replay protection for payment operations is enforced by the `usedHashes` registry in the Settlement Contract (SSF-SPEC-001, Section 18.3).

---

## 6. Token and Domain Separator Conventions

Throughout the wallet-gateway interface, supported tokens are identified primarily by their **EIP-712 domain separator** — a `bytes32` hex string that uniquely identifies a token contract within a specific network and version context.

Full token metadata — including domain separator, contract address, token name, decimals, version, and network identifier — is available from the Basic Data Service (basic-data-server). Wallet clients SHOULD retrieve and cache this data at startup.

The two exceptions where a token is identified by its **contract address** rather than its domain separator are the `PayWithPermitParams` and `BuyAcquiringPackPermitParams` structures (defined in SSF-SPEC-001, Sections 7.3 and 7.4). These structures are transmitted to the Settlement Contract and require the token address for on-chain permit validation.

The gateway MUST reject any request referencing a domain separator or token address that does not correspond to a currently supported token.

---

## 7. Wallet Initialisation

When a wallet address connects to the gateway for the **first time** — meaning no prior state exists for that address in the gateway's data store — the gateway MUST perform a transparent initialisation procedure:

1. **History collection** — the gateway retrieves the complete transfer history for the connecting address across all supported tokens, sourcing data from the balance-and-history service and the on-chain event index.
2. **Balance snapshot** — current token balances across all supported tokens are recorded in the data store concurrently with history collection.

This initialisation is transparent to the client; no explicit request is required. If initialisation is still in progress when a balance or history query arrives, the gateway MUST respond with an `INITIALISING` error (Section 12) rather than returning incomplete data.

Subsequent connections from the same address do not trigger re-initialisation. The gateway maintains an incrementally updated view of balances and history thereafter, independent of whether a client is connected.

---

## 8. Request–Response Operations

All request–response operations use the message envelope defined in Section 5.2. The `type` field identifies the operation. The gateway MUST respond on the same WebSocket connection with a message of the corresponding response type.

All response messages carry a `requestId` field that MUST echo the `requestId` supplied in the originating request.

### 8.1 Retrieve Nonce

Retrieves the current ERC-2612 permit nonce for a given wallet address and token. This enables the wallet to construct a valid `PermitParams` structure without a direct on-chain read.

**Request type:** `GET_NONCE`

```typescript
interface GetNoncePayload {
  requestId: string;
  domainSeparator: string; // bytes32 hex string — domain separator of the target token.
}
```

**Response type:** `NONCE_RESULT`

```typescript
interface NonceResultPayload {
  requestId: string;
  domainSeparator: string;
  nonce: string; // Current permit nonce as a decimal string (uint256).
}
```

The gateway MUST retrieve the nonce from live on-chain state at the time of the request. Stale nonce values MUST NOT be returned; a stale nonce would cause the resulting permit signature to be invalid at submission time.

### 8.2 Retrieve Fees

Returns the fee breakdown for a given transfer amount and acquirer. This allows the wallet to determine the total amount the payer must hold and authorise.

**Request type:** `GET_FEES`

```typescript
interface GetFeesPayload {
  requestId: string;
  domainSeparator: string; // Token for which fees are being calculated.
  amount: string;          // Principal amount intended for the beneficiary (decimal string, uint256).
  acquirerId: string;      // bytes16 hex string. Pass Zero-UUID if no acquirer applies.
}
```

**Response type:** `FEES_RESULT`

```typescript
interface FeesResultPayload {
  requestId: string;
  domainSeparator: string;
  brokenDownAmount: BrokenDownAmount;
}

interface BrokenDownAmount {
  fees: string;      // Total fees in token units (decimal string).
  remaining: string; // Principal amount that will reach the beneficiary (decimal string).
}
```

The values in `BrokenDownAmount` correspond directly to the return values of `breakdownTransferAmount` on the Settlement Contract (SSF-SPEC-001, Section 12.3): `remaining` maps to `principalAmount` and `fees` maps to `totalFees`.

> **Future Work:** The current Settlement Contract applies a uniform `baseFeeAmount` regardless of the token being transferred. A forthcoming revision will introduce per-token fee configuration. When that change is implemented, the `domainSeparator` parameter will directly influence the fee calculation result. Implementations MUST pass the token context to fee computation functions when this capability becomes available. See Section 14.

### 8.3 Retrieve Balance

Returns the current token balances for the wallet across one or more supported tokens.

**Request type:** `GET_BALANCE`

```typescript
interface GetBalancePayload {
  requestId: string;
  domainSeparators: string[]; // One or more domain separator hex strings.
}
```

**Response type:** `BALANCE_RESULT`

```typescript
interface BalanceResultPayload {
  requestId: string;
  balances: TokenBalance[];
}

interface TokenBalance {
  domainSeparator: string;
  balance: string; // Current balance in token units (decimal string, uint256).
}
```

The response array is parallel to the `domainSeparators` request array. If a requested domain separator is not recognised, the gateway MUST return an error for the entire request rather than silently omitting the unrecognised entry.

### 8.4 Retrieve Transfer History

Returns the transfer history for the wallet for one or more supported tokens. Transfer history includes only ERC-20 `Transfer` events for supported tokens. Transfers of unsupported tokens and all other on-chain activity are excluded.

**Request type:** `GET_HISTORY`

```typescript
interface GetHistoryPayload {
  requestId: string;
  domainSeparators: string[];
  cursor?: string;  // Optional pagination cursor from a previous response.
  limit?: number;   // Maximum number of records to return. Gateway MAY enforce a ceiling.
}
```

**Response type:** `HISTORY_RESULT`

```typescript
interface HistoryResultPayload {
  requestId: string;
  transfers: TransferRecord[];
  nextCursor?: string; // Present if further records are available.
}

interface TransferRecord {
  domainSeparator: string;
  txHash: string;       // 66-char hex string.
  blockNumber: number;
  timestamp: number;    // Unix timestamp (seconds) of the block.
  from: string;         // EVM address.
  to: string;           // EVM address.
  value: string;        // Amount in token units (decimal string).
  direction: "IN" | "OUT";
}
```

Results MUST be returned in reverse chronological order (most recent first). The gateway SHOULD support cursor-based pagination. If no `cursor` is supplied, the response begins from the most recent record.

### 8.5 Submit Payment Request

Submits a signed `TransferRequest` payload (SSF-SPEC-001, Section 8.2) for processing. This is an asynchronous operation: the gateway acknowledges receipt immediately and subsequently delivers status updates via the notification mechanism defined in Section 10.

**Request type:** `SUBMIT_PAYMENT`

```typescript
interface SubmitPaymentPayload {
  requestId: string;
  transferRequest: TransferRequest; // As defined in SSF-SPEC-001, Section 8.2.
}
```

**Acknowledgement response type:** `SUBMIT_PAYMENT_ACK`

```typescript
interface SubmitPaymentAckPayload {
  requestId: string;
  payloadId: string; // Echoes transferRequest.payloadId. Used to correlate subsequent status notifications.
  status: "ENQUEUING";
}
```

The acknowledgement confirms that the message passed envelope-level validation and has been accepted for processing. It does not indicate that the submission has been validated against Settlement Contract rules or broadcast to the network. Subsequent status transitions are delivered as `SUBMISSION_STATUS` notifications (Section 10).

If the submission fails envelope or first-line validation, the gateway MUST return an `ERROR` response (Section 12) rather than an acknowledgement.

### 8.6 Submit Acquirer Registration Request

Submits a signed `BuyAcquiringPackRequest` payload (SSF-SPEC-001, Section 8.3) for processing. The operation is asynchronous in the same manner as payment submission.

**Request type:** `SUBMIT_ACQUIRING`

```typescript
interface SubmitAcquiringPayload {
  requestId: string;
  buyAcquiringPackRequest: BuyAcquiringPackRequest; // As defined in SSF-SPEC-001, Section 8.3.
}
```

**Acknowledgement response type:** `SUBMIT_ACQUIRING_ACK`

```typescript
interface SubmitAcquiringAckPayload {
  requestId: string;
  status: "ENQUEUING";
}
```

Subsequent status transitions are delivered as `SUBMISSION_STATUS` notifications (Section 10).

---

## 9. Subscription Operations

Subscriptions allow a connected wallet to receive real-time push notifications without polling. Two subscription channels are defined: balance updates and transfer notifications.

**Subscriptions are scoped to the connection.** They are created when the client sends a subscription request and are automatically cancelled when the connection closes — whether by idle timeout (Section 4.3), supersession (Section 4.2), or any other reason. Subscription state is never persisted. A client that reconnects MUST re-register any subscriptions it requires.

### 9.1 Subscribe to Balance Updates

Registers the wallet to receive a notification whenever the balance of a specified token changes for the connected address.

**Request type:** `SUBSCRIBE_BALANCE`

```typescript
interface SubscribeBalancePayload {
  requestId: string;
  domainSeparators: string[]; // One or more tokens to subscribe to.
}
```

**Response type:** `SUBSCRIBE_BALANCE_ACK`

```typescript
interface SubscribeBalanceAckPayload {
  requestId: string;
  subscribedSeparators: string[];
}
```

**Push notification type:** `BALANCE_UPDATE`

```typescript
interface BalanceUpdatePayload {
  domainSeparator: string;
  balance: string; // New balance in token units (decimal string).
}
```

A `BALANCE_UPDATE` notification MUST be delivered whenever a confirmed on-chain transfer results in a balance change for the wallet on a subscribed token.

### 9.2 Subscribe to Transfer Notifications

Registers the wallet to receive a notification for each new confirmed transfer involving its address for one or more specified tokens.

**Request type:** `SUBSCRIBE_TRANSFERS`

```typescript
interface SubscribeTransfersPayload {
  requestId: string;
  domainSeparators: string[];
}
```

**Response type:** `SUBSCRIBE_TRANSFERS_ACK`

```typescript
interface SubscribeTransfersAckPayload {
  requestId: string;
  subscribedSeparators: string[];
}
```

**Push notification type:** `TRANSFER_NOTIFICATION`

```typescript
interface TransferNotificationPayload {
  transfer: TransferRecord; // As defined in Section 8.4.
}
```

### 9.3 Unsubscribe

Cancels an active subscription for one or more tokens on a given channel.

**Request type:** `UNSUBSCRIBE`

```typescript
interface UnsubscribePayload {
  requestId: string;
  channel: "BALANCE" | "TRANSFERS";
  domainSeparators: string[];
}
```

**Response type:** `UNSUBSCRIBE_ACK`

```typescript
interface UnsubscribeAckPayload {
  requestId: string;
  channel: "BALANCE" | "TRANSFERS";
  unsubscribedSeparators: string[];
}
```

---

## 10. Asynchronous Status Notifications

### 10.1 Submission Status Lifecycle

Accepted submissions progress through the following states:

| Status | Description |
|---|---|
| `ENQUEUING` | The submission has been accepted by the gateway and is being handed to the broadcast-service. |
| `PENDING` | The broadcast-service has received the submission and is performing full validation per SSF-SPEC-001 Section 14. |
| `BROADCASTING` | Validation passed. The broadcast-submitter is submitting the transaction to the network. |
| `SUCCESS` | The transaction was accepted by the network without revert. **This does not constitute final settlement.** Final settlement is confirmed by transfer-history after sufficient block confirmations, and delivered via a `TRANSFER_NOTIFICATION` push. |
| `FAILURE` | The submission failed validation, the transaction reverted on-chain, or the transaction could not be submitted. A `failureReason` is provided. |

`SUCCESS` and `FAILURE` are terminal states. No further status notifications are delivered after either is reached for a given submission.

### 10.2 Status Notification Message

**Push notification type:** `SUBMISSION_STATUS`

```typescript
interface SubmissionStatusPayload {
  payloadId: string;
  submissionType: "PAYMENT" | "ACQUIRING";
  status: SubmissionStatus;
  failureReason?: string;           // Present only when status is FAILURE.
  failureCategory?: FailureCategory; // Present only when status is FAILURE.
  txHash?: string;                  // Present when status is BROADCASTING or SUCCESS.
}

type SubmissionStatus = "ENQUEUING" | "PENDING" | "BROADCASTING" | "SUCCESS" | "FAILURE";
type FailureCategory = "STRUCTURAL_ERROR" | "SEMANTIC_ERROR" | "CRYPTOGRAPHIC_ERROR" | "BROADCAST_ERROR";
```

Failure categories correspond to those defined in SSF-SPEC-001, Section 17.

---

## 11. Message Type Reference

| Type | Direction | Description |
|---|---|---|
| `GET_NONCE` | Client → Gateway | Request current permit nonce for a token. |
| `NONCE_RESULT` | Gateway → Client | Response with current nonce. |
| `GET_FEES` | Client → Gateway | Request fee breakdown for an amount. |
| `FEES_RESULT` | Gateway → Client | Response with `BrokenDownAmount`. |
| `GET_BALANCE` | Client → Gateway | Request current balances for one or more tokens. |
| `BALANCE_RESULT` | Gateway → Client | Response with balance array. |
| `GET_HISTORY` | Client → Gateway | Request transfer history. |
| `HISTORY_RESULT` | Gateway → Client | Response with transfer records. |
| `SUBMIT_PAYMENT` | Client → Gateway | Submit a `TransferRequest` payload. |
| `SUBMIT_PAYMENT_ACK` | Gateway → Client | Acknowledgement with initial `ENQUEUING` status. |
| `SUBMIT_ACQUIRING` | Client → Gateway | Submit a `BuyAcquiringPackRequest` payload. |
| `SUBMIT_ACQUIRING_ACK` | Gateway → Client | Acknowledgement with initial `ENQUEUING` status. |
| `SUBSCRIBE_BALANCE` | Client → Gateway | Subscribe to balance update notifications. |
| `SUBSCRIBE_BALANCE_ACK` | Gateway → Client | Subscription confirmation. |
| `SUBSCRIBE_TRANSFERS` | Client → Gateway | Subscribe to transfer notifications. |
| `SUBSCRIBE_TRANSFERS_ACK` | Gateway → Client | Subscription confirmation. |
| `UNSUBSCRIBE` | Client → Gateway | Cancel a subscription. |
| `UNSUBSCRIBE_ACK` | Gateway → Client | Cancellation confirmation. |
| `BALANCE_UPDATE` | Gateway → Client | Push: balance changed for a subscribed token. |
| `TRANSFER_NOTIFICATION` | Gateway → Client | Push: new confirmed transfer for a subscribed token. |
| `SUBMISSION_STATUS` | Gateway → Client | Push: submission status transition. |
| `ERROR` | Gateway → Client | Error response for any failed request. |

---

## 12. Error Handling

The gateway MUST return an `ERROR` message in response to any request that cannot be processed.

**Error message type:** `ERROR`

```typescript
interface ErrorPayload {
  requestId?: string;
  errorCode: string;
  errorCategory: ErrorCategory;
  message: string; // Human-readable. MUST NOT include internal implementation detail.
}

type ErrorCategory =
  | "STRUCTURAL_ERROR"
  | "AUTHENTICATION_ERROR"
  | "SEMANTIC_ERROR"
  | "NOT_FOUND"
  | "RATE_LIMIT"
  | "INTERNAL_ERROR";
```

| Error Code | Category | Description |
|---|---|---|
| `MISSING_FIELD` | `STRUCTURAL_ERROR` | A required envelope or payload field is absent. |
| `INVALID_FORMAT` | `STRUCTURAL_ERROR` | A field does not conform to its expected format. |
| `INVALID_SIGNATURE` | `AUTHENTICATION_ERROR` | Signature is malformed or cannot be verified. |
| `ADDRESS_MISMATCH` | `AUTHENTICATION_ERROR` | Recovered signer does not match `callerAddress`. |
| `EXPIRED_DEADLINE` | `AUTHENTICATION_ERROR` | Message `deadline` is in the past. |
| `UNSUPPORTED_TOKEN` | `SEMANTIC_ERROR` | Domain separator or token address is not recognised. |
| `NONCE_MISMATCH` | `SEMANTIC_ERROR` | Supplied nonce does not match on-chain state. |
| `UNKNOWN_PAYLOAD_ID` | `SEMANTIC_ERROR` | `payloadId` does not correspond to a known session. |
| `ALREADY_SUBMITTED` | `SEMANTIC_ERROR` | `payloadId` has already been processed. |
| `WALLET_NOT_FOUND` | `NOT_FOUND` | No state found for the given wallet address. |
| `INITIALISING` | `SEMANTIC_ERROR` | Wallet state is being initialised; request cannot be served yet. |
| `INTERNAL_ERROR` | `INTERNAL_ERROR` | Unexpected gateway-side failure. |

The gateway MUST NOT include stack traces, internal service identifiers, or other implementation-sensitive detail in error messages returned to clients.

---

## 13. Security Considerations

**Per-message signature verification.** The gateway MUST verify the message signature on every received message. There is no authenticated session state that exempts a connected client from this requirement.

**Single-connection enforcement.** The single-connection-per-wallet constraint (Section 4.2) prevents a stale or compromised session from continuing to receive notifications after a new, authorised connection supersedes it.

**Idle timeout and resource cleanup.** The idle timeout (Section 4.3) bounds the period during which server-side resources — including subscription registrations — are held for a non-communicating client. Implementations MUST release all associated resources promptly upon connection close.

**No key material exposure.** Wallet clients MUST NOT transmit private keys, seed phrases, or any key derivation material to the gateway. All signing MUST be performed locally on the client device.

**TLS requirement.** All connections MUST use TLS. The gateway MUST refuse plaintext connections.

**Rate limiting.** The gateway SHOULD implement per-connection and per-address rate limiting to mitigate denial-of-service attempts. Rate-limit errors MUST use the `RATE_LIMIT` error category.

**No fund custody.** The gateway holds no token balances and cannot initiate on-chain transactions. A gateway compromise does not directly result in loss of funds; cryptographic integrity is enforced by the Settlement Contract independently.

---

## 14. Future Work

**Per-token fee model.** As noted in Section 8.2, the Settlement Contract currently applies a uniform `baseFeeAmount` across all supported tokens. A future revision will introduce per-token fee configuration, at which point the `domainSeparator` supplied to `GET_FEES` will directly influence the returned `BrokenDownAmount`. The `GET_FEES` interface defined here anticipates this change; implementations MUST pass the token context to fee computation functions when this capability becomes available.

**Person-to-person transfers.** The Stablecoin Stack architecture supports direct person-to-person token transfers in addition to merchant payment flows. A future wallet specification (SSF-SPEC-009) will define the client-side interface for this use case. The gateway's `SUBMIT_PAYMENT` flow accommodates this without protocol changes; the beneficiary address need not be a registered merchant.

**Wallet certification.** SSF-SPEC-009 (planned) will define a conformance test suite and certification programme for wallet client implementations, including tests of correct gateway interaction.

---

## 15. Conformance

A **conformant wallet-gateway** MUST:

- expose its interface exclusively over WebSocket with TLS (Section 4.1);
- enforce the single-connection-per-wallet constraint with most-recent-connection precedence (Section 4.2);
- enforce an idle timeout and release all connection resources, including subscription state, upon connection close (Section 4.3);
- verify the message signature on every received message, rejecting on any failure in the sequence defined in Section 5.3;
- perform transparent wallet initialisation on first connection and respond with `INITIALISING` if a query arrives before initialisation completes (Section 7);
- implement all request–response operations defined in Section 8;
- implement the subscription model defined in Section 9, with subscriptions scoped to the originating connection;
- deliver `SUBMISSION_STATUS` notifications for all accepted submissions through to a terminal state (Section 10);
- return structured `ERROR` messages conforming to Section 12 for all failures; and
- reject requests referencing unsupported tokens.

A **conformant wallet client** MUST:

- connect exclusively via WSS;
- include a valid signature in every message sent to the gateway;
- handle unexpected disconnection gracefully and re-establish the connection when required;
- re-register all required subscriptions after reconnection;
- not treat a `SUCCESS` submission status as final settlement; and
- not transmit key material of any kind to the gateway.

---

## 16. Change Log

| Version | Date | Author | Summary |
|---|---|---|---|
| 1.0.0 | 2026-03-10 | Stablecoin Stack Foundation | Initial release. |

---

## 17. License

Copyright © 2026 Stablecoin Stack Foundation. All rights reserved.

This specification is published under the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0). Unless required by applicable law or agreed to in writing, material distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
