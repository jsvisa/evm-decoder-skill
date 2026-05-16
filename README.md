# evm-decoder — Claude Code Skill

Give Claude the ability to decode EVM transactions, event logs, and fetch contract ABIs — just by asking.

## What you say → what Claude does

**"Decode this calldata"**
```
0xa9059cbb000000000000000000000000d8da6bf26964af9d7eed9e03e53415d37aa96045
000000000000000000000000000000000000000000000000002386f26fc10000
```
↓ Claude calls `GET /api/v1/decode` and returns:
```
transfer(address to, uint256 value)
  to:    0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045
  value: 10000000000000000 (0.01 ETH)
```

---

**"What is this event log?"**
```
topic0: 0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef
topics: [topic0, 0x000...from, 0x000...to]
data:   0x000...amount
```
↓ Claude calls `GET /api/v1/decode-event` and returns:
```
Transfer(address indexed from, address indexed to, uint256 value)
  from:  0x...
  to:    0x...
  value: 1000000 (1 USDC)
```

---

**"Get the ABI for USDC on Ethereum"**
```
address: 0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48
chain:   ethereum
```
↓ Claude calls `GET /api/v1/fetch-abi`, detects the proxy, merges implementation ABI, and returns the full ABI ready to use.

---

## How it works

This is a **Claude Code skill** — a reference guide Claude loads automatically. It tells Claude:

- Which API endpoints exist (`/decode`, `/decode-event`, `/fetch-abi`)
- What parameters each takes
- How to handle any EVM chain (built-in or custom via `chainId` + `rpcUrl`)

Claude calls [eth-decoder.vercel.app](https://eth-decoder.vercel.app) on your behalf. No setup required beyond installing the skill.

## Installation

### Manual (30 seconds)

```bash
mkdir -p ~/.claude/skills/evm-decoder-api
curl -fsSL https://raw.githubusercontent.com/Delweng/evm-decoder-skill/main/skills/evm-decoder-api/SKILL.md \
  -o ~/.claude/skills/evm-decoder-api/SKILL.md
```

### Via Claude Code plugin marketplace

```bash
/plugin install evm-decoder@superpowers-marketplace
```

## Supported chains

Built-in: **Ethereum · Arbitrum · Base · Polygon · BSC**

Any other EVM chain works too — just tell Claude the chain ID and RPC URL.

## Source

API powered by [Delweng/decoder](https://github.com/Delweng/decoder).

## License

MIT
