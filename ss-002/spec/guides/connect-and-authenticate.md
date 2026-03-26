# Connect and Authenticate

Step-by-step guide for establishing a connection to the wallet-gateway and sending your first authenticated message.

**Prerequisites:** read [Connection Model](../core-concepts/connection-model.md) and [Message Authentication](../core-concepts/message-authentication.md) first.

---

## What You're Doing

Connecting to the wallet-gateway involves three things:

1. Opening a WSS WebSocket connection to the gateway endpoint.
2. Constructing a signed message envelope for your first request.
3. Sending it within the authentication timeout window.

There is no separate login step. Your first signed message is your authentication.

---

## Step 1 — Open the WebSocket Connection

```javascript
const ws = new WebSocket('wss://gateway.your-processor.example/ws');

ws.addEventListener('open', () => {
  console.log('Connection open. Sending first message...');
  sendGetBalance(); // or any valid signed message
});

ws.addEventListener('message', (event) => {
  const msg = JSON.parse(event.data);
  handleMessage(msg);
});

ws.addEventListener('close', (event) => {
  console.warn(`Connection closed: ${event.code} ${event.reason}`);
  // Implement reconnection logic here if needed
});
```

The connection endpoint is published in the Basic Data Service (basic-data-server). Check with your processor for the correct URL.

---

## Step 2 — Build the Message Envelope

Every message you send must be wrapped in the standard envelope:

```typescript
interface GatewayMessage {
  type: string;
  callerAddress: string;   // Your wallet address
  deadline: number;        // Unix timestamp (seconds) — a few minutes from now
  payload: object;         // The operation-specific data
  signature: MessageSig;
}
```

Set `deadline` to a point a few minutes in the future. Messages with a deadline in the past are rejected.

---

## Step 3 — Sign the Message

The `signature` object requires an EIP-712 digest of the message, signed with your wallet's private key.

```javascript
import { ethers } from 'ethers';

async function buildSignedMessage(signer, type, payload) {
  const callerAddress = await signer.getAddress();
  const deadline = Math.floor(Date.now() / 1000) + 300; // 5 minutes

  // Define the EIP-712 domain for gateway messages.
  // Obtain name, version, and chainId from the Basic Data Service.
  const domain = {
    name: 'WalletGateway',
    version: '1',
    chainId: YOUR_CHAIN_ID,
  };

  const messageTypes = {
    GatewayMessage: [
      { name: 'type',          type: 'string'  },
      { name: 'callerAddress', type: 'address' },
      { name: 'deadline',      type: 'uint256' },
      { name: 'payloadHash',   type: 'bytes32' },
    ],
  };

  // Hash the payload separately so the outer type stays fixed.
  const payloadHash = ethers.keccak256(
    ethers.toUtf8Bytes(JSON.stringify(payload))
  );

  const messageData = { type, callerAddress, deadline, payloadHash };

  const sig = await signer.signTypedData(domain, messageTypes, messageData);
  const { v, r, s } = ethers.Signature.from(sig);
  const hash = ethers.TypedDataEncoder.hash(domain, messageTypes, messageData);

  return {
    type,
    callerAddress,
    deadline,
    payload,
    signature: {
      hash,
      v: v < 27 ? v + 27 : v,
      r,
      s,
    },
  };
}
```

> **Note:** The exact EIP-712 type definition for gateway messages is specified in the [formal specification, Section 5.2](../SPEC.md#52-message-envelope). Verify the domain parameters with the Basic Data Service before signing.

---

## Step 4 — Send Your First Message

Use any valid operation as your first message. A balance query is a natural starting point:

```javascript
async function sendGetBalance(ws, signer, domainSeparators) {
  const payload = {
    requestId: crypto.randomUUID(),
    domainSeparators,
  };

  const message = await buildSignedMessage(signer, 'GET_BALANCE', payload);
  ws.send(JSON.stringify(message));
}
```

---

## Step 5 — Handle Responses

All responses follow the same pattern: a `type` field identifies the message, and a `requestId` echoes the one you sent.

```javascript
function handleMessage(msg) {
  switch (msg.type) {
    case 'BALANCE_RESULT':
      console.log('Balances:', msg.payload.balances);
      break;
    case 'ERROR':
      console.error(`Error [${msg.payload.errorCode}]: ${msg.payload.message}`);
      break;
    default:
      console.log('Received:', msg.type, msg.payload);
  }
}
```

---

## Handling the Idle Timeout

The gateway closes connections that have been idle for too long. To keep a connection alive, respond to `ping` frames:

```javascript
// The browser WebSocket API handles ping/pong automatically.
// In Node.js (using the 'ws' library):
ws.on('ping', () => ws.pong());
```

---

## Handling Disconnection

If the connection closes unexpectedly, reconnect and re-register any subscriptions:

```javascript
ws.addEventListener('close', async () => {
  await delay(2000); // Simple backoff — use exponential in production
  reconnect();
});

async function reconnect() {
  // Re-open connection and re-register subscriptions
  openConnection();
  subscribeToTokens(myTokenList);
}
```

---

## Common Mistakes

| Mistake | Result | Fix |
|---|---|---|
| Deadline set in the past | `EXPIRED_DEADLINE` error | Set deadline to at least 60 seconds from now |
| Reusing a signed message | Rejected after deadline passes | Generate a fresh signature for each message |
| Sending before connection is open | Message lost | Wait for the `open` event before sending |
| Not re-subscribing after reconnect | No push notifications received | Re-send `SUBSCRIBE_BALANCE` / `SUBSCRIBE_TRANSFERS` after each reconnection |
| Wrong `v` value | `INVALID_SIGNATURE` error | Normalise: if `v < 27`, add 27 |

---

## Related Documents

- [Connection Model](../core-concepts/connection-model.md)
- [Message Authentication](../core-concepts/message-authentication.md)
- [Submit a Payment](./submit-a-payment.md)
- [Subscribe to Updates](./subscribe-to-updates.md)
- [Formal Specification, Sections 4–5](../SPEC.md)
