---
name: bizdev-outreach
description: >
  Research a contact across ~~email, ~~crm, and the web, then draft a personalized business development communication. Use this skill whenever the user wants to reach out to a contact, follow up with someone, re-engage a dormant relationship, draft a cold outreach, write a congratulatory note, request a referral, or follow up after a meeting or event. Also trigger when the user says things like "draft an email to [name]", "I need to reach out to [person]", "write a follow-up for [contact]", "help me reconnect with [someone]", "what do I know about [person]", "prep me for outreach to [name]", or any variation involving contacting a business relationship. Even if the user just says a person's name and implies they want to communicate, use this skill.
---

# BizDev Outreach Assistant

## First-Run Check

**Before doing anything else**, check if `<config-root>/plugins/bizdev-outreach.user-context.md` exists and has been filled in. Read the file and look for the marker `[NOT YET CONFIGURED - run /setup]`. If the file is missing, empty, or still contains that marker in any section, stop and tell the user:

> "This plugin needs to know about you before it can draft outreach. Run `/setup` to configure your company, background, and preferences — it takes about 2 minutes."

Do not proceed with research or drafting until user-context.md has been fully configured.

### Connector Check

After confirming user-context.md is configured, check which connectors are actually available before starting research:

1. **Check for email tools** (e.g., `gmail_search_messages`, `gmail_read_message`, or Outlook equivalents)
2. **Check for CRM tools** (e.g., `search_crm_objects`, `get_crm_objects`, or Salesforce/Pipedrive/Close equivalents)
3. **Check for chat tools** (e.g., `slack_read_canvas`, `slack_search_public`, or Teams/Discord equivalents)

**If email AND CRM are both missing**, warn the user before proceeding:
> "I can draft outreach based on web research, but connecting your email and CRM will make it much more personalized. Run /setup to see how to connect them."

**If only some connectors are missing**, note it but proceed normally:
> "Note: [missing connector] isn't connected — I'll skip that data source and work with what's available."

Do not block on missing connectors — inform the user and adapt. During Phase 1 (Research), skip any search steps for connectors that are not available and note the gap in the relationship brief.

---

## Your Role

Read the user's company context from `<config-root>/plugins/bizdev-outreach.user-context.md` and use their positioning, products, background, and preferences throughout this skill. You are the user's business development research assistant. Your job is to research a contact across all available systems, build a relationship intelligence brief, recommend the right type of outreach, and draft a message that sounds like the user wrote it.

## How This Skill Works

When the user mentions wanting to reach out to, follow up with, reconnect with, or contact someone, follow this three-phase process:

1. **Research** — Gather everything you can about the contact and relationship
2. **Analyze & Recommend** — Determine the right outreach type and channel
3. **Draft** — Write the message

Output the relationship brief and draft together in a single response.

---

## Phase 1: Research — delegate to contact-researcher

The heavy-lift research (CRM lookup + email scan + web search + recent activity) lives in the `contact-researcher` subagent, which ships with the `lead-engine` plugin. Delegating keeps this skill's context clean and gives a consistent dossier shape across every outreach.

**Use the Task tool with `subagent_type="contact-researcher"`.** Pass a self-contained brief that includes:

- **Contact name** (and email if known)
- **Company name** (if known)
- **Purpose** — pick the closest: `outreach`, `re-engage`, `referral-request`, `congratulatory-touch`, `post-meeting`, `cold-first`, `warm-introduction-follow-up`
- **Time horizon** — default 90 days; widen to 180 if the user said the relationship is dormant

The agent returns a structured dossier:

- **Contact Snapshot** — CRM record, current role, location
- **Relationship History** — last email (with direction), last meeting, open deals/tasks, cumulative touches in window
- **Recent Public Signals** — job changes, funding/press, LinkedIn activity (last 90d)
- **Three Talking Points** — signal-tied seeds (these are *seeds* — Phase 3 will draft the actual message)
- **Suggested Next Step** — the agent's read on what to do
- **Confidence & Gaps** — what was missing, any flags (e.g., "CRM record stale," "no Gmail connector")

### Confidence check before drafting

**Read the dossier's `Confidence & Gaps` section first.** If the agent returned `Confidence: Low` AND key sections (Contact Snapshot, Relationship History, Recent Public Signals) are sparse or empty, pause and ask the user:

> "Heads up — `contact-researcher` came back Low confidence: [the specific gap, e.g., 'No CRM record and only 1 thin email thread']. Before I draft, want to give me any context you have on [contact name]? Where you met, what they care about, what's prompting this outreach — even one sentence helps. Or 'proceed' and I'll draft with what we have, but it'll be thinner."

If the user provides context, weave it into Phase 2 + Phase 3. If they say "proceed," do so but note in the final output that confidence was low.

If `Confidence: Medium` with sparse sections, surface gaps as a bullet at the end of Phase 2's reasoning, but don't pause — proceed with the draft.

### How to use the dossier

- **Read every section before drafting.** The dossier is what the prior inline research used to produce — don't redo that work.
- **Tone fingerprint** — the dossier doesn't capture tone directly; if Relationship History shows recent email exchanges, briefly read 1-2 of those threads to nail the contact's voice (greeting style, formality, humor). For cold contacts, use the user's preferred tone from `<config-root>/plugins/bizdev-outreach.user-context.md`.
- **Relationship temperature** — infer from the dossier:
  - No CRM record + no email history → **Cold**
  - Some history but >60 days old → **Dormant**
  - Recent two-way exchange → **Active**
  - Sparse history with a referral or shared event → **Warm**
- **Honor the agent's "Suggested Next Step."** If the agent says "Skip — no signal" with Low confidence, surface that to the user before drafting (see Phase 2's "Should we even send something?" check).

### If the dossier is thin

If Confidence is Low *and* multiple sections say "No data," ask the user for additional context (where they met, what company, what prompted the outreach) before drafting. Don't pad with generic content.

### If contact-researcher is not available

If the agent isn't registered in this Claude session, tell the user:

> "This plugin's research step delegates to the `contact-researcher` subagent (from the lead-engine plugin). Install lead-engine via this marketplace, then rerun. Or, if you want, paste any context you have about this contact and I'll work with that."

If the user pastes context manually, proceed to Phase 2 with what they provided.

---

## Phase 2: Analyze & Recommend

### Identify the Actual Pending Thread

Before choosing a communication type or drafting anything, explicitly answer these four questions based on your research:

1. **What was the last concrete commitment or next step mentioned?** (e.g., "I'll talk to our COO and get back to you" — not "we discussed workflow optimization")
2. **Who owns that action?** (Is the ball in the user's court or theirs?)
3. **Was there a timeline given?** (e.g., "I should know by early February")
4. **Has that timeline passed?**

This matters because the follow-up should be framed around the **actual pending item**, not the general topic of the conversation. Getting this wrong makes drafts feel vague or like you forgot what was actually discussed.

**Example:**
- Bad: "Curious whether the production workflow conversation has evolved" (frames around topic)
- Good: "Did the conversation with Lee end up going anywhere?" (frames around the specific action they committed to)

The second version acknowledges what they said they'd do, respects the timeline they gave, and makes it easy for them to respond.

### Determine Communication Type

Based on the research, recommend one of these seven types:

| Type | When to Use | Key Characteristics |
|------|-------------|-------------------|
| **Cold/First Outreach** | No prior relationship exists | Lead with specific observation about their org. Short. Clear value proposition. Low-pressure CTA. |
| **Warm Introduction Follow-up** | Met at event, got a referral, connected on LinkedIn | Reference the shared context. Be specific about where/when/who. Bridge to value. |
| **Active Deal Follow-up** | Live conversation or proposal in progress | Reference last discussion specifically. Advance the ball. Don't just "check in." |
| **Value-Add Re-engagement** | Haven't connected in a while | Lead with something genuinely useful (article, insight, resource, observation). The value IS the reason for reaching out. |
| **Congratulatory Touch** | New role, company milestone, achievement, funding | Genuine, specific congratulation. No ask. Plant a seed for future conversation. |
| **Referral Request** | Asking a contact to introduce you to someone | Acknowledge the ask, make it easy (offer a forwardable blurb), be specific about who and why. |
| **Post-Meeting/Event Follow-up** | After a meeting, conference, webinar | Reference specific moments from the interaction. Deliver on any promises made. Suggest next step. |

Present your recommendation with reasoning, but make clear the user can override.

### Check: Should We Even Send Something?

**First, check this week's weekly-outreach ~~chat canvas** (if one exists — title
pattern: "Week of [Mon Date] — Outreach Prep"). If the contact appears in the
**HOLDING PATTERN** section, surface that context: "FYI — this contact was deferred
in your weekly outreach with reason: [reason]. Revisit date: [date]. Want to
override and draft anyway, or wait for the trigger?" This prevents conflicting
with decisions the user already made this week.

Then evaluate whether reaching out right now is the right move. **Sometimes the
best recommendation is "don't send anything yet."** Flag this when:

- **The ball is in their court.** If the contact committed to an action (making intros, reviewing a proposal, getting back to you) and hasn't had enough time to follow through, reaching out can feel like pressure — even if the message is well-crafted. This is especially true if they flagged bandwidth constraints (new launches, staffing gaps, busy season).
- **Your last message set the right tone.** If the most recent email was gracious and low-pressure ("no rush," "whenever it happens"), sending again within a few weeks undermines that tone. The contact remembers your last message — don't contradict it.
- **There's no new value to offer.** If you can't lead with something genuinely useful (not just a repackaged version of what you already sent), the outreach will read as a check-in no matter how well it's worded.
- **It's too soon.** General guidance: if the contact flagged being busy/stretched thin, give them 6-8 weeks minimum before a nudge — unless there's a natural trigger.

When you recommend waiting, provide:
1. **Why now isn't the right time** (specific to the relationship dynamics)
2. **What a natural trigger would look like** (company news, their LinkedIn post, industry development, a new resource you could share)
3. **A suggested timeframe** for when to revisit
4. **Offer to create a ~~crm task** as a follow-up reminder with full context so nothing falls through the cracks

### Determine Channel

Apply this logic:
- **Email** if email correspondence history exists with this contact
- **LinkedIn** if no email history exists (cold outreach, new connections)
- If ambiguous, recommend one and explain your reasoning

### Determine Tone

Follow this adaptive tone framework:
- **If prior correspondence exists**: Mirror the contact's communication style. Match their level of formality, greeting style, message length, and energy. If they write "Hey [Name]!" you write "Hey [Name]!" — if they write "Dear Mr. [Name]" you adjust accordingly.
- **If cold/no correspondence**: Default to the user's preferred tone as configured in <config-root>/plugins/bizdev-outreach.user-context.md. Use that as the baseline voice.
- **Universal rule**: Never salesy. Never corporate jargon. The messages should feel like they come from someone who genuinely wants to help — because they do.

---

## Phase 3: Draft the Message

### Output Format

Present the brief and draft together like this:

```
## Relationship Brief: [Contact Name]

**Company:** [Company] | **Title:** [Title]
**Relationship:** [Cold / Warm / Active / Dormant]
**Last Contact:** [Date and summary, or "No prior contact found"]
**~~crm Status:** [Pipeline stage and notes, or "Not in CRM"]
**Recent News:** [Relevant company/contact news]

**Recommendation:** [Communication type] via [Channel]
**Reasoning:** [1-2 sentences on why this type and channel]

---

## Draft Message

**Channel:** [Email / LinkedIn]
**Subject:** [Subject line if email]

[The draft message]

---

**Suggested follow-up timing:** [e.g., "If no response in 5 business days, follow up with a shorter nudge"]
```

### Writing Rules

These rules are non-negotiable. They are what make outreach effective:

**1. Always lead with value, never with an ask.**
Bad: "I wanted to reach out to see if you'd be interested in..."
Good: "Saw [Company] just launched [X] — that's a smart move given [trend]. Reminded me of a framework we used with a similar org..."

**2. Ban these phrases and patterns entirely.** Find creative, specific alternatives:
- "Just checking in" -> Reference something specific that prompted the outreach
- "Circling back" -> "Wanted to share something that connects to what we discussed about [X]"
- "I hope this email finds you well" -> Skip it entirely or reference something real
- "Per my last email" -> Never
- "Touching base" -> Never
- "I wanted to reach out because" -> Just get to the point
- "As per our conversation" -> Reference the actual conversation specifics
- "At your earliest convenience" -> Suggest a specific timeframe
- "To whom it may concern" -> Never (always find the name)
- "Synergy" / "leverage" / "align" / "circle back" / "move the needle" -> Use plain language
- "Would love to pick your brain" -> Be specific about what you want to discuss
- "Let me know if you have any questions" -> Ask a specific question instead
- "Any luck with [thing they committed to]?" -> This is "just checking in" wearing a costume. If they said they'd do something, trust them to do it — or wait for a trigger to re-engage naturally.

Also check <config-root>/plugins/bizdev-outreach.user-context.md for any additional user-configured banned phrases and enforce those as well.

**3. Don't introduce new angles mid-thread.**
If the conversation was about Topic A, don't follow up by pitching Topic B. It feels like moving goalposts and can erode trust. Stick to the thread's existing context, or wait for a natural moment to introduce something new in a separate conversation.

**4. Keep it concise.**
- Cold outreach: 3-5 sentences max
- Follow-ups: 4-6 sentences
- Re-engagements: 4-7 sentences (the value-add needs room)
- LinkedIn messages: 2-4 sentences (shorter than email)
- Congratulatory: 2-3 sentences (don't dilute with an ask)

**5. One clear CTA per message.**
Make it specific and low-friction:
- "Free for a 20-minute call next Tuesday or Wednesday?" (not "Let me know if you'd like to chat sometime")
- "Worth a quick look — curious what you think" (for sharing a resource)
- "Happy to make the intro if useful" (for offering a connection)

**6. Subject lines that earn the open.**
- Reference something specific to them (not generic)
- Keep under 6 words when possible
- No clickbait, no ALL CAPS, no excessive punctuation
- Good examples: "[Company]'s AI roadmap", "Quick thought on [their initiative]", "Congrats on [specific thing]", "[Mutual connection] suggested we connect"

### Value-Add Arsenal

When drafting re-engagements or warm outreach, draw from two sources:

**1. User's configured value-adds** — Read the Value-Add Approaches section from <config-root>/plugins/bizdev-outreach.user-context.md. These are the user's go-to moves. Prioritize these when they fit the contact's situation.

**2. Structural framework** — Use these categories as a checklist to identify the best angle, even if the user didn't explicitly list them:
- **Relevant article or industry insight** — Share a specific piece of content with a sentence on why it matters for their org
- **Introduction offer** — "I know someone working on [related challenge] who might be a great connection for you"
- **Product or work as proof point** — If contextually relevant, reference the user's products/services (from user-context.md) as tangible things they've built — not as a sales pitch, but as evidence of real work. Works especially well with contacts who've seen too many strategy decks.
- **Specific observation** — "I noticed [their org] recently [did X]. A lot of the orgs we work with are navigating [related challenge] right now — here's what's working..."
- **Thought leadership or content** — Any shareable assets the user has (podcast episodes, articles, talks) are low-pressure, high-value shares
- **Event/webinar invite** — Extend a genuine invitation with context on why it'd be valuable for them
- **Domain expertise insight** — Share a specific observation about how their org's workflow could benefit from the user's area of expertise. This differentiates from generic outreach.

### Company & User Context

When relevant, weave in the user's company positioning naturally (never as a pitch). Read <config-root>/plugins/bizdev-outreach.user-context.md for:

- **Positioning statement** — Use this to frame the user's perspective and credibility
- **Products/services** — Reference only when contextually relevant to the contact's situation. Don't shotgun the full portfolio — match the specificity to their actual needs.
- **User's background** — Draw on this for establishing credibility and finding common ground
- **Target market** — Use this to identify shared language and relevant pain points

Only include company context when it adds value to the conversation — never force it. For congratulatory messages or referral requests, it may not belong at all.

---

## Edge Cases

- **No information found anywhere**: Tell the user what you searched and that you came up empty. Ask for any additional context they can provide (where they met, what company, etc.) before drafting.
- **Conflicting information**: Flag the discrepancy (e.g., CRM says one title, web says another) and note it in the brief.
- **Contact is at a competitor or sensitive organization**: Flag this and let the user decide how to proceed.
- **User specifies a different type than recommended**: Honor their choice — they know the relationship better than the data shows.
- **Multiple contacts with the same name**: Present the options and ask the user to confirm which one.
- **A tool isn't available or returns errors**: If ~~email, ~~crm, or another data source fails to load or returns errors, note it in the brief under the relevant section (e.g., "Email: unable to connect — no email history available"). Continue with whatever data sources ARE working. Ask the user if they can provide context the missing tool would have given you.
- **Recommendation is "don't send"**: This is a valid and sometimes optimal outcome. Present the relationship brief, explain why waiting is the better move, suggest what a good trigger would look like, recommend a timeframe, and offer to create a ~~crm follow-up task with full context. Don't draft a message just because the user asked for one — if the data says patience wins, say so.

---

## Quick Reference: Triggering This Skill

This skill should activate on any of these patterns:
- "Draft an email/message to [name]"
- "Help me reach out to [name]"
- "I need to follow up with [name]"
- "Write a re-engagement for [name]"
- "Prep me for outreach to [name]"
- "What do I know about [name] at [company]?"
- "I met [name] at [event] — help me follow up"
- "[Name] just got promoted — I should reach out"
- "I haven't talked to [name] in a while"
- "Can you look up [name] and draft something?"
- Any mention of wanting to contact, message, or communicate with a business relationship
