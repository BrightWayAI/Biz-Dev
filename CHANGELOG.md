# Changelog

All notable changes to bizdev-outreach are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/). Versions match `plugin.json`.

## [Unreleased]

### Changed
- Phase 1 (Research) now delegates to the `contact-researcher` subagent (in `lead-engine`) instead of doing inline CRM/email/web pulls. Cleaner conversation context; consistent dossier shape. Falls back to inline research if `lead-engine` isn't installed.
- Confidence-aware delegation: pauses on Low confidence and asks the user for context before drafting, rather than producing thin output silently.
- Setup reads `~/Documents/Claude/identity.md` and `~/Documents/Claude/voice.md` (shared config from cortex) when available; skips identity/voice questions in the interview.

## [0.1.0] — Initial release

### Added
- Three-phase methodology: Research → Analyze & Recommend → Draft.
- Seven communication types (cold, warm intro follow-up, active deal follow-up, value-add re-engagement, congratulatory, referral request, post-meeting).
- "Should we even send?" filter — recommends waiting when timing is wrong rather than drafting for the sake of drafting.
- Pending thread analysis to identify the actual open commitment, not just the topic.
- Tone-mirroring against prior correspondence.
- Strict writing rules with banned phrase list ("just checking in", "circling back", etc.) and concise length caps.
- Setup interview captures company positioning, products/services, target market, voice, value-add approaches, custom banned phrases.
