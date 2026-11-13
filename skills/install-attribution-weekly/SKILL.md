---
name: install-attribution-weekly
description: >-
  Weekly install-attribution read for one of the user's Shopify apps: which
  search terms earned installs, which stores actually installed, and how that
  moved revenue and churn. Pulls from the app's GA4 property AND the Shopify
  Partner API together — the join other dashboards cannot do. Use when the
  user asks "which keyword drove installs", "where are my installs coming
  from", "what's working this week", or wants a weekly attribution report.
  Requires the BestAppify MCP server (bestappify.com/mcp), which is where
  every tool named here comes from.
---

# Weekly install attribution, GA4 + Partner API in one pass

Shopify publishes no install counts on the public listing. The only places
the number lives are the user's own GA4 property (the click into Install)
and the Partner API (the signed-in subscription event). This skill reads
both, in order, and ends with a five-line weekly report — not a dashboard.

## Scope / requires

- MCP server connected (every plan)
- An app on the user's account — `list_my_apps` must return at least one
- A GA4 property linked on that app — `get_listing_analytics` returns
  `connected=true`
- The app linked to Shopify Partners — `get_revenue_and_churn` returns
  `connected=true`. Growth plan or higher; without it the skill still runs
  but drops the revenue section.

## Before anything else

Call `list_my_apps`. Confirm with the user which app the report is for if
the account has more than one.

## 1. The funnel at the top

`get_listing_analytics({app, days: 7})` — listing views, install clicks,
install-click rate for the last seven days. This is the shape of the funnel
before Shopify's install screen.

If `connected=false`, stop and tell the user to link GA4 in
`/dashboard/integrations/google`. There is no honest weekly attribution
without it.

## 2. What terms did the work

`get_search_terms({app, days: 7})` — every search term a merchant typed
to reach the listing in the last seven days, with the position they saw and
whether they clicked Install. Rank by `installs`, descending.

The headline is not the top term by clicks; it is the top term by installs.
"Product bundles" with 400 clicks and 2 installs matters less than
"frequently bought together" with 40 clicks and 11 installs. Say which it
is in the report.

## 3. Which stores installed

`lookup_install_shops({app, days: 7, limit: 100})` — each install
resolved to the Shopify store that made it, with its date, device and
country. The "likely term" column is an inference (Shopify's install event
is server-side and carries no referrer page), so report it as such.

If the response carries a `note` about the GA4 custom dimension shop-url
not being set up, tell the user that is why the list is empty — it is not
"nobody installed", it is "we cannot resolve which store installed". Point
them to the GA4 setup doc; the rest of the report still works.

## 4. What the installs moved

`get_revenue_and_churn({app, months: 2})` — monthly MRR, trials,
installs, uninstalls and net revenue from the Partner API. Compare this
week's install count against the install count reconstructed from
`get_listing_analytics.installs` — they usually agree within a day of lag.
If they diverge badly, say so in the report: the Partner API is the one
Shopify bills on.

If `connected=false`, skip this section and say "Partner API not linked"
in the report. Do not invent revenue numbers from the GA4 funnel.

## 5. Write the report, five lines

Hand the user back exactly five lines, in this order:

1. **Funnel** — `{views} views → {clicks} install clicks ({clickrate}%) →
   {installs} installs (Partner API), week of {date_from}–{date_to}`.
2. **Top term that earned installs** — `"{term}": {installs} installs from
   {clicks} clicks (position {position})`.
3. **Second term that earned installs** — same shape, or "no second term
   above 5 installs this week" if the tail is flat.
4. **Where they came from** — `{n} resolved stores, top country {country}
   ({pct}%)`, or "shop_url dimension not set up — resolution blank".
5. **Revenue effect** — `MRR {this_month} vs {last_month}, churn {rate}%`,
   or "Partner API not linked".

Four actionable next steps are overkill for a weekly read. The user does
not want a to-do list; they want to know what happened.

## What the data cannot tell you — read this before quoting a number

**Install clicks are not installs.** Shopify's Install button is a click;
the signed install event is separate and smaller. Use the Partner API
number in the funnel line, not the click number.

**Likely term is an inference.** `lookup_install_shops.likely_term` is our
best guess from the GA4 session that ended closest to the install event;
Shopify does not tell us. A store with no GA4 session gets no term.

**The Partner API is the one that counts.** When `get_listing_analytics`
and `get_revenue_and_churn` disagree on the install count, the Partner API
wins — it is what Shopify bills Shopify on.

**MRR and churn are reconstructed.** Shopify publishes neither; both are
derived from the subscription charge lifecycle. Treat them as our
reconstruction, not billed figures.

**A term with no position is still a term.** `position=null` means the crawl did
not capture a rank that day. Do not pretend a merchant saw rank zero.

## When to run it

Weekly, same day. For a Monday read, ask for `days: 7` on Monday and the
window will be last Monday → yesterday. For a comparison read, run the
tools twice with `days: 7` and `days: 14` and subtract.
