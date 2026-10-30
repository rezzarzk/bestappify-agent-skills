# BestAppify Agent Skills

Three Agent Skills that teach Claude, Cursor or any MCP-aware agent how to actually
*run* work against the live Shopify App Store — rankings, competitors, reviews,
listing changes, revenue — rather than just answer questions about it.

They pair with the free [BestAppify MCP server](https://bestappify.com/mcp?utm_source=github&utm_medium=repo&utm_campaign=agent-skills),
which exposes the tools. The skills give the agent the method: which tool to
reach for, in what order, and the half-dozen things about this data that will
otherwise make it confidently wrong.

> Tool descriptions tell an agent *what* a tool does. A skill tells it *when*
> and *why*.

## The skills

| Skill | What the agent does with it |
| --- | --- |
| [`shopify-aso-week`](skills/shopify-aso-week/SKILL.md) | A full week of ASO: works out what moved, decides what is worth changing, then changes it — listing copy, screenshots, keywords under watch. |
| [`shopify-competitor-watch`](skills/shopify-competitor-watch/SKILL.md) | Finds who is really competing with you, reads what they changed, and says which of it deserves a response this week. |
| [`shopify-market-research`](skills/shopify-market-research/SKILL.md) | Sizes a niche without guessing — how crowded, what the apps charge, how good the incumbents are, where the gap sits. |

## Install

### 1. Connect the MCP server

Everything these skills do runs through the BestAppify MCP server. One-click
install (OAuth, free plan, no card):

<https://bestappify.com/mcp?utm_source=github&utm_medium=repo&utm_campaign=agent-skills>

Or point your own client at `https://bestappify.com/mcp` with an API key from
your dashboard.

### 2. Drop the skills into your agent

Clone this repo and copy the folder for the skill you want into wherever your
agent loads skills from.

**Claude Code**

```bash
git clone https://github.com/rezzarzk/bestappify-agent-skills.git
mkdir -p ~/.claude/skills
cp -R bestappify-agent-skills/skills/shopify-aso-week ~/.claude/skills/
```

**Claude Desktop / ChatGPT / Cursor**

Each client has its own skills folder. The important part is that the whole
folder (containing `SKILL.md`) ends up where the agent discovers skills — the
contents are plain Markdown with YAML frontmatter and need no build step.

### 3. Ask in your own words

Once the MCP server is connected and the skill is installed, you ask in
natural language. The skill picks which MCP tools to call, in what order.

```
How did my app do this week?
Why did I drop for my main keyword?
What did my competitors change?
Is this niche worth entering?
```

## What these carry that a tool list cannot

Every skill ends with the same four points — the things about Shopify App Store
data that will otherwise make an agent confidently wrong:

- **A null rank is missing data.** It means we did not capture that day — never
  that the app was unranked. An agent that reads the gap as a fall will invent
  a crisis.
- **"Category rank" means two different things.** One orders the apps we hold
  by review count. The other is Shopify's own page order. Only the second is
  where a merchant actually sees you.
- **Keyword positions stopped on 2026-09-06.** Shopify disallowed crawling its
  search page. Stored positions end there; current ones come from on-demand
  checks or the customer's own Google Analytics.
- **Review counts move at our crawl's pace.** A flat week can mean a flat week
  or an unvisited app. Compare over weeks, and say the window.

## License

[MIT](LICENSE). Fork, change, ship it in your own product. If you build
something good on top of these, [tell us](https://bestappify.com/contact) — we
read every message.

## Links

- Site: <https://bestappify.com>
- Skills page: <https://bestappify.com/skills>
- MCP server: <https://bestappify.com/mcp>
- API reference: <https://bestappify.com/docs/api>
