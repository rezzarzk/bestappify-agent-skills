---
name: keyword-gap
description: >-
  Find the Shopify App Store search terms where rivals in my category place in
  the top 10 and my app does not — the keyword gap worth closing. Returns a
  short list of terms with the rival that leads, the user's current position,
  and whether the term is in the user's own vocabulary. Use whenever the ask is
  "where are my keyword gaps", "what should I target", "which keywords am I
  missing". Requires the BestAppify MCP server (bestappify.com/mcp), which is
  where every tool named here comes from.
---

# The gap between what my market searches and where I show up

Every category has terms where two or three rivals are quietly collecting
installs and the user's app is not on the page at all. Those are the terms
to chase — not the biggest terms, not the easiest terms, the terms the user
is losing even though their listing is in the same market.

## Before anything else

`list_my_apps`. The gap only makes sense for an app on the account: it is
the gap between where the user ranks and where their category's rivals rank.
If the user named one and it is not on the account, say so and stop.

## 1. The gap, from the one tool that knows it

`get_keyword_opportunities` on the user's app. This is the primary tool for
this skill, and it already does the comparison you would otherwise have to
build by hand:

- It looks only at apps in the user's primary category.
- It keeps only terms where at least two closest rivals placed in the top
  10 of our search reads.
- It drops the user's own app's top 20 placements — those are not gaps.
- It keeps only terms where at least 30% of the top 10 are category apps,
  so a term about a different market does not slip in.
- It removes other apps' brand names, which the user cannot legally use.
- It filters to the user's own vocabulary — terms the user's listing could
  plausibly answer.

Each row carries a measured-on date and a stale flag. Read these — most
reads are from the bulk crawl that ended 2026-09-05, so a stale row is the
norm rather than the exception, and you must say so when returning the
list.

## 2. Where the user actually stands on each gap

For every opportunity, `get_keyword_rankings` on the user's app has their
current and prior positions for terms they already track. Join these in: a
gap where the user ranks #35 is different from a gap where the user is not
on the page at all. If the opportunity term is not in `get_keyword_rankings`
yet, say "not yet tracked" — do not report a null position as #0.

## 3. Where real merchants searched

`get_search_opportunities` on the same app. This one is quieter but better:
when the account has Google Analytics connected, it knows what real
merchants typed into Shopify's search before clicking through, and that is
strictly better evidence than any crawl. If the connected flag is false,
skip this step and say so — the gap list from step 1 is still useful, just
less sharp without GA.

## 4. Pick, do not list

Rank the gaps by three things, in order:

- Whether a strong rival sits in the top ten, because a term with no rival
  on it may be a term the market does not search.
- Whether the user's listing could plausibly rank — a term about a feature
  the app does not have is not a gap, it is a mismatch.
- The term's demand score, not its difficulty: the easiest term is also
  usually the term nobody searches.

Return the top five. Not twenty. The point of this skill is to leave the
user with a short, actionable list.

## 5. Measure what you proposed

For each of the five, `track_keyword` puts it under daily watch so the next
check-in reports a real position. Ask first — tracked keyword slots are a
limited resource on the user's plan. Do not call `track_keyword` for a term
the user has not explicitly agreed to add.

## What the data cannot tell you

**Shopify disallowed crawling its search results on 2026-09-06.** Positions
inside `get_keyword_opportunities` come from the bulk crawl before that
date for untracked terms; tracked ones get re-read. If you quote a position
older than that, say when it was captured. A number without a date reads
like today.

**A demand score of 100 is not a merchant count.** Demand and difficulty
are relative 0-100 indices built from the review volume of each term's
top 10, not search volume — Shopify publishes search volume nowhere.
Treat demand as "how big is the market around this term", not "how many
merchants will see me".

**A gap closed is not an install won.** Ranking for a term and selling to
the merchants on it are different problems; the second needs listing
copy that answers the search intent, not only a top-10 position. If the
user is already running `shopify-aso-week`, point them at
`propose_listing_rewrite` next.

## Finishing

Return the five terms as a small list — term, who leads today, user's
current position (or "not tracked"), measured_on — and one closing line
about which one to work on first and why. One term, not three.
