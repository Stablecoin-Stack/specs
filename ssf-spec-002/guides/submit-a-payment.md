# Submit a Payment

Step-by-step guide for submitting a signed payment payload to the wallet-gateway and handling the asynchronous status updates.

**Prerequisites:** read [SSF-SPEC-001 guides — Submit a Payment](../../ssf-spec-001/guides/submit-a-payment.md) to understand how to construct the `TransferRequest` payload. This guide covers only the gateway interaction.

---

## What You're Doing

After constructing and signing a `TransferRequest` (per SSF-SPEC-001), you submit it to the gateway via the `SUBMIT_PAYMENT` message type. The gateway acknowledges receipt immediately and then delivers status updates as the operation progresses.

---

## Step 1 — Fetch the Nonce

Before constructing the payment payload, retrieve the current permit nonce for your wallet:

```javascript
const noncePayload = { requestId: crypto.randomUUID(), domainSeparator: TOKEN_DOMAIN_SEPARATOR };
const nonceMsg = await buildSignedMessage(signer, 'GET_NONCE', noncePayload);
ws.send(JSON.stringify(nonceMsg));

// In your message handler:
// case 'NONCE_RESULT': nonce = msg.payload.nonce
```

---

## Step 2 — Fetch the Fee Breakdown

```javascript
const feesPayload = {
  requestId: crypto.randomUUID(),
  domainSeparator: TOKEN_DOMAIN_SEPARATOR,
  amount: principalAmount.toString(),
  acquirerId: acquirerId ?? '0x00000000000000000000000000000000', // Zero-UUID if no acquirer
};
const feesMsg = await buildSignedMessage(signer, 'GET_FEES', feesPayload);
ws.send(JSON.stringify(feesMsg));

// In your message handler:
// case 'FEES_RESULT': { fees, remaining } = msg.payload.brokenDownAmount
// permitValue = BigInt(remaining) + BigInt(fees)
```

---

## Step 3 — Construct and Sign the TransferRequest

Construct your `TransferRequest` as defined in SSF-SPEC-001, Section 8.2, using the nonce and permit value from steps 1 and 2. This involves signing both the Permit and the Binding Signature against their respective EIP-712 domains.

Refer to [SSF-SPEC-001 guides — Submit a Payment](../../ssf-spec-001/guides/submit-a-payment.md) for the full signing procedure.

---

## Step 4 — Submit to the Gateway

```javascript
const payloadId = crypto.randomUUID();

const transferRequest = {
  payWithPermitParams: { /* ... */ },
  payWithPermitSig: { /* ... */ },
  permitSig: { /* ... */ },
  payloadId,
};

const submitPayload = {
  requestId: crypto.randomUUID(),
  transferRequest,
};

const submitMsg = await buildSignedMessage(signer, 'SUBMIT_PAYMENT', submitPayload);
ws.send(JSON.stringify(submitMsg));
```

---

## Step 5 — Handle the Acknowledgement

The gateway responds immediately with `SUBMIT_PAYMENT_ACK`:

```javascript
case 'SUBMIT_PAYMENT_ACK':
  console.log(`Accepted. Tracking payloadId: ${msg.payload.payloadId}`);
  // Status is "ENQUEUING" — processing has begun
  break;
```

---

## Step 6 — Handle Status Updates

Status updates arrive as `SUBMISSION_STATUS` push messages. Handle each transition:

```javascript
case 'SUBMISSION_STATUS':
  const { payloadId, status, failureReason, failureCategory, txHash } = msg.payload;

  switch (status) {
    case 'ENQUEUING':
      updateUI('Preparing submission...');
      break;
    case 'PENDING':
      updateUI('Validating...');
      break;
    case 'BROADCASTING':
      updateUI(`Submitting to network... tx: ${txHash}`);
      break;
    case 'SUCCESS':
      // Network accepted the transaction — but this is NOT final settlement.
      // Await a TRANSFER_NOTIFICATION for confirmed settlement.
      updateUI('Processing — awaiting confirmation...');
      break;
    case 'FAILURE':
      updateUI(`Failed [${failureCategory}]: ${failureReason}`);
      break;
  }
  break;
```

---

## Step 7 — Await Final Settlement Confirmation

`SUCCESS` means the transaction was submitted without error. It does not mean the payment is complete. Final settlement is confirmed when the transfer-history service observes the on-chain event after sufficient block confirmations. This arrives as a `TRANSFER_NOTIFICATION`:

```javascript
case 'TRANSFER_NOTIFICATION':
  const { transfer } = msg.payload;
  if (transfer.direction === 'OUT' && transfer.value === expectedAmount) {
    updateUI('Payment confirmed.');
  }
  break;
```

To receive this notification, you must have an active `SUBSCRIBE_TRANSFERS` subscription for the relevant token before the payment is confirmed. See [Subscribe to Updates](./subscribe-to-updates.md).

---

## Common Mistakes

| Mistake | Result | Fix |
|---|---|---|
| Treating `SUCCESS` as final settlement | Premature confirmation shown to user | Wait for `TRANSFER_NOTIFICATION` |
| Using a stale nonce from local state | Signature invalid at submission | Always fetch nonce via `GET_NONCE` immediately before constructing the payload |
| No active transfer subscription | Final confirmation never received | Subscribe to the token's transfer channel before submitting |
| Reusing a `payloadId` | `ALREADY_SUBMITTED` error | Generate a new UUID per submission |

---

## Related Documents

- [Subscribe to Updates](./subscribe-to-updates.md)
- [Subscription Model](../core-concepts/subscription-model.md)
- [Formal Specification, Sections 8.5 and 10](../specifications/ssf-spec-002.md#85-submit-payment-request)
- [SSF-SPEC-001 — Submit a Payment guide](../../ssf-spec-001/guides/submit-a-payment.md)
- [Message Type Index](../reference/message-type-index.md)
