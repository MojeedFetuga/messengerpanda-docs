# DNS & Email Deliverability Guide

> **Before you send a single campaign**, spend 15 minutes configuring the three DNS records described in this guide. They are the single most impactful thing you can do to ensure your emails reach the inbox — not the spam folder.

---

## Why Do My Emails Land in Spam?

When a contact's email provider (Gmail, Outlook, Yahoo, etc.) receives an email from you, it asks a fundamental question before deciding where to put it:

> *"Can I verify that this email was genuinely sent by the person or business it claims to be from?"*

Without the proper DNS records on your domain, the answer is **no** — and the email is treated as suspicious. Spam filters penalise unverified senders heavily, regardless of how good your email content is.

The three records below are the industry-standard way to prove to every major inbox provider that your emails are legitimate. They are free to configure, take about 15 minutes, and once set up, they work automatically forever.

---

## The Three Records You Need

| Record | What It Does | Without It |
|---|---|---|
| **SPF** | Tells the world which mail servers are allowed to send email on your domain's behalf | Emails may be rejected or marked as suspicious |
| **DKIM** | Adds a cryptographic signature to every email, proving the content was not tampered with in transit | Emails may fail authentication checks at destination |
| **DMARC** | Ties SPF and DKIM together and tells providers what to do if either check fails | No consistent enforcement — makes phishing your domain easier |

Think of it this way:
- **SPF** is the guest list at the door — it says who is allowed in.
- **DKIM** is the wax seal on the envelope — it proves the contents haven't been opened.
- **DMARC** is the bouncer's policy — it says what happens when someone is not on the list.

---

## Step 1 — SPF (Sender Policy Framework)

### What You Are Doing

Adding a TXT record to your domain that lists the mail servers permitted to send email as `@yourdomain.com`.

### How to Find Your SPF Value

Each SMTP provider has its own SPF entry. Use the value for your provider:

| Provider | SPF include value |
|---|---|
| **Brevo** | `include:spf.sendinblue.com` |
| **Mailgun** | `include:mailgun.org` |
| **Amazon SES (US East)** | `include:amazonses.com` |
| **SendGrid** | `include:sendgrid.net` |
| **Postmark** | `include:spf.mtasv.net` |
| **Resend** | `include:spf.resend.com` |
| **Google/Gmail** | `include:_spf.google.com` |

### Your SPF Record (Complete Format)

Replace `PROVIDER_INCLUDE` with the value from the table above:

```
v=spf1 include:PROVIDER_INCLUDE ~all
```

**Example for Brevo:**
```
v=spf1 include:spf.sendinblue.com ~all
```

**Example for Amazon SES + Google (two providers):**
```
v=spf1 include:amazonses.com include:_spf.google.com ~all
```

> **Important:** You can only have **one SPF TXT record** per domain. If you already have one, add the new `include:` values to the existing record — do not create a second one.

### What `~all` Means

The ending of your SPF record tells providers how to handle mail from unlisted servers:

| Ending | Meaning | Recommendation |
|---|---|---|
| `~all` | Soft fail — accept but mark as suspicious | ✅ Use this |
| `-all` | Hard fail — reject outright | Use only after confirming all your senders are listed |
| `?all` | Neutral — no policy | ❌ Avoid |

Start with `~all` and move to `-all` once you are confident all legitimate sending sources are listed.

### How to Add the SPF Record

#### GoDaddy

1. Log in at [godaddy.com](https://www.godaddy.com) and open **My Products**.
2. Click **DNS** next to your domain.
3. Click **Add New Record**.
4. Set **Type** to `TXT`.
5. Set **Name** to `@` (this represents your root domain).
6. Paste your complete SPF value into **TXT Value**.
7. Set **TTL** to `1 Hour` (or `3600`).
8. Click **Save**.

#### Namecheap

1. Log in at [namecheap.com](https://www.namecheap.com) and go to **Domain List**.
2. Click **Manage** next to your domain.
3. Click the **Advanced DNS** tab.
4. Click **Add New Record**.
5. Set **Type** to `TXT Record`.
6. Set **Host** to `@`.
7. Paste your SPF value into **Value**.
8. Set **TTL** to `Automatic` or `3600`.
9. Click the green checkmark to save.

#### Cloudflare

1. Log in at [cloudflare.com](https://www.cloudflare.com) and select your domain.
2. Click **DNS** in the top navigation.
3. Click **Add record**.
4. Set **Type** to `TXT`.
5. Set **Name** to `@`.
6. Paste your SPF value into **Content**.
7. Leave **Proxy status** set to **DNS only** (grey cloud — not orange).
8. Click **Save**.

---

## Step 2 — DKIM (DomainKeys Identified Mail)

### What You Are Doing

Adding a TXT record containing a **public cryptographic key**. Your SMTP provider uses its matching private key to sign every outbound email. When an inbox provider receives the email, it retrieves your public key from DNS and verifies the signature — confirming the email genuinely came from your provider and was not modified in transit.

### How to Get Your DKIM Record

Each provider generates a unique DKIM key for your domain. You must retrieve this from your provider's dashboard.

#### Brevo

1. In your Brevo dashboard, go to **Senders & IP → Domains**.
2. Click your verified domain.
3. Brevo shows a TXT record under **DKIM**. Copy the **Name** (e.g., `mail._domainkey`) and the full **Value** string.

#### Mailgun

1. In the Mailgun dashboard, go to **Sending → Domains**.
2. Click your domain.
3. Under **DNS records**, find the record labelled **DKIM**. Copy the **Hostname** (e.g., `mailo._domainkey.mail.yourdomain.com`) and the **Value**.

#### Amazon SES

1. In the AWS SES console, go to **Configuration → Verified identities**.
2. Click your domain.
3. Click the **Authentication** tab.
4. SES provides **three CNAME records** (not TXT) for DKIM. Add all three exactly as shown.

> **Note:** Amazon SES uses CNAME records for DKIM, while most other providers use TXT records. Refer to the record type your provider specifies.

#### Google Workspace

1. In the [Google Admin Console](https://admin.google.com), go to **Apps → Google Workspace → Gmail**.
2. Click **Authenticate email**.
3. Select your domain and click **Generate new record**.
4. Copy the DKIM record name and value provided.

### How to Add the DKIM Record

The process is the same as adding the SPF record, with these differences:

- **Type:** `TXT` (or `CNAME` if your provider specifies CNAME)
- **Name/Host:** Use exactly what your provider shows (e.g., `mail._domainkey` or `mailo._domainkey.mail`)
- **Value:** The full DKIM key string from your provider (it begins with `v=DKIM1; k=rsa; p=` followed by a long string of characters)

**Example DKIM TXT record value:**

```
v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC7PsuNDRbLhbKM...
```

Follow the same steps as SPF for your registrar (GoDaddy, Namecheap, or Cloudflare), substituting the DKIM name and value.

> **Cloudflare users:** If Cloudflare is proxying your domain (orange cloud), DKIM records must still have **Proxy status** set to **DNS only** (grey cloud). Proxying a DKIM record breaks email authentication.

---

## Step 3 — DMARC (Domain-based Message Authentication, Reporting & Conformance)

### What You Are Doing

Adding a policy record that tells inbox providers what to do when an email fails SPF or DKIM — and optionally requesting reports so you can monitor your domain's email authentication health.

### Your DMARC Record

DMARC records are added as TXT records at the hostname `_dmarc.yourdomain.com`.

**Start with a monitoring-only policy (recommended for new setups):**

```
v=DMARC1; p=none; rua=mailto:dmarc-reports@yourdomain.com
```

Replace `dmarc-reports@yourdomain.com` with any email address you control. DMARC aggregate reports will be sent there weekly, showing you how your domain's email is performing across the internet.

**Policy levels explained:**

| `p=` value | Meaning | When to use |
|---|---|---|
| `p=none` | Monitor only — take no action on failures | ✅ Start here. Observe reports for 2–4 weeks |
| `p=quarantine` | Send failing emails to the spam folder | Move here once your legitimate emails consistently pass |
| `p=reject` | Reject failing emails outright | Use once you are confident in your setup and want maximum protection |

### How to Add the DMARC Record

| Field | Value |
|---|---|
| **Type** | `TXT` |
| **Name/Host** | `_dmarc` |
| **Value** | Your DMARC policy string (see above) |
| **TTL** | `3600` (1 hour) |

Add this record in your registrar's DNS settings following the same process as SPF and DKIM.

**Example — upgrading to quarantine after 4 weeks of clean reports:**

```
v=DMARC1; p=quarantine; pct=10; rua=mailto:dmarc-reports@yourdomain.com
```

The `pct=10` applies the quarantine policy to only 10% of failing emails initially, giving you a safety net to catch configuration problems before enforcing the full policy.

---

## Step 4 — Verify Your Configuration

Once you have added all three records, allow **15–60 minutes** for DNS to propagate (occasionally up to 48 hours for some registrars).

### Using MessengerPanda's Built-In DNS Checker

1. In MessengerPanda, navigate to **Campaigns → New Campaign** and proceed to the Email Editor.
2. In the editor toolbar, click **Domain Checker**.
3. Enter your sending domain.
4. MessengerPanda will check your SPF, DKIM, and DMARC records in real time and flag any issues.

### Using Free Online Tools

You can also verify your records using these trusted public tools:

| Tool | URL | What it checks |
|---|---|---|
| MXToolbox | [mxtoolbox.com/spf.aspx](https://mxtoolbox.com/spf.aspx) | SPF record validity |
| MXToolbox DKIM | [mxtoolbox.com/dkim.aspx](https://mxtoolbox.com/dkim.aspx) | DKIM record lookup |
| DMARC Analyser | [dmarcanalyzer.com/dmarc/dmarc-record-checker](https://www.dmarcanalyzer.com/dmarc/dmarc-record-checker/) | DMARC policy validation |
| Mail Tester | [mail-tester.com](https://www.mail-tester.com) | Send a real test email and get a deliverability score |

A correctly configured domain should score **9/10 or higher** on Mail Tester.

---

## Common Questions

### Do I need to do this if I am using Gmail or Google Workspace?

Yes. Even if you are sending through Gmail's SMTP, your **From Email** domain needs SPF and DMARC configured. DKIM for Gmail is set up through the Google Admin Console (see the DKIM section above).

### My registrar is not listed above. What do I do?

The process is the same for every registrar — you are adding DNS TXT records. Look for a **DNS Management** or **Advanced DNS** section in your domain's control panel. If you cannot find it, contact your registrar's support and say: *"I need to add TXT records to my domain's DNS for SPF, DKIM, and DMARC email authentication."*

### How long does DNS propagation take?

Usually **15–60 minutes**, but it can take up to **48 hours** in rare cases. You can check propagation progress at [dnschecker.org](https://dnschecker.org).

### I added the records but the checker still shows errors.

- Wait another 30 minutes and check again — propagation takes time.
- Double-check that you copied the **full** DKIM value (these are very long strings and are easy to truncate).
- Confirm your SPF record has no duplicate `v=spf1` prefix — you can only have one SPF record per domain.
- If using Cloudflare, confirm DKIM and SPF records are set to **DNS only** (grey cloud), not proxied.

### Can I use MessengerPanda without these records?

You can connect an SMTP account and send emails without these records configured. However, your campaigns are significantly more likely to land in spam folders. Major providers including Gmail and Yahoo require a DMARC record for bulk senders — without it your emails risk being rejected outright. Configuring these records is not optional for professional email marketing.

---

## Complete Setup Checklist

Use this checklist to confirm your domain is fully configured before your first campaign:

- [ ] SPF TXT record added at `@` with your provider's `include:` value
- [ ] DKIM TXT or CNAME record(s) added at the hostname your provider specifies
- [ ] DMARC TXT record added at `_dmarc` with `p=none` to start
- [ ] DNS propagation confirmed (waited at least 30 minutes)
- [ ] Verified using MessengerPanda's Domain Checker or MXToolbox
- [ ] Mail Tester score of 9/10 or higher on a test send
- [ ] DMARC reports email address monitored after first week of sending

---

## Summary

| Record | DNS Type | Hostname | Example Value |
|---|---|---|---|
| SPF | TXT | `@` | `v=spf1 include:spf.sendinblue.com ~all` |
| DKIM | TXT or CNAME | `mail._domainkey` (varies by provider) | `v=DKIM1; k=rsa; p=MIGf...` |
| DMARC | TXT | `_dmarc` | `v=DMARC1; p=none; rua=mailto:reports@yourdomain.com` |

Once all three records are green, you have done everything possible at the DNS level to ensure your campaigns reach the inbox. The rest comes down to great content, a clean list, and consistent sending behaviour — all of which MessengerPanda is built to help you manage.

---

*Need help configuring DNS records? Email [support@messengerpanda.app](mailto:support@messengerpanda.app) with your domain name and we will walk you through it.*

*MessengerPanda is a product of Palladium Innovations. © 2026 MessengerPanda. All rights reserved.*
