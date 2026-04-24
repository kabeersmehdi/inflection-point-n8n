# INFLECTION POINT — Setup Checklist

Follow these steps in order. Do not skip any step.

---

## 1. Accounts to Create

Create these accounts before proceeding:

- [ ] **Formspree** — https://formspree.io (free tier works)
- [ ] **Resend** — https://resend.com (for sending emails)
- [ ] **n8n** — https://n8n.io (Cloud or self-hosted)
- [ ] **Anthropic API** — https://console.anthropic.com (for Claude)
- [ ] **Google Account** — For Google Sheets integration

---

## 2. Google Sheets Setup

Create a new Google Sheet named exactly: `Inflection Point Data`

Create three tabs with these exact names and columns (in order):

### Tab 1: `user_profile`
| Column | Type | Description |
|--------|------|-------------|
| `first_name` | Text | User's first name |
| `email` | Text | Email address |
| `focus` | Text | What they do professionally |
| `goals` | Text | Comma-separated goals |
| `skill_level` | Text | AI comfort level |
| `tools_using` | Text | AI tools they use (optional) |
| `industries` | Text | Comma-separated interest areas |
| `exclude` | Text | Topics to exclude (optional) |
| `style_anchor` | Text | Style directive |
| `weekly_context` | Text | **UPDATE THIS WEEKLY** — what they're working on |
| `source` | Text | Signup source |
| `submitted_at` | Text | ISO timestamp |

### Tab 2: `raw_content`
| Column | Type | Description |
|--------|------|-------------|
| `timestamp` | Text | Week identifier (ISO format) |
| `raw_content` | Text | All RSS + GitHub content concatenated |
| `source_count` | Number | Number of sources (should be 11) |

### Tab 3: `send_log`
| Column | Type | Description |
|--------|------|-------------|
| `timestamp` | Text | Send timestamp |
| `user_email` | Text | Recipient email |
| `user_name` | Text | Recipient name |
| `quality_passed` | Boolean | Whether quality check passed |
| `subject` | Text | Email subject line |
| `status` | Text | "sent" or "flagged" |

⚠️ **REPLACE**: Copy your Google Sheet ID from the URL (between `/d/` and `/edit`) — you'll need this for both n8n workflows.

---

## 3. Resend Setup

1. Go to https://resend.com and sign in
2. Click "Add Domain"
3. Follow domain verification steps:
   - Add the DNS records Resend provides to your domain registrar
   - Wait for DNS propagation (can take up to 48 hours, usually ~15 minutes)
4. Once verified, go to API Keys
5. Create a new API key with "Full Access"
6. Copy the API key

⚠️ **REPLACE**: Your sending domain in the workflow (e.g., `hello@yourdomain.com`)
⚠️ **REPLACE**: Your admin email for quality failure alerts

---

## 4. Formspree Setup

1. Go to https://formspree.io and sign in
2. Click "New Form"
3. Name it "Inflection Point Onboarding"
4. Set the redirect URL (optional — the HTML handles success state itself)
5. Copy the form endpoint ID (looks like `xvndrzlk`)

⚠️ **REPLACE**: In `onboarding.html`, find `⚠️ REPLACE_WITH_YOUR_FORMSPREE_ID` and paste your Formspree form ID

---

## 5. Anthropic API Setup

1. Go to https://console.anthropic.com
2. Sign in or create account
3. Go to "Keys" in the left sidebar
4. Click "Create Key"
5. Name it "Inflection Point Newsletter"
6. Copy the key immediately (you can't see it again)

⚠️ **REPLACE**: Set this as an environment variable in n8n (next step)

---

## 6. n8n Environment Variables

In your n8n instance, go to **Settings → Environment Variables** and add:

| Variable Name | Value Source |
|---------------|--------------|
| `ANTHROPIC_API_KEY` | From Step 5 |
| `RESEND_API_KEY` | From Step 3 |

Save and restart n8n if using self-hosted.

---

## 7. Import collector-workflow.json

1. Open n8n
2. Go to **Workflows** in the left sidebar
3. Click "Import from File"
4. Select `collector-workflow.json`
5. The workflow opens in the editor

### Fix the placeholders:

1. Click the **Google Sheets — Write Raw Content** node
2. Under "Document ID", paste your Sheet ID from Step 2
3. Under "Credentials", create new Google Sheets OAuth2 credentials:
   - Click "Create New"
   - Follow the OAuth flow (see Step 9)
4. Save the node

6. Click the **Schedule Trigger** node
7. Verify it's set to Sunday at 21:00
8. Save the workflow

---

## 8. Import newsletter-workflow.json

1. In n8n, go to **Workflows**
2. Click "Import from File"
3. Select `newsletter-workflow.json`
4. The workflow opens in the editor

### Fix the placeholders:

1. Click **Google Sheets — Read All User Profiles**
   - Paste your Sheet ID
   - Select your Google Sheets credentials

2. Click **Google Sheets — Read Latest Raw Content**
   - Paste your Sheet ID
   - Select your Google Sheets credentials

3. Click **HTTP Request — Send Newsletter via Resend**
   - Change `hello@⚠️ REPLACE_WITH_YOUR_DOMAIN` to your actual sending domain

4. Click **HTTP Request — Send Flag Alert via Resend**
   - Change `⚠️ REPLACE_WITH_YOUR_ADMIN_EMAIL` to your admin email

5. Click **Google Sheets — Log Send**
   - Paste your Sheet ID
   - Select your Google Sheets credentials

6. Click the **Schedule Trigger** node
7. Verify it's set to Monday at 07:00
8. Save the workflow

---

## 9. Google Sheets OAuth2 Setup in n8n

1. In n8n, go to **Credentials** in the left sidebar
2. Click "Add Credential"
3. Search for "Google Sheets OAuth2 API"
4. Click "Create"
5. You'll need:
   - **OAuth Client ID** and **OAuth Client Secret** from Google Cloud Console:
     - Go to https://console.cloud.google.com
     - Create a new project (or select existing)
     - Enable "Google Sheets API"
     - Go to "Credentials" → "Create Credentials" → "OAuth Client ID"
     - Application type: "Web application"
     - Authorized redirect URIs: Add your n8n OAuth callback URL (shown in the credential setup)
     - Copy the Client ID and Client Secret
   - Paste both into n8n
6. Click "Connect"
7. Sign in with your Google account and grant permissions
8. Name the credential "Inflection Point Sheets"
9. Save

---

## 10. Fill in Your User Profile

In your Google Sheet, go to the `user_profile` tab and add your first row:

| Column | Example Value |
|--------|---------------|
| `first_name` | Kabeer |
| `email` | kabeersmehdi@gmail.com |
| `focus` | Computer science student building AI tools |
| `goals` | Build something (app, tool, automation, product), Learn to code or automate things |
| `skill_level` | I use AI tools regularly |
| `tools_using` | Claude, Cursor, GitHub Copilot |
| `industries` | Coding & Dev Tools, AI Models & Research, Startups & Venture |
| `exclude` | crypto, hype articles |
| `style_anchor` | a brilliant friend who works in AI and explains things clearly without dumbing them down |
| `weekly_context` | Building an n8n newsletter system — want to see how personalization works |
| `source` | inflection-point-onboarding |
| `submitted_at` | 2026-04-24T00:00:00.000Z |

⚠️ **REPLACE**: `weekly_context` every Monday morning before the workflow runs. One sentence on what you're working on or thinking about this week.

---

## 11. Testing Before Going Live

### Manual Test:

1. In n8n, open `newsletter-workflow.json`
2. Click "Execute Workflow" (or "Test Workflow")
3. Wait for execution to complete
4. Check:
   - Did Claude API return content?
   - Did quality checks pass?
   - Did the email send?
   - Check your inbox — does the email render correctly?
   - Are all sections properly styled?
   - Is your name mentioned?
   - Are exclusions respected?

### Verify Email Rendering:

- Open the email in Gmail
- Check dark mode rendering
- Check mobile view
- Verify all links work
- Verify the unsubscribe link is present

---

## 12. Going Live

Once testing is complete:

1. Open `collector-workflow.json`
2. Toggle "Active" to ON (top right)
3. Open `newsletter-workflow.json`
4. Toggle "Active" to ON
5. Both schedules are now running:
   - **Collector**: Every Sunday at 21:00
   - **Newsletter**: Every Monday at 07:00

---

## 13. Weekly Maintenance

Every Monday morning (before 07:00):

1. Open your Google Sheet
2. Go to `user_profile` tab
3. Update the `weekly_context` column with what you're working on this week
4. That's it — the workflows handle the rest

Example weekly_context entries:
- "Evaluating AI coding agents for my startup"
- "Building a RAG pipeline for document search"
- "Researching multimodal models for a project"
- "Thinking about AI safety and alignment"

---

## Quick Reference

### Environment Variables
```
ANTHROPIC_API_KEY=sk-ant-...
RESEND_API_KEY=re_...
```

### Google Sheet Tabs
- `user_profile` — User data (update `weekly_context` weekly)
- `raw_content` — Auto-populated by collector workflow
- `send_log` — Auto-populated by newsletter workflow

### Placeholders to Replace
| File | Placeholder | What Goes Here |
|------|-------------|----------------|
| `onboarding.html` | `⚠️ REPLACE_WITH_YOUR_FORMSPREE_ID` | Formspree form ID |
| `collector-workflow.json` | `⚠️ REPLACE_WITH_YOUR_SHEET_ID` | Google Sheet ID |
| `collector-workflow.json` | `⚠️ REPLACE_WITH_YOUR_CREDENTIAL_ID` | Google Sheets credential ID |
| `newsletter-workflow.json` | `⚠️ REPLACE_WITH_YOUR_SHEET_ID` | Google Sheet ID |
| `newsletter-workflow.json` | `⚠️ REPLACE_WITH_YOUR_CREDENTIAL_ID` | Google Sheets credential ID |
| `newsletter-workflow.json` | `⚠️ REPLACE_WITH_YOUR_DOMAIN` | Your verified domain |
| `newsletter-workflow.json` | `⚠️ REPLACE_WITH_YOUR_ADMIN_EMAIL` | Your admin email |

### Workflow Schedules
| Workflow | Schedule | Timezone |
|----------|----------|----------|
| Collector | Sunday 21:00 | Your local time |
| Newsletter | Monday 07:00 | Your local time |

---

## Troubleshooting

### Collector workflow fails
- Check Google Sheets credentials are valid
- Verify Sheet ID is correct
- Check RSS feeds are accessible (some may rate-limit)

### Newsletter workflow fails
- Verify Anthropic API key is set in environment variables
- Check Resend domain is verified
- Verify Google Sheets credentials

### Quality check always fails
- Check that Claude is returning HTML with `letter-spacing:0.12em` (section headers)
- Verify the user's name is being passed correctly
- Check exclusion list logic

### Email not sending
- Verify Resend API key
- Check domain verification in Resend dashboard
- Ensure "from" address uses your verified domain

---

**You're done.** The system is now fully operational. Update `weekly_context` every Monday and watch your personalized AI briefing arrive.
