# BestAppify Agent Skills

Fourteen Agent Skills that teach Claude, Cursor or any MCP-aware agent how to
actually *run* work against the live Shopify App Store — rankings, competitors,
reviews, listing changes, revenue — rather than just answer questions about it.

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
| [`weekly-app-store-brief`](skills/weekly-app-store-brief/SKILL.md) | One sweep, one five-bullet reply: rank, what competitors did, rival complaints, one keyword to watch, one small test to run this week. The Monday-morning read before coffee. |
| [`rival-teardown`](skills/rival-teardown/SKILL.md) | Deep read on a single competitor the user names: where they sit, where their price lands, what has moved on their listing lately, and what their market complains about. |
| [`review-triage`](skills/review-triage/SKILL.md) | Reads recent reviews for one of your apps, clusters them into the two or three complaints merchants are actually raising, and drafts one reply per cluster. |
| [`pricing-audit`](skills/pricing-audit/SKILL.md) | Audits one of your apps' price ladder against the top of its own category: free-plan norm, entry-tier quartile, tier count, top rivals' ladders side by side. |
| [`keyword-gap`](skills/keyword-gap/SKILL.md) | Finds the App Store search terms where rivals in your category place in the top 10 and your app does not. A short, actionable list, then daily watch on the ones you pick. |
| [`launch-brief`](skills/launch-brief/SKILL.md) | Market brief for launching into a category: crowding, leaders, keyword demand, pricing norm, and the one gap in the complaints a new honest listing could land in. |
| [`upgrade-your-plan`](skills/upgrade-your-plan/SKILL.md) | Upgrades the account from inside the chat when a tool refuses "this needs Growth/Pro": looks up the current plan and usage, mints a signed checkout link, reads it back for the user to open. |
| [`ship-listing-rewrite`](skills/ship-listing-rewrite/SKILL.md) | Rewrites the listing against the terms that actually earned installs — from the app's own GA4, not the crawl. Drafts, reviews the draft critically, and puts the chosen terms under daily watch. |
| [`install-attribution-weekly`](skills/install-attribution-weekly/SKILL.md) | Weekly install-attribution for one app: which search terms earned installs (GA4), which stores actually installed, and how that moved MRR and churn (Partner API). The join no other Shopify tool can do. |
| [`rival-passed-me-today`](skills/rival-passed-me-today/SKILL.md) | Today read, not weekly: find the single competitor who overtook your app in the last day or two, name the move that did it, and decide in one word whether to answer with copy, price, reviews or just watch. |
| [`affiliate-partner-finder`](skills/affiliate-partner-finder/SKILL.md) | Real cross-promotion, affiliate or integration candidates in one App Store category — apps the catalog holds, above a rating floor, with the public listing email the vendor already published. Never drafts the outreach itself. |

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
