---
name: rival-teardown
description: >-
  Teardown of one Shopify App Store rival the user names: what it is, where it
  sits in its category, where its price ladder lands, what has moved on its
  listing lately, and what its own market complains about — enough to decide
  how to answer it. Works on any public app, not only ones on the account. Use
  when the ask is "break down <app>", "what is <competitor> doing", "size up
  this rival". Requires the BestAppify MCP server (bestappify.com/mcp), which
  is where every tool named here comes from.
---

# One rival, taken apart

A competitor-watch sweep reads the whole watch list. This one goes deep on a
single app a user has named — the one they are nervous about this week.

## Before anything else

The user will name the rival by a handle, a listing URL, or a product name.
Pass it straight through: `get_app_details` accepts all three. If no app
matches, call `search_apps` with part of the name and show them the three
closest hits rather than guessing.

The user does NOT have to own the rival — none of the public tools here need
it to be on their account.

## 1. The profile

`get_app_details` with `include_intelligence: true`. One call gives you:

- Rating, review count, description, categories, launch date, whether the
  listing is still live.
- Under intelligence: rank trajectory, where it sits on its category pages,
  and the pricing benchmark — where its cheapest paid tier lands in that
  category's quartiles.

Read the pricing benchmark carefully. It compares the rival's ladder against
the top of each of its category pages in Shopify's own order, plus any
tracked peers — not the whole store, because a merchant comparing bundle
apps compares bundle prices.

## 2. What they have changed lately

`get_listing_changes` with `days: 90`. The result mixes the user's own apps
and the competitors they track; filter it to this rival (is_mine false and
the matching handle). You are looking for the two things that signal a rival
working on growth: a tagline/description rewrite, and a pricing move.

If the rival is NOT in the result at all, the user is not tracking it yet.
`track_competitor` adds it under one of their apps so the next sweep sees it.
Do not run `track_competitor` without asking — tracking slots are a limited
resource on their plan.

## 3. What this rival's market complains about

If the user owns an app in the same primary category, call
`get_rival_complaints` with their own app. The response is the themes behind
one and two star reviews across that category — the complaints merchants are
already shouting into the void, with verbatim quotes. That is where "what to
build to answer this rival" comes from, in the words merchants use.

If the user has no app in the category, say so and skip this step rather than
calling `get_rival_complaints` with an unrelated app — the themes will be
about a different market.

## 4. Head to head, if they have an app of their own

For a user who already competes with this rival, finish with `compare_apps`
(the user's handle as app_a, the rival as app_b). It puts the two profiles
side by side, including descriptions, so a tagline the rival sharpened is
easy to spot against the user's own.

## What the data cannot tell you

**A rival's review count rising is not evidence they are growing.** Review
counts here move at the pace of our crawl of that app, not at the pace of the
market. Compare over weeks, never over days.

**Keyword positions stopped on 2026-09-06.** Shopify disallowed crawling its
search results then, so stored search ranks for the rival end there. The
rank trajectory inside `get_app_details` with intelligence is their
category-page position (review order), which is a different reading and
still moves.

**Listing history starts when we first saw the app.** A field with no recorded
change may well have changed before we were watching. Say "no change on record
since our first read on <date>", never "unchanged since launch".

**The pricing benchmark reports the position, not the verdict.** It tells
you whether the price is high, median or low for the category. Nothing in
it tells you whether that price works for that app, because nothing here
links a price to its revenue.

## Finishing

Return four short sections — Profile, Recent moves, What their market
complains about, One line of advice — and a blunt last line: should the user
answer this rival this week, or watch them. Most weeks the answer is watch,
and saying so is the useful reply.
