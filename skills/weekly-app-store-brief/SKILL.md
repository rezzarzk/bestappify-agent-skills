---
name: weekly-app-store-brief
description: >-
  Run a single sweep that answers "what moved on my app this week?" in five
  bullets: rank, what competitors did, what their reviewers started complaining
  about, one keyword to pay attention to, and one small listing test worth
  trying. Use this whenever the user asks for a Monday brief, a weekly check-in,
  or a quick read of their Shopify App Store position without a full ASO
  session. Requires the BestAppify MCP server (bestappify.com/mcp), which is
  where every tool named here comes from.
---

# Your week on the Shopify App Store, in five bullets

A full ASO review is a session. This one is a glance before coffee: one sweep
of five tools, one five-bullet reply, no questions back to the user unless
something is missing.

## Before anything else

The user will name an app by its handle (the slug in its App Store URL). If
they do not, call `list_my_apps` once and use the first one — this skill is for
the Monday morning read, not for picking which app to look at.

## The five calls, in order

Run these one after another. Each one is one of the five bullets you will
return at the end.

1. **Rank** — `get_app_details` for the app's current category rank, rating and
   review count. Read the app-category-positions block inside the response, not
   the app-intelligence rank: the second is our review-count order, the first
   is where a merchant actually sees them on Shopify's category page.
2. **Movers** — `get_movers` with `range=7d`. Pull the two or three category
   peers that moved the most, in either direction. The user is asking "did the
   neighbourhood move this week?" and this is the answer.
3. **Rival complaints** — `get_rival_complaints` with `range=7d`. Which of
   their reviewers started complaining about something in the last week, and
   about what. This is what "what to build next" looks like in one sentence.
4. **Review intelligence** — `get_review_intelligence` for the user's own app.
   Rating trend, new review themes. One line: "your reviewers this week keep
   mentioning X."
5. **Keyword to watch** — `list_popular_keywords` with `limit=10`. Pick ONE
   term from the list that fits the user's category and that they are not
   already strong on. Not three — one. Monday morning is for one thing.

If any call errors or returns nothing useful, say so in that bullet in one
sentence rather than skipping it. A silent gap in a weekly brief looks like
nothing happened, which is not what the data said.

## The reply template

Reply with exactly five bullets, in the order above, then one closing line
proposing the smallest test the user could run this week. Nothing else — no
preamble, no sign-off, no "let me know if…".

```
• Rank: #<N> in <category> (<up/down/flat> from the last reading), <rating>★ on <n> reviews
• Movers: <peer A> <delta>, <peer B> <delta> — <one-sentence reading>
• Rival complaints: <theme>, from <peer> — <one line of what they wanted that the app did not do>
• Your reviewers: <theme that showed up this week, with a count>
• Watch: <keyword> — <why this one, in six words>

Smallest test this week: <one concrete change — a tagline line, a screenshot headline, a reply to a review — not a project>
```

The point is that the user can read the whole thing in ten seconds and act on
the last line before Tuesday.

## What the data cannot tell you — read this before quoting a number

**A null rank means "not captured that day", never "unranked."** The gap is
honest missing data. Do not report it as a fall, and do not fill it in.

**Search-keyword rank is frozen.** Shopify disallowed crawling its search page
on 2026-09-06 (`robots.txt`, `*q=*`). `list_popular_keywords` tells you which
terms matter in a category; it does NOT tell you where the user currently
ranks for them. If the user asks "where do I rank for X", say the position is
either an on-demand check or from their Google Analytics, and name which one.

**"Category rank" has two meanings and only one is Shopify's.** Our
app-intelligence rank orders apps by review count — it jumps when reviews
land. The app-category-positions block inside `get_app_details` is Shopify's
own category page order, which is where a merchant actually sees you. The
first bullet uses the second one.

**Review counts move at our crawl's pace, not the market's.** A flat week of
review counts can mean a flat week or an unvisited app — say "this week" and
mean the last read, not the last seven calendar days.

## Finishing

Do not expand into a plan. If the user asks for the plan behind the "smallest
test" line, hand them off to the `shopify-aso-week` skill — that one ends with
the listing changed, this one ends with the brief read.
