# Security Policy

## What this plugin does with your data

BizDev Outreach researches a contact and drafts personalized outreach. Read-only against your data sources; produces drafts for you to review and send.

**Reads:**
- **CRM** (any CRM connector authorized in Cowork) — contact records, company records, deal pipeline activity, notes.
- **Email** (Gmail / Outlook, if connected) — threads with the contact for tone and relationship history.
- **Chat** (Slack / Teams, if connected) — relevant canvas content (e.g., this week's outreach prep).
- **Web** (`WebSearch` + web fetch) — recent company news, funding, contact's public posts.
- **Plugin references** — `skills/bizdev-outreach/references/user-context.md` (your positioning, voice rules).
- **Shared user-level config** — `~/Documents/Claude/identity.md`, `~/Documents/Claude/voice.md` (read-only).

**Writes:**
- **Plugin user-context** — `references/user-context.md` (after `/setup`).
- **Drafts** — produced inline in the conversation for your review. Not persisted.

**Does not:**
- **Send any emails or messages on your behalf.** Drafts are review-then-send by the user.
- **Modify CRM records.** Read-only against CRM.
- **Bypass your "should we even send?" filter.** If timing is wrong, the plugin recommends *not* sending — and explicitly does not produce a draft just to satisfy a request.

## Where data lives

- Plugin reference files inside the installed plugin directory (Cowork manages the location).
- Shared identity/voice (read-only) at `~/Documents/Claude/`.
- All other data stays in your authorized connectors.

## What gets sent off your machine

- Whatever your authorized CRM, email, chat, and WebSearch connectors send when invoked. No additional outbound traffic from this plugin.

## Supported versions

| Version | Supported |
|---------|-----------|
| 0.1.x   | Yes       |

## Reporting a vulnerability

Report privately via GitHub Security Advisories:

https://github.com/BrightWayAI/Biz-Dev/security/advisories/new

Do not open a public issue for security concerns. We aim to respond within 5 business days.
