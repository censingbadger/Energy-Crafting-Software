# Energy Crafting Companion — Design Proposal

A live classroom companion for **Energy Crafting for Resilient Leaders** (Mike Christian, UNC Kenan-Flagler Executive Development). Participants open one link at the start of class; everything they do flows to a live dashboard the facilitator keeps on the presenting screen.

This proposal is grounded in the actual session materials: the *Energy Crafting Session Overview* (3-hour agenda), the *2026 Timelines* workbook (60-min through 6-hour variants), and the ADS Executive Sales Leadership Program schedule (3.5-hour session, Wed 8:30–12:00).

A clickable prototype lives at [`prototype/index.html`](prototype/index.html) — open it in a browser and toggle between the participant and presenter views.

---

## 1. The core concept: one link, eight pills

Participants get a QR code / short link on the first slide. No login, no app install — they pick their name from the roster (pre-loaded from the participant biographies) and land on a single screen: **eight large pills, one per segment of the session, in the order you teach them.**

Only the pill you've made "live" glows and sits at the top. Tapping it expands the activity in place — answer, rank, sort, or write — then it collapses to a checkmark. Nobody ever navigates; the screen always answers "what do I do right now?"

The eight pills map to the 3.5-hour flow from the timelines workbook:

| # | Pill | Course segment | Participant does | Dashboard shows |
|---|------|----------------|------------------|-----------------|
| 1 | **Check In** | Intro, defining resilience | One word: "What does resilience mean to you?" + current energy 0–10 | Live word cloud + cohort energy gauge |
| 2 | **Stress Signatures** | Signature competition debrief | 2-tap appraisal poll: did it feel like a *threat* or a *challenge*? | Split bar: threat vs. challenge appraisals |
| 3 | **What Keeps You Up at Night** | Demands discussion / "zebras" | Rank 6 stressors — **AI-hypothesized from their own bios** | Ranked bars of the cohort's top stressors |
| 4 | **Your Energy Map** | Rechargers / Catalysts / Slow Leaks / Fireworks | Sort common activities into the 2×2 (gives vs. drains × calm vs. intense) | Live density on the 2×2 matrix |
| 5 | **Owl or Lark** | Awareness, circadian scheduling | 4-question chronotype quiz | Cohort distribution + peak-hours ribbon |
| 6 | **Recovery & the 5 Ms** | Recovery paradox, replenishment | Pick your M (Motion, Mastery, Making connections, Meaning, Mindfulness) + one commitment | M distribution + scrolling commitment feed |
| 7 | **Case Breakouts** | Bob Holden video case | See assigned group + the 3 discussion questions; scribe captures answers | Group report cards for the debrief |
| 8 | **My Energy Plan** | Now What / big rocks / wrap | Worksheet auto-assembled from answers 1–7 + big rocks; "email me my plan" | Completion meter + anonymized highlights |

Because the timelines workbook shows ~10 session formats (60-min KF Culture through 6-hour Anderson), a **session is just an ordered subset of an activity library**. You'd assemble the ADS session from the same blocks as the EMBA session — different pills, same machinery. That's the single most important architectural decision.

## 2. Three surfaces

**Participant (phone).** Narrow, thumb-first, zero-typing wherever possible (taps, sliders, arrows). Every activity finishes in under 90 seconds. Works if they join late or drop off wifi — the pill stack re-syncs.

**Presenter dashboard (projector / your PowerPoint machine).** One full-screen view per activity, typography sized for the back of the room. You keep it in a browser tab and flip to it when you'd normally say "let's see what the room said." Numbers animate in as responses arrive — that moment of watching the word cloud grow *is* the engagement hook, same instinct as Poll Everywhere but tailored to your content.

**Facilitator remote (your phone).** A strip of the same eight pills with open/close toggles and a live response counter, so you can advance activities without touching the podium machine.

## 3. Before class: the bio import

You upload the participant biographies (PDF/Word, as the program office sends them). An AI pass over the bios produces three things:

1. **Roster** — names for one-tap join, plus org/role shown on group cards.
2. **Connection map** — shared threads across the cohort (industry, military service, geography, career stage). Each participant's screen shows 2–3 "people to find at the break" — e.g., *"You and 3 others here led teams through rapid growth."* The dashboard gets a cohort connections panel you can show during intros.
3. **Stressor hypotheses** — the personalized version of your "AI-generated challenge list." Instead of a generic list, pill 3 says: *"We read your bios. Here's what we think keeps this room up at night — rank them."* The reveal that the list was inferred from *their* bios is a moment in itself, and the ranking data feeds directly into the demands discussion.

Group formation for the Bob Holden breakouts also draws on the bios: groups balanced across organizations and chronotypes (once pill 5 is done), assigned automatically and shown on each phone — no counting off.

## 4. After class

- Each participant gets **their energy plan emailed** as a one-pager: their chronotype and peak hours, their slow leak to cut, their recharger to schedule, their 5M commitment, their big rocks.
- You get a **cohort recap**: every chart, the group answers, and the commitment list — useful for the program office and for follow-up sessions (ADS runs intersession coaching).
- Data persists per cohort, so a returning program (like ADS week 2) can open with "here's what this room said last time."

## 5. Design principles

- **Zero-friction join.** QR → tap your name → in. No accounts, no email at entry (email only requested at the end, to send the plan).
- **One thing at a time.** The live pill is the only loud element. Everything else is quiet.
- **Projector-legible.** Dashboard type ≥ 28px equivalent; charts readable at 40 feet; colorblind-safe palette (validated for deutan/protan/tritan).
- **Graceful without wifi.** Every activity also works as show-of-hands; the app enhances, never gates, the teaching.
- **Anonymous by default on screen.** Individual answers are never attributed on the dashboard (except group report-outs); participants see their own dot highlighted privately on their phone.

## 6. Suggested architecture

| Layer | Suggestion | Why |
|---|---|---|
| Web app | Next.js (single app serves participant, presenter, facilitator routes) | One deploy, three views |
| Realtime + data | Supabase (Postgres + realtime channels) or a single Node/WebSocket server with SQLite | Class-sized load is tiny; keep ops near zero |
| Bio analysis | Claude API (`claude-sonnet-5`): extract roster, connections, stressor hypotheses from uploaded bios | One pre-class batch job, human-reviewed before class |
| Session config | JSON "session template" = ordered list of activity blocks with per-cohort copy | Mirrors the timelines workbook; new formats without code |
| Join | Short code + QR per cohort; facilitator PIN for dashboard/remote | No participant auth |
| Export | Server-rendered PDF for personal plans + cohort recap | The lasting artifact |

## 7. Roadmap

1. **v0 (this repo, now):** clickable prototype with simulated cohort data — validate the flow and dashboard moments.
2. **v1 (single session MVP):** real-time backend, roster join, pills 1–6, dashboard, facilitator remote. Enough to run a live session.
3. **v1.5:** bio import + AI enrichment (roster, connections, stressor hypotheses), group formation, plan email.
4. **v2:** session template editor covering all timeline formats; cohort history; program-office recap exports.
