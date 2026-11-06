---
name: find-leads
description: >
  This skill should be used when the user wants to find business leads or
  contact details with The Massive, for example "find leads", "find me
  plumbers in Brisbane", "get a list of electricians with emails",
  "run a Massive search", "search Google Ads for businesses", "build a
  prospect list", or invokes /find-leads. It runs the guided, paid business
  search: check sign-in, collect keyword, country, locations and email,
  confirm a full summary, then submit.
metadata:
  version: "0.2.0"
---

# Find Leads with The Massive

The Massive is a **paid** business search. One request runs a Google Ads search for a single keyword across 1–100 locations in one country and returns matching businesses with website, email address, phone number and physical address, where available. Results arrive by email as a CSV. A search cannot be cancelled once it is created.

How payment works depends on the account:
- **Saved card** (added in Settings → Billing on themassive.com.au): the card is charged automatically when the search completes, and the CSV download link is emailed.
- **No saved card:** a Stripe payment link is emailed when the search completes; the CSV download link is emailed after payment.
- If an automatic charge fails, a Stripe payment link is emailed instead.

Follow these steps in order. Never skip the sign-in check or the confirmation step.

## 1. Confirm the user is signed in

Call the Massive `get-account` tool before asking for any search details.

- If it succeeds, note the connected name, email address, and **whether a card is saved**. Greet the user by name briefly.
- If it fails, returns 401/Unauthorized, or reports no connected account: stop. Ask the user to connect The Massive from their connector settings and finish the browser sign-in (email/password, or Continue with Google, GitHub, Apple, Facebook or Microsoft). Do not collect search details first.
- Never ask for a password, card details, or for the user to pick a sign-in provider in chat. Sign-in only happens on The Massive's browser login page.

## 2. Collect the four search details

Ask only for what the user's message has not already given. Group missing items into a single question where possible.

| Field | Rules |
|---|---|
| `keyword` | Exactly **one** keyword or phrase, e.g. "plumber", "emergency electrician". If the user gives several, explain each keyword is a separate paid search and ask which one to run first. |
| `country` | One country, as a full name ("Australia") or 2-letter ISO code ("AU"). |
| `locations` | 1–100 specific places within that country. Must be specific enough to verify, e.g. "Sydney, New South Wales, Australia" rather than "Sydney". If the user describes a region ("all of South East Queensland", "every capital city") or gives more than a handful of loose place names, use the **plan-locations** skill to build and confirm the list. |
| `email` | Where the results (and any payment link) go. Offer the connected account's email as the default and let the user choose another. Never guess or invent an address. |

Tips to share only if helpful:
- A broader keyword ("plumber") usually returns more businesses than a narrow one ("plumber hot water repairs").
- More locations generally means more results, up to the 100-location cap.

## 3. Show a full summary and wait for explicit confirmation

Present everything that will be submitted, in this shape:

```
Ready to run this paid search:

Keyword:   <keyword>
Country:   <country>
Locations: <count> locations
  1. <location>
  2. <location>
  ...
Email:     <email>

<payment line>
This starts a paid search that can't be cancelled. Shall I submit it?
```

Use the payment line that matches the account from step 1:
- Saved card: "Your saved card will be charged automatically when the search finishes, and the CSV download link will be emailed to that address."
- No saved card: "You'll get a Stripe payment link at that email when the search finishes, then the CSV download link after payment."

List every location; do not truncate. Then stop and wait. Only an explicit yes from the user ("yes", "go ahead", "submit it") counts. If the user changes anything, show the updated summary and ask again.

## 4. Submit

Call `create-search-request` with exactly the confirmed `keyword`, `country`, `locations` and `email`.

On success, tell the user:
- The **request ID**.
- What happens next, matching their account:
  - Saved card: the card is charged when the search finishes and the CSV link is emailed. Do not tell them to wait for a payment email or pay an invoice.
  - No saved card: a payment link is emailed when the search finishes, then the CSV link after payment. Mention that if the payment link expires, **/retry-payment** with the request ID renews it.

## Handling errors

- **Unpaid search already exists for this email:** Only one unpaid search is allowed per email address. Explain that the earlier search must be paid before a new one can start. Offer to renew its payment link with the **retry-payment** skill (ask for the request ID if it is not known). Do not resubmit with a different email just to get around the limit unless the user asks for that themselves.
- **Location not recognised:** Ask the user to make the location more specific (add state/region and country) and show the corrected summary before resubmitting.
- **401 / Unauthorized mid-flow:** Ask the user to reconnect The Massive from their connector settings, then call `get-account` again before continuing.
- **Any other error:** Report the error message plainly and do not retry automatically, since each request is a paid search.

## Never

- Submit without the explicit confirmation in step 3.
- Invent locations, email addresses, or confirmation.
- Combine several keywords into one request.
- Ask for passwords or payment card details. Cards are only added on The Massive's site or Stripe's page.
