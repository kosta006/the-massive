# The Massive for Claude

Find business leads without leaving Claude. The Massive runs a Google Ads business search for a keyword across the locations you choose and returns each business's **website, email address, phone number and physical address** as a CSV.

## What's included

| Component | What it does |
|---|---|
| **The Massive connector** | Connects Claude to `https://themassive.com.au/mcp/search` (secure OAuth sign-in, no API keys). |
| **/find-leads** | Guided search: checks you're signed in, collects your keyword, country, locations and email, shows a full summary, and only submits after you confirm. |
| **/plan-locations** | Turns "all of South East Queensland" or "every capital city" into a clean list of up to 100 specific locations for you to approve. |
| **/retry-payment** | Renews payment for a search whose emailed Stripe link has expired. |

## Setup

1. Install the plugin.
2. When Claude first uses The Massive, you'll be asked to sign in. A browser page opens where you can use an existing email/password account or **Continue with Google, GitHub, Apple, Facebook or Microsoft**. A new account is created automatically on first social sign-in.
3. That's it. Claude never asks for your password or card details in chat.

## Using it

Just ask in plain language, or use the slash commands:

- "Find me plumbers in Brisbane, Gold Coast and Sunshine Coast"
- "I need electricians across every capital city in Australia"
- `/find-leads roof repairs`
- "My payment link expired for search 1234"

## How searches and payment work

1. Claude shows you the keyword, country, every location and the email address, and waits for your **yes**. Searches can't be cancelled once created.
2. The search runs. When it finishes:
   - **With a card saved** in Settings → Billing on themassive.com.au, your card is charged automatically and the **CSV download link** is emailed to you.
   - **Without a saved card**, a **Stripe payment link** is emailed to you. After payment, the **CSV download link** is emailed to the same address.
   - If an automatic charge fails, you'll get a Stripe payment link instead.

Good to know:

- **Searches are paid** and charged only when the search completes.
- **One keyword per search.** Each extra keyword is a separate search.
- **Up to 100 locations per search**, all in one country.
- **One unpaid search per email address.** Pay for the current search before starting another. Use `/retry-payment` with your request ID if the payment link has expired. If you have a saved card, renewing charges it straight away.
- Keep the **request ID** Claude gives you after submitting; it's also in your emails.

## Support

Visit [themassive.com.au](https://themassive.com.au).

## Privacy

See The Massive's [Privacy Policy](https://themassive.com.au/privacy).
