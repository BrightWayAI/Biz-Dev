# Changelog

All notable changes to bizdev-outreach are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/). Versions match `plugin.json`.

## [0.2.3] — Platform-agnostic Step 0 (2026-05-12)

### Changed
- **Setup command Step 0 now platform-agnostic.** Every `request_cowork_directory(...)` call is conditional: "In Cowork, call `request_cowork_directory(...)`. In Claude Code (or any environment with direct filesystem access), no mount is needed." Same plugin source works in both runtimes.

### Why this matters
Phase 0 of SECOND-BRAIN-V2-SPEC. Removes the implicit Cowork-only assumption so Claude Code users do not hit unsupported tool calls during setup.

## [0.2.0] — Config-root refactor + delegation features

### Changed (config-root refactor)
- **Plugin config moved to a user-chosen folder.** Reads and writes now go to `<config-root>/plugins/bizdev-outreach.user-context.md`, where `<config-root>` is the folder the user chooses on first plugin setup (recorded at `~/Documents/.claude-plugin-config-root`). The old `${CLAUDE_PLUGIN_ROOT}/skills/bizdev-outreach/references/user-context.md` path failed silently under Cowork's read-only mount.
- **`/setup` gets Step 0** — resolves the config root via the pointer; prompts for it on first run; reads shared identity and voice; offers to migrate legacy `~/Documents/Claude/identity.md` / `voice.md` and any pre-staged plugin configs.
- **Operating skill updated** — `skills/bizdev-outreach/SKILL.md` reads from the new path.
- **User-facing prompts and skill descriptions debranded** for fork-friendliness.

### Changed (previously in-flight)
- Phase 1 (Research) now delegates to the `contact-researcher` subagent (in `lead-engine`) instead of doing inline CRM/email/web pulls. Cleaner conversation context; consistent dossier shape. Falls back to inline research if `lead-engine` isn't installed.
- Confidence-aware delegation: pauses on Low confidence and asks the user for context before drafting, rather than producing thin output silently.

## [0.1.0] — Initial release

### Added
- Three-phase methodology: Research → Analyze & Recommend → Draft.
- Seven communication types (cold, warm intro follow-up, active deal follow-up, value-add re-engagement, congratulatory, referral request, post-meeting).
- "Should we even send?" filter — recommends waiting when timing is wrong rather than drafting for the sake of drafting.
- Pending thread analysis to identify the actual open commitment, not just the topic.
- Tone-mirroring against prior correspondence.
- Strict writing rules with banned phrase list ("just checking in", "circling back", etc.) and concise length caps.
- Setup interview captures company positioning, products/services, target market, voice, value-add approaches, custom banned phrases.
