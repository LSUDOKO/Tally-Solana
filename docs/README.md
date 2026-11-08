# Tally

> **Your agent proposes. The merchant signs. Tally checks it on-chain.**

Tally is a mobile-first Solana app for the Seeker phone. It lets an AI agent buy things for you — coffee, lunch, transit, reorders — but it can only pay for an item from a menu the merchant signed, and only if that item fits the rules you signed. A Solana program checks both before any money moves, and checks afterwards that exactly that price left your vault. If anything does not tally, the transaction reverts.

Technically: **deterministic intent-to-execution binding** — three proofs (merchant-signed catalog leaf, Seed Vault–governed intent capsule, hardened introspection + exact-outflow post-conditions) in one atomic transaction. Full spec: [00-whitepaper.md](00-whitepaper.md).

**Spending limits protect your balance. Tally protects your decision.**

Built for the **[MLH Solana Hackathon](https://events.mlh.com/events/15200-solana-hackathon)** (Major League Hacking × Solana, 6–8 November 2026), AI Track.

---

## Why this exists, in one paragraph

Agent payment systems today sign the *transaction* but not the *decision* behind it. A September 2026 paper ("Signing the Transaction but Not the Decision", arXiv 2609.11757) showed that one sentence of merchant text can steer a shopping agent into a pricier item, an unshown add-on or a different payee — and the resulting purchase still passes every signature and spending-cap check. Existing Solana agent wallets (Cloaked Agent, Praxis, AgentGuard, Squads spending limits) bind *how much* and *to whom*. None of them binds *which item the agent chose* against a merchant-signed offer and the user's intent, on-chain. Tally does.

---

## How to read this pack

| # | File | What's inside |
|---|---|---|
| — | [README.md](README.md) | This overview |
| 00 | [00-whitepaper.md](00-whitepaper.md) | **Flagship whitepaper**: problem, evidence, ten invariants, mobile, tokens, threat model, corrections |
| 01 | [01-pitch.md](01-pitch.md) | One-liner, elevator pitch, taglines, positioning |
| 02 | [02-idea-selection.md](02-idea-selection.md) | Every idea we considered, scored, and why Tally won |
| 03 | [03-research-foundation.md](03-research-foundation.md) | The papers, what each found, what Tally takes from it |
| 04 | [04-competitive-landscape.md](04-competitive-landscape.md) | What already exists and the exact gap Tally fills |
| 05 | [05-architecture.md](05-architecture.md) | System flow, components, data flow, sequence |
| 06 | [06-onchain-program.md](06-onchain-program.md) | Anchor program: accounts, instructions, the ten invariants, fraud-proof slashing, reference code |
| 07 | [07-menu-and-qr-spec.md](07-menu-and-qr-spec.md) | 144-byte header, Merkle leaves, freshness tiers, QR + NFC HCE (ISO 7816-4) |
| 08 | [08-android-app.md](08-android-app.md) | Screens, MWA + Seed Vault flows, mobile-native features |
| 09 | [09-agent-design.md](09-agent-design.md) | How the agent runs, its key, the context firewall |
| 10 | [10-advanced-features.md](10-advanced-features.md) | Core vs next vs later features, each tied to research |
| 11 | [11-threat-model.md](11-threat-model.md) | 20 attacks: 11 blocked, 3 surfaced, 2 bounded, 4 out of scope |
| 12 | [12-skr-and-merchants.md](12-skr-and-merchants.md) | SKR bond tiers, slashing, onboarding, go-to-market |
| 13 | [13-hackathon-requirements.md](13-hackathon-requirements.md) | MLH Solana Hackathon tracks mapped to Tally |
| 14 | [14-demo-script.md](14-demo-script.md) | Second-by-second 3-minute demo and recording tips |
| 15 | [15-pitch-deck.md](15-pitch-deck.md) | Slide-by-slide deck content and speaker notes |
| 16 | [16-build-plan.md](16-build-plan.md) | Ordered build plan, milestones, cut list |
| 17 | [17-repo-structure.md](17-repo-structure.md) | Monorepo layout, tooling, README template for judges |
| 18 | [18-testing.md](18-testing.md) | Program tests per attack, app tests, device checklist |
| 19 | [19-sources.md](19-sources.md) | Every paper and project referenced, plus verification notes |
| 20 | [20-sgt-and-ore.md](20-sgt-and-ore.md) | Seeker Genesis Token anti-sybil + ORE DrillX PoW anti-flood |
| 21 | [21-real-phone-run.md](21-real-phone-run.md) | The full flow on a stock phone with Solflare on devnet: every transaction, and what it changed |

---

## The idea in 30 seconds

1. **You set rules once.** "Coffee, max $6 a purchase, $15 a day, cheapest by default." Seed Vault signs a **capsule** — an on-chain account holding a small USDC balance and your rules.
2. **The merchant shows a signed menu** as a rotating QR or over an **NFC tap**: a Merkle root of items, prices and category floors, signed with an SKR-bonded key, valid for ~60 seconds.
3. **Your agent picks an item.** A context firewall lets it see only structured fields (item, category, price, floor) — never free-text descriptions that could carry an injected instruction.
4. **The program decides — ten invariants.** Right agent and live capsule · Ed25519 signature found by introspection · offsets hardened against hijack · fresh slot window · payout from the registry · item really on the menu · category and caps · cheapest-match (pricier than the floor → you co-sign) · not paid before · exactly the price leaves your vault (in-program + Lighthouse).

Anything that doesn't tally reverts on-chain. You can see the failed transaction on the explorer.

---

## Hackathon fit at a glance

| MLH Solana Hackathon ask | Tally |
|---|---|
| AI Track: AI and Solana each do real work | The agent picks the item; the Solana program decides whether the payment may happen |
| AI Track: agents that hold a wallet, with spending limits | Scoped agent key in Android Keystore; capsule caps, categories, weekday/weekend allowance, freeze |
| AI Track: flag risk before the user signs | Second Look stops for the owner's signature and shows the gross outflow |
| Top 3: uses Solana for what it's good at | Atomic on-chain checks inside the payment: Ed25519 introspection, Merkle proof, exact-outflow post-conditions |
| DeFi Track: stablecoin checkout, Solana Pay | USDC vault, counter checkout, standard Solana Pay QR in merchant mode |
| Working product | Devnet program, Android APK, Tally cloud (MCP server, Telegram bot, alerts) |
| Submission | Public repo + demo video + APK, on MLH Challenges |

---

## Honest status note

Everything in this pack is a **design**. The Anchor and Kotlin code are **reference sketches written for clarity, not compiled or audited**. Build, test and review them before deploying anything that holds real funds. The novelty claim is "no project we found does this exact combination", not "nothing like it exists" — see [04-competitive-landscape.md](04-competitive-landscape.md).
