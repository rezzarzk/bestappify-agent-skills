---
name: review-triage
description: >-
  Read a Shopify app's recent reviews, cluster them into the two or three
  themes merchants are actually raising, and draft a reply for each cluster
  that acknowledges the complaint in the user's voice. Use whenever the ask is
  "reply to my reviews", "triage my latest reviews", "what are my reviewers
  complaining about this week". Requires the BestAppify MCP server
  (bestappify.com/mcp), which is where every tool named here comes from.
---

# Reviews, read as a batch

An app owner opening the review tab sees twenty one-off replies to write. Most
of those twenty are the same two or three complaints in different voices. The
useful move is to find the clusters first and draft one reply per cluster, so
the owner edits two or three and personalises them, instead of writing twenty.

## Before anything else

Call `list_my_apps`. Reviews only make sense for an app the user owns. If the
user named one and it is not on the account, say so and stop — there is no
public reviews tool here, and we will not fabricate one from the public
rating.

If there are several apps on the account and the user did not name one, pick
the one they named most recently in this conversation and say which you
picked.

## 1. Read the themes

`get_review_intelligence` with `recent: 30`. The response has two parts you
need:

- The AI theme summary: what reviewers love, complain about and request.
  Those three buckets are the clusters you will reply to.
- The recent list — the actual review bodies. Keep them, because every
  draft needs a verbatim quote behind it to be worth sending.

For a one-star-only triage, filter the recent list to those stars locally;
there is no star filter on the call itself.

## 2. Get the market context

`get_rival_complaints` on the same app. This returns the themes behind one
and two star reviews across the user's category — not for this app, for the
market. Two reasons to pull it:

- If a complaint on the user's app is already a market-wide theme, the reply
  is "we hear you, here is why we are different", not "this is unusual".
- The flag on each market theme for whether the user's own listing mentions
  it — a complaint about something the listing never claimed may deserve a
  listing fix, not a reply.

## 3. Draft the replies

There is no reply drafter in the toolbelt. Write the replies yourself from the
clusters, following these rules:

- One reply per cluster, not per review. Three replies total is a good target.
- Start with the complaint named plainly ("You said the import stalls on CSVs
  over 10,000 rows"). Vagueness is read as dodging.
- One concrete sentence about what will change or why it will not. If the
  answer is "won't fix", say that and why — merchants prefer a clear no.
- No apology-and-nothing-else replies. Those make the pile look larger, not
  smaller.
- Mark each draft with the review ids it covers, so the owner can see which
  review they are personalising it onto.

Separately, if a theme points at wording the listing could do better, call
`propose_listing_rewrite` — the taglines and bullets it drafts will mention
the complaint theme they answer, and `review_listing_draft` will tell the
user whether the draft is safe before publishing.

## 4. Measure something, not nothing

If the user adopted a reply cluster, call `track_keyword` for the theme word
or two that keeps appearing in the complaints (e.g. "slow import"). It won't
report on the complaint directly, but it will put the demand term under daily
watch, so the next read includes a position for it. A recommendation nobody
is measuring is a guess with a timestamp.

## What the data cannot tell you

**A quiet month is not necessarily a flat month.** Review counts here move at
the pace of our crawl, not at the pace of merchants typing. If
`get_review_intelligence` shows a monthly trend dropping to zero in the most
recent week, that is almost always because the week has not been fully
crawled yet. Report trends over the full month, not the last few days.

**The theme summary is an AI reading of the reviews.** It is calibrated to be
useful, not perfect. Before quoting a theme in a reply, open the review bodies
it points at and make sure a reviewer actually said it.

**Keyword positions stopped on 2026-09-06**, so a theme like "slow import"
tracked today will not have historical positions. Say "watching from today"
when telling the owner what will happen next.

## Finishing

Return a small table: cluster, how many reviews, one verbatim quote, the
draft. Then one closing line: which cluster, if any, is a listing problem
rather than a reply problem.
