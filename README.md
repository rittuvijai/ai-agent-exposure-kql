# 🛡️ AI Agent Exposure — KQL Hunting Pack

[![KQL](https://img.shields.io/badge/language-KQL-blue)](https://learn.microsoft.com/en-us/kusto/query/)
[![Defender XDR](https://img.shields.io/badge/platform-Defender%20XDR-0078D4)](https://learn.microsoft.com/en-us/defender-xdr/advanced-hunting-overview)
[![Queries](https://img.shields.io/badge/queries-6-brightgreen)](#-the-queries)
[![Syntax](https://img.shields.io/badge/syntax-validated-success)](#-testing)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)

Your AI agents are the new privileged insiders. This pack answers one question with KQL:

> **"Configured is not the same as observed. Observed is not the same as validated."**

It inventories every AI agent Defender XDR can see (Copilot Studio / Microsoft 365 Copilot), flags the ones shared with your entire tenant, and mines the Security Exposure Management graph for AI-agent-related findings — rolled up to one row per asset so the output stays signal, not noise.

## 📦 The queries

| # | Query | What it answers |
|---|-------|-----------------|
| 01 | [`01-agent-inventory.kql`](queries/01-agent-inventory.kql) | Full inventory of AI agents in the tenant |
| 02 | [`02-broadly-shared-agents.kql`](queries/02-broadly-shared-agents.kql) | Agents reachable by **every** user — the blast-radius list |
| 03a | [`03a-exposure-node-types.kql`](queries/03a-exposure-node-types.kql) | What node types exist in your exposure graph |
| 03b | [`03b-ai-agent-node-discovery.kql`](queries/03b-ai-agent-node-discovery.kql) | Which node types relate to AI agents (feeds 04) |
| 04 | [`04-ai-exposure-findings-per-asset.kql`](queries/04-ai-exposure-findings-per-asset.kql) | AI-agent exposure findings, one row per asset |
| 05 | [`05-agent-access-posture-summary.kql`](queries/05-agent-access-posture-summary.kql) | Executive summary of agent sprawl |

## ✅ Prerequisites

- Access to **Defender XDR Advanced Hunting** (`security.microsoft.com` → Hunting → Advanced hunting)
- **Microsoft Security Exposure Management** onboarded (queries 03–04)
- AI agent inventory data flowing — the `AgentsInfo` table (queries 01, 02, 05)

## 🚀 Quick start

1. Open **Advanced Hunting**.
2. Run **01** — expect one row per non-deleted agent. If the `AgentsInfo` table doesn't exist, this tenant isn't sending agent inventory; check licensing before continuing.
3. Run **03a**, then **03b** — note the `NodeLabel` value that represents AI-agent recommendations in your tenant.
4. Open **04**, replace `AI_AGENT_NODE_LABEL` with that value, run — one row per affected asset.
5. Run **02** and **05** for the sharing and posture views.

## 🧪 Testing

All queries are validated with Microsoft's official Kusto language parser (`@kusto/language-service-next`) — **0 syntax errors**. Table and column references follow the published Defender XDR advanced hunting schema. If a query returns empty in your tenant, see [troubleshooting](#-troubleshooting).

## 🔧 Troubleshooting

| Symptom | Likely cause |
|---------|--------------|
| `AgentsInfo` table not found | Tenant isn't sending AI agent inventory — check Defender for Cloud Apps / Copilot agent visibility licensing |
| `ExposureGraphNodes` not found | Microsoft Security Exposure Management isn't onboarded |
| 03b returns nothing | Broaden the `has_any` terms, or check 03a output for your tenant's naming |
| 04 shows duplicates | Your finding nodes may carry a timestamp property — swap the `make_set` rollup for an `arg_max()` dedup on it |

## 📁 Structure

```
ai-agent-exposure-kql/
├── README.md
├── LICENSE
└── queries/
    ├── 01-agent-inventory.kql
    ├── 02-broadly-shared-agents.kql
    ├── 03a-exposure-node-types.kql
    ├── 03b-ai-agent-node-discovery.kql
    ├── 04-ai-exposure-findings-per-asset.kql
    └── 05-agent-access-posture-summary.kql
```

## 🙏 Credits

Original queries by [Rittu Vijai](https://www.linkedin.com/in/rittuvijai). Pattern inspiration from Steven Lim ([@0x534c](https://www.linkedin.com/in/0x534c/) / [SlimKQL](https://github.com/SlimKQL/Hunting-Queries-Detection-Rules)) and his public work on exposure-management KQL — referenced with thanks, not copied.

## 📄 License

MIT — see [LICENSE](LICENSE). Use it, fork it, hunt with it.
