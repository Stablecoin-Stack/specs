# Error Codes

Complete reference for all error codes returned by the wallet-gateway in `ERROR` messages and in the `failureCategory` field of `SUBMISSION_STATUS` failure notifications.

For normative definitions see [formal specification, Section 12](../SPEC.md#12-error-handling).

---

## Error Message Structure

```typescript
interface ErrorPayload {
  requestId?: string;        // Echoes the originating request ID, if available.
  errorCode: string;
  errorCategory: ErrorCategory;
  message: string;           // Human-readable description.
}
```

---

## Error Code Reference

### STRUCTURAL_ERROR

Returned when a message cannot be parsed or a required field is missing or incorrectly formatted.

| Code | Description |
|---|---|
| `MISSING_FIELD` | A required field in the message envelope or payload is absent. |
| `INVALID_FORMAT` | A field is present but does not conform to its expected type or format (e.g. an address that is not 42 characters, a hex string missing the `0x` prefix, a non-integer value where `uint256` is expected). |

### AUTHENTICATION_ERROR

Returned when signature verification fails at any step.

| Code | Description |
|---|---|
| `INVALID_SIGNATURE` | The signature is malformed, uses an invalid `v` value, or cannot be verified. |
| `ADDRESS_MISMATCH` | The address recovered from the signature does not match `callerAddress`. |
| `EXPIRED_DEADLINE` | The message `deadline` is in the past (beyond the gateway's clock tolerance). |

### SEMANTIC_ERROR

Returned when the message is structurally valid and the signature is correct, but a business rule is violated.

| Code | Description |
|---|---|
| `UNSUPPORTED_TOKEN` | A domain separator or token address in the request does not correspond to a currently supported token. |
| `NONCE_MISMATCH` | The permit nonce supplied in a submission payload does not match the current on-chain nonce for the wallet on the specified token. |
| `UNKNOWN_PAYLOAD_ID` | The `payloadId` in a `SUBMIT_PAYMENT` request does not correspond to a known, active checkout session held by the processor. |
| `ALREADY_SUBMITTED` | The `payloadId` has already been processed (or is currently being processed). Submissions are single-use. |
| `INITIALISING` | The gateway is still collecting initial transfer history and balance data for this wallet. The request cannot be served until initialisation completes. Retry after a short delay. |

### NOT_FOUND

| Code | Description |
|---|---|
| `WALLET_NOT_FOUND` | No state was found for the given wallet address. This should not occur after a successful connection and initialisation; if received, reconnecting will trigger initialisation. |

### RATE_LIMIT

| Code | Description |
|---|---|
| `RATE_LIMIT_EXCEEDED` | The request rate for this connection or wallet address has been exceeded. Wait before retrying. |

### INTERNAL_ERROR

| Code | Description |
|---|---|
| `INTERNAL_ERROR` | An unexpected error occurred within the gateway. The request could not be processed. The gateway will not include internal implementation detail in the `message` field. |

---

## Submission Failure Categories

When a `SUBMISSION_STATUS` notification carries `status: "FAILURE"`, the `failureCategory` field uses the following values (drawn from SS-SPEC-001, Section 17):

| Category | Description |
|---|---|
| `STRUCTURAL_ERROR` | The submission payload failed structural validation. |
| `SEMANTIC_ERROR` | Structural validation passed but a business rule was violated (e.g. expired deadline, nonce mismatch, unknown `payloadId`). |
| `CRYPTOGRAPHIC_ERROR` | A payment signature could not be verified, or the recovered signer did not match the expected owner. |
| `BROADCAST_ERROR` | Validation passed but the on-chain transaction reverted or could not be submitted to the network. |

---

## Related Documents

- [Message Type Index](./message-type-index.md)
- [Formal Specification, Section 12](../SPEC.md#12-error-handling)
- [SS-SPEC-001, Section 17 — Error Handling](../../ss-001/SPEC.md#17-error-handling)
