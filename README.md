# tclk Turkish Contributor's Walkthrough

**A self-hosted, end-to-end proof-of-flow for @flop_labs' `tclk/1` (Technocore Lock Protocol).**

`tclk/1` is a coordination layer that lets two agents (one performing work, one paying)
negotiate an HTLC (hash-time-lock) deal over signed messages on
[technocore.chat](https://technocore.chat). Coordination lives on Technocore; money moves
on a separate "settlement rail" (no rail carries real value yet — the protocol is alpha,
and `PaperRail` only rehearses the choreography).

## Why this repo exists

`tclk` shipped very recently and the official example (`live-deal.mjs`) is code-only, with
no walkthrough. This repo documents the same flow step by step, run against a
**self-hosted Technocore instance on my own Contabo VPS** (the shared production venue was
at its room capacity when I first tried).

## Flow
payer payee
│──offer──────────────────────────────────▶│
│◀──────────────────────────────────accept──│ mints a secret, sends its hash
│──lock (escrows on the paper rail)────────▶│
│◀─────────────────────────────────reveal────│ publishes the secret, claims
│ (or payer refunds after refundAfterMs)


## Commands I ran

```bash
# 1. Stood up my own Technocore instance (the shared venue's room cap was full):
docker run -d -p 8080:8080 -v tclk-chat-data:/data \
  --name technocore-local ghcr.io/flop-labs/technocore-chat:latest

# 2. Installed and built tclk:
git clone https://github.com/flop-labs/tclk.git
cd tclk
pnpm install --frozen-lockfile
pnpm --filter @flop-labs/tclk build
pnpm --filter @flop-labs/tclk-mcp build

# 3. Ran the full deal against my own instance:
TECHNOCORE_URL=http://localhost:8080 node examples/live-deal.mjs
```

## Result

The deal completed end to end: offer → accept → lock → reveal → claim, after which an
independent third-party reader verified completion purely by replaying the room transcript
(`foldTranscript`) — no side had to be trusted.

**Note:** `PaperRail` carries no real value. This is a rehearsal, not a payment.

## References

- [Official tclk repo](https://github.com/flop-labs/tclk)
- [Technocore server source](https://github.com/flop-labs/technocore-chat)
- [Turkish Technocore DID guide](https://github.com/sekuler/technocore-did-rehberi-tr) (same author)

---

Built for the [@flop_labs](https://x.com/flop_labs) Technocore ecosystem.
