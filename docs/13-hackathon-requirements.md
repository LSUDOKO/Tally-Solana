# 13 — Hackathon requirements mapped to Tally

<p>
  <img src="brand/partners/solana-horizontal-black.svg" alt="Solana" height="28">
  &nbsp;&nbsp;&nbsp;
  <img src="brand/partners/mlh-logo-color.svg" alt="Major League Hacking" height="40">
</p>

Event: **[Solana Hackathon by Major League Hacking](https://events.mlh.com/events/15200-solana-hackathon)** (MLH Solana Hackathon), online, **Friday 6 November 2026, 12:00 PM ET → Sunday 8 November 2026, 1:00 PM ET**. Initial submission is due on MLH Challenges on **Sunday 8 November, 10:00 AM ET (8:30 PM IST)**; the closing ceremony is at 12:00 PM ET.

## Prize tracks and where Tally enters

| Track | What the organisers ask for | Tally |
|---|---|---|
| **AI Track** (primary) | "Build something where AI and Solana each do real work." Ideas listed: *autonomous agents that hold a wallet and act on-chain … with spending limits*, *security tools that … flag risky programs, tokens and wallets before a user signs*, *machine payments … with stablecoins*. | An AI agent holds a scoped key and pays in USDC; a Solana program checks every payment against a merchant-signed menu and the owner's rules, and reverts anything else. The agent proposes, the chain decides, and the owner co-signs only the exceptions (Second Look). |
| **Top 3 Overall** | Every eligible submission is entered. "A working product that uses Solana for something it's good at (speed, low fees, composability, on-chain ownership), not a chain bolted onto a web2 app." | The security model only works because the check runs on-chain, atomically, inside the payment: Ed25519 introspection, Merkle proofs, exact-outflow post-conditions, about a second per settlement. |
| **DeFi Track** (secondary fit) | "Payments and stablecoins: checkout flows … using Solana Pay or SPL stablecoins." | USDC capsule vault, a counter checkout, and a standard Solana Pay QR from merchant mode. |

## What a judge can check

| Ask | How Tally answers it | Evidence |
|---|---|---|
| A working product | Program deployed on devnet; Android APK; Tally cloud (MCP server, Telegram bot, alerts) | [VERIFY.md](VERIFY.md), [Releases](https://github.com/LSUDOKO/Tally-Solana/releases) |
| AI and Solana each do real work | The agent picks the item from structured fields behind a context firewall; the program enforces ten invariants on every `pay_offer` | [09-agent-design.md](09-agent-design.md), [06-onchain-program.md](06-onchain-program.md) |
| Spending limits | Per-purchase and daily caps, categories, weekday/weekend allowance, freeze | Capsule account; Settings and Home in the app |
| Flag risk before the user signs | Second Look shows the gross outflow and stops for the owner's signature when the agent picks a pricier item or an under-bonded merchant | Demo video, Second Look chapter |
| Attacks fail on-chain | Payee swap, price tampering, phantom add-on and replay each revert with an exact error | Explorer links in the README, [THREAT_SUITE.md](../tests/litesvm/THREAT_SUITE.md) |
| Tests | 155 Rust · 19 TS · 97 Kotlin · 16 cloud, run in CI on every push | [CI](https://github.com/LSUDOKO/Tally-Solana/actions/workflows/ci.yml) |

## Submission checklist

- [x] Public repository: <https://github.com/LSUDOKO/Tally-Solana>
- [x] Demo video (about 3:40): [`docs/video/tally-mlh-solana-demo.mp4`](video/tally-mlh-solana-demo.mp4)
- [x] Devnet program ID and explorer links in the README
- [x] APK on the Releases page
- [ ] Submit on MLH Challenges before 10:00 AM ET, Sunday 8 November 2026

## Honest notes

- Tally runs on **devnet**. SKR and the Seeker Genesis Token are mock mints from our faucet there.
- The research figures in the pitch (73.3%, 140 → 0) are other teams' published results, not our measurements.
- No security audit yet; no mainnet deployment.
