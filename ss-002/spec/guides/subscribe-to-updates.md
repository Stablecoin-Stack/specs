# Subscribe to Updates

Step-by-step guide for registering balance and transfer subscriptions and handling push notifications.

**Prerequisites:** [Connect and Authenticate](./connect-and-authenticate.md). You must have an active, authenticated connection before subscribing.

---

## What You're Doing

Once connected, you can register to receive push notifications for:

- **balance changes** on one or more tokens;
- **new confirmed transfers** involving your wallet on one or more tokens.

Both registrations are sent as signed messages, like any other gateway request. Both are cancelled automatically when your connection closes.

---

## Subscribe to Balance Updates

```javascript
async function subscribeToBalances(ws, signer, domainSeparators) {
  const payload = {
    requestId: crypto.randomUUID(),
    domainSeparators,
  };
  const msg = await buildSignedMessage(signer, 'SUBSCRIBE_BALANCE', payload);
  ws.send(JSON.stringify(msg));
}

// Example: subscribe to two tokens
subscribeToBalances(ws, signer, [USDC_DOMAIN_SEPARATOR, EURC_DOMAIN_SEPARATOR]);
```

The gateway responds with `SUBSCRIBE_BALANCE_ACK` listing the active tokens. After that, a `BALANCE_UPDATE` is pushed whenever your balance changes for any subscribed token:

```javascript
case 'SUBSCRIBE_BALANCE_ACK':
  console.log('Balance subscriptions active:', msg.payload.subscribedSeparators);
  break;

case 'BALANCE_UPDATE':
  const { domainSeparator, balance } = msg.payload;
  updateBalanceDisplay(domainSeparator, balance);
  break;
```

---

## Subscribe to Transfer Notifications

```javascript
async function subscribeToTransfers(ws, signer, domainSeparators) {
  const payload = {
    requestId: crypto.randomUUID(),
    domainSeparators,
  };
  const msg = await buildSignedMessage(signer, 'SUBSCRIBE_TRANSFERS', payload);
  ws.send(JSON.stringify(msg));
}

subscribeToTransfers(ws, signer, [USDC_DOMAIN_SEPARATOR]);
```

```javascript
case 'SUBSCRIBE_TRANSFERS_ACK':
  console.log('Transfer subscriptions active:', msg.payload.subscribedSeparators);
  break;

case 'TRANSFER_NOTIFICATION':
  const { transfer } = msg.payload;
  console.log(`${transfer.direction} ${transfer.value} — block ${transfer.blockNumber}`);
  // If direction is 'OUT' and this matches a pending payment, it is now settled.
  break;
```

---

## Unsubscribe

To cancel a subscription for specific tokens:

```javascript
async function unsubscribe(ws, signer, channel, domainSeparators) {
  const payload = {
    requestId: crypto.randomUUID(),
    channel,           // 'BALANCE' or 'TRANSFERS'
    domainSeparators,
  };
  const msg = await buildSignedMessage(signer, 'UNSUBSCRIBE', payload);
  ws.send(JSON.stringify(msg));
}

// Remove one token from the balance channel
unsubscribe(ws, signer, 'BALANCE', [EURC_DOMAIN_SEPARATOR]);
```

---

## Re-subscribing After Reconnection

Subscriptions do not survive a connection close. After reconnecting, re-register:

```javascript
async function onReconnect(ws, signer) {
  await subscribeToBalances(ws, signer, myTokenList);
  await subscribeToTransfers(ws, signer, myTokenList);

  // Also re-sync current state, since notifications during
  // the disconnection window were not delivered.
  await sendGetBalance(ws, signer, myTokenList);
  await sendGetHistory(ws, signer, myTokenList);
}
```

---

## Using Transfer Notifications for Settlement Confirmation

The `TRANSFER_NOTIFICATION` channel is the correct signal for confirmed payment settlement. A payment submitted via `SUBMIT_PAYMENT` reaches `SUCCESS` status when the network accepts the transaction, but this is not final. The `TRANSFER_NOTIFICATION` arrives only after the transfer-history service has observed the on-chain event with the required block confirmation depth.

To ensure you receive the settlement notification for a payment you are submitting, subscribe to transfers on the relevant token **before** submitting the payment.

---

## Common Mistakes

| Mistake | Result | Fix |
|---|---|---|
| Subscribing after submitting a payment | May miss the `TRANSFER_NOTIFICATION` if confirmation is fast | Subscribe before submitting |
| Not re-subscribing after reconnection | No notifications received on the new connection | Re-register in your reconnection handler |
| Assuming subscriptions persist across reconnects | Silent data gap | Subscriptions are connection-scoped; always re-register |

---

## Related Documents

- [Subscription Model](../core-concepts/subscription-model.md)
- [Submit a Payment](./submit-a-payment.md)
- [Formal Specification, Section 9](../SPEC.md#9-subscription-operations)
- [Message Type Index](../reference/message-type-index.md)
