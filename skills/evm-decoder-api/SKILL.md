---
name: evm-decoder-api
description: Use when calling the EVM Transaction Decoder API to decode transaction calldata, event logs, look up function or event signatures by selector, or fetch verified contract ABIs on Ethereum and other EVM chains.
---

# EVM Transaction Decoder API

Reference for the public `/api/v1/` endpoints exposed by a deployed [jsvisa/decoder](https://github.com/jsvisa/decoder) instance.

## Base URL

`https://eth-decoder.vercel.app/api/v1/`

## Decode Transaction Calldata

```
GET /api/v1/decode?data=<hex>
```

| Param | Required | Description |
|-------|----------|-------------|
| `data` | Yes | Hex calldata (with or without `0x`) |
| `multicall` | No | `true` to recursively decode nested multicall calls |
| `with_abi` | No | `true` to include matched ABI in response |
| `with_sign` | No | `true` to include the 4-byte selector |

**Response:** `{ "msg": "ok", "data": [...] }` — falls back to OpenChain for unknown signatures.

**Example:**
```
GET /api/v1/decode?data=0xa9059cbb000000000000000000000000d8da6bf26964af9d7eed9e03e53415d37aa96045000000000000000000000000000000000000000000000000002386f26fc10000
```

## Decode Event Log

```
GET /api/v1/decode-event?sign=<topic0>&topics=<csv>&data=<hex>
```

| Param | Required | Description |
|-------|----------|-------------|
| `sign` | Yes | `topic0` — keccak256 hash of the event signature |
| `topics` | No | Comma-separated list of all topics, `topic0` first |
| `data` | No | Hex-encoded log data (defaults to `0x`) |

**Example (ERC-20 Transfer):**
```
GET /api/v1/decode-event?sign=0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef&topics=0xddf252ad...,0x000...from,0x000...to&data=0x000...value
```

## Look Up Function / Event Signature

```
GET /api/v1/query?sign=<hex>&count=<n>
```

| Param | Required | Description |
|-------|----------|-------------|
| `sign` | Yes | 4-byte function selector (e.g. `0xa9059cbb`) or 32-byte event topic0 |
| `count` | No | Max number of matching signatures to return (default `1`) |

**Response:** matching human-readable signatures from the backend signature DB.

**Example (ERC-20 transfer selector):**
```
GET /api/v1/query?sign=0xa9059cbb&count=3
```

## Fetch Contract ABI

```
GET /api/v1/fetch-abi?address=<addr>&chain=<chain>&apiKey=<key>
```

| Param | Required | Description |
|-------|----------|-------------|
| `address` | Yes | Contract address (checksummed or lowercase) |
| `chain` | No | `ethereum` (default), `arbitrum`, `base`, `polygon`, `bsc` |
| `apiKey` | No | Etherscan API key (falls back to server `ETHERSCAN_API_KEY`) |
| `detectProxy` | No | `true` to force proxy detection via on-chain storage slots |

Self-contained — no `BACKEND_URL` needed. Fetches from Etherscan V2, falls back to Sourcify. Auto-detects EIP-1967, EIP-1967 beacon, OZ legacy, and EIP-1167 minimal proxy patterns; merges implementation ABI into proxy ABI.

**Example (USDC on Ethereum):**
```
GET /api/v1/fetch-abi?address=0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48&chain=ethereum&apiKey=YOUR_KEY
```

## Quick Reference

| Endpoint | Needs BACKEND_URL | Needs Etherscan key |
|----------|-------------------|---------------------|
| `/api/v1/decode` | Yes | No |
| `/api/v1/decode-event` | Yes | No |
| `/api/v1/query` | Yes | No |
| `/api/v1/fetch-abi` | No | Optional (higher rate limits) |

**Supported chains:** Any EVM-compatible chain. Built-in: ethereum (1), arbitrum (42161), base (8453), polygon (137), bsc (56). For any other chain, pass `chainId=<id>&rpcUrl=<url>` to use a custom RPC.

**OpenChain fallback:** Both decode endpoints automatically try [openchain.xyz](https://openchain.xyz) when the primary backend has no match for the selector/topic.
