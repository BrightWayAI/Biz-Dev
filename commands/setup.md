---
name: setup
description: >
  Set up or update your BizDev Outreach plugin profile. Collects your company info, personal background, tone preferences, and value-add approaches, then saves them so the outreach skill can personalize every draft to you. Run this on first use or anytime you want to update your info.
---

# BizDev Outreach — Setup

You are running the onboarding flow for the BizDev Outreach plugin. Your job is to collect the user's information through a friendly, conversational series of questions, then write it to the user-context file.

**Important:** Ask ONE question at a time. Wait for each answer before asking the next. Be conversational — this should feel like a quick intake call, not a form.

## Step 0 — Resolve plugin config root

Per-plugin config in this marketplace lives under a user-chosen folder, recorded at `~/.claude-plugin-config-root` (a single-line text file in the user's home directory). Resolve it before doing anything else.

### A — Try the pointer

Call `request_cowork_directory(~)` once if not already granted, then read `~/.claude-plugin-config-root`.

- **Pointer exists**: read line 1 → that's the config root path. Call `request_cowork_directory(<config-root>)` to mount it. Skip to section C.
- **Pointer missing**: continue to section B.

### B — First-time bootstrap

This is the user's first plugin setup of any kind. Prompt:

> "First-time plugin setup. Where should I store your plugin config — identity, voice, and per-plugin settings? Pick a folder you control. Examples: `~/Documents/Claude/` (a common pick — and where cortex memory already writes if you have it installed) or `~/Documents/PluginConfig/` or any other path you prefer. The folder will hold one `identity.md`, one `voice.md`, and a `plugins/` subdirectory with one file per plugin you set up."

Once the user provides the path:

1. Call `request_cowork_directory(<path>)` to mount it.
2. Create `<path>/plugins/` if it doesn't exist.
3. Write the absolute path to `~/.claude-plugin-config-root`.
4. Confirm: "Saved. All marketplace plugin configs will live under `<path>` from now on."
5. **Migration**: if `~/Documents/Claude/identity.md` or `~/Documents/Claude/voice.md` exists and `<path>` is *not* `~/Documents/Claude/`, ask: "Migrate existing identity.md / voice.md into `<path>`? (Y/N)" — if yes, copy.
6. **Pre-staged content**: if any `~/Documents/Claude/plugin-configs/*.user-context.md` files exist, offer to copy them into `<path>/plugins/`.

### C — Read shared identity

Read `<config-root>/identity.md` (canonical identity from cortex's `/setup-identity`).

- **Exists and populated** → pre-fill Question 1 (Company Name), Question 1b (Website), and role/identity follow-ups from those values. The auto-research step still runs so the rest of the interview benefits from fresh website context.
- **Missing** → offer to run `/setup-identity` first (recommended, ~2 min) or proceed inline.

### D — Read shared voice

Read `<config-root>/voice.md` (canonical voice from cortex's `/setup-voice`).

- **Exists and populated** → pre-fill voice-related questions (banned phrases, tone descriptors, sign-off) from those values. Skip those questions; just confirm.
- **Missing** → offer to run `/setup-voice` first or proceed inline.

For the rest of this document, **`<config-root>`** refers to the resolved path. This plugin's config file lives at **`<config-root>/plugins/bizdev-outreach.user-context.md`**.

---

## Step 1: Collect Information

Ask the following questions in order, one at a time. Use AskUserQuestion for each.

### Question 1 — Company Name
"Let's get your outreach profile set up! First — what's your company name?"

### Question 1b — Company Website
"What's your company's website? I'll pull what I can from it to pre-fill some of these answers."

### Auto-Research (Not a Question — Do This Automatically)

Once the user provides their company website, perform the following research before continuing to Question 2:

1. **Use WebFetch** to read the company website's homepage and about page (try both `/` and `/about` or `/about-us` paths).
2. **Use WebSearch** to search for recent news and context about the company (e.g., "[company name] recent news", "[company name] products").
3. Based on what you find, **pre-fill draft answers** for:
   - Positioning statement (Question 2)
   - Products/services (Question 3)
   - Target market (Question 4)
4. For each subsequent question, **present your draft to the user** so they can confirm, edit, or override rather than writing from scratch.

For example, Question 2 becomes: "Based on your website, it looks like your positioning might be: '[draft]'. Does that sound right, or would you phrase it differently?"

If the website is unreachable or doesn't have enough information, fall back to asking the questions normally without drafts.

### Question 2 — Positioning Statement
"Got it. Now give me a one-sentence positioning statement — the 'We help [who] do [what] by [how]' version. Don't overthink it — we can always update this later."

(If auto-research produced a draft, present it: "Based on your website, it looks like your positioning might be: '[draft]'. Does that sound right, or would you phrase it differently?")

### Question 3 — Products or Services
"What are your key products or services? Give me 2-5, each with a name and a one-line description. For example:
- **Acme Analytics**: Dashboard platform for marketing teams
- **Acme Connect**: Integration middleware for enterprise data pipelines"

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
`<config-root>/plugins/bizdev-outreach.user-context.md`

Use this format:

```markdown
# User Context — BizDev Outreach Plugin

> Last updated: [current date]

---

## Company

**Name:** [their answer]

**Website:** [their answer]

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

"Your outreach profile has been saved. Here's a summary of what I captured:

- **Company:** [name] — [positioning]
- **Default tone:** [tone]
- **Value-adds:** [count] approaches configured
- **Custom bans:** [count or 'none']

Now let me check your tool connections..."

## Step 4: Connect Your Tools

Check which MCP tools are available in the current environment and report the status of each connector category:

### Email
Check for email tools (look for tools like `gmail_search_messages`, `gmail_read_message`, `gmail_read_thread`, or Outlook equivalents).
- **If found:** Report it — e.g., "Email: Gmail connected"
- **If not found:** Tell the user: "I don't see an email connector (Gmail or Outlook) set up. To get the most out of this plugin, you'll want to connect one. You can add a Gmail or Outlook MCP server in your Claude Code settings or Cowork workspace."

### CRM
Check for CRM tools (look for tools like `search_crm_objects`, `get_crm_objects`, `manage_crm_objects`, or Salesforce/Pipedrive/Close equivalents).
- **If found:** Report it — e.g., "CRM: HubSpot connected"
- **If not found:** Tell the user: "I don't see a CRM connector (HubSpot, Salesforce, Pipedrive, or Close) set up. Adding one lets the plugin check your pipeline and contact records automatically."

### Chat
Check for chat tools (look for tools like `slack_read_canvas`, `slack_search_public`, or Microsoft Teams/Discord equivalents).
- **If found:** Report it — e.g., "Chat: Slack connected"
- **If not found:** Tell the user: "I don't see a chat connector (Slack, Microsoft Teams, or Discord) set up. This is optional — it's used to check your weekly outreach canvas for holding patterns."

### Wrap-Up

End the connector check with:

"The plugin works best with all three connectors, but email + CRM covers most use cases. You can add connectors anytime through your MCP server settings.

You're all set! Run `/setup` again anytime to update your profile or recheck connectors. Now try reaching out to someone — just say a contact's name and I'll handle the rest."
