# Connectors — BizDev Outreach Plugin

This plugin uses three connector placeholders that map to your specific tool stack. Configure these when installing the plugin.

---

## `~~email`

**Purpose:** Email service used for researching correspondence history with contacts.

**Options:**
| Option | MCP Server / Tool |
|--------|------------------|
| **Gmail** | Gmail MCP (`gmail_search_messages`, `gmail_read_message`, `gmail_read_thread`) |
| **Outlook** | Outlook/Microsoft Graph MCP |

**Used in:** Phase 1 (Research) — searching for prior emails, analyzing tone and communication style, identifying last touchpoint and open threads.

---

## `~~crm`

**Purpose:** CRM platform used for contact records, company data, deal pipeline status, and logged activities.

**Options:**
| Option | MCP Server / Tool |
|--------|------------------|
| **HubSpot** | HubSpot MCP (`search_crm_objects`, `get_crm_objects`, `manage_crm_objects`) |
| **Salesforce** | Salesforce MCP |
| **Pipedrive** | Pipedrive MCP |
| **Close** | Close CRM MCP |

**Used in:** Phase 1 (Research) — looking up contact/company records, deal stages, notes, logged calls and meetings. Phase 2 (Analyze) — checking weekly outreach canvas, creating follow-up tasks. Phase 3 (Draft) — populating the relationship brief.

---

## `~~chat`

**Purpose:** Team chat platform used for checking weekly outreach prep canvases (holding patterns, deferred contacts).

**Options:**
| Option | MCP Server / Tool |
|--------|------------------|
| **Slack** | Slack MCP (`slack_read_canvas`, `slack_search_public`) |
| **Microsoft Teams** | Microsoft Teams MCP |
| **Discord** | Discord MCP |

**Used in:** Phase 2 (Analyze) — checking if a contact appears in a weekly outreach canvas's "HOLDING PATTERN" section before drafting.

---

## How Connectors Work

When you install this plugin and configure your connectors, the `~~placeholder` references in the skill are resolved to the specific MCP tools for your stack. For example:

- If you set `~~email = Gmail`, the skill's email search instructions will use Gmail MCP tools
- If you set `~~crm = Salesforce`, the CRM lookup instructions will use Salesforce MCP tools

This allows the same outreach methodology to work regardless of your specific tool stack.

---

## Checking Connector Status

Run `/setup` to see which connectors are currently detected in your environment. The setup flow includes a "Connect Your Tools" step that automatically checks for available email, CRM, and chat MCP tools and reports what's connected and what's missing. You can re-run `/setup` anytime to recheck your connector status after adding new MCP servers.
