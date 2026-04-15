# Setup Guide — PRISM

This guide walks you through everything you need to get PRISM running from scratch. No technical experience required.

**What you're setting up:** An AI "skill" that runs inside Claude Code — Anthropic's coding tool — and produces a complete messaging system for any product feature you're launching. Once installed, you type `Run Prism — Init` in Claude Code and the agent interviews you, scrapes your competitors and user communities, and delivers positioning, copy variants, and publication-ready landing pages.

PRISM is feature-agnostic and brand-agnostic. Use it for your own product, a client's product, or any feature you're positioning.

---

## What You Need Before Starting

| Requirement | Cost | Notes |
|---|---|---|
| **Claude Code** | Included with Claude Pro ($20/mo) | You need Claude Pro at minimum. Claude Max ($100/mo) is better for heavy use — higher usage limits. |
| **This repo** | Free | Either downloaded or cloned from GitHub |
| **A feature folder** | Free | Just a folder on your computer for each feature you position |

That's the minimum. The optional extras (analytics connections, research MCPs) are covered in Part 4.

---

## Part 1 — Get Claude Code

Claude Code is the tool PRISM runs inside. It's a chat interface that can also read files, run code, and connect to external tools.

### Step 1: Sign up for Claude Pro

Go to [claude.ai](https://claude.ai) and sign up for **Claude Pro** ($20/month). This gives you access to Claude Code.

> If you're going to position multiple features or run PRISM often, **Claude Max** ($100/month) gives you much higher usage limits. A full PRISM run is context-heavy — Max is worth it for real work.

### Step 2: Download Claude Code

Go to [claude.ai/code](https://claude.ai/code) and download the desktop app for your system (Mac or Windows).

Install it like any other app. Sign in with the same account you just created.

---

## Part 2 — Download This Repo

You have two options. Option B is easier if you've never used git.

### Option A: Clone with git (if you have git installed)

Open a terminal and run:
```bash
git clone https://github.com/<your-org>/prism.git
```

### Option B: Download as a ZIP (easier)

1. Go to the PRISM repo on GitHub
2. Click the green **Code** button
3. Click **Download ZIP**
4. Unzip the file somewhere on your computer (e.g. your Desktop or Documents)

---

## Part 3 — Install the Skill

This puts PRISM in the right place so Claude Code can find it.

PRISM lives in a folder called `.agents/skills/prism/`. You have two install modes — per-project or global.

### Option A: Per-project install (recommended for most marketers)

Copy the `prism/` skill folder into your working project so the layout looks like this:

```
your-project/
└── .agents/
    └── skills/
        └── prism/
            ├── SKILL.md
            ├── references/
            └── templates/
```

**Mac or Linux** — open Terminal, navigate to your project, then:
```bash
mkdir -p .agents/skills
cp -r ~/Downloads/prism-main/.agents/skills/prism .agents/skills/prism
```

**Windows** — open the downloaded repo, copy the `.agents/skills/prism` folder, and paste it into the same path inside your own project folder.

### Option B: Global install (available in every project)

Install PRISM into your user-level Claude Code skills folder:

**Mac or Linux:**
```bash
mkdir -p ~/.claude/skills
cp -r ~/Downloads/prism-main/.agents/skills/prism ~/.claude/skills/prism
```

**Windows:** Copy the `prism` skill folder to `C:\Users\<you>\.claude\skills\prism`.

**What this does:** Tells Claude Code where to find the skill. Per-project keeps PRISM scoped to one workspace. Global makes it available anywhere you open Claude Code.

---

## Part 4 — Set Up Data Connections (the power-ups)

PRISM runs on minimum inputs out of the box. Each optional connection below unlocks a specific upgrade. **None are required.**

---

### Power-up 1: Firecrawl — Competitor & Community Research

**Used in:** Phase 0 (Signal Collection)

**What it does:** Lets PRISM scrape competitor homepages and Reddit threads for real hero headlines and verbatim user language — the raw material for evidence-based copy. Without it, you paste competitor headlines and Reddit threads in manually.

**How to install:**

1. Get an API key from [firecrawl.dev](https://firecrawl.dev)
2. Open a terminal and run:
   ```bash
   claude mcp add firecrawl-mcp --scope user
   ```
3. Add your API key when prompted

---

### Power-up 2: Mixpanel or Amplitude — Behavioral Audience Data

**Used in:** Phase 1 (Audience Definition)

**What it does:** Turns Phase 1 from inference into evidence. PRISM reads real cohort data — who activates, who drops off, exactly where — and builds the audience profile on behavior rather than guesswork.

**Two ways to connect:**

**Basic (CSV export):**
1. In Mixpanel or Amplitude, export a cohort breakdown as CSV
2. Drop it into `prism/inputs/analytics-export.csv`
3. PRISM reads it automatically in Phase 0.1

**Advanced (live API connection):**
1. Get an API secret from your Mixpanel project settings (Amplitude works the same way with its own key)
2. Configure the connection:
   ```
   Tool: Mixpanel API
   Auth: API secret from project settings
   Reads: /data/cohorts endpoint filtered by [feature_name]
   Output: auto-saved to prism/inputs/analytics-export.csv
   ```

---

### Power-up 3: The Standalone Skills

PRISM is designed to consume output from three optional sibling skills. Each runs once and saves a file that all future PRISM runs automatically pick up.

| Skill | Output file | How often to run |
|---|---|---|
| Competitor Intelligence Skill | `competitor-intelligence-table.md` | Monthly |
| Brand Voice Skill | `brand-voice-document.md` | Once, update on rebrand |
| Style Skill | `company-style.css-variables` | Once, update on design refresh |

If any file is missing, PRISM flags the gap and continues. It never stops for an optional input.

---

### Summary: What to set up first

| Priority | Power-up | Why |
|---|---|---|
| Install first | **Firecrawl** | Unlocks real competitor and community research |
| Install if you have it | **Mixpanel / Amplitude** | Turns audience work into evidence |
| Install later | **Brand Voice + Style Skills** | Make the final HTML publishable, not just accurate |
| Install monthly | **Competitor Intelligence Skill** | Keeps positioning fresh as the category moves |

---

## Part 5 — Set Up Your First Feature

Every feature gets its own folder. Claude Code reads and writes files relative to whichever folder it's open in — so keeping features separate means their signal reports, variants, and pages never mix.

### Step 1: Create a feature folder

```
Documents/
  prism-workspace/
    search-filters/      ← one folder per feature
    team-billing/
    ai-summaries/
```

Name the folder something simple and lowercase (no spaces — use hyphens).

### Step 2: Open that folder in Claude Code

**In the Claude Code desktop app:**
1. Open Claude Code
2. Click **Open Folder** (or File → Open Folder)
3. Select your feature folder

> This matters. Claude Code works in the folder you have open. If you're in the wrong folder, context files from one feature will bleed into another.

### Step 3: Run Init

Type this in the chat:
```
Run Prism — Init
```

Press Enter. PRISM asks you 4 quick questions (feature name, competitor URLs, subreddits to watch, Twitter keywords) and scaffolds the workspace for you.

---

## Part 6 — Your First Run (What to Expect)

Here's what a first run looks like on a new feature:

**1. Run `Run Prism — Init`**
PRISM asks 4 questions, writes `prism/config.txt` from your answers, and drops two template files into `prism/inputs/` for you to fill in:
- `product-context.md` — 5 sentences about the feature
- `brand-voice.md` — tone, rules, banned words

> **Already have a launch brief?** Paste it into `prism/inputs/brief.md` and run `Run Prism — Init from brief` instead. PRISM parses it and only asks for what's missing.

**2. Fill the two input files**
Both templates have inline prompts. About 5 minutes each. Any editor works.

**3. Run the full pipeline**
```
Feature: [your-feature-slug]
Dataset: [what's new — Reddit angle / analytics cohort / competitor observation]
Run Prism — Full
```

PRISM runs Phases 0–6 autonomously. It pauses exactly once — at Phase 6.3 — so you can review 5 scored copy variants before it generates HTML.

**4. Review the variant-review document**
PRISM stops and tells you:

> *"Review `prism/outputs/[feature-name]/variant-review.md`. Confirm the recommended variants or specify your preference. I'll generate HTML when you're ready."*

You see all 5 variants with full copy, score breakdowns, why each could win, why each could fail, and the recommendation. Confirm or override.

**5. PRISM generates 2 HTML pages**
Winner + runner-up, fully styled, saved to `prism/outputs/[feature-name]/pages/`.

**6. Add a signal later? Run a Delta.**
New Reddit thread, new cohort data, new competitor — drop the file into `prism/inputs/` and run a Delta Run. PRISM layers the new signal on top of the existing pipeline instead of redoing everything. Outputs are versioned (`signal-report-v2.md`), never overwritten.

---

## Common Questions

**Do I need to be technical to use this?**
No. You run a command, PRISM asks you questions, you answer them. The skill is designed to be conversational.

**Can I use this for multiple features or clients?**
Yes — create a separate folder for each feature (or client) and open that folder in Claude Code before running PRISM. Brand voice and style files can be reused across features in the same brand.

**What if a run stops mid-way?**
PRISM saves the output of every phase before moving on. Re-run the same command — it picks up from where the existing output files leave off.

**Do I need all the power-ups?**
No. PRISM runs fine with nothing but the two input files and 3–5 competitor URLs. Firecrawl and analytics upgrade the evidence; they don't gate the output.

**What's the difference between a Full Run and a Delta Run?**
Full Run = first run on a feature, or a new feature entirely. Delta Run = you already have a signal report and are adding new data (audience or competitive) without redoing the whole pipeline.

**Why is my usage running out?**
A Full Run reads internal docs, competitor pages, community threads, and generates 5 variants plus 2 styled HTML pages — it's context-heavy by design. Claude Max ($100/mo) gives significantly higher limits and is recommended for regular use.

**PRISM flagged a missing file. What do I do?**
Nothing, unless you want to. PRISM continues without optional inputs and tells you exactly what was missing and where that would have strengthened the output. You can re-run with the missing input added any time.

---

## Quick Reference

```
First-time setup on a new feature:
  Run Prism — Init              → 4-question interview, scaffolds workspace
  Run Prism — Init from brief   → same, but extracts from an existing brief

Full pipeline:
  Feature: [feature-slug]
  Dataset: [what's new]
  Run Prism — Full              → Phases 0–6, pauses once before HTML

Adding a new signal (Delta):
  Feature: [feature-slug]
  Mode: Delta / Audience        → new user data, rebuilds from Phase 1
  Mode: Delta / Competitive     → new competitor, rebuilds from Phase 2
  New input: prism/inputs/[new-file]

Output lives at:
  prism/outputs/[feature-name]/
```

---

## Still Stuck?

- **Claude Code help:** [claude.ai/code](https://claude.ai/code) has full documentation
- **MCP setup issues:** Each connection has its own support — check the service's own docs (Firecrawl, Mixpanel, Amplitude)
- **PRISM isn't picking up a file:** Make sure the file is inside `prism/inputs/` in the folder you have open in Claude Code, and that the feature slug in your run command matches the folder name under `prism/outputs/`
