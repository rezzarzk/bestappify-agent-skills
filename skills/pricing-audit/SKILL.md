---
name: pricing-audit
description: >-
  Audit one of my Shopify apps' pricing against its category: whether a free
  plan is normal, where my cheapest paid tier sits in the quartiles, how many
  paid tiers is typical, and how the top rivals' ladders compare line by line.
  Returns the positions and the questions they raise, not a price to charge.
  Use whenever the ask is "audit my pricing", "am I priced right", "what do
  apps in my category charge". Requires the BestAppify MCP server
  (bestappify.com/mcp), which is where every tool named here comes from.
---

# Pricing, read against the market

A merchant comparing bundle apps compares bundle prices, not the whole store.
That is the only comparison worth making, and that is what this skill does:
one of the user's apps against the top of its own category, with their own
tracked rivals folded in.

## Before anything else

`list_my_apps`. Pricing only works for an app on the account, because the
benchmark depends on which category pages Shopify puts it on. If the user
named one and it is not on the account, say so and stop — there is no public
pricing benchmark here.

## 1. The benchmark

`get_pricing_benchmark` on the user's app. One call, and the answer carries
the three positional findings worth returning:

- **Free plan.** Whether the app offers one, and how many apps in the
  category do. The right answer in a category where everyone has a free plan
  is almost never "charge from day one".
- **Entry price quartile.** Where the cheapest paid tier sits against the
  category's quartiles. A Q1 entry price in a Q3 category is a lever the
  user is leaving on the table; a Q4 entry price in a Q1 category is a reason
  conversion is low.
- **Tier count.** How many paid tiers is normal. Fewer than the category
  norm is usually fine; more than two above is noise that costs clicks.

Read the questions in the response rather than inventing one. Each
positional finding comes with the one question an owner should actually
answer before changing the price.

## 2. The rivals, line by line

The benchmark gives positions. For the specifics of the ladders the user is
being compared against, call `get_leaderboard` for the primary category
(limit 5) and then `get_app_details` on each of those five with
`include_intelligence: true`. The response has the pricing plans — plan
name, price, interval, trial, and the feature list. That is what to
tabulate.

Add the user's own tracked rivals on top: `get_competitors` on their app
returns the ones they already watch. Call `get_app_details` on each new one
and append their plans to the same table.

## 3. The comparison

Lay the plans out as a small table, one row per rival (including the user)
and one column per plan tier. Highlight three things:

- Features the top three rivals charge extra for that the user bundles into
  the base tier (a feature given away free is money left on the table).
- Features the user charges extra for that most rivals bundle (a reason the
  base plan might feel thin to a merchant comparing cards).
- Trial length — a shorter trial than the category's median is a conversion
  tax the user is probably paying unknowingly.

## 4. What the data cannot tell you — read before quoting a number

**Nothing here links a price to the user's revenue.** The benchmark reports
where the price sits in the market, not whether the price is working. If the
user asks "should I raise prices", the honest answer is "I can show you that
the market would allow it; I cannot tell you whether your merchants will
tolerate it".

**A free plan is not always the growth lever it looks like.** In categories
where free plans dominate, the free plan is table stakes rather than a lever;
dropping it will hurt installs with no revenue upside until well after.

**Pricing changes on rivals show up on our crawl's clock.** The plans on a
rival are their last-read ladder, not necessarily today's — tracked rivals
are re-read nightly, the rest of the catalog less often. Say "as of our
last read" with the date when quoting a rival's plan.

**"Category rank" in the leaderboard is review order, not Shopify's.** It
reports the apps BestAppify holds in that category by review count. The apps
a merchant actually scrolls past on Shopify's category page may be a slightly
different set — the leaderboard is a good proxy for who is big here, not a
reproduction of Shopify's ordering.

## Finishing

Return the three positional findings as plain sentences, the plans table
right under, and one closing line: the single change the user could make
this quarter that the market would allow. If none of the findings supports a
change, say so and recommend watching instead of acting.
