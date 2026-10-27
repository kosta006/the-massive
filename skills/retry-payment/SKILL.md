---
name: retry-payment
description: >
  This skill should be used when the user's Massive payment link has
  expired or gone missing, for example "my payment link expired",
  "I can't find the payment email", "resend the payment link",
  "pay for my search", "I can't start a new search because one is unpaid",
  or invokes /retry-payment. It fetches a fresh Stripe payment link for an
  existing search request.
metadata:
  version: "0.1.0"
---

# Get a Fresh Payment Link

Each Massive search is paid through a Stripe link emailed when the search finishes. Stripe links expire. The `retry-payment` tool creates a new link for an existing request and returns it directly, so the user doesn't need the email.

## Steps

1. **Check sign-in.** Call `get-account`. If it fails or reports no connected account, ask the user to connect The Massive from their connector settings and complete the browser sign-in, then stop.
2. **Get the request ID.** It is a whole number returned when the search was submitted and included in the Massive emails. Use it if it already appears in this conversation; otherwise ask the user for it. Never guess an ID.
3. **Call `retry-payment`** with `request_id`.
4. **Share the link.** Give the user the fresh payment URL as a clickable link and explain that the CSV download link is emailed to the search's email address once payment clears. Also mention that paying clears the one-unpaid-search limit, so they can start their next search afterwards.

## Notes

- Never open, fill in or complete the payment page on the user's behalf, and never ask for card details in chat. The user pays on Stripe's page themselves.
- If the tool reports the request is already paid, tell the user to check the inbox (and spam folder) of the email used for that search for the CSV link.
- If the tool reports the request ID is not found or belongs to another account, ask the user to double-check the ID and that they are signed in with the same account that created the search.
- Report any other error plainly without retrying in a loop.
