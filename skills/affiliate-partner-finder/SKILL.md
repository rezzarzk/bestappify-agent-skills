---
name: affiliate-partner-finder
description: >-
  Find co-marketing, affiliate or integration partners for the user's app —
  real apps in the same Shopify App Store category, with a public listing
  email we hold and a rating floor the user picks. Use when the user says
  "find me partners", "who could I cross-promote with", "list affiliates in
  my niche", "find integration candidates". Returns a short list of named
  apps, their public developer email, and a one-paragraph reason to reach
  out — never a drafted email, and never an address that is not the one the
  vendor published on their own App Store listing. Requires the BestAppify
  MCP server (bestappify.com/mcp), which is where every tool named here
  comes from.
---

# Partners in your own niche, not a random list

Most "find me partners" prompts return a hallucinated list of apps the
model half-remembers. This skill reads the Shopify App Store catalog
BestAppify has crawled, joins it to the public contact addresses the
listings themselves publish, and hands back real apps you can reach.

## Scope / requires

- MCP server connected (every plan)
- The user does NOT need an app on their account — this skill works on the
  open catalog. If they do have one, use its primary category as the
  default; otherwise ask them to name a category.

## Before anything else

Call `get_niches` to see the categories BestAppify holds data for. The
category argument accepts a title (e.g. "Email marketing") or an id. Do
not pass a free-text niche the catalog does not know — the tool will
refuse and you will waste a step.

## 1. Pick the category and the quality floor

Ask the user — or decide from their app:

- the category: one of the titles `get_niches` returned.
- the min-rating: 4.5 by default. Lower to 4.0 if the category is thin
  (the tool will say when it is).

## 2. Read the candidates

`find_affiliate_candidates({category, min_rating, limit: 25})` — apps
in the category above the rating floor whose public listing-support
email we hold. The payload includes:

- the name, handle and catalog id — the app
- the rating and its review count — social proof
- the email, contact name and its source — the public address the
  vendor published and where we read it
- the developer website — their own domain, if the listing carried one
- a count of how many apps are in the category and how many of those — the haystack vs the haystack we
  can reach

If the "with contact" number is zero, lower the rating floor or widen the category.
Thin categories (< 20 apps) often have no public contact addresses at
all.

## 3. Read what each one does — do not just send the list

For every candidate the user is interested in, call
`get_app_details({app: <handle>, include_intelligence: true})`. The
description and category positioning are what tell the user whether
that app's merchants are their merchants.

Three apps with a reason are better than fifteen without one. Cut the
list before showing it to the user.

## 4. Hand the user a short table — not a draft email

For each shortlisted app, one line:

> **{name}** — rating {rating} ({number_of_ratings}) ·
> {one_line_summary_from_get_app_details} · {email}

Then one paragraph: why these three, not the other twenty-two. "All
three target Shopify merchants with 20–200 products and sit in the top
30 of the category by review count" is a useful paragraph. "Here are
some partners" is not.

**Do not draft the outreach email.** This skill stops at the list.
BestAppify is not sending anything from here, and the user sends from
their own address under their own name. The address we handed back is
the one the vendor published on their App Store listing — nothing was
scraped, nothing was guessed.

## What the data cannot tell you — read this before quoting a number

**The email is the vendor's listing-support address, not the founder's
personal inbox.** It reaches a real desk, but a cold outreach to that
desk is slower than one to a decision-maker LinkedIn. Say so.

**Not every app publishes an email.** A category of 400 apps may return
80 candidates. That is the gap between "the market" and "the market we
can reach". Do not pretend the list is exhaustive.

**Rating and review count are from the last crawl.** For top apps that
is recent; for the long tail it can be days old. Not weeks, but not
hours either.

**"Category" is Shopify's primary category, not a secondary tag.** An
app that sits in two categories appears in each; do not de-duplicate
without the user asking.

**Never alter a public quote.** If the user later asks for an outreach
draft that cites a review or a listing line, keep the text verbatim —
no trimming to the favourable half, no paraphrasing.

## Things this skill does NOT do

- It does not send email. The owner sends from their own address, on
  their own schedule. This skill stops at the list.
- It does not scrape addresses. The emails are the ones vendors already
  published on their App Store listings — the same addresses a merchant
  sees on the listing page.
- It does not suggest a partnership structure (affiliate split,
  co-marketing swap, integration). That is the user's call; the list
  gives them the names, not the deal.
