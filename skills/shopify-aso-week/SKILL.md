---
name: shopify-aso-week
description: >-
  Run a week of Shopify App Store optimization for an app you have connected to BestAppify: work out
  what moved and why, decide what is worth changing, and then actually change it — listing copy,
  screenshots, and the keywords being tracked. Use this whenever the task is "how is my app doing",
  "what should I fix on my listing", "why did I drop", or a weekly/monthly ASO check. Requires the
  BestAppify MCP server (bestappify.com/mcp), which is where every tool named here comes from.
---

# A week of ASO, end to end

Most rank tools stop at a table. This one ends with the listing changed, because
the BestAppify MCP server has tools that write as well as read. Do not stop at
the report unless the user asked for a report.

## Before anything else

Call `list_my_apps`. Every other tool takes an app handle from it. If it returns
nothing, the user has not added an app yet — say so and stop rather than
guessing a handle from the App Store.

## 1. What happened

`growth_briefing` for the app, then `whats_changed`.

Read them together. `growth_briefing` is the state; `whats_changed` is the
delta, including what competitors did. A move in the briefing with nothing in
the changes usually means the market moved, not the app.

## 2. Why it happened

For any rank move worth explaining, `explain_rank_move`. It separates a move
that is yours from one the whole category felt — which is the difference between
"fix your listing" and "wait".

`get_review_intelligence` if the rating or review pace shifted. `get_rival_complaints`
when the question is what to build rather than what to write.

## 3. What to change

`get_keyword_opportunities` for terms worth chasing, and `get_search_opportunities`
if the account has Google Analytics connected — that one knows what real
merchants searched, and it is strictly better evidence than the crawl when it
exists.

`audit_my_listing` scores the listing itself. Treat its output as a list of
candidates, not a work order: a listing can score badly on a dimension that does
not matter for its category.

## 4. Actually change it

This is the part other tools cannot do.

- `propose_listing_rewrite` drafts the tagline and description against the
  keywords you picked. `review_listing_draft` then reads the draft back
  critically — run it before showing the user, and show them the version that
  survived.
- `get_screenshot_headlines` reads the words on rivals' screenshots;
  `review_screenshot_headlines` judges the user's own against that pattern;
  `render_screenshots` produces new images.
- `track_keyword` puts the terms you chose under daily watch. Do this — a
  recommendation nobody is measuring is a guess with a timestamp.

Ask before writing anything the user will publish. Drafting is free; publishing
their listing is theirs.

## What the data cannot tell you — read this before quoting a number

**A null rank means "not captured that day", never "unranked."** The gap is
honest missing data. Do not report it as a fall, and do not fill it in.

**Search-keyword rank is frozen.** Shopify disallowed crawling its search page
on 2026-09-06 (`robots.txt`, `*q=*`). Stored keyword positions stop there.
Current positions come from on-demand checks, and from Google Analytics when the
user has connected it. If you are about to say "you rank #N for X", make sure
you know which of those it came from and how old it is.

**"Category rank" has two meanings and only one is Shopify's.** The rank inside
`get_app_intelligence` orders the apps BestAppify holds in a category by review
count — it moves when reviews move. `get_category_positions` is the position on
Shopify's own category page, which is where a merchant actually sees the app.
Use the second one when the user asks where they rank. The first jumped an app
from 67 to 35 on the day a batch of reviews landed; that was not a ranking event.

**Review counts move at our crawl's pace, not the market's.** A flat week of
review counts can mean a flat week or an unvisited app.

**Rank history is not comparable across long gaps.** Which keywords were being
captured changed over time, so a set of terms from three months ago is a
different set, not the same set moved.

## Finishing

End with three actions, most important first, each naming the tool you would run
next. A week's review that produces no next action has not been run.
