# Message Type Index

Complete reference for all message types defined in SS-SPEC-004. For full field definitions, see the [formal specification](../SPEC.md).

---

## Client → Gateway Messages

| Type | Section | Description |
|---|---|---|
| `GET_NONCE` | §8.1 | Retrieve the current permit nonce for a given wallet and token. |
| `GET_FEES` | §8.2 | Retrieve the fee breakdown for a given principal amount and acquirer. |
| `GET_BALANCE` | §8.3 | Retrieve current balances for one or more tokens. |
| `GET_HISTORY` | §8.4 | Retrieve transfer history for one or more tokens. |
| `SUBMIT_PAYMENT` | §8.5 | Submit a signed `TransferRequest` payload for processing. |
| `SUBMIT_ACQUIRING` | §8.6 | Submit a signed `BuyAcquiringPackRequest` payload for processing. |
| `SUBSCRIBE_BALANCE` | §9.1 | Register to receive balance update notifications. |
| `SUBSCRIBE_TRANSFERS` | §9.2 | Register to receive transfer notifications. |
| `UNSUBSCRIBE` | §9.3 | Cancel an active subscription for specified tokens on a channel. |

---

## Gateway → Client Messages

### Responses to Requests

| Type | Section | Description |
|---|---|---|
| `NONCE_RESULT` | §8.1 | Current permit nonce. |
| `FEES_RESULT` | §8.2 | `BrokenDownAmount` with `fees` and `remaining` fields. |
| `BALANCE_RESULT` | §8.3 | Array of `{ domainSeparator, balance }` objects. |
| `HISTORY_RESULT` | §8.4 | Array of `TransferRecord` objects, paginated. |
| `SUBMIT_PAYMENT_ACK` | §8.5 | Acknowledgement with initial `ENQUEUING` status and echoed `payloadId`. |
| `SUBMIT_ACQUIRING_ACK` | §8.6 | Acknowledgement with initial `ENQUEUING` status. |
| `SUBSCRIBE_BALANCE_ACK` | §9.1 | Confirms which tokens are now subscribed on the balance channel. |
| `SUBSCRIBE_TRANSFERS_ACK` | §9.2 | Confirms which tokens are now subscribed on the transfer channel. |
| `UNSUBSCRIBE_ACK` | §9.3 | Confirms which tokens were removed from a subscription channel. |
| `ERROR` | §12 | Error response for any failed request. |

### Push Notifications

| Type | Section | Trigger |
|---|---|---|
| `BALANCE_UPDATE` | §9.1 | A subscribed token's balance changed for the connected wallet. |
| `TRANSFER_NOTIFICATION` | §9.2 | A new confirmed transfer was indexed for the connected wallet on a subscribed token. |
| `SUBMISSION_STATUS` | §10.2 | A submission's status changed. Delivered at each transition through `ENQUEUING → PENDING → BROADCASTING → SUCCESS/FAILURE`. |

---

## Submission Status Values

Used in `SUBMIT_PAYMENT_ACK`, `SUBMIT_ACQUIRING_ACK`, and `SUBMISSION_STATUS` messages.

| Status | Terminal | Description |
|---|---|---|
| `ENQUEUING` | No | Accepted by the gateway; being handed to broadcast-service. |
| `PENDING` | No | Received by broadcast-service; full validation in progress. |
| `BROADCASTING` | No | Validation passed; transaction being submitted to the network. |
| `SUCCESS` | Yes | Network accepted the transaction without revert. Not final settlement. |
| `FAILURE` | Yes | Submission failed. See `failureCategory` and `failureReason`. |

---

## Error Categories

Used in `ERROR` messages and the `failureCategory` field of `SUBMISSION_STATUS` (failure case).

| Category | Description |
|---|---|
| `STRUCTURAL_ERROR` | A required field is absent or malformed. |
| `AUTHENTICATION_ERROR` | Signature invalid, address mismatch, or expired deadline. |
| `SEMANTIC_ERROR` | Structurally valid but a business rule was violated. |
| `NOT_FOUND` | Referenced resource does not exist. |
| `RATE_LIMIT` | Request rate exceeded. |
| `INTERNAL_ERROR` | Unexpected gateway-side failure. |

For the full error code list, see [Error Codes](./error-codes.md).

---

## Related Documents

- [Error Codes](./error-codes.md)
- [Glossary](./glossary.md)
- [Formal Specification — SS-SPEC-004](../SPEC.md)
