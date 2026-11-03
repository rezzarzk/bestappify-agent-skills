---
name: launch-brief
description: >-
  Brief for building or launching a new Shopify app into a category: how
  crowded the market is, who already leads, where the keyword demand sits,
  what the pricing norm looks like, and the gap the new app would occupy.
  Works whether the user names a category by title or by an existing anchor
  app. Use whenever the ask is "should I build in <category>", "brief me on
  the <category> market before launch", "size up this niche". Requires the
  BestAppify MCP server (bestappify.com/mcp), which is where every tool named
  here comes from.
---

# Before you build, read the market

A new app in a crowded category is a review-count footrace against incumbents
who have been collecting them for years. A new app in an open category can
reach a top-10 position in weeks. The point of this brief is to say which of
those two the user is walking into — before the code.

## Before anything else

The user will name either a category ("gift wrap", "inventory sync") or an
anchor app that is near the thing they want to build. Both are fine inputs,
and the first few calls differ only in which one you lead with.

If they named a category title, confirm it exists before anything else: call
`list_popular_keywords` with the title as `query`, and skim the result for a
category your tools recognise. If nothing clean comes back, say so and ask
for a closer term — a market brief for a category we do not hold is a brief
written from thin air.

## 1. How crowded, how growing

`find_niches` with `limit: 100`. The result is every category the catalog
holds, with its app count, recent growth, and the leader's share of reviews.
Pull the row for the user's category and read these three numbers:

- The app count — twelve apps is sparse, three hundred is a knife fight.
- The recent growth — a shrinking category is a bad place to launch.
- The leader's review share — a leader holding more than 40% of reviews
  means one incumbent owns the mindshare, and the new app's listing has to
  argue against them specifically.

## 2. Who already leads

`get_leaderboard` with that category and `limit: 10`. This is the slate the
new app will fight for a cell on the first page of Shopify's category view.
For the top three, call `get_app_details` with `include_intelligence: true`:
you want each one's pricing benchmark position (so the pricing norm step
has data) and the current rank trajectory.

## 3. What this market searches for

`list_popular_keywords` with `query` set to the category word and
`sort: "demand"`. Keep the first ten. For each one, you want three readings:

- The difficulty score — the easier the term, the more realistic a top 10
  is for a listing with no reviews.
- The demand score — size of the term's market.
- The leader on the term — if the same incumbent sits on top of five of the
  ten terms, this is a one-incumbent category, not a ten-player one.

`get_keyword_apps` on one or two of the easiest terms will show you how
close to the top page a new app with zero reviews might realistically sit.

## 4. The pricing norm

You do not have a user app to benchmark here, so `get_pricing_benchmark`
is not the right call. Instead, read the pricing plans from the
`get_app_details` calls in step 2. Tabulate the top five:

- Does every one have a free plan? If yes, the new app almost certainly
  needs one.
- Entry price — the mean of the top five is a safer launch starting point
  than the median, because the median is what the small apps charge.
- Highest tier — the ceiling of what this category will pay a mature app.
  Not the number to launch with, but the number to know.

## 5. What the market complains about

Pick the top-three leader as an anchor app (`get_app_details` already gave
you its handle). Call `get_rival_complaints` with it as the `app` argument.
The result is the themes behind the one and two star reviews across its
category — which is the user's category — in merchants' own vocabulary.

Read the per-theme flag for whether an existing listing mentions it. A
complaint theme that nobody's listing mentions is the one place where a
new, honest tagline can land. That is the positioning line for the launch
brief.

## What the data cannot tell you — read this before quoting a number

**Review counts move at our crawl's pace.** The leaderboard is ranked by
review volume, and a category that looks flat in reviews may just not have
been fully re-crawled this week. Say "as of our last read" rather than
"this week".

**"Category rank" means two different things.** `get_leaderboard` is review
order among the apps we hold; where a merchant actually sees an app on a
Shopify category page is a separate reading. For a launch brief the review
order is the right proxy for "big in this market"; for a weekly position
check it is the wrong one.

**Keyword positions stopped on 2026-09-06.** The positions inside
`get_keyword_apps` come from the bulk crawl before that date for terms we
do not actively re-read. Treat them as a snapshot of the market shape, not
today's leaderboard.

**A quiet niche is not always an open niche.** A category with a dozen apps
and no growth may be a market that has already decided — the first few apps
captured demand and nothing new is landing. Compare the recent-growth
number to the app count: real openings have both.

## Finishing

Return five short sections — Crowding, Leaders, Keyword terrain, Pricing
norm, The gap in the complaints — and one closing paragraph: build, do not
build, or build only if the user can carry a free plan long enough to earn
first reviews. Not three recommendations. One.
