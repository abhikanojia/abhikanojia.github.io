# YouTube scripts — internal

This directory holds SparkRails YouTube production scripts. The leading underscore makes Jekyll ignore the directory at build time, so nothing here ships to sparkrails.com.

## Conventions

- `NN-slug.md` — one file per video, numbered in release order.
- Each script includes: production notes, scene-by-scene VO + on-screen cues, end-screen cards, YouTube description template, pinned comment, and pronunciation reminders for ElevenLabs.

## Current scripts

- [01-channel-trailer.md](01-channel-trailer.md) — Channel trailer (~75 sec). Pinned at top of channel.
- [02-rails-6-to-7-upgrade-checklist.md](02-rails-6-to-7-upgrade-checklist.md) — Episode 1. 10–11 min. Maps to `/rails-upgrade-consultant/`.

## Brand voice + audio kit

**Voice choice (ranked):**

1. **Best — Clone your own voice.** ElevenLabs Instant Voice Cloning (Creator plan, $22/mo). 60-second one-time recording, then your voice becomes a regular voice in your library. Faceless videos rendered in your actual voice — identity matches the brand, name pronunciation perfect, no ongoing voice work after the initial 60-sec sample.
2. **Indian English narrator.** Browse the community library filtered by `language: English`, `accent: Indian`, `category: Narration`. Or use Voice Design (Pro plan) with the prompt: *"Indian English male, late 30s, calm and senior, slow narrator pacing, no upbeat energy."* Locks brand consistency without recording yourself.
3. **Brand-first scripts + any voice.** All scripts in this directory are written voice-neutral — no first-person identity claims like "I'm [name]" in VO. Name appears in on-screen text only. This means even an American voice like Brian works without breaking the brand, though it's not optimal.

**Avoid:** American/British voices delivering first-person identity claims about an Indian-named consultant. The accent/name mismatch reads as off.

**Settings (any voice):** Eleven Multilingual v2, Stability 60%, Similarity 80%, Style 10%, Speaker Boost ON. Skip Eleven v3 — too expressive for technical content.
- **Music kit:** 3 signature tracks reused across all videos.
  - Track A — intro/outro signature (ambient minimal, slight lift)
  - Track B — main content bed (pure ambient drone, sits flat)
  - Track C — outro/CTA signature (resolves Track A's mood)
- **Audio mixing:** VO at -3 to -6 dB peak, music at -28 to -32 dB under VO, music sidechain-ducked when VO plays.

## Companion fake-asset scripts

For the "real terminal logs" and "fake APM dashboard" shots used in the trailer (and most future videos), see the helper scripts (to be added under `_youtube_tools/` or similar).
