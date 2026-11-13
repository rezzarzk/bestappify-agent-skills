---
name: rival-passed-me-today
description: >-
  Find out which competitor passed the user's app today — by rank, by review
  volume, or by a listing change — and decide whether to answer them this
  week. Not a weekly read; a specific "something moved past me in the last
  day or two" read. Use when the user says "did a rival overtake me", "who
  passed me", "I'm not top-10 any more", "what changed above me in the
  category". Requires the BestAppify MCP server (bestappify.com/mcp), which
  is where every tool named here comes from.
---

# One rival, passed you, today

Weekly briefings catch most moves. This skill catches the one the user
felt — somebody climbed past them in the last day or two — and names the
specific listing change, price change, review burst or category move that
did it.

## Scope / requires

- MCP server connected (every plan)
- An app on the user's account — `list_my_apps` must return at least one
- Enough history for a one-day delta. Fresh accounts in their first 24
  hours see nothing and should run this tomorrow.

## Before anything else

Call `list_my_apps` and confirm the app the user is asking about.

## 1. Did anyone actually pass them

`get_movers({category: <the app's primary category>, limit: 50})` — the
biggest category climbers and fallers in the last seven days. Narrow the
list to apps that:

- climbed (`change > 0`), AND
- ended at a `rank` better than the user's current `rank`, AND
- a week ago were at a the "rank a week ago" column worse than the user's today

That is the set of rivals who overtook them. If the set is empty, say so
and stop — do not invent a story about the market.

`get_competitors({app, include_timeline: true})` — the daily rank
timeline for every tracked rival. Scan for a rival whose line crosses the
user's line in the last few days; that is a faster signal than the
category movers board and catches rivals in other categories too.

## 2. Pick the one worth answering

Prefer, in order:

1. The rival the user already tracks and who overtook them (from
   `get_competitors`). They already decided this rival matters.
2. The rival on the movers board whose jump is largest.
3. The rival whose rating or review count moved the most this week — a
   review-count jump can lift category-page rank on its own.

One rival, not three. The user can answer one this week.

## 3. What did they actually do

For the chosen rival:

- `get_app_details({app: <rival_handle>, include_intelligence: true})` —
  the rival's rank trajectory, category positioning and pricing.
- `get_review_intelligence({app: <rival_handle>, recent: 10})` — what
  their market loves, complains about and requests. A rival who cleared
  their complaint backlog climbed for a reason.

If BestAppify holds the rival's listing-change history, mention the
latest change date. A change on the rival's listing plus a rank climb is
not a coincidence.

## 4. Decide: answer or watch

Four possible outcomes. Pick exactly one:

- **Answer with copy.** The rival changed their tagline or key screenshot
  and it reads better than the user's. Suggest the `ship-listing-rewrite`
  skill, focused on the terms the rival now targets.
- **Answer with price.** The rival dropped their entry price and the
  user's category is price-sensitive. Suggest the user re-read their
  pricing page; BestAppify does not change prices for them.
- **Answer with reviews.** The rival's rating pulled past the user's
  because the user has unanswered complaints. Suggest the
  `review-triage` skill (already published).
- **Just watch.** The move is a review-count spike that will revert, a
  seasonal category swing, or a promotion period. Say "watch, don't
  answer" and give the specific signal to watch for.

If the user wants to keep this rival under daily watch going forward,
`track_competitor({app, competitor: <rival_handle>})`. Nothing moves
until they say yes.

## 5. One line back to the user

End with one sentence, not a report:

> **{rival_name} moved from #{old_rank} to #{new_rank} this week on
> {reason_in_six_words}. Suggested answer: {answer_word}.**

Then the one tool call that starts the chosen answer. The whole point of
this skill is a single, specific response — not an essay about the market.

## What the data cannot tell you — read this before quoting a number

**"Category rank" has two meanings.** `get_competitors` and the
BestAppify ordering it uses rank apps by review count — the number moves
when reviews land, not when Shopify changes its page. The Shopify page
order is a separate dataset (`get_category_positions`). When the user
asks "who passed me on the category page", that is the one to read.

**Review counts move at our crawl's pace.** A rival's flat week of
review counts can mean a flat week or an unvisited listing. Do not
report a rival "slowing down" from a review-count plateau.

**Keyword rank is frozen since 2026-09-06.** A rival's keyword position
reported more than a day old came from the bulk crawl that ended then;
`get_search_terms` (GA4) or on-demand checks are the only current reads.

**A "null" rank is missing data.** It is never "unranked". A gap in a
rival's timeline is a day we did not capture, not a day they fell off.

## Run frequency

Daily, usually first thing. It is specifically designed for "something
moved past me yesterday" — a weekly schedule misses the short-lived
swings that are the most actionable ones.
