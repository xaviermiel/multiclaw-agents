# MultiClaw Agents

Pinata-hosted [OpenClaw](https://agents.pinata.cloud/) agent templates that
operate **MultiClaw vaults** on Base. Each agent's on-chain authority is bounded
by a Safe [Zodiac](https://www.zodiac.wiki/) module — **not** by its system
prompt. So an agent can be given real on-chain power without being able to exceed
its limits, regardless of prompt injection or model error.

## One primitive, many bounded roles

Every template here is built on the **same** skill — `multiclaw-vault` — which
talks to a MultiClaw vault within its on-chain limits (read budget/status/history,
check the recipient whitelist, execute capped transfers). The templates differ
only in **persona** (`SOUL.md`) and **on-chain configuration** (roles, caps,
whitelist). Same building block, different bounded job.

## Templates

| Template                                                          | Category               | What it does                                                   | Worst case if compromised  |
| ----------------------------------------------------------------- | ---------------------- | -------------------------------------------------------------- | -------------------------- |
| [Capped Trader](openclaw/actions-and-transactions/capped-trader/) | Actions & Transactions | Trades within a per-window USD spending cap                    | Spends up to the cap       |
| [Vault Guardian](openclaw/monitoring-and-alerts/vault-guardian/)  | Monitoring & Alerts    | Watches positions; withdraws to the owner on a credible threat | Returns funds to the owner |

## The shared skill

`multiclaw-vault` lives in each template's `workspace/skills/multiclaw-vault/`
and is **byte-identical** across templates — so `pinata agents skills create`
pins it to the **same IPFS CID**. Pin it once (from either template) and
reference that one CID in every manifest's `skills[0].cid`. It wraps
[`@multiclaw/core`](https://www.npmjs.com/package/@multiclaw/core) (on npm), which
resolves via the template's `scripts.build` (`npm install`).

> Keep the copies in sync. If you change the skill, update it in every template
> so they continue to pin to one CID.

## Layout

Mirrors Pinata's [agent-templates](https://github.com/PinataCloud/agent-templates)
convention — `openclaw/<category>/<template>/`, each template self-contained with
its own `manifest.json`, `SOUL.md`, `BOOTSTRAP.md`, and `workspace/`:

```
openclaw/
├── actions-and-transactions/
│   └── capped-trader/
└── monitoring-and-alerts/
    └── vault-guardian/
```

## Using a template

```bash
cd openclaw/<category>/<template>
pinata agents skills create workspace/skills/multiclaw-vault   # → CID
#   paste the CID into manifest.json skills[0].cid (same CID across templates)
pinata agents templates validate .
pinata agents create --template <repo-url>
```

Each template's `README.md` and `BOOTSTRAP.md` cover its secrets, vault setup, and
smoke test. These target **Base mainnet** by default and move **real funds** —
read the per-template notes before deploying.
