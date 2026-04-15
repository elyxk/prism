# PRISM — Phase Instructions
Full skill instructions for each phase. Read the relevant section before executing.

---

## Table of Contents
- [Phase 0 — Signal Collection](#phase-0)
- [Phase 1 — Audience Definition](#phase-1)
- [Phase 2 — Positioning](#phase-2)
- [Phase 3 — Messaging Architecture](#phase-3)
- [Phase 4 — Copy Variants](#phase-4)
- [Phase 5 — Scoring and Recommendation](#phase-5)
- [Phase 6 — Landing Page Implementation](#phase-6)

---

## Phase 0 — Signal Collection {#phase-0}

**Goal:** Read everything before touching messaging. Build a structured foundation of internal truth, competitive intelligence, and real user language.

**Two-track analysis.** Phase 0 collects two different kinds of signal under two different lenses. Do not blend them.

- **Track A — Audience signals** (0.1 internal read, 0.3 community read, analytics) → lens: *what is true about the user*. Feeds Phase 1.
- **Track B — Competitive signals** (0.2 competitor read) → lens: *what is takeable in the market*. Feeds Phase 2.

Track B is the marketer's version of user reality, not the user's. Never mine competitor copy for user pains — that contaminates Phase 1 with secondhand interpretation. The tracks converge only at 0.4 Signal Synthesis and again at Phase 3.

### 0.1 — Internal Read

```
Read every file in prism/inputs/.
For each file extract and tag:
- FEATURE TRUTH: what the feature does
- AUDIENCE SIGNAL: who it is for
- PAIN POINT: what problem it solves
- HISTORICAL MESSAGING: what has been said before
- ANALYTICS SIGNAL: any behavioral or usage data

Do not interpret yet. Only collect and tag.
Produce an Internal Truth Table with one row per key finding.
Save to prism/outputs/[feature-name]/internal-truth-table.md
```

### 0.2 — Competitor Read

```
Check if prism/skills/intelligence/competitor-intelligence-table.md exists.

IF EXISTS:
  Load into working memory as the Competitor Intelligence Table.
  If last updated more than 30 days ago — flag:
  "⚠️ Competitor Intelligence Table is [X] days old. Consider re-running the Competitor Intelligence Skill."

IF NOT EXISTS:
  Flag: "⚠️ Competitor Intelligence Table not found. Using competitor list from config.txt only.
  Run the Competitor Intelligence Skill for a full position map."
  Read competitor names and hero headlines from config.txt.
  Continue with that reduced input — do not stop.
```

### 0.3 — Community Read

Both Reddit and Twitter/X are optional. Flag what is missing, never stop.

```
CHECK FOR REDDIT INPUT:
Option A — subreddits listed in config.txt → fetch top 10 threads per subreddit
Option B — thread file in prism/inputs/ → read that file directly
Option C — neither → flag: "⚠️ No Reddit source. Language Bank built from internal signals only."

IF Reddit source found — extract from each thread:
- Exact words used to describe the problem
- Exact words used to describe ideal solutions
- Comparisons to other tools
- Emotional language: frustration, relief, excitement

CHECK FOR TWITTER/X:
Only run if Twitter/X keywords are in config.txt.
If search fails → flag and continue.

Produce Real User Language Bank. Label each entry by source.
CRITICAL: Exact phrases only. No paraphrases.
Save to prism/outputs/[feature-name]/user-language-bank.md
```

### 0.4 — Signal Synthesis

```
Take Internal Truth Table + Competitor Intelligence Table + Real User Language Bank.

Find and tag:
- AMPLIFY: internal claims that match real user language
- REFRAME: internal claims that don't match user language
- MISSING: user pain points not in internal docs
- OPPORTUNITY: competitor gaps that match user frustrations

Produce Signal Report in four sections: Amplify / Reframe / Missing / Opportunity
Save to prism/outputs/[feature-name]/signal-report-v1.md
(Delta runs: save as signal-report-v2.md etc. Never overwrite.)
```

---

## Phase 1 — Audience Definition {#phase-1}

**Goal:** Identify the exact human this feature is for — using their words and actual behavior. Zero invented personas.

### 1.1 — Behavioral Audience

```
Read analytics signals from the Internal Truth Table.

Identify and profile:
- POWER USER: who uses this most, how often, what outcome they achieve
- DORMANT USER: who has access but zero/minimal usage
- DROP-OFF POINT: the exact moment engagement stops and what it signals

If no analytics data exists — flag: "⚠️ No analytics input. Behavioral profile inferred from
community signals only. Provide analytics export or Mixpanel connection for data-backed profiles."
Continue with inference — do not stop.

Produce Behavioral Audience Profile.
```

### 1.2 — Linguistic Audience

```
Read Real User Language Bank from Phase 0.

Extract and tag every phrase by emotional state:
- FRUSTRATION: words used when describing the problem at its worst
- RELIEF: words used when describing a solution that worked
- COMPARISON: words used when switching or comparing tools

For each category identify:
- The three most repeated phrases
- The single most emotionally loaded phrase
- Words that appear across multiple sources

Do not paraphrase. Exact quotes only.
Produce Linguistic Audience Profile.
```

**Phase 1 output — User Truth Document:**
```
WHO THEY ARE (from analytics or inference)
- Power User: [role, behavior, outcome]
- Dormant User: [who, why not activated]
- Drop-off point: [where, what it signals]

WHAT THEY FEEL (from real language)
- Frustration in their words: "[exact quote]"
- Relief in their words: "[exact quote]"
- What they say about alternatives: "[exact quote]"

THE INSIGHT
[One paragraph connecting behavioral data to linguistic data]
```
Save to `prism/outputs/[feature-name]/user-truth-document.md`

---

## Phase 2 — Positioning {#phase-2}

**Goal:** Find and claim the specific angle nobody else is owning.

**Competitive Delta entry point.** When invoked in Delta / Competitive mode, Phase 2 is the restart point. User Truth Document is loaded as-is — do not rebuild it. Only the Competitor Intelligence Table and the gap map are refreshed. After Phase 2 completes, decide:

- **Position Claim still ownable** → skip regeneration. Re-score existing Phase 4 variants against the new gap map in Phase 5. Note the new competitive boundary in the Positioning Map.
- **Position Claim now blocked** (a competitor occupies or credibly contests the same claim) → regenerate Phase 3 → 6 on the existing User Truth.

If the Gap Analysis surfaces new user pains that were not in the User Truth Document, stop and escalate to an Audience Delta — this is a signal that the audience picture itself has shifted.

### 2-of-3 Filter Rule (applies throughout Phase 2 and 3)
- **Stranger Test** — clear without context?
- **Differentiation Test** — could a competitor say this?
- **Evidence Test** — traces to real data or user language?

Pass 2 of 3 → accepted with flag on failing filter. Pass 1 or 0 → rewrite.

### 2.1 — Competitive Position Map

```
Read Competitor Intelligence Table.

Identify across all competitors:
- DOMINANT NARRATIVE: the claim that appears most frequently
- TONE PATTERN: the consistent emotional register across the category
- PRIMARY AUDIENCE: who competitors are writing for
- IGNORED AUDIENCE: who nobody is addressing

Map each competitor on two axes:
- Axis 1: Functional claim vs Emotional claim
- Axis 2: Individual user vs Team / Organization

Produce Competitive Position Map.
```

### 2.2 — Gap Analysis

```
Compare Competitive Position Map against User Truth Document.

Find and tag:
- LANGUAGE GAP: words competitors use vs words users actually use
- AUDIENCE GAP: who competitors target vs who your data shows getting value
- OUTCOME GAP: what competitors promise vs what users say they actually want

For each gap assess: size of opportunity, credibility, defensibility.
Rank by opportunity size.
Produce Gap Analysis.
```

### 2.3 — Position Claim

```
Select the single highest-opportunity gap.

Write a Positioning Statement:
"For [audience]
Who [frustration in their words]
[Feature] is the [category]
That [outcome]
Unlike [competitor approach]
We [differentiator]"

Test against the 2-of-3 filter. Flag any failing filter explicitly.
Write one paragraph explaining why this position over the other gaps.
```

**Phase 2 output — Positioning Map:**
Save to `prism/outputs/[feature-name]/positioning-map.md`

---

## Phase 3 — Messaging Architecture {#phase-3}

**Goal:** Build the strategic skeleton connecting Position Claim to every word of copy.

### 3.1 — Core Value Proposition

```
Write one sentence:
"[Feature] helps [specific audience] [achieve specific outcome]
without [specific frustration in their words]"

Test against 2-of-3 filter. Note evidence source from Phase 0 and 1.
```

### 3.2 — Three Pillars

```
PILLAR 1 — FUNCTIONAL
What specific capability delivers the core value? One sentence. Mechanism focused.
Evidence: feature brief from /inputs/

PILLAR 2 — EMOTIONAL
What changes in daily experience? One sentence. Before/after framing.
Evidence: relief language from User Language Bank

PILLAR 3 — SOCIAL
Who is already experiencing this? One sentence. Credibility focused.
Evidence: power user profile from analytics
```

### 3.3 — Narrative Arc (5 beats for landing page)

```
BEAT 1 — RECOGNITION (Hero section)
Emotional state: "this is for me"
Content: user's frustration in their exact words
Source: Frustration Language from Language Bank

BEAT 2 — UNDERSTANDING (Problem section)
Emotional state: "they get it"
Content: deeper reason the problem exists
Source: Gap Analysis from Phase 2

BEAT 3 — POSSIBILITY (Solution section)
Emotional state: "what if"
Content: feature as a new reality
Source: Core Value Proposition + Functional Pillar

BEAT 4 — CREDIBILITY (Proof section)
Emotional state: "this is real"
Content: behavioral proof and social evidence
Source: Social Pillar + analytics data

BEAT 5 — DECISION (CTA section)
Emotional state: "I want this"
Content: CTA framed as relief not action
Source: Relief Language from Language Bank
```

### 3.4 — Voice Constraints

```
Check if prism/skills/voice/brand-voice-document.md exists.

IF EXISTS:
  Extract writing constraints:
  - VOCABULARY: use list / never list / jargon to avoid
  - SENTENCES: typical length, paragraph length, use of questions
  - TONE: register (formal/conversational), personality, humour
  - STRUCTURAL PATTERNS: how the brand opens sections, closes CTAs

IF NOT EXISTS:
  Flag: "⚠️ Brand Voice Document not found. Copy will be strategically correct
  but without brand voice constraints. Run Brand Voice Skill before next run."
  Continue — do not stop.
```

**Phase 3 output — Messaging Framework:**
Save to `prism/outputs/[feature-name]/messaging-framework.md`

---

## Phase 4 — Copy Variants {#phase-4}

**Goal:** Write 5 complete copy sets, each leading with a different psychological angle.

### Pre-Step — Audience-Driven Structure Selection

```
Based on User Truth Document, select page structure:

Highly skeptical audience → Social proof → Problem → Solution → Features → CTA
Already aware of problem → Outcome headline → How it works → Proof → CTA
Comparing tools actively → Comparison → Differentiator → Proof → CTA
Frustrated, needs validation → Pain → Understanding → Solution → Features → CTA
Outcome-driven → Outcome → Mechanism → Proof → CTA
No clear signal → Headline → Subheadline → Body → Features → CTA

State the chosen structure and reason before writing.
Apply to all 5 variants.
```

### Universal Writing Rules

- No sentence longer than 20 words
- No banned words: revolutionary, powerful, seamless, effortless, game-changing
- Every claim traces to a source
- User language verbatim where possible
- Product name max 3 times per variant
- Apply Voice Constraints from Phase 3

### The Five Variants

| Variant | Angle | Converts |
|---|---|---|
| 1 | Functional | Analytical — needs to understand mechanism first |
| 2 | Emotional | Feeling-first — decides emotionally, validates with logic |
| 3 | Pain-First | Frustrated — needs to feel understood before listening |
| 4 | Outcome-First | Ambitious — motivated by achievement not escape |
| 5 | Social Proof | Skeptical — needs evidence before any claim lands |

**For each variant write:**
```
HEADLINE (max 8–10 words depending on angle)
SUBHEADLINE (max 20 words)
BODY COPY (3 paragraphs following selected structure)
FEATURE SECTION (3 points — format varies by angle)
CTA LINE + BUTTON TEXT (max 4 words on button)

Tag each element with its source from previous phases.
```

**Variant-specific angle rules:**
- **Functional:** Lead with Pillar 1 capability. Feature section: `[action] → [result]`
- **Emotional:** Headline from Relief Language. Feature section: `Instead of [frustration] — [relief]`
- **Pain-First:** Headline states the problem (no product name). Feature section: `No more [frustration] — [relief]`
- **Outcome-First:** Headline states the outcome. Feature section: `[outcome] in [timeframe/condition]`
- **Social Proof:** Headline references Power User profile. Feature section: `[user type] uses this to [measurable result]`

Save each to `prism/outputs/[feature-name]/messaging-variants/variant-[N]-[angle].md`

---

## Phase 5 — Scoring and Recommendation {#phase-5}

**Goal:** Score all 5 variants comparatively. Find the strongest one for this specific audience.

**Re-score mode (Competitive Delta, claim still ownable).** Do not regenerate variants. Load existing Phase 4 variants and re-run scoring with the updated Positioning Map. The criterion that typically moves is **Differentiation** — a new entrant can narrow what is ownable without changing the audience fit. If scores shift enough to swap Winner and Runner Up, surface this explicitly in the Recommendation Document.

### Scoring Criteria (1–5 each, total /20 or /25 with Voice Fit)

**Criterion 1 — Angle Fit** *(unique to Phase 5)*
Does this psychological angle match how this audience makes decisions?
- 5: Precisely matches primary decision-making style of Power User from Phase 1
- 3: Works for this audience but not their primary style
- 1: Contradicts what Phase 1 revealed

**Criterion 2 — Differentiation**
Remove product name. Could this belong to a competitor?
- 5: Clearly ownable. Nobody in the category sounds like this.
- 3: Directionally different but tone matches category pattern
- 1: Could belong to any competitor

**Criterion 3 — Resonance**
Does this use language the audience actually uses?
- 5: Multiple exact phrases from Language Bank. Correct audience segment.
- 3: Paraphrases user language, no exact phrases. Correct audience.
- 1: Wrong audience and invented language throughout

**Criterion 4 — Conversion Potential**
CTA as relief/achievement/belonging? Stakes visible? Outcome specific?
- 5: Relief/achievement CTA. Clear stakes. Specific outcome with data.
- 3: Generic CTA. Stakes unclear. Directional outcome.
- 1: CTA disconnected from copy. No desire created.

**Criterion 5 — Voice Fit** *(optional — only if Brand Voice Document exists)*
- 5: Unmistakably sounds like the brand.
- N/A: Brand Voice Document not available.

### Output — Recommendation Document

```
SCORING TABLE
                    Angle Fit  Differ.  Resonance  Conversion  Voice Fit*  TOTAL
Variant 1 Functional  /5        /5        /5          /5          /5        /25*
Variant 2 Emotional   /5        /5        /5          /5          /5        /25*
Variant 3 Pain-First  /5        /5        /5          /5          /5        /25*
Variant 4 Outcome     /5        /5        /5          /5          /5        /25*
Variant 5 Social Proof/5        /5        /5          /5          /5        /25*

WINNER: Variant [X] — [angle] — [total]
RUNNER UP: Variant [X] — [angle] — [total]

WHY IT WINS
[One paragraph — fully evidence-based, every claim referenced to a phase output]

ITS WEAK POINT
[Lowest criterion] — [specific fix] — [phase source for the fix]

THE TEST
Test [Winner] against [Runner Up]
Metric: [specific metric — CTR / time on page / scroll depth / conversion]
Why: [one sentence]
```

Save to `prism/outputs/[feature-name]/recommendation.md`

---

## Phase 6 — Landing Page Implementation {#phase-6}

**Goal:** Produce two outputs — a decision document for the marketer, then two HTML pages.

### 6.1 — Style Load

```
Check if prism/skills/style/company-style.css-variables exists.

IF EXISTS:
  Load all CSS variables into working memory.
  If older than 90 days — flag: "⚠️ Style file is [X] days old."

IF NOT EXISTS:
  Flag: "⚠️ Style file not found. HTML will use sensible defaults.
  Run Style Skill for brand-accurate output."
  Continue with a clean default style — do not stop.
```

### 6.2 — Page Architecture

```
For each of the 5 variants map copy to sections:

Section 1 — Hero: headline + subheadline / Beat: Recognition
Section 2 — Problem: body paragraph 1 / Beat: Understanding
Section 3 — Solution: body paragraphs 2–3 / Beat: Possibility
Section 4 — Proof: feature section / Beat: Credibility
Section 5 — CTA: CTA line + button / Beat: Decision

Flag any copy element that doesn't fit cleanly. Resolve before HTML.
Produce 5 Section Maps.
```

### 6.3 — Variant Review Document ← HUMAN CHECKPOINT

```
For each of the 5 variants produce a review block:

VARIANT [X] — [ANGLE]
Phase 5 Score: [X]/20 or /25

SCORING BREAKDOWN
- Angle Fit [X/5]: [one sentence — Phase 1 reference]
- Differentiation [X/5]: [one sentence — Phase 2 competitor reference]
- Resonance [X/5]: [exact Language Bank phrases used, listed]
- Conversion Potential [X/5]: [CTA language + specificity]
- Voice Fit [X/5 or N/A]

COPY
Headline / Subheadline / Body / Features / CTA

KEY EVIDENCE
- Audience source: [Phase 1 reference]
- Language source: [Language Bank quotes used]
- Positioning source: [Phase 2 gap exploited]

WHY THIS ANGLE COULD WIN / WHY IT COULD FAIL
[One sentence each]

---

After all 5 blocks:

PHASE 5 RECOMMENDATION
Winner: Variant [X] — [score] — [one sentence why]
Runner Up: Variant [X] — [score] — [one sentence why]

MARKETER OVERRIDE PROMPT
"If you disagree — state which variant you prefer and why.
Prism will generate HTML for your chosen variants instead."
```

Save to `prism/outputs/[feature-name]/variant-review.md`

**Then pause. Output to user:**
> "Review `prism/outputs/[feature-name]/variant-review.md`. Confirm the recommended variants or specify your preference. I'll generate HTML when you're ready."

### 6.4 — HTML Generation

Runs after marketer confirms. Default: Winner + Runner Up. Override: marketer-specified.
Add `Variants: all` to trigger if all 5 HTML files are needed.

```
For each selected variant generate a self-contained HTML file:

1. Comment block:
   <!-- PRISM — Variant [X] — [Angle] -->
   <!-- Phase 5 Score: [X]/20 or /25 -->
   <!-- Run: [date] — Feature: [feature name] -->

2. <style> block with CSS variables
   (from style file if available, sensible defaults if not)

3. Full semantic HTML:
   <section> per beat, <header>, <main>, <footer>
   Responsive breakpoints

4. All copy from Phase 4 — zero placeholders, zero lorem ipsum

FILE NAMING:
[angle]-winner.html
[angle]-runner-up.html

Save to prism/outputs/[feature-name]/pages/
```

**Phase 6 final output:**
```
prism/outputs/[feature-name]/variant-review.md     ← all 5 variants + scores + evidence
prism/outputs/[feature-name]/pages/
  [angle]-winner.html
  [angle]-runner-up.html
```
