# Getting Started with MessengerPanda

> **Welcome to MessengerPanda** — the email marketing platform built for businesses that want enterprise-grade automation without enterprise-grade pricing.

---

## What is MessengerPanda?

MessengerPanda is a **Bring Your Own SMTP (BYOS)** email marketing and CRM platform. Instead of charging you per email sent — the way Mailchimp, Klaviyo, or ActiveCampaign do — MessengerPanda charges a flat monthly fee for the software, and lets you connect your own email-sending service (called an **SMTP provider**) to handle the actual delivery.

This single architectural decision changes your economics permanently.

### The BYOS Cost Advantage

Here is what the same email volume costs on a traditional platform versus MessengerPanda:

| Monthly Volume | Mailchimp (Essentials) | Klaviyo | **MessengerPanda (Pro)** |
|---|---|---|---|
| 10,000 emails | ~$26/mo | ~$45/mo | **$19/mo flat** |
| 50,000 emails | ~$80/mo | ~$150/mo | **$19/mo flat** |
| 250,000 emails | ~$350/mo | ~$700/mo | **$49/mo flat** |

With MessengerPanda, your bill does not grow as your list grows. You pay for the software once, then send through a provider like **Brevo** (free up to 300 emails/day), **Amazon SES** (~$0.10 per 1,000 emails), or **Mailgun** — keeping sending costs minimal or near-zero.

> **Real-world example:** A business sending 100,000 emails per month through Amazon SES pays approximately $10 in sending fees + $19 for MessengerPanda = **$29 total**. The equivalent Klaviyo plan would cost ~$400/mo.

---

## What Can You Do With MessengerPanda?

- **Email Campaigns** — Design, schedule, and send bulk campaigns to your contact lists with real-time open and click tracking.
- **Automation Sequences** — Build drip sequences that automatically send emails when a contact joins a list.
- **Opt-In Forms** — Create embeddable signup forms with double opt-in support to grow your list compliantly.
- **A/B Testing** — Split-test subject lines and email content to optimise your campaigns.
- **Analytics** — Track opens, clicks, bounces, and unsubscribes per campaign and per contact.
- **Deliverability Tools** — Built-in spam score checker, SPF/DKIM DNS validator, and unsubscribe link enforcement.

---

## Onboarding Checklist

Follow these four steps in order. The whole process takes under 15 minutes.

---

### Step 1 — Create Your Account

1. Go to [messengerpanda.app](https://messengerpanda.app) and click **Start Free**.
2. Enter your name, email address, and a strong password.
3. Check your inbox for a **verification email** and click the confirmation link.
4. Log in to your dashboard.

> Your account starts on the **Free plan** — 500 contacts, 3 campaigns/month, 1,500 emails/month. Upgrade at any time from **Settings → Billing**.

---

### Step 2 — Connect Your First SMTP Account

This is the most important step. Without an SMTP account connected, MessengerPanda cannot send emails on your behalf.

1. In the left sidebar, navigate to **SMTP Accounts**.
2. Click **Add SMTP Account**.
3. Choose your provider from the presets (Brevo, Resend, Mailgun, SendGrid, Amazon SES, Postmark) or enter custom credentials.
4. Fill in your **Host**, **Port**, **Username**, **Password**, **From Name**, and **From Email**.
5. Click **Test Connection** — you will see a green checkmark if everything is configured correctly.
6. Click **Save**.

> **Recommended for beginners:** Start with **Brevo**. Their free tier includes 300 emails/day with no credit card required. See the [SMTP Configuration Guide](./smtp-configuration.md) for step-by-step setup instructions for every provider.

---

### Step 3 — Upload Your Contact List

1. Navigate to **Contacts** in the sidebar.
2. Click **New List** and give it a name (e.g., "Newsletter Subscribers").
3. Click **Import Contacts**.
4. Upload a **CSV file** with at least one column for `email`. Optional columns: `firstName`, `lastName`.

**Example CSV format:**

```csv
email,firstName,lastName
jane@example.com,Jane,Smith
john@example.com,John,Doe
alex@example.com,Alex,Johnson
```

5. Map your CSV columns to the correct fields in the import wizard.
6. Click **Import**. MessengerPanda will validate addresses and flag any duplicates.

> **GDPR note:** Only import contacts who have given you explicit consent to receive marketing emails. MessengerPanda records the import source, timestamp, and IP address as your consent audit trail.

---

### Step 4 — Send Your First Campaign

1. Navigate to **Campaigns** and click **New Campaign**.
2. Fill in the **Campaign Settings** step:
   - **Campaign name** (internal — your contacts never see this)
   - **Subject line** (what contacts see in their inbox)
   - **From name** and **From email**
   - **SMTP account** to send through
   - **Contact list** to send to
3. Click **Continue to Editor**.
4. Write your email in the rich-text editor. Type **/** to insert blocks like headings, buttons, images, and spacers.
5. Click **Save Campaign** when ready.
6. On the campaign detail page, click **Send Campaign** (or **Schedule** to send at a specific time).

> Before sending, use the **Spam Score Checker** in the editor toolbar to review potential deliverability issues. Aim for a score of 0–2 (Excellent).

---

## Understanding Your Plan Limits

| Feature | Free | Pro ($19/mo) | Business ($49/mo) |
|---|---|---|---|
| Contacts | 500 | 10,000 | 50,000 |
| Campaigns/month | 3 | Unlimited | Unlimited |
| Emails/month | 1,500 | 50,000 | 250,000 |
| SMTP accounts | 1 | Unlimited | Unlimited |
| Automations | ✓ | ✓ | ✓ |
| A/B Testing | ✓ | ✓ | ✓ |
| Priority support | — | ✓ | ✓ |

Upgrade or cancel at any time from **Settings → Billing**.

---

## Getting Help

- **SMTP Setup Guide:** [smtp-configuration.md](./smtp-configuration.md)
- **DNS & Deliverability Guide:** [dns-deliverability.md](./dns-deliverability.md)
- **All guides:** [messengerpanda-docs on GitHub](https://github.com/MojeedFetuga/messengerpanda-docs)
- **Support:** [support@messengerpanda.app](mailto:support@messengerpanda.app)

---

*MessengerPanda is a product of Palladium Innovations. © 2026 MessengerPanda. All rights reserved.*
