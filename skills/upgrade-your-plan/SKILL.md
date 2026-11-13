---
name: upgrade-your-plan
description: >-
  Upgrade the user's BestAppify plan from inside the same MCP conversation. Use
  when the user asks to upgrade, mentions their plan, says they want more apps
  or more MCP calls, or when a tool refuses something and says Growth or Pro is
  needed. Looks up the current plan and usage, mints a short-lived signed
  checkout link, and reads the link back for the user to open in their browser.
  Does NOT charge anyone from here — Stripe collects payment on the user's own
  device. Requires the BestAppify MCP server (bestappify.com/mcp), which is
  where every tool named here comes from.
---

# Upgrade when you actually need it

Most agents cannot sell. When a BestAppify tool says "this needs Growth" or
"this needs Pro", the usual answer is to tell the user to go to a website, sign
in, find the pricing page and pick a plan — which is a lot of steps in the
middle of the work they asked for. This skill does it from inside the same
conversation.

Two tools matter:

- `get_subscription_status` — the current plan, status, and this month's usage
  against the limits that bite (apps tracked, API calls today, MCP calls this
  month). It also carries an upgrade_available flag and the exact next tool
  call to run.
- `start_upgrade` — mints a signed checkout URL for the account on the API
  key, one plan at a time. The link lives for 30 minutes.

## When to use it

Use it when the user says any of:

- "upgrade my plan", "put me on Growth", "go to Pro", "take my money"
- "I want to track more apps / more keywords"
- "I keep hitting the limit", "why can't I use X"

Use it ALSO when another tool returns an error that names a plan — e.g.
`get_revenue_and_churn` refusing on free with "the Growth plan includes this",
or `create_webhook` refusing with "webhooks are Pro". Do not go silent on that
refusal; read the user the next step.

Do NOT use it if the user is only asking what plans exist, or to compare
plans — there is a pricing page for that (`https://bestappify.com/pricing`)
and reading a sales pitch is not what this skill is for.

## The flow

### 1. Check where they are

Start with `get_subscription_status`. It costs one MCP call and tells you both
whether an upgrade is possible and which one to run.

```
get_subscription_status()
```

Look at the returned fields:
- plan — "free", "growth" or "pro"
- needs_payment_fix — true means a paid plan has a card issue; send the user
  to https://bestappify.com/dashboard/billing instead of calling
  `start_upgrade`
- usage — tell the user which number is actually pushing the upgrade
  decision. "You're at 92/100 MCP calls today" is a better reason than "Growth
  is better".
- upgrade_next_tool_call — the exact next line to run.

If upgrade_available is false (they are on Pro), say so and stop; there is no
plan above Pro.

### 2. Start the upgrade

Run `start_upgrade` with the plan from the step above. One of:

```
start_upgrade({plan: "growth"})
start_upgrade({plan: "pro"})
```

The tool returns `{plan, price_usd, checkout_url, expires_at, message}`.

### 3. Hand the link back verbatim

The link is specific to this account and expires in 30 minutes. Read it to the
user and ask them to open it on their own device, where their BestAppify
sign-in and their card details already live. Do not try to open the URL with
WebFetch or any other browser-side tool — payment pages do not work that way
and will leak the link into logs.

A good reply looks like:

> You're on free and you hit today's 100-call cap this afternoon — Growth
> raises that to 1,000 and adds the change feed for the competitors you're
> watching. Here's a checkout link for $29/month, good for 30 minutes:
>
> https://bestappify.com/checkout/mcp?t=...
>
> Open it in your browser; Stripe takes it from there. Tell me once you're
> back and I'll pick up where we left off.

### 4. After they upgrade

The user does not need to tell you anything — their next tool call will see
the new plan and limits. But it is reasonable to ask: "Did it go through?" and
re-run `get_subscription_status` to confirm. If they say the link expired, run
`start_upgrade` again for a fresh one.

## Honest things to tell the user

- Stripe collects the card, not BestAppify. We only see the subscription state
  afterwards.
- The daily / monthly limits shown by `get_subscription_status` are ours, not
  Shopify's — the App Store itself imposes no rate on reading the public
  listings (though the search path has been disallowed for bulk crawling
  since 2026-09-06, which is why keyword re-reads are a separate, slower loop).
- Upgrading changes nothing about the data the model can see. It raises how
  often you can ask, which apps the account may add, and whether webhooks and
  revenue tools are available.

## Things this skill does NOT do

- It does not change the plan silently in the background. The user must click.
- It does not cancel, downgrade, or add seats — all of that lives in
  `https://bestappify.com/dashboard/billing`.
- It does not apply discount codes; the Stripe page has a promo-code field if
  the user has one.
- It does not work when the account is connected through the ChatGPT
  directory — OpenAI's rules forbid selling subscriptions inside the app, so
  `start_upgrade` refuses in that context and the user has to visit the
  pricing page.
