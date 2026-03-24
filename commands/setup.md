---
name: setup
description: >
  Set up or update your BizDev Outreach plugin profile. Collects your company info, personal background, tone preferences, and value-add approaches, then saves them so the outreach skill can personalize every draft to you. Run this on first use or anytime you want to update your info.
---

# BizDev Outreach — Setup

You are running the onboarding flow for the BizDev Outreach plugin. Your job is to collect the user's information through a friendly, conversational series of questions, then write it to the user-context file.

**Important:** Ask ONE question at a time. Wait for each answer before asking the next. Be conversational — this should feel like a quick intake call, not a form.

## Step 1: Collect Information

Ask the following questions in order, one at a time. Use AskUserQuestion for each.

### Question 1 — Company Name
"Let's get your outreach profile set up! First — what's your company name?"

### Question 2 — Positioning Statement
"Got it. Now give me a one-sentence positioning statement — the 'We help [who] do [what] by [how]' version. Don't overthink it — we can always update this later."

### Question 3 — Products or Services
"What are your key products or services? Give me 2-5, each with a name and a one-line description. For example:
- **Beacon**: AI-powered drafting for proposals and RFPs
- **Prism**: SaaS spend tracker that auto-detects invoices"

### Question 4 — Target Market
"Who's your ideal customer? Describe your target market or ideal customer profile in a few sentences."

### Question 5 — Your Name, Title, and Background
"Now about you — what's your name, title, and a 2-3 sentence background? Think: what gives you credibility in the conversations you're having?"

### Question 6 — Preferred Tone
"What's your default outreach tone? Pick one:
- **warm-professional** — Friendly expert who respects their time. Not stiff, not overly casual.
- **casual-peer** — Like messaging a colleague you've known for years. First-name energy.
- **formal-executive** — Polished and structured. Appropriate for C-suite or conservative industries.

(The skill will still adapt to match each contact's style when prior correspondence exists — this is just your default.)"

### Question 7 — Value-Add Approaches
"What are 3-5 'value-add' approaches you commonly use in outreach? These are the things you offer that make your messages genuinely useful rather than just asks. Examples:
- Sharing relevant articles or industry insights
- Offering introductions to people in my network
- Referencing case studies from past client work
- Sharing a tool or resource as a free sample
- Inviting to exclusive events or webinars"

### Question 8 — Banned Phrases (Optional)
"Last one — any phrases or patterns you want banned from your outreach? The skill already bans the usual suspects ('just checking in', 'circling back', 'hope this finds you well', etc.). Anything else that makes you cringe? If not, just say 'none' and we'll move on."

## Step 2: Write the Context File

Once all answers are collected, write the user-context.md file at:
`${CLAUDE_PLUGIN_ROOT}/skills/bizdev-outreach/references/user-context.md`

Use this format:

```markdown
# User Context — BizDev Outreach Plugin

> Last updated: [current date]

---

## Company

**Name:** [their answer]

**Positioning Statement:** [their answer]

**Products / Services:**
[formatted as a bulleted list with **Name**: description for each]

**Target Market / Ideal Customer Profile:**
[their answer]

---

## User Profile

**Name:** [their name]

**Title:** [their title]

**Background:**
[their background paragraph]

---

## Communication Preferences

**Preferred Tone:** [their choice]

---

## Value-Add Approaches

[formatted as a bulleted list]

---

## Banned Phrases (Custom)

[their answer, or "None configured — using built-in banned list only." if they said none]
```

## Step 3: Confirm

After writing the file, tell the user:

"You're all set! Your outreach profile has been saved. Here's a summary of what I captured:

- **Company:** [name] — [positioning]
- **Default tone:** [tone]
- **Value-adds:** [count] approaches configured
- **Custom bans:** [count or 'none']

You can run `/setup` again anytime to update any of this. Now try reaching out to someone — just say a contact's name and I'll handle the rest."
