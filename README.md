# evm-decoder — Claude Code Skill

A Claude Code skill that gives your agent reference knowledge for the [EVM Transaction Decoder](https://eth-decoder.vercel.app) API — decode transaction calldata, event logs, and fetch verified contract ABIs across any EVM chain.

## What it does

Installs the `evm-decoder-api` skill, which Claude uses automatically when you ask it to:

- Decode raw EVM transaction input data (`0x...`)
- Decode event logs given a topic0 + data
- Fetch a contract's verified ABI (with automatic proxy detection)

## Installation

### Via Claude Code plugin marketplace

```bash
/plugin install evm-decoder@superpowers-marketplace
```

### Manual install

Copy `skills/evm-decoder-api/SKILL.md` to `~/.claude/skills/evm-decoder-api/SKILL.md`.

```bash
mkdir -p ~/.claude/skills/evm-decoder-api
curl -fsSL https://raw.githubusercontent.com/Delweng/evm-decoder-skill/main/skills/evm-decoder-api/SKILL.md \
  -o ~/.claude/skills/evm-decoder-api/SKILL.md
```

## API reference (quick look)

All endpoints live at `https://eth-decoder.vercel.app/api/v1/`.

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/v1/decode` | GET | Decode transaction calldata |
| `/api/v1/decode-event` | GET | Decode an event log |
| `/api/v1/fetch-abi` | GET | Fetch verified contract ABI |

Supports any EVM chain — built-in: Ethereum, Arbitrum, Base, Polygon, BSC. For others, pass `chainId=<id>&rpcUrl=<url>`.

See the [full skill](skills/evm-decoder-api/SKILL.md) for parameter details and examples.

## Source

The API is served by [Delweng/decoder](https://github.com/Delweng/decoder).

## License

MIT
