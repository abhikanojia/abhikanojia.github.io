# Channel Trailer — "Production Rails. Explained."

**Target length:** ~75 seconds
**Purpose:** Pinned at top of channel. Sets expectations, filters audience, drives subscribe + the consulting page.
**Voice:** Voice-neutral script. Works with any ElevenLabs voice. Recommended path: clone your own voice (Instant Voice Cloning, 60-sec sample, Creator plan) so the brand voice matches the actual consultant. Fallback: Indian English narrator from the community library or Voice Design.
**Model / settings:** Eleven Multilingual v2, Stability 60%, Similarity 80%, Style 10%, Speaker Boost ON
**Music:** Track A (intro/outro signature, ambient minimal, ~-30 dB under VO)

---

## Production notes

- Output at 1080p minimum, 30fps. Code zoomed to ~140% so it's readable on phones.
- Use a dark editor theme (One Dark Pro or similar).
- Add 200–300ms of silence at the start and end of each ElevenLabs clip so the cut isn't abrupt.
- Background music at -28 to -32 dB under VO. Subtle.
- Use the same voice + music kit on every video for brand audio memory.

---

## [SCENE 1 — 0:00–0:08]

**ON SCREEN:** Terminal window. Production logs scrolling fast. A red `ERROR: Sidekiq worker timeout` line stays visible briefly. Then an APM dashboard (mock New Relic / Skylight / AppSignal) showing a flat-then-spiking latency chart.

**VO:**

> Your Sidekiq queue is backing up. Your APM is on fire. And nobody on the team has touched this Rails app's internals in two years.

---

## [SCENE 2 — 0:08–0:22]

**ON SCREEN:** Cut to clean black title card. SparkRails logo center-screen. In Quicksand under the logo: **"Production Rails. Explained."** Below that, smaller, muted: **"by Abhishek Kanojia"**.

**VO (voice-neutral — brand-first, no identity claim):**

> This is SparkRails. Senior Ruby on Rails consulting for SaaS teams running production apps. The kind that need to stay stable, upgradable, and maintainable for years — not weeks.

**Why the rewrite:** the original VO ("I'm Abhishek Kanojia…") forces the chosen voice to claim a specific identity, which sounds off when the AI voice doesn't match the named person. Brand-first framing carries the same information (name on screen as text) without that mismatch, so this scene renders cleanly in any voice — American, Indian English, or a cloned version of your own voice.

---

## [SCENE 3 — 0:22–0:35]

**ON SCREEN:** Three quick cuts, ~4 seconds each, with caption overlay:

1. APM dashboard with a slow endpoint highlighted. Caption: *Production debugging.*
2. Gemfile diff showing version bumps. Caption: *Rails upgrades.*
3. Sidekiq web UI showing stuck queue. Caption: *Background jobs.*

**VO:**

> This channel covers the kind of Rails problems that show up in real production. Slow endpoints. Stuck background jobs. Upgrades that have stalled for months. Not the kind of problems you read about in tutorials.

---

## [SCENE 4 — 0:35–0:50]

**ON SCREEN:** Light red overlay panel. Heading: **"Not on this channel:"** Three strikethrough lines appear one by one:

- ~~Hello World tutorials~~
- ~~Rails news roundups~~
- ~~Rewrites from scratch~~

**VO:**

> No beginner tutorials. No framework news. No rewrites. Every video walks through one production Rails problem. How to diagnose it. What the actual fix looks like.

---

## [SCENE 5 — 0:50–1:05]

**ON SCREEN:** Clean text card with three bullets in wine accent (#873d48):

- **Production debugging walkthroughs**
- **Rails upgrade explainers**
- **Sidekiq, Redis, and caching deep dives**

Below: **"New video every two weeks."**

**VO:**

> Production debugging. Rails upgrades. Sidekiq, Redis, and caching. New video every two weeks. Calm, senior, and built for SaaS teams running real apps.

---

## [SCENE 6 — 1:05–1:15]

**ON SCREEN:** SparkRails logo. Under it: **sparkrails.com** and a subscribe button glow. Slow zoom out.

**VO:**

> If you run a Rails SaaS app and it's started to feel heavy, subscribe. And if you need direct help, the link to book a Rails consultation is in the description.

---

## End-screen cards (last 5 sec, no VO)

- Subscribe button + the most recent video.

---

## YouTube description template

```
SparkRails is senior Ruby on Rails consulting for SaaS teams running production apps.

— Book a Rails consultation: https://sparkrails.com/?utm_source=youtube&utm_medium=trailer
— Rails upgrade help: https://sparkrails.com/rails-upgrade-consultant/?utm_source=youtube&utm_medium=trailer
— Legacy Rails maintenance: https://sparkrails.com/legacy-rails-maintenance/?utm_source=youtube&utm_medium=trailer

Run by Abhishek Kanojia, senior Rails consultant.
Available for US, UK, EU, Canada, Singapore, and remote-first teams.
```

---

## ElevenLabs pronunciation reminders

- Sidekiq → "side kick"
- SparkRails → "spark rails" (two syllables, even spacing)
- APM → "A P M" (spell out)
- SaaS → "sass"
