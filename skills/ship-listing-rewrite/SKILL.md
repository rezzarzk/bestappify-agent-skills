---
name: ship-listing-rewrite
description: >-
  Rewrite one of the user's Shopify App Store listings against the terms that
  actually earned installs, from the app's own Google Analytics — not a guess
  from the crawl. Use when the user says "rewrite my listing", "my copy is
  tired", "rework my tagline", or when their installs have stopped growing and
  nothing has changed on the listing in weeks. Works only on an app connected
  to BestAppify with a GA4 property linked. Requires the BestAppify MCP server
  (bestappify.com/mcp), which is where every tool named here comes from.
---

# Rewrite the listing from the data, not the gut

Most "rewrite my listing" prompts end with a model inventing copy that reads
fine and matches nothing the user's merchants typed. This skill chains three
MCP tools so the new draft is anchored to real GA4 traffic and then read back
critically before the user sees it.

## Scope / requires

- MCP server connected (every plan, free included)
- An app on the user's account — `list_my_apps` must return at least one
- A GA4 property linked on that app — `get_listing_analytics` returns
  `connected=true`. Without it this skill falls back to the crawl and the draft
  gets a much softer evidence section.
- `propose_listing_rewrite` is on the paid plans (Growth / Pro); on free the
  skill still produces the term shortlist and stops there.

## Before anything else

Call `list_my_apps`. Pick the app the user named, or ask them to pick if the
account has more than one. Never guess a handle from the App Store — tracked
apps are the only ones the write tools will act on.

## 1. Read the traffic

`get_listing_analytics({app, days: 90})` — listing views, install clicks,
install conversion.

`get_search_terms({app, days: 90})` — the terms real merchants typed to find
this app, with the position the merchant actually saw and whether they clicked
Install. This is first-hand GA4 evidence; sponsored slots are already removed
so positions match organic rank.

If `get_listing_analytics` returns `connected=false`, stop and tell the user
to link the app's GA4 property in `/dashboard/integrations/google`. Rewriting
copy without that is guessing — do not pretend otherwise.

## 2. Pick the terms that earned installs

From `get_search_terms`, keep the rows where `installs > 0` or
the install-rate column is in the top quartile of the response. Those are the terms
merchants used AND converted on; the draft should sound like those merchants'
problem, not like the category.

Cross-check against `get_keyword_rankings({app})` so the draft only targets
terms the user is already placing for or very close to. Chasing a term the app
is #42 for is a different piece of work.

## 3. Draft, then review, then show the user

- `propose_listing_rewrite({app, focus_terms: [...]})` — the tagline and
  description are rewritten against your shortlist. Costs one AI review from
  the day's cap.
- `review_listing_draft({app, tagline, description})` — reads the draft back
  critically. Run it before the user sees the copy. If it flags a problem,
  rewrite that section yourself and re-review.

Show the user the version that survived the review, not the raw rewrite.
Drafting is free; publishing their listing is theirs — ask before writing
anything that will replace what Shopify shows today.

## 4. Put the chosen terms under watch

`track_keyword({app, keyword: "..."})` for each term the user kept. A
rewrite nobody is measuring is a guess with a timestamp; with the terms
tracked, next week's `growth_briefing` will say whether the rewrite paid off.

## What the data cannot tell you — read this before quoting a number

**Install clicks are not installs.** Shopify's Install button is a click;
the merchant then signs in and approves scopes on their own domain, and some
drop out there. `get_listing_analytics.installs` is the signed-in event, and
it is lower than clicks by a healthy margin. Do not quote clicks as installs.

**GA4 positions are not the crawl's.** `get_search_terms` reads the position
the merchant actually saw at the moment they searched. It is more accurate
than the crawl for that merchant, and worse as a snapshot of the category —
one merchant's experience is not everybody's.

**A null rank means missing data, not unranked.** If the search-terms row
carries `position=null`, do not write the copy around winning a term we did
not capture a position for.

**Keyword positions stopped on 2026-09-06.** Shopify disallowed crawling its
search page; stored positions end there. Rely on GA4 for current positions
wherever this skill makes a copy decision.

## Finishing

End with three lines, in order:

1. The rewrite — tagline, first paragraph, and the one-sentence pitch the
   reviewer did not flag.
2. The keywords now under watch (as `track_keyword` returned them).
3. "Open your App Store listing editor in a new tab — BestAppify cannot
   publish for you." Shopify requires the merchant signs in to change the
   listing; everything above was a draft they paste.
