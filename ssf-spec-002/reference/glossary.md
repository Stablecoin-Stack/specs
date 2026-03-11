# Glossary

Terms used in SSF-SPEC-004 and the wallet-gateway documentation. Terms carried forward from SSF-SPEC-001 are noted as such.

For the SSF-SPEC-001 glossary, see the [SSF-SPEC-001 Glossary](../../ssf-spec-001/reference/glossary.md).

---

| Term | Definition |
|---|---|
| **Wallet Client** | A conformant client application that connects to the wallet-gateway on behalf of a payer. |
| **Gateway Message** | A JSON object transmitted over the WebSocket connection, conforming to the message envelope defined in SSF-SPEC-004, Section 5.2. |
| **Caller Address** | The EVM address that identifies the wallet in a given gateway message. Must match the address recovered from the message signature. |
| **Domain Separator** | A `bytes32` EIP-712 domain separator hex string. Used as the primary identifier for a supported token throughout the wallet-gateway interface. Full token metadata (including contract address, name, decimals, version, and network identifier) is available from the Basic Data Service. *(From SSF-SPEC-001)* |
| **Transfer History** | The record of ERC-20 `Transfer` events for supported tokens associated with a given wallet address. Transfers of unsupported tokens and all other on-chain activity are excluded. *(From SSF-SPEC-001)* |
| **Subscription** | A per-connection registration to receive real-time push notifications for a specified set of tokens. Active only while the originating connection remains open and non-idle. |
| **Idle Timeout** | The gateway-configurable duration after which a connection with no inbound activity is closed. Recommended value: 5 minutes. |
| **BrokenDownAmount** | A response object returned by `GET_FEES` carrying two fields: `fees` (the total fee in token units) and `remaining` (the principal amount that will reach the beneficiary). |
| **Submission Status** | One of the ordered states a payment or registration submission passes through: `ENQUEUING`, `PENDING`, `BROADCASTING`, `SUCCESS`, `FAILURE`. |
| **Terminal State** | A submission status from which no further transitions occur. The terminal states are `SUCCESS` and `FAILURE`. |
| **Permit Nonce** | The per-owner counter maintained by an ERC-2612 token contract. Each successfully executed permit increments the nonce, invalidating all previously issued permits for that owner. The gateway provides nonce retrieval via `GET_NONCE` so wallets need not query the token contract directly. *(From SSF-SPEC-001)* |
| **Payload ID** | A client-generated identifier (UUID) included in a `TransferRequest` submission. Used by the gateway and broadcast-service to enforce idempotency and correlate status notifications back to the originating submission. *(From SSF-SPEC-001)* |
| **Zero-UUID** | The Acquirer ID consisting entirely of zero bytes (`0x00000000000000000000000000000000`). Passed as `acquirerId` in `GET_FEES` and in the payment payload when no acquirer is involved. *(From SSF-SPEC-001)* |
| **WSS** | WebSocket Secure — the WebSocket protocol carried over TLS. The wallet-gateway accepts WSS connections only. |
| **Ping / Pong** | WebSocket-level keepalive frames (RFC 6455). The gateway sends `ping` frames before closing idle connections. Clients SHOULD respond with `pong` to reset the idle timer. |
| **Supersession** | The event by which a new connection for a given wallet address displaces and terminates an existing connection for the same address. The previous connection is closed with code `4001 / "superseded"`. |

---

## Related Documents

- [Formal Specification — SSF-SPEC-004](../specifications/ssf-spec-002.md)
- [SSF-SPEC-001 Glossary](../../ssf-spec-001/reference/glossary.md)
