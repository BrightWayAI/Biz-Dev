# BizDev Outreach Plugin

A Claude Code plugin (compatible with Cowork) that researches contacts across your email, CRM, and the web, then drafts personalized business development communications using a proven three-phase methodology.

## What It Does

When you mention wanting to reach out to someone — by name, company, or situation — this plugin:

1. **Researches** the contact across your email history, CRM records, and the web
2. **Analyzes** the relationship to recommend the right outreach type, channel, and tone
3. **Drafts** a message that sounds like you wrote it

It handles seven communication types: cold outreach, warm introduction follow-ups, active deal follow-ups, value-add re-engagements, congratulatory touches, referral requests, and post-meeting follow-ups. It also knows when *not* to send — sometimes patience is the better move.

## Setup

### 1. Install the Plugin

#### From Cowork Marketplace
Search for "bizdev-outreach" in the Cowork plugin marketplace and click Install.

#### Manual Installation
Clone this repo into your project's plugins directory, or add it as a git submodule:

```bash
git clone https://github.com/BrightWayAI/Biz-Dev.git bizdev-outreach-plugin
```

### 2. Configure Connectors

This plugin uses three connectors that map to your tools:

| Connector | Options |
|-----------|---------|
| `~~email` | Gmail, Outlook |
| `~~crm` | HubSpot, Salesforce, Pipedrive, Close |
| `~~chat` | Slack, Microsoft Teams, Discord |

See [CONNECTORS.md](CONNECTORS.md) for details.

### 3. Run `/setup`

On first use, the plugin will prompt you to run `/setup`. This collects:

- **Company name** and one-sentence positioning statement
- **Products/services** (2-5 with descriptions)
- **Target market** / ideal customer profile
- **Your name, title, and background** (what gives you credibility)
- **Preferred outreach tone** (warm-professional, casual-peer, or formal-executive)
- **Value-add approaches** (3-5 things you offer that make outreach genuinely useful)
- **Custom banned phrases** (on top of the built-in banned list)

This information is stored locally in `skills/bizdev-outreach/references/user-context.md` and used to personalize every draft.

## Usage

Just mention a contact naturally:

- "Draft an email to Sarah Chen"
- "I need to follow up with Marcus at Acme Corp"
- "Help me reconnect with James — haven't talked to him in months"
- "Lisa Park just got promoted — I should reach out"
- "I met David at the conference last week, help me follow up"
- "What do I know about Rachel at TechCo?"

The plugin handles research, analysis, and drafting in a single response.

## What Makes This Different

The core methodology is opinionated and battle-tested:

- **"Should we even send?" logic** — Recommends waiting when the timing isn't right, rather than drafting for the sake of drafting
- **Pending thread analysis** — Identifies the actual open thread (not just the topic) so follow-ups reference specific commitments
- **Tone mirroring** — Matches each contact's communication style based on prior correspondence
- **Strict writing rules** — Bans generic phrases ("just checking in", "circling back"), enforces conciseness, requires value-first framing
- **Communication type taxonomy** — Seven distinct outreach types with specific guidelines for each

## Companion plugins

The plugin works on its own, but pairs naturally with others in the [BrightWay AI marketplace](https://github.com/BrightWayAI/nucleus):

- **[lead-engine](https://github.com/BrightWayAI/lead-engine)** — provides the `contact-researcher` subagent. When installed, the bizdev-outreach Phase 1 (research) delegates to that agent instead of doing inline CRM/email/web pulls. Cleaner conversation context and a more consistent dossier shape.
- **[claude-cortex](https://github.com/BrightWayAI/claude-cortex)** — when installed, prior context about the contact (gotchas, prior commitments, relationship history) auto-surfaces during drafting.
- **[weekly-outreach](https://github.com/BrightWayAI/weekly-outreach)** — for batched weekly BD prep. bizdev-outreach is for ad-hoc per-contact drafting; weekly-outreach is for the full weekly queue.

Without these, the plugin runs inline research using whatever connectors you have. With them, it's faster and the output is sharper.

## Customizing Later

Run `/setup` again anytime to update your profile. You can also directly edit `skills/bizdev-outreach/references/user-context.md` if you prefer.

## File Structure

```
bizdev-outreach-plugin/
  .claude-plugin/
    plugin.json          # Plugin manifest
  skills/
    bizdev-outreach/
      SKILL.md           # The outreach methodology (core skill)
      references/
        user-context.md  # Your personalized profile (created by /setup)
  commands/
    setup.md             # Onboarding flow
  CONNECTORS.md          # Connector documentation
  README.md              # This file
```
