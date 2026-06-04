# SMTP Configuration Guide

> This guide explains how to connect an external SMTP provider to MessengerPanda so your campaigns can be delivered. Follow the instructions for your chosen provider carefully — incorrect settings are the most common reason emails fail to send.

---

## What is SMTP and Why Does It Matter?

**SMTP (Simple Mail Transfer Protocol)** is the universal standard for sending email. When you connect an SMTP account to MessengerPanda, you are giving it the credentials it needs to dispatch your campaigns through your chosen provider's servers.

MessengerPanda stores your SMTP password encrypted with **AES-256-GCM** before it is written to the database. It is never stored or transmitted in plain text, and it is never exposed through any API endpoint. Decryption happens only inside an isolated serverless function at the exact moment an email is dispatched.

---

## How to Add an SMTP Account in MessengerPanda

1. Navigate to **SMTP Accounts** in the left sidebar.
2. Click **Add SMTP Account**.
3. Select your provider from the preset list, or choose **Custom SMTP** to enter details manually.
4. Fill in the required fields (detailed for each provider below).
5. Click **Test Connection** to verify the credentials work.
6. Click **Save**.

You can add multiple SMTP accounts and choose which one to use per campaign.

---

## Provider Guides

---

### Gmail / Google Workspace

> **Important:** Google no longer allows your regular Gmail password for third-party apps. You must generate a dedicated **App Password**. This applies to both personal Gmail accounts and Google Workspace accounts.

#### Prerequisites

- **2-Step Verification must be enabled** on your Google account. App Passwords are not available without it.
  - Go to [myaccount.google.com/security](https://myaccount.google.com/security) and enable 2-Step Verification.

#### Step 1 — Generate an App Password

1. Go to [myaccount.google.com/apppasswords](https://myaccount.google.com/apppasswords) (you may be prompted to sign in again).
2. In the **Select app** dropdown, choose **Mail**.
3. In the **Select device** dropdown, choose **Other (custom name)** and type `MessengerPanda`.
4. Click **Generate**.
5. Google will show you a **16-character password** (e.g., `abcd efgh ijkl mnop`). Copy it immediately — it will not be shown again.

#### Step 2 — Configure in MessengerPanda

| Field | Value |
|---|---|
| **Provider** | Custom SMTP (or select Gmail preset) |
| **SMTP Host** | `smtp.gmail.com` |
| **Port** | `465` |
| **Encryption** | `SSL` |
| **Username** | Your full Gmail address (e.g., `you@gmail.com`) |
| **Password** | The 16-character App Password from Step 1 (no spaces) |
| **From Name** | Your name or business name |
| **From Email** | Your Gmail address |

> **Google Workspace users:** Use the same steps above but replace `@gmail.com` with your Workspace domain (e.g., `you@yourcompany.com`). Make sure the admin has enabled App Passwords or SMTP relay for your account.

#### Gmail Sending Limits

| Account Type | Daily Limit |
|---|---|
| Free Gmail | 500 emails/day |
| Google Workspace (Starter) | 2,000 emails/day |
| Google Workspace (Business) | 2,000 emails/day |

> **For high-volume sending**, Gmail is not recommended. Consider Brevo or Amazon SES instead.

---

### Brevo (formerly Sendinblue)

Brevo is the recommended starting point for most MessengerPanda users. Their free tier is generous, their SMTP setup is simple, and their infrastructure has excellent deliverability.

**Free tier:** 300 emails/day, unlimited contacts.

#### Step 1 — Create a Brevo Account

1. Sign up at [brevo.com](https://www.brevo.com) (no credit card required for the free plan).
2. Complete email verification.

#### Step 2 — Generate Your SMTP Credentials

1. In your Brevo dashboard, click your account name in the top-right corner and go to **SMTP & API**.
2. Click the **SMTP** tab.
3. Click **Generate a new SMTP key**.
4. Give it a name (e.g., `MessengerPanda`) and click **Generate**.
5. Copy the **Login** and **Master password** shown on screen.

> **Note:** Brevo's SMTP password is a long alphanumeric string, not your Brevo account password. Store it securely.

#### Step 3 — Configure in MessengerPanda

| Field | Value |
|---|---|
| **Provider** | Brevo (select from presets) |
| **SMTP Host** | `smtp-relay.brevo.com` |
| **Port** | `587` |
| **Encryption** | `TLS` (STARTTLS) |
| **Username** | Your Brevo login (shown in the SMTP credentials page) |
| **Password** | The SMTP key you generated |
| **From Name** | Your name or business name |
| **From Email** | A verified sender address in your Brevo account |

#### Verifying Your Sender Domain (Recommended)

To unlock higher daily limits and improve deliverability with Brevo:

1. In Brevo, go to **Senders & IP → Domains**.
2. Click **Add a domain** and enter your domain (e.g., `yourcompany.com`).
3. Brevo will provide DNS records to add to your domain registrar. See the [DNS & Deliverability Guide](./dns-deliverability.md) for step-by-step instructions.
4. Once verified, your daily sending limit increases significantly.

---

### Mailgun

Mailgun is a developer-focused SMTP and API service with strong deliverability and detailed logging. Their Flex plan allows up to 5,000 free emails per month for the first three months.

#### Step 1 — Create a Mailgun Account

1. Sign up at [mailgun.com](https://www.mailgun.com).
2. Verify your email address.

#### Step 2 — Add and Verify Your Domain

Mailgun requires a verified sending domain for production use. Sandbox domains (provided during signup) are limited to authorised test recipients only.

1. In the Mailgun dashboard, go to **Sending → Domains**.
2. Click **Add New Domain**.
3. Enter your sending domain. Mailgun recommends using a subdomain for email (e.g., `mail.yourcompany.com`) to protect your root domain's reputation.
4. Mailgun will display a set of DNS records to add (SPF, DKIM, tracking). Add these to your domain registrar. See the [DNS & Deliverability Guide](./dns-deliverability.md) for help.
5. Click **Verify DNS Settings** after adding the records. DNS propagation can take up to 48 hours, but typically completes in under an hour.

#### Step 3 — Get Your SMTP Credentials

1. In the Mailgun dashboard, navigate to **Sending → Domains** and click your verified domain.
2. Click **SMTP credentials** in the domain settings.
3. Note the **Login** and **Default Password**, or create a new SMTP user.

#### Step 4 — Configure in MessengerPanda

**US Region (default):**

| Field | Value |
|---|---|
| **Provider** | Mailgun US (select from presets) |
| **SMTP Host** | `smtp.mailgun.org` |
| **Port** | `587` |
| **Encryption** | `TLS` (STARTTLS) |
| **Username** | Your Mailgun SMTP login (e.g., `postmaster@mail.yourcompany.com`) |
| **Password** | Your Mailgun SMTP password |
| **From Name** | Your name or business name |
| **From Email** | An address on your verified Mailgun domain |

**EU Region:**

| Field | Value |
|---|---|
| **SMTP Host** | `smtp.eu.mailgun.org` |
| **Port** | `587` |
| **Encryption** | `TLS` |
| *(all other fields the same)* | |

> **Choose EU region** during domain setup if your contacts are primarily in Europe and you need GDPR data residency compliance.

---

### Amazon SES (Simple Email Service)

Amazon SES is the most cost-effective option for high-volume senders. At approximately **$0.10 per 1,000 emails**, sending 100,000 emails per month costs around $10. It requires more technical setup than other providers but is well worth it for growing businesses.

#### Prerequisites

- An **AWS account** at [aws.amazon.com](https://aws.amazon.com).
- A domain you control (required to exit the SES sandbox).

#### Step 1 — Verify Your Sending Identity

You must verify either a domain or an email address before sending.

**Recommended: Verify your full domain**

1. In the AWS Console, search for **SES** and open **Amazon Simple Email Service**.
2. In the left menu, go to **Configuration → Verified identities**.
3. Click **Create identity**.
4. Select **Domain**, enter your domain (e.g., `yourcompany.com`), and click **Create identity**.
5. AWS will provide CNAME records for DKIM. Add these to your domain registrar. See the [DNS & Deliverability Guide](./dns-deliverability.md) for instructions.
6. Your domain shows as **Verified** once DNS propagates (usually within a few minutes to a few hours).

#### Step 2 — Request Production Access (Exit the Sandbox)

New AWS SES accounts are placed in a **sandbox** that only allows sending to verified email addresses. To send to real contacts, you must request production access.

1. In the SES console, go to **Account dashboard**.
2. Click **Request production access**.
3. Fill in the request form:
   - **Mail type:** Transactional or Marketing (choose Marketing for campaigns)
   - **Website URL:** Your business website
   - **Use case description:** Describe what you are sending and how you obtained consent
   - **Additional contacts:** An email you want SES to contact
4. AWS typically approves requests within 24 hours.

#### Step 3 — Create SMTP Credentials

SES SMTP credentials are **separate** from your regular AWS IAM credentials. Do not use your AWS access key directly.

1. In the SES console, go to **SMTP settings** (under **Account dashboard**).
2. Click **Create SMTP credentials**.
3. Give the IAM user a name (e.g., `messengerpanda-smtp`) and click **Create user**.
4. Download or copy the **SMTP username** and **SMTP password** shown. This is the only time the password is displayed.

> **Security note:** These are IAM credentials with the minimum permissions needed to send email via SMTP. Do not grant this user any additional AWS permissions.

#### Step 4 — Configure in MessengerPanda

SES SMTP endpoints are region-specific. Use the endpoint for the AWS region where you created your SES identity.

**Common regions:**

| AWS Region | SMTP Endpoint |
|---|---|
| US East (N. Virginia) | `email-smtp.us-east-1.amazonaws.com` |
| US West (Oregon) | `email-smtp.us-west-2.amazonaws.com` |
| EU (Ireland) | `email-smtp.eu-west-1.amazonaws.com` |
| EU (Frankfurt) | `email-smtp.eu-central-1.amazonaws.com` |
| AP (Sydney) | `email-smtp.ap-southeast-2.amazonaws.com` |

| Field | Value |
|---|---|
| **Provider** | Amazon SES (select from presets) |
| **SMTP Host** | Your region-specific endpoint (see table above) |
| **Port** | `587` |
| **Encryption** | `TLS` (STARTTLS) |
| **Username** | The SMTP username from Step 3 |
| **Password** | The SMTP password from Step 3 |
| **From Name** | Your name or business name |
| **From Email** | An address on your verified SES domain |

> **Important:** Port `587` with TLS is strongly recommended. SES also supports port `465` with SSL, but port `25` is blocked on many cloud platforms including AWS EC2 and Vercel.

---

## Troubleshooting Common Errors

### "Authentication failed" or "535 Authentication Required"

- Double-check your username and password. For Gmail, ensure you are using the **App Password**, not your regular Gmail password.
- For Brevo, ensure you are using the **SMTP key** (long alphanumeric string), not your Brevo account login password.
- For Amazon SES, ensure you are using the **SMTP credentials** downloaded during IAM user creation, not your AWS Access Key ID / Secret.

### "Connection timed out"

- Verify the **Host** and **Port** values match exactly what is listed above for your provider.
- If port `587` times out, try port `465` (SSL).
- Some corporate networks block outbound SMTP ports. Test from a different network if unsure.

### "Sender address rejected"

- Your **From Email** must be a verified sender on your SMTP provider. For Mailgun and SES, it must belong to your verified sending domain.
- For Gmail, the **From Email** must be your Gmail or Workspace address exactly.

### "Daily sending limit exceeded"

- You have hit your provider's daily cap. Upgrade your provider plan, or spread sends across multiple SMTP accounts using MessengerPanda's multi-account support.

### Test email works, but campaign delivery fails

- Check that your **From Email** matches the address verified with your provider — some providers reject bulk sends from unverified addresses even if SMTP authentication succeeds.
- Review the campaign's **spam score** in the editor and resolve any flagged issues before resending.

---

## Security Best Practices

- **Never use your main account password** as an SMTP password. Always use provider-generated App Passwords, API keys, or dedicated SMTP credentials.
- **Rotate credentials** if you suspect compromise. MessengerPanda stores credentials encrypted — but you should update them in the platform after rotation.
- **Use a subdomain** for bulk sending (e.g., `mail.yourcompany.com`) to protect your root domain's sender reputation.
- **Monitor your provider's bounce and complaint rates** from their dashboard. High rates lead to account suspension and damage your sender reputation.

---

## Next Steps

Once your SMTP account is connected and tested, configure your domain's DNS records to maximise deliverability. Skipping this step is the most common reason campaigns land in spam folders.

→ **[Read the DNS & Deliverability Guide](./dns-deliverability.md)**

---

*MessengerPanda is a product of Palladium Innovations. Need help? Email [support@messengerpanda.app](mailto:support@messengerpanda.app)*
