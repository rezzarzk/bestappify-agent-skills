---
name: shopify-market-research
description: >-
  Size a corner of the Shopify App Store before building or repositioning: how crowded a category
  is, what the apps in it charge, how good they actually are, and where the gaps sit. Use for "is
  this niche worth entering", "what should I build", "who leads this category", "what do these apps
  charge". Reads the public App Store, so it needs no account of the user's. Requires the BestAppify
  MCP server (bestappify.com/mcp).
---

# Sizing a niche without guessing

Every number here is observed — crawled from the public App Store or counted
from Shopify's own sitemap. None of it is modelled. That is the whole reason to
use it instead of asking a model what it remembers.

## Start with the shape of the market

`find_niches` ranks categories across the whole store as opportunities;
`get_niches` gives every category with its app count, growth and how much of the
category's reviews the leader holds. `get_leaderboard` for who leads a given
category, `get_movers` for who climbed this week.

A category with many apps is not automatically a bad one. Crowded-and-bad — lots
of apps, poor ratings, loud complaints — is the best shape there is.

## Then whether the incumbents are actually good

`get_review_intelligence` across the category, and `get_rival_complaints` on the
leaders. Ratings alone hide too much: a 4.8 built on 50 reviews from 2021 is a
different competitor from a 4.6 collecting reviews weekly.

`get_pricing_benchmark` for what the category charges — the cheapest, the
median, the most expensive, and whether free plans are normal there. Pricing
against a median is the fastest way to find a position nobody occupies.

## Then the specific apps

`search_apps` to find them, `get_app_details` and `get_app_intelligence` for one,
`compare_apps` for several side by side. `get_listing_changes` shows what a
specific app has been working on lately, which tells you where they think their
problem is.

## What these numbers are, exactly

**The catalogue is counted, not estimated.** It is every handle in Shopify's own
`sitemap_apps_en.xml`, recounted daily.

**There is no search volume, and you must not invent one.** Shopify does not
publish it. What exists here are relative demand and difficulty indices,
labelled as indices. If a number would imply "N merchants searched this", it is
not available — say so.

**Review counts reflect our crawl cadence.** For a market study, compare over
weeks and say the window. Daily review deltas measure us, not the market.

**Ratings are a snapshot of the whole history**, so a long-established app moves
slowly by construction. Review *pace* separates a growing app from a coasting
one far better than rating does.

**Category rank has two meanings.** `get_app_intelligence` ranks by review count
within the category; `get_category_positions` is Shopify's own page order. For
"who leads this category" as a merchant would see it, use the second.

**Launch dates are unreliable.** The date we hold is when the app entered our
catalogue, not when it launched, unless the listing itself states otherwise.

## Finishing

Answer the question that was asked — enter or not, build or not, price here or
there — and name the two or three numbers that decided it. A market study that
ends in a table has handed the decision back to the person who asked.
