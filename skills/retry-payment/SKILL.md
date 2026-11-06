---
name: retry-payment
description: >
  This skill should be used when the user's Massive payment link has
  expired or gone missing, for example "my payment link expired",
  "I can't find the payment email", "resend the payment link",
  "pay for my search", "I can't start a new search because one is unpaid",
  or invokes /retry-payment. It renews payment for an existing search
  request: a fresh Stripe link, or an automatic charge to a saved card.
metadata:
  version: "0.2.0"
---

# Renew Payment for a Search

Each Massive search is paid when it completes. Accounts without a saved card get a Stripe payment link by email, and Stripe links expire. The `renew-payment-link` tool renews payment for an existing request:
- **No saved card:** returns a fresh Stripe payment URL directly, so the user doesn't need the email.
- **Saved card:** charges the card **immediately** and emails the CSV download link instead of returning a URL.

## Steps

1. **Check sign-in.** Call `get-account`. If it fails or reports no connected account, ask the user to connect The Massive from their connector settings and complete the browser sign-in, then stop. Note whether the account has a saved card.
2. **Get the request ID.** It is a whole number returned when the search was submitted and included in the Massive emails. Use it if it already appears in this conversation; otherwise ask the user for it. Never guess an ID.
3. **Confirm before calling.** Repeat the request ID back to the user and wait for an explicit yes. If the account has a saved card, say clearly that the card will be charged straight away for this search.
4. **Call `renew-payment-link`** with `request_id`.
5. **Report the outcome.**
   - Payment URL returned: share it as a clickable link and explain that the CSV download link is emailed to the search's email address once payment clears.
   - Saved card charged: confirm the charge went through and that the CSV download link is on its way by email. Do not send them to a payment page.
   - Either way, mention that once paid, the one-unpaid-search limit is cleared and they can start their next search.

## Notes

- Never open, fill in or complete the payment page on the user's behalf, and never ask for card details in chat. The user pays on Stripe's page themselves, or adds a card in Settings → Billing on themassive.com.au.
- If the tool reports the request is already paid, tell the user to check the inbox (and spam folder) of the email used for that search for the CSV link.
- If the tool reports the request ID is not found or belongs to another account, ask the user to double-check the ID and that they are signed in with the same account that created the search.
- If a saved-card charge fails, the tool or an email provides a Stripe payment link instead; share it if returned.
- Report any other error plainly without retrying in a loop.
