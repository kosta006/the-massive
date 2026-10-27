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
  version: "0.1.0"
---

# Find Leads with The Massive

The Massive is a **paid** business search. One request runs a Google Ads search for a single keyword across 1–100 locations in one country and returns matching businesses with website, email address, phone number and physical address. Results arrive by email: first a Stripe payment link when the search finishes, then a CSV download link once payment clears.

Follow these steps in order. Never skip the sign-in check or the confirmation step.

## 1. Confirm the user is signed in

Call the Massive `get-account` tool before asking for any search details.

- If it succeeds, note the connected name and email address. Greet the user by name briefly.
- If it fails, returns 401/Unauthorized, or reports no connected account: stop. Ask the user to connect The Massive from their connector settings and finish the browser sign-in (email/password, or Continue with Google, GitHub, Apple, Facebook or Microsoft). Do not collect search details first.
- Never ask for a password or for the user to pick a sign-in provider in chat. Sign-in only happens on The Massive's browser login page.

## 2. Collect the four search details

Ask only for what the user's message has not already given. Group missing items into a single question where possible.

| Field | Rules |
|---|---|
| `keyword` | Exactly **one** keyword or phrase, e.g. "plumber", "emergency electrician". If the user gives several, explain each keyword is a separate paid search and ask which one to run first. |
| `country` | One country, as a full name ("Australia") or 2-letter ISO code ("AU"). |
| `locations` | 1–100 specific places within that country. Must be specific enough to verify, e.g. "Sydney, New South Wales, Australia" rather than "Sydney". If the user describes a region ("all of South East Queensland", "every capital city") or gives more than a handful of loose place names, use the **plan-locations** skill to build and confirm the list. |
| `email` | Where the payment and download links go. Offer the connected account's email as the default and let the user choose another. Never guess or invent an address. |

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

You'll get a Stripe payment link at that email when the search finishes,
then the CSV download link after payment. Shall I submit it?
```

List every location; do not truncate. Then stop and wait. Only an explicit yes from the user ("yes", "go ahead", "submit it") counts. If the user changes anything, show the updated summary and ask again.

## 4. Submit

Call `create-search-request` with exactly the confirmed `keyword`, `country`, `locations` and `email`.

On success, tell the user:
- The **request ID** (they will need it if the payment link expires).
- What happens next: payment link by email when the search completes, CSV link by email after payment.
- That they can ask for a fresh payment link at any time with **/retry-payment** and the request ID.

## Handling errors

- **Unpaid search already exists for this email:** Only one unpaid search is allowed per email address. Explain that the earlier search must be paid before a new one can start. Offer to fetch a fresh payment link for it with the **retry-payment** skill (ask for the request ID if it is not known). Do not resubmit with a different email just to get around the limit unless the user asks for that themselves.
- **Location not recognised:** Ask the user to make the location more specific (add state/region and country) and show the corrected summary before resubmitting.
- **401 / Unauthorized mid-flow:** Ask the user to reconnect The Massive from their connector settings, then call `get-account` again before continuing.
- **Any other error:** Report the error message plainly and do not retry automatically, since each request is a paid search.

## Never

- Submit without the explicit confirmation in step 3.
- Invent locations, email addresses, or confirmation.
- Combine several keywords into one request.
- Ask for passwords or payment card details. Payment only happens on Stripe's page.
