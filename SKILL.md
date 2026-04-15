---
name: prism
description: >
  A senior AI marketer's messaging agent. Run PRISM whenever a user wants to generate positioning,
  messaging variants, or landing page copy for a product feature — especially when they have
  audience data, community signals, or competitor context to work from. Trigger on phrases like
  "Run Prism", "generate messaging for", "build a messaging framework", "create landing page
  variants", "run a delta on", or "new signal received". Also trigger when a user drops a Reddit
  thread, analytics export, or competitor observation and wants to turn it into copy. PRISM takes
  any combination of inputs (the more the better) and produces a complete, evidence-based messaging
  system: positioning, copy variants, scored recommendation, and publication-ready HTML pages.
---

# PRISM — Senior AI Marketer's Messaging Agent

PRISM runs as a waterfall. Each phase saves its output before the next phase reads it.
You never start a phase blind — always read the previous phase's saved output first.
The only human pause is after Phase 6.3. Everything else is autonomous.

Read `references/prism-phases.md` for the full instruction set for each phase.
This file is the orchestrator — it tells you what to run and in what order.

---

## Skill vs Workspace

The **skill** (this folder: `.agents/skills/prism/`) is read-only. It contains orchestration logic, phase instructions, and templates. Never write run output into it.

The **workspace** is a `prism/` folder at the root of the user's project. It contains the user's config, inputs, and outputs. Everything PRISM reads or writes at runtime lives here.

```
<repo root>/
├── .agents/skills/prism/           ← the skill (read-only)
│   ├── SKILL.md
│   ├── references/prism-phases.md
│   └── templates/                  ← blank files the user copies into their workspace
└── prism/                          ← the workspace (user's run data)
    ├── config.txt
    ├── inputs/
    └── outputs/
```

If the workspace doesn't exist, run **Init mode** (below) before anything else.

---

## Run Modes

**Init** — first-time setup on a new project
```
Run Prism — Init
```
Conducts a short interview (feature name, competitor URLs, subreddits, Twitter keywords), fetches competitor hero headlines from the URLs, writes `prism/config.txt` itself, and scaffolds the rest of the workspace. The marketer never edits `config.txt` by hand. The two files that still need human input — `product-context.md` and `brand-voice.md` — are copied from templates and flagged in the next-steps message. Never overwrites existing files.

**Init from brief** — if you already have a one-pager
```
Run Prism — Init from brief
```
Reads `prism/inputs/brief.md` (prose, natural language) and extracts feature name, competitor URLs/names, and subreddits from it. Falls back to interview questions for anything missing. Use this when you already have a launch brief in Notion or Linear — paste it in, skip the interview.

**Full Run** — new feature or first run on a feature
```
Feature: [feature name]
Dataset: [what's new — Reddit angle / audience segment / usage data / competitor]
Run Prism — Full
```
Reads `prism/config.txt` by default. Pass `Config: [path]` only if you keep it elsewhere.

**Delta Run — Audience Signal** — new audience data on an existing feature (Reddit thread, analytics cohort, user interview, support tickets)
```
Feature: [feature name]
Mode: Delta / Audience
New input: [path to new document]
Skip to: Phase 1
```
Reads existing Signal Report + new audience document. Rebuilds User Truth; cascades through Phase 2 → 6.
Versions outputs (`signal-report-v2.md`, etc.) — never overwrites.

**Delta Run — Competitive Signal** — new competitor, competitor repositioning, or a competitor campaign that shifts the category
```
Feature: [feature name]
Mode: Delta / Competitive
New input: [competitor name + hero headline, or link]
Skip to: Phase 2
```
User Truth Document stays intact — the audience has not changed. Updates Competitor Intelligence Table, then reruns Phase 2 (Competitive Position Map + Gap Analysis + Position Claim).

**Then decide before rerunning Phase 3+:**
- **Claim still ownable** → keep existing Messaging Framework and variants. Re-score Phase 5 against the new gap map. Note the new competitive boundary. Done.
- **Claim now blocked** (a competitor occupies or credibly contests your Position Claim) → regenerate Phase 3 → 6 on the existing User Truth.

**Escalation check:** if the new competitor changes *what users are saying* (not just who is saying similar things), treat it as an Audience Signal instead and skip to Phase 1.

---

## Inputs

### Required (gates the run)
| Input | What | Where |
|---|---|---|
| `config.txt` | Feature name, competitor names, optional subreddits | `prism/config.txt` |
| `product-context.md` | 5 sentences: what, who, replaces, capability, constraint | `prism/inputs/` |
| `brand-voice.md` | Tone, rules, banned words | `prism/inputs/` |
| Competitor list | 3–5 names + hero headline each | Inside `config.txt` |

Filenames are the convention, not a hard requirement — PRISM reads every file in `prism/inputs/`. Use these names unless you have a reason not to; templates ship with them pre-named.

### Optional (improves output — never blocking)
| Input | Unlocks | How to provide |
|---|---|---|
| Reddit thread | Verbatim user language | Drop `.txt` or `.md` into `prism/inputs/` OR list subreddits in config |
| Analytics data | Behavioral audience profile | CSV in `prism/inputs/` OR Mixpanel/Amplitude API connection |
| Competitor Intelligence Table | Full position map | Run Competitor Intelligence Skill → saves to `prism/skills/intelligence/` |
| Brand Voice Document | Voice constraints on all copy | Run Brand Voice Skill → saves to `prism/skills/voice/` |
| Style file | Branded HTML output | Run Style Skill → saves to `prism/skills/style/` |
| Previous campaigns | Avoids repeating failed angles | Paste into `prism/inputs/previous-campaigns.md` |

**Missing prerequisites:** flag inline, continue. Never stop for a missing optional input.

---

## `config.txt` Schema

**You should almost never write this file by hand** — Init writes it for you from the interview or brief. The schema is documented here for transparency and for edge cases where a marketer wants to edit an entry after the fact.

Simple `key: value` lines. Blank lines and lines starting with `#` are ignored. Required keys gate the run.

| Key | Required | Format | Example |
|---|---|---|---|
| `feature` | yes | single line, kebab-case | `feature: meeting-scheduler` |
| `competitors` | yes | 3–5 entries, one per line. Preferred: homepage URL. Fallback: `name \| hero headline` | see below |
| `subreddits` | no | comma-separated, no `r/` prefix | `subreddits: productivity, artificial` |
| `twitter_keywords` | no | comma-separated | `twitter_keywords: ai agents, work automation` |

**Competitor entries.** Init prefers URLs because it fetches the hero headline itself. The resulting line is stored as `name | hero headline    # source: URL`. If a fetch fails or the page has no clear hero, Init asks the marketer to paste the headline manually and that becomes a `name | hero headline` line with no source comment.

**Example (post-Init):**
```
# prism/config.txt — generated by Run Prism — Init on 2026-04-15
feature: meeting-scheduler

competitors:
  Calendly | Easy scheduling, ahead of the meeting    # source: https://calendly.com
  Motion   | The AI that runs your calendar           # source: https://usemotion.com
  Reclaim  | Automate your schedule for what matters  # source: https://reclaim.ai

subreddits: productivity, artificial, sideproject
```

---

## Workspace Layout

```
prism/
├── config.txt                        ← write once per feature
├── inputs/                           ← drop documents here
│   ├── product-context.md            ← required (5-sentence product description)
│   ├── brand-voice.md                ← required (tone, rules, banned words)
│   ├── analytics-export.csv          ← optional
│   ├── previous-campaigns.md         ← optional
│   └── [reddit-thread].txt           ← optional
├── skills/                           ← standalone skill outputs (all optional)
│   ├── intelligence/competitor-intelligence-table.md
│   ├── voice/brand-voice-document.md
│   └── style/company-style.css-variables
└── outputs/
    └── [feature-name]/
        ├── internal-truth-table.md
        ├── user-language-bank.md
        ├── signal-report-v1.md
        ├── user-truth-document.md
        ├── positioning-map.md
        ├── messaging-framework.md
        ├── variant-review.md          ← Phase 6.3 — marketer reviews here
        └── pages/
            ├── [angle]-winner.html
            └── [angle]-runner-up.html
```

Templates for the required files live in `.agents/skills/prism/templates/` and are copied here by `Run Prism — Init`.

---

## The Waterfall — Execution Order

Run each phase in sequence. Save output. Read it back before starting the next phase.

**Two-track principle (first run):** Phase 0 runs two parallel tracks that feed different downstream phases. They use different analytical lenses — do not blend them.

- **Track A — Audience data** (internal signals, Reddit, analytics, interviews) → feeds Phase 1 (User Truth). Lens: *what is true about the user*. Verbatim language, pains, desires, behavior.
- **Track B — Competitor data** (hero headlines, positioning, campaigns) → feeds Phase 2 (Positioning). Lens: *what is takeable in the market*. Claims owned, language captured, gaps.

The two tracks converge at Phase 3: angles must be both **true** (Track A) and **ownable** (Track B). Competitor marketing is not evidence of user reality — it is evidence of what other marketers believe about user reality. Never run competitor copy through the Phase 1 lens.

```
Phase 0 — Signal Collection
  0.1 Internal Read       → Internal Truth Table
  0.2 Competitor Read     → Competitor Intelligence Table (from file or flag)
  0.3 Community Read      → Real User Language Bank (Reddit/Twitter optional)
  0.4 Signal Synthesis    → Signal Report (Amplify / Reframe / Missing / Opportunity)

Phase 1 — Audience Definition
  1.1 Behavioral Audience → Behavioral Audience Profile
  1.2 Linguistic Audience → Linguistic Audience Profile
  → Final output: User Truth Document

Phase 2 — Positioning
  2.1 Competitive Position Map
  2.2 Gap Analysis
  2.3 Position Claim (2-of-3 filter: Stranger / Differentiation / Evidence)
  → Final output: Positioning Map

Phase 3 — Messaging Architecture
  3.1 Core Value Proposition
  3.2 Three Pillars (Functional / Emotional / Social)
  3.3 Narrative Arc (5 beats)
  3.4 Voice Constraints
  → Final output: Messaging Framework

Phase 4 — Copy Variants
  Pre-step: audience-driven structure selection
  → 5 variants: Functional / Emotional / Pain-First / Outcome-First / Social Proof
  Each saved to prism/outputs/[feature-name]/messaging-variants/

Phase 5 — Scoring and Recommendation
  Score all 5 variants: Angle Fit / Differentiation / Resonance / Conversion / Voice Fit*
  → Recommendation Document (Winner + Runner Up + Test plan)

Phase 6 — Landing Page Implementation
  6.1 Style Load (from file or flag missing)
  6.2 Page Architecture (Section Maps for all 5 variants)
  6.3 Variant Review Document ← PAUSE HERE. Wait for marketer review / override.
  6.4 HTML Generation (Winner + Runner Up only by default)
```

**The one human checkpoint:** After producing `variant-review.md` (Phase 6.3), output:
> "Review `prism/outputs/[feature-name]/variant-review.md`. Confirm the recommended variants or specify which two you want as HTML. I'll proceed when you're ready."

Wait for response before running Phase 6.4.

---

## Init Mode — execution

Init is conversational. The marketer never writes `config.txt` by hand — you interview them, fetch what can be fetched, and write the file yourself.

### When invoked as `Run Prism — Init`

1. **Workspace check.** If `prism/` exists and is non-empty, stop. List what's present and ask the marketer to confirm before touching anything.

2. **Interview.** Ask these one at a time and wait for each answer. Keep the questions casual — this is a conversation, not a form.

   - **Q1.** "What feature are we positioning?" (Accept any phrasing; convert to kebab-case for `feature:` — e.g. "Our new meeting scheduler" → `meeting-scheduler`. Confirm the slug back to them before continuing.)
   - **Q2.** "Paste 3–5 competitor homepage URLs, one per line. I'll grab their hero headlines myself." (Accept name-only entries too; flag them for manual headline entry in step 3.)
   - **Q3.** "Any subreddits worth watching for user language? Comma-separated, no `r/`. Skip if none come to mind."
   - **Q4.** "Any Twitter/X keywords or hashtags to track? Skip if none."

3. **Fetch competitor heroes.** For each URL from Q2:
   - Fetch the page. Extract the most prominent headline (largest H1, or the top-of-fold marketing claim if the H1 is navigation).
   - If fetch succeeds → store as `Name | hero headline    # source: URL`.
   - If fetch fails or the hero is ambiguous → tell the marketer which URL failed and ask them to paste the headline. Store as `Name | hero headline` without a source comment.
   - If they gave a name only in Q2 → ask for the headline directly.

4. **Scaffold the workspace.** Create `prism/`, `prism/inputs/`, `prism/outputs/`.

5. **Write `prism/config.txt`** from the interview. Include a top comment line with the date and how it was generated (interview vs. brief).

6. **Copy the two human-input templates.** These still need the marketer:
   - `templates/product-context.md` → `prism/inputs/product-context.md`
   - `templates/brand-voice.md` → `prism/inputs/brand-voice.md`

7. **Print next steps:**
   > Workspace created. I filled in `prism/config.txt` from our conversation. Two files still need you:
   > 1. `prism/inputs/product-context.md` — 5 sentences about the product. Prompts are in the file.
   > 2. `prism/inputs/brand-voice.md` — tone, rules, banned words. Prompts are in the file.
   > 3. *(Optional)* Drop a Reddit thread `.txt`, analytics `.csv`, or past campaign list into `prism/inputs/`.
   >
   > When those two are filled in, run: `Feature: [slug] / Dataset: [what's new] / Run Prism — Full`

### When invoked as `Run Prism — Init from brief`

1. Workspace check (same as above).
2. Read `prism/inputs/brief.md`. Extract:
   - Feature name → kebab-case slug.
   - Competitor URLs (preferred) or names.
   - Subreddits / Twitter keywords if mentioned.
3. For anything missing from the brief, ask the marketer (interview fallback, one question at a time).
4. Continue from step 3 of the standard Init flow (fetch heroes, scaffold, write config, copy templates, print next steps).

The brief itself stays in `prism/inputs/brief.md` and is read alongside everything else in Phase 0.1 — no information is lost.

---

## Universal Rules (apply across all phases)

- Every claim traces to a source. No invented data.
- User language used verbatim where available — never paraphrased.
- No banned copy words: revolutionary, powerful, seamless, effortless, game-changing.
- No sentence longer than 20 words in copy output.
- Product name max 3 times per copy variant.
- Flag missing inputs inline. Keep moving.
- Save every phase output to `prism/outputs/[feature-name]/` before proceeding.

---

## Phase Detail

Full instructions for each phase — skill instructions, tools, output formats — are in:
**`references/prism-phases.md`**

Read the relevant section before executing each phase.
The section headers match the phase names exactly.
