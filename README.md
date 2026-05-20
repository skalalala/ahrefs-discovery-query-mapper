# Ahrefs Discovery Query Mapper

A Claude Skill that reviews your product positioning and maps it to real, validated SEO keywords, AI-search prompt queries, and content opportunities.

The key rule: the LLM can create seed concepts, but final keyword recommendations must come from Ahrefs MCP, an Ahrefs export, Google Search Console, or another user-provided search dataset.

## What it does

Give it product positioning, target audience, market, and a search data source. It will:

- Extract seed concepts from the positioning brief
- Use Ahrefs MCP or an uploaded export to validate keyword opportunities
- Filter out irrelevant, too-broad, or off-positioning terms
- Cluster keywords by intent and funnel stage
- Map validated queries back to positioning pillars
- Recommend content opportunities
- Label AI-search prompts as synthetic unless validated by Profound or another AI-search dataset

## How users actually use it

There are two normal usage paths.

## Path A: Ahrefs MCP connected

This is the recommended path.

In this mode, the user does **not** paste Ahrefs credentials into the chat, the skill, or the repo. The user connects Ahrefs MCP to Claude Code once, then pastes the product messaging into Claude Code during the session.

### 1. Connect Ahrefs MCP in Claude Code

Run:

```bash
claude mcp add ahrefs https://api.ahrefs.com/mcp/mcp -t http
```

Then open Claude Code and run:

```text
/mcp
```

Authenticate the Ahrefs MCP server, select the Ahrefs workspace, and verify that Ahrefs queries return results.

Official docs:

- Ahrefs MCP overview: https://docs.ahrefs.com/en/mcp/docs/introduction
- Ahrefs Claude Code setup: https://docs.ahrefs.com/en/mcp/docs/claude-code
- Ahrefs Keywords Explorer API reference: https://docs.ahrefs.com/en/api/reference/keywords-explorer

### 2. Use the skill in Claude Code

After the skill is installed and Ahrefs MCP is connected, prompt Claude Code like this:

```text
Use the ahrefs-discovery-query-mapper skill.

Data mode: Ahrefs MCP connected
Market: United States
Product: The Giving Block

Positioning brief:
[paste positioning brief]

Audience:
[paste ICP or audience notes]

Competitors:
[paste competitor list]

Use Ahrefs MCP to find validated keyword opportunities.
Do not include final keyword recommendations unless Ahrefs returns the keyword.
Do not invent volume, KD, traffic potential, CPC, ranking URLs, or SERP data.
```

The messaging goes in the Claude Code chat. The Ahrefs credentials stay in the MCP authentication flow.

See:

- `examples/example-input-mcp.md`
- `examples/example-output-mcp.md`

## Path B: Ahrefs export fallback

Use this path when Ahrefs MCP is not connected or when you want a reproducible demo.

In this mode, the user uploads or pastes an Ahrefs keyword export. Claude clusters and prioritizes only the keyword data in the export.

Recommended export columns:

```csv
keyword,country,volume,kd,traffic_potential,cpc,source_report,seed,ranking_url
```

Required columns:

```csv
keyword,country,source_report
```

If a metric is missing from the export, the skill should leave it blank. It should not estimate missing numbers.

See:

- `examples/example-input-export.md`
- `examples/example-output-export.md`

## Credential and API key rules

Do not paste Ahrefs API keys, MCP keys, or access tokens into:

- GitHub
- `README.md`
- `SKILL.md`
- Example files
- Claude prompts
- Uploaded docs

For Claude Code, use the MCP authentication flow:

```bash
claude mcp add ahrefs https://api.ahrefs.com/mcp/mcp -t http
```

Then authenticate through:

```text
/mcp
```

If another MCP client requires a manually generated key, store it only in that client’s private local configuration or secrets manager.

Use placeholders only in private local config, never in files committed to GitHub:

```json
{
  "Authorization": "Bearer YOUR_AHREFS_MCP_TOKEN_HERE"
}
```

## Repository structure

```text
ahrefs-discovery-query-mapper/
├── README.md
├── SKILL.md
├── .gitignore
└── examples/
    ├── example-input-mcp.md
    ├── example-output-mcp.md
    ├── example-input-export.md
    └── example-output-export.md
```

## Install

Clone this repository into your local Claude Code skills directory.

```bash
git clone [repo-url] ~/.claude/skills/ahrefs-discovery-query-mapper
```

Restart Claude Code so it can detect the new skill.

## Inputs

| Input | Required? | Description |
|---|---:|---|
| Product positioning brief | Yes | Source for product category, audience, value pillars, differentiators, and proof points |
| Target market/country | Yes | Country or market for keyword data |
| Ahrefs MCP or Ahrefs export | Yes for validated SEO output | Source of truth for keyword candidates and metrics |
| Competitor list | No | Used to identify comparison, alternative, and competitor-intent queries |
| Existing URL | No | Useful for mapping current content to keyword opportunities |
| Google Search Console export | No | Useful for first-party query data |
| Profound or AI-search export | No | Useful for validating AI-search prompts and brand visibility |

## Output

The skill returns:

- Data-source status
- Seed concepts extracted from positioning
- Validated keyword clusters
- Search intent and funnel stage
- Positioning-to-query map
- AI-search prompt candidates
- Content opportunity priorities
- Queries to run next in Ahrefs
- Confidence note

## Hard rule

Do not include a keyword in the final SEO recommendations unless it appears in Ahrefs, Google Search Console, or another user-provided keyword dataset.

Do not invent:

- Search volume
- Keyword difficulty
- Traffic potential
- CPC
- Ranking URLs
- SERP features
- Competitor rankings
- AI-search visibility metrics

## What this skill does not do

- It does not invent keyword data.
- It does not treat LLM-generated keywords as validated demand.
- It does not replace Ahrefs, GSC, Profound, or another analytics source.
- It does not guarantee rankings.
- It does not create final content briefs unless explicitly asked.
- It does not verify live SERPs unless Ahrefs MCP or another data source is connected.

## Built by

Built by Sam Kahler (@skalalala). PRs welcome.
