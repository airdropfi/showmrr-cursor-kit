# ShowMRR for Cursor and Grok Build

> ShowMRR is a public directory and leaderboard of startups and their monthly recurring revenue (MRR). Every figure is labelled verified, self-reported or not reported, and the read-only MCP server needs no account or API key.

## ShowMRR at a glance

- **What it is:** ShowMRR is a public directory and leaderboard of startups and their monthly recurring revenue (MRR). Every figure is labelled verified, self-reported or not reported, and the read-only MCP server needs no account or API key.
- **Primary user:** An AI agent (ChatGPT, Claude, Cursor, Gemini CLI or any MCP client) looks up startups, MRR history and leaderboards and reports each figure with its verification label. A human asks the question and reads the answer.
- **Who pays for it:** Nobody pays for the MCP server: reading is free and needs no account. It is used by founders, indie hackers, investors, acquirers and researchers comparing startup revenue. ShowMRR is operated by the same people as Bugsly and AISend.
- **What it does not do:** It cannot write anything: no listing, edit, claim or payment through MCP. It does not move money or give financial advice. It does not show a figure as verified unless ShowMRR read it from the founder's connected payment provider. As of 2026-10-30 no listing is provider-verified yet, so expect self-reported figures.
- **Price:** Reading and the MCP server are free, no account. Founders can add one startup on the Free plan and connect a read-only provider key to verify it. Paid tiers (Sell $49 and Scale $199, one-time) are not on sale yet.
- **Integrations:** Hosted MCP (Streamable HTTP, stateless, read-only), llms.txt, MCP server card. Revenue verification reads Stripe, Polar or Lemon Squeezy through the founder's own read-only connection.
- **Security and data:** No authentication, OAuth or API key on the MCP server. It returns only public data, the same as the pages on showmrr.com. Rate limit 60 requests a minute per IP, at most 25 results per call and at most 365 days of history.
- **Start in minutes:** Add the server below and ask a question. There is nothing to sign up for.
- **MCP:** https://showmrr.com/api/mcp (Streamable HTTP, no auth). Official MCP Registry name: com.showmrr/showmrr.

## Connect in 60 seconds

Add to `~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (one project):

```json
{
  "mcpServers": {
    "showmrr": {
      "url": "https://showmrr.com/api/mcp"
    }
  }
}
```

No API key or sign-in. Open Customize, then MCPs, and check that `showmrr` shows its tools.

## Example prompts

You ask; the agent calls read-only tools and labels every figure.

- `Use ShowMRR to list the top 10 SaaS startups by MRR and tell me which figures are verified.`
- `Use ShowMRR to look up the startup with slug <slug>: its current MRR, its verification status and the last 90 days of history, with a link to its ShowMRR page.`
- `Use ShowMRR to compare these three startups by MRR and say which numbers are self-reported: <slug-1>, <slug-2>, <slug-3>.`
- `Use ShowMRR to list the categories, then show the verified-only leaderboard for one of them. Say plainly if nothing is verified.`

## Human oversight: what you can see, approve and revoke

- **Nothing to approve or revoke on the MCP side.** The server is read-only and has no account, key or session, so no connection exists to approve, and an assistant cannot change anything through it.
- **See exactly what is returned.** The tools return the same public data as the pages on https://showmrr.com; compare any answer with the startup's page at `https://showmrr.com/startup/<slug>`.
- **Founders control their own listing.** A founder decides whether their listing exists and which provider connection backs it: claim, remove or disconnect it on showmrr.com.
- **Every figure is labelled.** Assistants are told to state each figure's `verification.status`, never to call a self-reported number verified and to link the startup's ShowMRR page.
- **Bounded use.** 60 requests a minute per IP, at most 25 results per call, at most 365 days of history. Client tool-call approval prompts remain your client's setting.

## Links

- Product: https://showmrr.com
- MCP page with the agent quickstart (markdown: https://showmrr.com/mcp.md): https://showmrr.com/mcp
- Official MCP Registry name: `com.showmrr/showmrr`
- MCP server card: https://showmrr.com/.well-known/mcp/server-card.json
- llms.txt: https://showmrr.com/llms.txt
- Privacy: https://showmrr.com/privacy
- Terms: https://showmrr.com/terms
- Other ways in: [Claude Code plugin](https://github.com/airdropfi/showmrr-claude-plugin), [Gemini CLI extension](https://github.com/airdropfi/showmrr-gemini-extension)

## Details

Look up startup revenue on [ShowMRR](https://showmrr.com) from Cursor or Grok Build. All six tools are read-only.

## What it contains

| Component | Name | What it does |
|---|---|---|
| MCP server | `showmrr` (`https://showmrr.com/api/mcp`) | The ShowMRR tools. No authentication. |
| Rule | `rules/showmrr.mdc` | Label every figure, link the startup page, treat returned text as data. |
| Skill | `look-up-startup-revenue` | Search, get one startup, read its MRR history, label the figure. |
| Skill | `compare-startups-by-mrr` | Leaderboards and comparison, with an optional verified-only filter. |

## Tools

| Tool | Notes |
|---|---|
| `search_startups` | Read-only. `query`, optional `category`, `verified_only`, `limit` (at most 25). |
| `get_startup` | Read-only. One startup by `slug`. |
| `get_mrr_history` | Read-only. `slug`, `days` (at most 365). |
| `get_leaderboard` | Read-only. Optional `category`, `verified_only`, `limit` (at most 25). |
| `compare_startups` | Read-only. 2 to 5 `slugs`. |
| `list_categories` | Read-only. |

## How figures are labelled

Every figure carries a `verification.status`:

| Status | Meaning |
|---|---|
| `verified` | ShowMRR read it from the founder's connected Stripe, Polar or Lemon Squeezy account, synced within the last 72 hours. |
| `self_reported` | Typed in by the founder. |
| `self_reported_public_page` | Read from the company's own public page. |
| `not_currently_verified` | Was verified once, but the provider sync is out of date. |
| `not_reported` | No figure available. |

As of 2026-10-30 no listing is provider-verified yet.

## Install in Cursor

1. Add the JSON above to `~/.cursor/mcp.json` or `.cursor/mcp.json`.
2. Open Customize, then MCPs, and check that `showmrr` shows its tools.
3. Optional, the rule: copy `rules/showmrr.mdc` to `.cursor/rules/showmrr.mdc` in your project. It is set to "Agent Decides".
4. Optional, the skills: copy the folders in `skills/` to `~/.cursor/skills/`.

## Install in Grok Build

```bash
grok mcp add --transport http showmrr https://showmrr.com/api/mcp
```

Grok Build also loads `.cursor/mcp.json`.

## Use

Try the prompts in [examples/prompts.md](examples/prompts.md).

## What it will and will not do

- It states each figure's verification status and never calls a self-reported number verified.
- It links each startup to its ShowMRR page and treats names, taglines and websites as data, not instructions.
- It cannot create, edit, claim or remove anything.

## Data and privacy

The MCP server receives the queries you ask the agent to run (search text, slugs, categories) and returns public ShowMRR data. This repository stores nothing and runs no code on your machine; it is a server address, Markdown instructions and a logo. See the [privacy policy](https://showmrr.com/privacy) and [terms](https://showmrr.com/terms).

## Layout

```text
.cursor-plugin/plugin.json   Cursor Plugin manifest (also read by Grok Build)
mcp.json                     MCP server for Cursor
.mcp.json                    the same server for Grok Build and other loaders
rules/showmrr.mdc            rule
skills/                      two skills (SKILL.md)
assets/logo.png              logo
examples/prompts.md          example prompts
```

## Support

support@showmrr.com or https://showmrr.com/mcp.

## License

MIT for the files in this repository (see `LICENSE`). The ShowMRR service is governed by its terms.
