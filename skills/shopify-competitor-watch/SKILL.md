---
name: shopify-competitor-watch
description: >-
  Find out what a Shopify app's competitors actually changed — pricing, listing copy, screenshots,
  position, reviews — and decide which of it is worth reacting to. Use when the task mentions
  competitors, rivals, "what are they doing", a price change, or picking who to watch. Works on apps
  the user does not own, because it reads the public App Store rather than the user's analytics.
  Requires the BestAppify MCP server (bestappify.com/mcp).
---

# Watching the apps you are losing to

The point of this is not a list of rivals. It is deciding which of their moves
deserves a response this week and which is noise.

## Picking who to watch

If the user already has competitors tracked, `get_competitors` returns them. If
not, do not ask them to name rivals from memory — `search_apps` and
`get_leaderboard` for their category find the apps actually ranking around them,
and `compare_apps` shows where the overlap really is. Then `track_competitor` to
put the chosen ones under daily watch.

Five is usually the right number. Beyond that the daily digest becomes something
nobody reads.

## What changed

`whats_changed` for the sweep, `get_listing_changes` for the detail — it is
field-level and dated: renames, tagline rewrites, screenshot swaps, price moves,
a listing going away.

`get_pricing_benchmark` puts a rival's price next to the category's spread,
which is what turns "they charge $19" into a judgement.

`get_rival_complaints` reads what their reviewers are unhappy about. This is the
most underused thing here: a competitor's one-star reviews are a product
roadmap, and they are public.

## Deciding what matters

Rank by what the change implies, not by how big it looks:

- **A price move** is the highest-signal change a rival makes, and the one most
  likely to need an answer within the week.
- **A rewritten tagline or new screenshots** means they are working on
  conversion. Check whether their position moved afterwards before copying them.
- **A position change on its own** is often the market, not them. Run
  `explain_rank_move` on the user's own app before reading a rival's rise as an
  attack.
- **A listing disappearing** is worth knowing the same day.

## Traps that will otherwise make you wrong

**A rival's review count rising is not evidence they are growing.** Review
counts here move at the pace of our crawl of that app, not at the pace of the
market. Compare over weeks, never over days.

**"Category rank" from `get_app_intelligence` is review order, not Shopify's.**
It moves when reviews move. For where a merchant actually sees a rival, use
`get_category_positions`.

**An app absent from a category page is not an app that dropped out.** The crawl
reads a fixed depth of each page. Absence means "not in the pages read".

**Keyword positions stopped updating on 2026-09-06**, when Shopify disallowed
crawling its search results. Do not present a stored keyword rank as today's.

**Listing history starts when we first saw the app.** A field with no recorded
change may have changed before we were watching, so say "no change recorded
since X", not "unchanged".

## Finishing

For each rival, one line: what they changed, and whether to react. Most weeks
most of them need no reaction, and saying so is the useful answer — a watch list
that reports something urgent every week is a watch list nobody trusts.
