# INFLECTION POINT — n8n Newsletter System

A production-ready, personalized AI newsletter system built with n8n, Claude API, and Google Sheets.

**Tagline:** "AI intelligence, curved to you."

---

## What This Is

Inflection Point is a weekly AI briefing that personalizes itself entirely based on what each reader tells us about themselves. No section is hardcoded to a profession. Everything is dynamic and driven by the reader profile.

- **Student?** You get AI models, learning resources, and tools for building.
- **Founder?** You get venture news, strategy insights, and competitive intelligence.
- **Developer?** You get GitHub trending repos, dev tools, and API updates.
- **Designer?** You get creative AI tools, design workflows, and media innovations.

The system reads the user's profile and builds the briefing from their selections.

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    INFLECTION POINT SYSTEM                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐     ┌──────────────┐     ┌──────────────┐    │
│  │  Onboarding  │────▶│   Google     │────▶│  Collector   │    │
│  │    Form      │     │   Sheets     │     │  Workflow    │    │
│  └──────────────┘     └──────────────┘     └──────────────┘    │
│         │                    │                      │           │
│         │                    │                      ▼           │
│         │                    │            ┌──────────────┐     │
│         │                    │            │  RSS Feeds   │     │
│         │                    │            │  GitHub API  │     │
│         │                    │            └──────────────┘     │
│         │                    │                      │           │
│         │                    │                      ▼           │
│         │                    │            ┌──────────────┐     │
│         │                    │◀───────────│  Newsletter  │     │
│         │                    │            │  Workflow    │     │
│         │                    │            └──────────────┘     │
│         │                    │                      │           │
│         │                    │                      ▼           │
│         │                    │            ┌──────────────┐     │
│         └────────────────────┼────────────│   Claude     │     │
│                              │            │    API       │     │
│                              │            └──────────────┘     │
│                              │                      │           │
│                              │                      ▼           │
│                              │            ┌──────────────┐     │
│                              └────────────│    Resend    │     │
│                                           │   (Email)    │     │
│                                           └──────────────┘     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Deliverables

| File | Description |
|------|-------------|
| `onboarding.html` | Standalone onboarding form (vanilla HTML/CSS/JS) |
| `collector-workflow.json` | n8n workflow: collects RSS + GitHub data every Sunday |
| `newsletter-workflow.json` | n8n workflow: personalizes and sends every Monday |
| `prompt-template.txt` | Master Claude prompt (system + user) |
| `setup-checklist.md` | Step-by-step setup guide |

---

## Features

### Universal Personalization
- 8 onboarding fields capture who the reader is
- 10 interest areas to choose from (pick 2+)
- 9 goal options (pick 1+)
- 3 skill levels (beginner, intermediate, builder)
- Exclusion filtering (what you never want to see)
- Style anchor (how you want it written)
- Weekly context (what you're working on this week)

### Dynamic Content Sections
Sections are included or excluded based on the reader's interests:

| Section | Always | Triggered By |
|---------|--------|--------------|
| What Just Happened | ✓ | — |
| Tools Worth Knowing | | Coding, Automation, Business, Design, Content |
| GitHub This Week | | Coding, Automation |
| AI Models & Research | | AI Models, Science & Healthcare |
| Startups & Venture | | Startups, Business |
| Design & Creative AI | | Design, Content |
| Policy & Society | | Policy |
| Skill of the Week | ✓ | — |
| Your Inflection Point | ✓ | — |

### Quality Checks
Before sending, the system validates:
- User's name is mentioned
- Content is >800 characters
- Section headers are properly formatted
- Exclusion list is respected

If quality fails, an alert email is sent to the admin instead of the newsletter.

---

## Quick Start

### 1. Clone or Download
```bash
git clone https://github.com/kabeersmehdi/inflection-point-n8n.git
cd inflection-point-n8n
```

### 2. Create Required Accounts
- [Formspree](https://formspree.io) — form handling
- [Resend](https://resend.com) — email sending
- [n8n](https://n8n.io) — workflow automation
- [Anthropic API](https://console.anthropic.com) — Claude
- Google account — Google Sheets

### 3. Follow the Setup Checklist
See [`setup-checklist.md`](./setup-checklist.md) for the complete step-by-step guide.

### 4. Deploy the Onboarding Form
Upload `onboarding.html` to any static hosting:
- GitHub Pages
- Netlify
- Vercel
- Your own server

---

## Color Palette

| Color | Hex | Usage |
|-------|-----|-------|
| Primary Background | `#0a0a0a` | Page background |
| Card/Surface | `#111318` | Form cards, email containers |
| Primary Accent | `#6c63ff` | Electric violet — buttons, headers |
| Secondary Accent | `#00d4aa` | Mint teal — success, signals |
| Text Primary | `#f0f0f0` | Main text |
| Text Secondary | `#8a8fa8` | Meta text, helpers |
| Divider | `#1e2030` | Borders, rules |
| Badge Background | `#1a1a2e` | Verdict badges |
| Badge Text | `#6c63ff` | Badge text |
| Warning | `#ff6b6b` | Errors, skip verdicts |

---

## RSS Sources

The collector workflow pulls from 8 RSS feeds:

| Source | URL |
|--------|-----|
| Anthropic Blog | https://www.anthropic.com/rss.xml |
| Hugging Face Blog | https://huggingface.co/blog/feed.xml |
| MIT Technology Review | https://www.technologyreview.com/feed/ |
| The Rundown AI | https://www.therundown.ai/rss |
| Product Hunt AI | https://www.producthunt.com/feed?category=artificial-intelligence |
| TechCrunch AI | https://techcrunch.com/category/artificial-intelligence/feed/ |
| Wired AI | https://www.wired.com/feed/tag/artificial-intelligence/latest/rss |

Plus 3 GitHub searches:
- Python repos (stars >100, created last 7 days)
- All AI/ML repos (stars >200, AI topics)
- JavaScript AI repos (topic:ai)

---

## Google Sheets Structure

### Tab: `user_profile`
Stores subscriber data. One row per user.

| Column | Description |
|--------|-------------|
| `first_name` | User's first name |
| `email` | Email address |
| `focus` | What they do |
| `goals` | Comma-separated goals |
| `skill_level` | AI comfort level |
| `tools_using` | Tools they use |
| `industries` | Comma-separated interests |
| `exclude` | Topics to exclude |
| `style_anchor` | Style directive |
| `weekly_context` | **Update weekly** |
| `source` | Signup source |
| `submitted_at` | ISO timestamp |

### Tab: `raw_content`
Auto-populated by collector workflow every Sunday.

| Column | Description |
|--------|-------------|
| `timestamp` | Week identifier |
| `raw_content` | All RSS + GitHub content |
| `source_count` | Number of sources |

### Tab: `send_log`
Auto-populated by newsletter workflow every Monday.

| Column | Description |
|--------|-------------|
| `timestamp` | Send timestamp |
| `user_email` | Recipient email |
| `user_name` | Recipient name |
| `quality_passed` | Boolean |
| `subject` | Email subject |
| `status` | "sent" or "flagged" |

---

## Environment Variables

Set these in n8n (Settings → Environment Variables):

```bash
ANTHROPIC_API_KEY=sk-ant-...
RESEND_API_KEY=re_...
```

---

## Workflow Schedules

| Workflow | Schedule | Description |
|----------|----------|-------------|
| Collector | Sunday 21:00 | Gathers RSS + GitHub data |
| Newsletter | Monday 07:00 | Personalizes and sends |

Both schedules use your n8n instance's local timezone.

---

## Testing

### Manual Test
1. Open `newsletter-workflow.json` in n8n
2. Click "Execute Workflow"
3. Check the execution log
4. Verify the email arrives in your inbox

### What to Verify
- [ ] Email renders correctly in dark mode
- [ ] All sections match your interests
- [ ] Your name is mentioned
- [ ] Exclusions are respected
- [ ] Links work
- [ ] Unsubscribe link is present

---

## Weekly Maintenance

Every Monday morning (before 07:00):

1. Open your Google Sheet
2. Go to `user_profile` tab
3. Update `weekly_context` with what you're working on
4. That's it

Example:
```
"Evaluating AI coding agents for my startup"
"Building a RAG pipeline for document search"
"Researching multimodal models"
```

---

## Troubleshooting

### Collector workflow fails
- Check Google Sheets OAuth credentials
- Verify Sheet ID is correct
- Some RSS feeds may rate-limit

### Newsletter workflow fails
- Verify `ANTHROPIC_API_KEY` is set
- Verify `RESEND_API_KEY` is set
- Check Resend domain is verified

### Quality check always fails
- Ensure Claude returns HTML with `letter-spacing:0.12em`
- Check user's name is being passed
- Verify exclusion list logic

### Email not sending
- Verify Resend domain verification
- Check "from" address uses verified domain
- Ensure `RESEND_API_KEY` is valid

---

## File Structure

```
inflection-point-n8n/
├── README.md
├── setup-checklist.md
├── onboarding.html
├── collector-workflow.json
├── newsletter-workflow.json
└── prompt-template.txt
```

---

## Credits

Built for: **Kabeer** (@kabeersmehdi)
Email: kabeersmehdi@gmail.com

---

## License

MIT — Use it, fork it, build on it.

---

## Sources

- [n8n Documentation](https://docs.n8n.io)
- [Anthropic API Docs](https://docs.anthropic.com)
- [Resend Docs](https://resend.com/docs)
- [Formspree Docs](https://help.formspree.io)
