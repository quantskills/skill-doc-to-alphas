# Agent Instructions: How to Create a New QuantSkills Skill

Use these instructions when the user asks you to create a new skill for the
[QuantSkills](https://github.com/quantskills) ecosystem. You will scaffold a
complete, registry-compatible `skill-*` repository from the
[skill-template](https://github.com/quantskills/skill-template).

---

## 1. Understand What a QuantSkills Skill Is

A QuantSkills **skill** is a self-contained, reusable capability package
published under `github.com/quantskills`. Skills are designed for AI agents
(Claude Code, Codex, Cursor, OpenClaw, Hermes, WorkBuddy) to discover, load,
and execute.

Examples of existing skills (study these for reference):

| Skill | What it does | Category |
|---|---|---|
| `skill-doc-to-alphas` | Generate OHLCV alpha factors from documents with contract + validator | `factor` |
| `skill-pandadata-api` | Route natural-language data requests to 185 Pandadata APIs | `data-api` |
| `skill-market-daily-review` | Generate A-share after-close daily market review reports | `analyst` |
| `skill-quant-factor-directional-alpha` | 296 directional OHLCV factor skills, real-data validated | `factor` |
| `skill-a-share-stock-dossier` | A-share stock due-diligence dossier from Pandadata | `analyst` |
| `skill-index-valuation-rotation` | Index PE/PB percentile, industry rotation analysis | `analyst` |
| `skill-paper-replication` | Turn quant finance papers into reproducible experiments | `replication` |
| `skill-backtest` | Cross-sectional long-only backtest protocol + diagnostics | `tooling` |
| `skill-time-series-analysis` | Stationarity, cointegration, mean-reversion diagnostics | `tooling` |
| `skill-options-vol-analyst` | Options volatility analysis, IV percentiles, skew | `analyst` |

Browse all existing skills at:
- **Registry index**: https://github.com/quantskills/registry/blob/main/INDEX.md
- **Org repos**: https://github.com/orgs/quantskills/repositories

---

## 2. Repository Naming Rules

- **Prefix**: `skill-` (lowercase) for skills, `agent-` for agents.
- **Format**: lowercase kebab-case. Use hyphens, not underscores or spaces.
- **Examples**: `skill-five-day-momentum`, `skill-factor-validation`,
  `skill-report-replication`.
- The repo name must be unique within `github.com/quantskills`.

---

## 3. Required Files (Minimum Viable Skill)

Every skill repository MUST contain these files at the root:

| File | Purpose |
|---|---|
| `SKILL.md` | **Declaration file** — YAML frontmatter + Markdown body. This is the entry point AI agents discover. |
| `README.md` | Human-readable docs (Chinese default, with `README.en.md` for English). |
| `README.en.md` | English version of README. |
| `LICENSE` | Must be **GPL-3.0**. Copy from skill-template or any existing skill. |

Optional but strongly recommended:

| Path | Purpose |
|---|---|
| `references/` | Contract docs, API mappings, templates — anything the agent reads at runtime. |
| `scripts/` | Python scripts the agent executes (validators, generators, callers). |
| `agents/` | Cross-agent adapter files (see §8). |
| `.gitignore` | Standard Python gitignore. |
| `requirements.txt` | If scripts need packages beyond stdlib. |

---

## 4. The SKILL.md File — Full Specification

### 4.1 YAML Frontmatter (Required)

The frontmatter must be valid YAML between `---` delimiters. The registry
nightly scanner validates this against a JSON Schema.

```yaml
---
name: my-skill-name                    # Must match repo name (without skill- prefix)
description: >-                         # ≥60 chars, MUST contain "Use when"
  "One sentence on what this skill does. Use when an agent needs to
  <trigger scenario 1>, <trigger scenario 2>, or <trigger scenario 3>
  on portable agent platforms such as Claude Code, OpenClaw, or Codex-style
  skill systems."
license: GPL-3.0-only
metadata:
  organization: QuantSkills
  organization_url: https://github.com/quantskills
  repository: skill-my-skill-name      # Full repo name
  repository_url: https://github.com/quantskills/skill-my-skill-name
  project_type: skill
  collection: <collection-name>        # Optional grouping
  creator: <github-username>
  creator_url: https://github.com/<github-username>
  maintainer: <github-username>
  maintainer_url: https://github.com/<github-username>
quantSkills:
  organization: QuantSkills
  organization_url: https://github.com/quantskills
  repository: skill-my-skill-name
  repository_url: https://github.com/quantskills/skill-my-skill-name
  project_type: skill
  collection: <collection-name>
  category: tooling                    # See §4.2 for enum
  tags:                                # 1–10 lowercase kebab-case tags
    - my-tag-one
    - my-tag-two
  platforms:                           # At least one
    - claude-code
    - codex
    - cursor
    - openclaw
  language: zh-en                      # zh-en, en, zh, ja, etc.
  status: draft                        # draft | active | stable | deprecated
  validation_level: listed             # listed | runnable | verified
  maintainer_type: community           # official | community
  requires: []                         # Dependent skill-* or agent-* repo names
  summary_zh: 一句话中文简介 (8–120 字符)
  summary_en: One-line English summary (8–200 characters)
---
```

### 4.2 Category Enum (14 Values)

**Skill categories** (7):
- `trader-research` — Research models from trader posts, public materials
- `factor` — Alpha factors, factor libraries, factor validation
- `data-api` — Data access, API routing, data warehousing
- `replication` — Paper replication, report replication, research reproduction
- `monitor` — Market monitoring, event alerts, macro surveillance
- `analyst` — Analysis reports, dossiers, reviews, research output
- `tooling` — Workflow tools, backtest protocols, debugging, evaluation

**Agent categories** (7):
- `research-agent`, `monitor-agent`, `risk-agent`, `workflow-agent`,
  `review-agent`, `data-agent`, `automation-agent`

### 4.3 Validation Levels

| Level | Label | Requirements |
|---|---|---|
| L1 | `listed` | Default. Research methods, prompts, early ideas, teaching examples. |
| L2 | `runnable` | Needs install instructions + example input/output. |
| L3 | `verified` | Needs data sources, no-look-ahead checks, backtest evidence, risk notes. |

### 4.4 Markdown Body (After Frontmatter)

The body should follow this structure:

```markdown
# My Skill Name

Use this skill to <core purpose in one sentence>.

## Creator, Maintainer, And Scope

- Creator: `<username>` (`<github-url>`).
- Maintainer: `<username>` for the QuantSkills community.
- Repository: `<repo-url>`.
- License: GPL-3.0-only.
- Scope: <what this skill covers and what it explicitly does NOT cover>.

## Core Workflow

1. Step one.
2. Step two.
3. Step three.

## Output Contract

Produce:
- `<output_file_1>`
- A concise report with <details>

## Calling Pattern

```bash
python scripts/xxx.py --arg value
```

## Agent Prompt Template

When the user asks to <trigger>, use this prompt:

```
You are a <role>. Based on <input>, do <task>.
Follow the contract in references/<file>.md.
Output: <format>.
```

## Cross-Agent Use

- Claude Code / Codex: load via `SKILL.md`.
- Cursor: use `agents/cursor-rule.mdc`.
- Hermes / OpenClaw: use `agents/portable-loader.md`.
- OpenAI-style: read `agents/openai.yaml`.

## Reference Files

- `references/<file>.md` — description.
- `scripts/<file>.py` — description.
```

---

## 5. README.md Structure

Follow the bilingual pattern from existing skills:

```markdown
# 🧩 Skill Name

**简体中文** | [English](README.en.md)

> One-line positioning: what problem this skill solves.

![type](https://img.shields.io/badge/type-agent--skill-blue)
![license](https://img.shields.io/badge/license-GPLv3-blue)

## 📖 这是什么

Two to three paragraphs explaining: what the skill is, what it includes,
relationship to sibling repos.

## 🚀 快速开始

### Agent 工作流

```text
User: <trigger example>
Agent:
1. <step>
2. <step>
```

### 直接使用

```bash
<direct usage example>
```

## 📦 目录结构

```
skill-xxx/
├── SKILL.md
├── README.md / README.en.md
├── LICENSE
├── references/
│   └── contract.md
├── scripts/
│   └── tool.py
└── agents/
    ├── openai.yaml
    ├── cursor-rule.mdc
    └── portable-loader.md
```

## ⚠️ 免责声明

本仓库仅作研究方法层面的整理，不构成任何投资建议。

## 👤 维护者

创建与维护：`<username>`（QuantSkills community）。

## 📜 License

GPL-3.0. See [LICENSE](LICENSE).
```

---

## 6. Required Cross-Agent Adapters (`agents/`)

Every skill should include these three files for cross-agent compatibility:

### 6.1 `agents/openai.yaml`

```yaml
interface:
  display_name: "My Skill Name"
  short_description: "One-line description of what this skill does"
  default_prompt: "Use $my-skill-name to <do the thing>."
```

### 6.2 `agents/cursor-rule.mdc`

```markdown
---
description: Use the <Skill Name> skill to <purpose>.
alwaysApply: false
---

# <Skill Name> Skill

When the user asks to <trigger condition>:
1. Read `.cursor/skills/<skill-name>/SKILL.md`.
2. Read `.cursor/skills/<skill-name>/references/<contract>.md`.
3. <specific workflow steps>.
4. Validate with: `python .cursor/skills/<skill-name>/scripts/<script>.py --arg <value>`.
5. Report results.
```

### 6.3 `agents/portable-loader.md`

```markdown
# Portable Loader Prompt

Use this prompt in Claude Code, Hermes, OpenClaw, or any agent runtime that does
not natively discover `SKILL.md` folders.

```text
You have access to a local skill named <skill-name> at:
<<SKILL_NAME>_SKILL_ROOT>

When the user asks to <trigger>:
1. Read <<SKILL_NAME>_SKILL_ROOT>/SKILL.md.
2. Read <<SKILL_NAME>_SKILL_ROOT>/references/<contract>.md.
3. <steps>.
4. Validate: python <<SKILL_NAME>_SKILL_ROOT>/scripts/<script>.py --arg <value>.
5. Report results.
```

Runtime placement notes:
- Codex: keep under a Codex skill path, invoke `$<skill-name>`.
- Claude Code: keep under a Claude skill path, invoke `$<skill-name>`.
- Cursor: copy to `.cursor/skills/<skill-name>`, enable `agents/cursor-rule.mdc`.
- Hermes/OpenClaw: mount as local skill root or paste loader prompt with real path.
```

### 6.4 `references/agent-integration.md` (Recommended)

Include install commands and smoke tests for each agent platform:

```markdown
# Agent Integration — <skill-name>

## Universal Smoke Test

```bash
# Verify the skill works
python -c "from scripts.xxx import something; assert something is not None; print('OK')"
```

## Claude Code
```bash
mkdir -p ~/.claude/skills
rsync -a --exclude '__pycache__' ./ ~/.claude/skills/<skill-name>/
```
Use: `<trigger example using $<skill-name>>`

## Codex
```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
rsync -a --exclude '__pycache__' ./ "${CODEX_HOME:-$HOME/.codex}/skills/<skill-name>/"
```

## Cursor
```bash
mkdir -p .cursor/skills .cursor/rules
rsync -a --exclude '__pycache__' ./ .cursor/skills/<skill-name>/
```
Create `.cursor/rules/<skill-name>.mdc` from `agents/cursor-rule.mdc`.

## OpenClaw
```bash
mkdir -p ~/.openclaw/skills
rsync -a --exclude '__pycache__' ./ ~/.openclaw/skills/<skill-name>/
```
```

---

## 7. Registry Health Checks — 8 Deterministic Rules

The registry nightly scanner runs 8 checks against every repo. Your skill MUST
pass all of them to avoid quarantine.

| # | Check | Severity | What to ensure |
|---|---|---|---|
| 1 | `required-files` | **fail** | `SKILL.md` + `README.md` + `LICENSE` must exist at root |
| 2 | `frontmatter` | fail/warn | YAML must parse; `name` required (fail); `description` ≥60 chars must include "Use when"; all `quantSkills` fields present (warn) |
| 3 | `path-refs` | fail/warn | Markdown links to repo-internal files must resolve (dead links = fail). Backtick mentions of missing paths = warn |
| 4 | `git-hygiene` | fail/warn | No single file >10MB (fail). No data files >2MB (csv/parquet/json/db/zip = warn) |
| 5 | `secrets` | **fail** | No AWS keys, GitHub PATs, `sk-` tokens, Slack tokens anywhere in repo |
| 6 | `trader-disclaimer` | **fail** | If `category: trader-research`, README must contain BOTH "不构成投资建议" AND "非官方/不隶属" |
| 7 | `python-syntax` | **fail** | Every `.py` file must pass `py_compile` |
| 8 | `requires` | warn | Any dependency listed in `requires` must exist as a real repo in the org |

**Outcome**: Any **fail** → `quarantined` (excluded from public registry).
Only **warn** → `warning`. All clear → `healthy`.

---

## 8. Designing a Good Skill — Principles from Existing Examples

### 8.1 Self-Contained

Skills should be as self-contained as possible. `skill-doc-to-alphas` is the
archetype: it needs only Python + numpy + pandas, no API keys, no external
services.

If your skill DOES need external services (like Pandadata), declare the
dependency clearly in `requires` and document setup steps.

### 8.2 Contract-Driven

Every skill that involves code generation or structured output should define
a **contract** in `references/`. The contract is the single source of truth
that the agent reads before generating any output. Examples:

- `references/alpha_ops.md` in skill-doc-to-alphas — defines 6 fields + 27 functions
- `references/method-index.md` in skill-pandadata-api — maps 185 API methods
- `references/pandadata-map.md` in skill-market-daily-review — maps report sections to APIs

### 8.3 Validator Script

Every skill that produces output should include a **validator script** that
the agent runs after generating output. This catches errors before delivery.
Examples:

- `scripts/generate_alphas.py --alphas <file>` in skill-doc-to-alphas
- `scripts/validate_report.py <file>` in skill-market-daily-review
- `scripts/search_api_docs.py --method <name>` in skill-pandadata-api

### 8.4 Retry Loop (Auto-Correction)

For skills that involve LLM generation, include a retry loop pattern (up to 5
attempts). After the validator catches errors, feed diagnostics back to the
LLM and regenerate. See `--correction-context` in skill-doc-to-alphas.

### 8.5 Cross-Agent Design

Every skill must work across agent platforms. This means:

- **No hardcoded paths** — use relative paths within the skill folder.
- **SKILL.md is the entry point** — it must contain all the information an
  agent needs to use the skill without reading other files (though it should
  reference them).
- **Python scripts use stdlib + common packages** (numpy, pandas). Avoid
  obscure dependencies.
- **All scripts accept CLI arguments**, not hardcoded config.

### 8.6 Bilingual Documentation

- Metadata, titles, summaries: **English primary** (for global AI agents).
- README body: **Chinese default** with English version in `README.en.md`.
- SKILL.md body can be in either language, but English is preferred for
  metadata fields.

---

## 9. Step-by-Step Creation Workflow

When the user asks you to create a new skill, follow this exact workflow:

### Phase A: Discovery & Research

1. **Read the skill template** at https://github.com/quantskills/skill-template
   to get the latest baseline.
2. **Browse similar skills** in the registry at
   https://github.com/quantskills/registry/blob/main/INDEX.md to understand
   patterns for the target category.
3. **Read the community rules** at
   https://github.com/quantskills/join/blob/main/COMMUNITY_RULES.md.

### Phase B: Design the Skill

4. **Choose a name**: `skill-<kebab-case-description>`. Check the org for
   naming conflicts.
5. **Choose a category** from the 7 skill categories (§4.2).
6. **Define the contract**: What fields, functions, APIs, or templates will
   the agent use? Write these as reference docs in `references/`.
7. **Design the workflow**: What are the exact steps an agent follows? Write
   this in the SKILL.md Core Workflow section.
8. **Plan the validator**: What script verifies the agent's output? What
   checks does it run? What error diagnostics does it provide?
9. **Set the validation level**: Start at `listed`. Upgrade to `runnable`
   when you have install instructions + example I/O. Upgrade to `verified`
   with data sources + backtest evidence.

### Phase C: Scaffold the Repository

10. **Create the directory** with the repo name.
11. **Write `SKILL.md`** with complete YAML frontmatter (§4.1) and Markdown
    body (§4.4). Double-check:
    - `description` ≥ 60 characters and includes "Use when"
    - `category` is a valid enum value
    - `tags` are 1–10 lowercase kebab-case strings
    - `platforms` includes at least claude-code and codex
    - `summary_zh` is 8–120 characters, `summary_en` is 8–200 characters
12. **Write `README.md`** (Chinese default) and `README.en.md` following §5.
    Include the disclaimer:
    > 本仓库仅作研究方法层面的整理，不构成任何投资建议。
13. **Copy `LICENSE`** — GPL-3.0 from the template.
14. **Create `references/`** with contract docs.
15. **Create `scripts/`** with Python tools (all must pass `py_compile`).
16. **Create `agents/`** with the three adapter files (§6).
17. **Create `references/agent-integration.md`** (§6.4).
18. **Create `.gitignore`**:
    ```
    __pycache__/
    *.pyc
    .DS_Store
    *.env
    .pandadata/
    ```

### Phase D: Validate Locally

19. **Run the registry health check script** (if available locally):
    ```bash
    pip install pyyaml requests
    python scripts/validate_skill.py /path/to/skill-repo
    ```
    Or manually verify all 8 checks from §7.
20. **Run the smoke test** from `references/agent-integration.md`.
21. **Verify frontmatter YAML** is valid (use Python `yaml.safe_load`).
22. **Verify all `.py` files compile**: `python -m py_compile scripts/*.py`.
23. **Verify all Markdown links** resolve to existing files.

### Phase E: Deliver

24. **Present a summary** to the user with:
    - Skill name, category, and one-line description
    - Directory tree
    - Key design decisions (contract, workflow, validator)
    - Validation level and what's needed to upgrade
    - Any known limitations or assumptions

25. **Remind the user** that to publish the skill to quantskills they need to:
    1. Join the org via https://github.com/quantskills/join (submit a public
       Join Request issue)
    2. Agree to the community rules
    3. Push the repo to `github.com/quantskills/skill-<name>`
    4. The nightly registry scan will automatically index it

---

## 10. Common Pitfalls to Avoid

1. **Missing "Use when" in description** — the registry scanner checks for
   this exact phrase. Without it, the frontmatter check warns.
2. **Using unsupported category values** — only the 14 enum values in §4.2
   are valid.
3. **Forgetting the disclaimer** — if `category: trader-research`, both
   免责声明 sentences are mandatory or the repo gets quarantined.
4. **Hardcoding paths** — agents run from different working directories.
   Always use relative paths from the skill root.
5. **Large data files** — keep data files under 2MB or they'll trigger
   git-hygiene warnings. Generate data programmatically instead.
6. **`requires` pointing to nonexistent repos** — verify dependencies exist
   in the org before declaring them.
7. **Single-language README** — always provide both `README.md` (zh) and
   `README.en.md` (en).
8. **Incomplete agents/ folder** — all three adapter files are expected by
   the community convention.
9. **Invalid YAML frontmatter** — test with `yaml.safe_load()` before
   finalizing.
10. **Missing LICENSE file** — this is a hard fail in the health check.

---

## 11. Reference: Full Directory Tree of a Well-Formed Skill

```
skill-<name>/
├── SKILL.md                          # Declaration file (YAML frontmatter + body)
├── README.md                         # Chinese documentation
├── README.en.md                      # English documentation
├── LICENSE                           # GPL-3.0
├── .gitignore                        # Python gitignore
├── requirements.txt                  # If needed
├── references/
│   ├── <contract>.md                 # The field/function/API contract
│   └── agent-integration.md          # Multi-agent install & smoke test
├── scripts/
│   ├── <tool>.py                     # Main tool/validator
│   └── <helper>.py                   # Supporting modules
└── agents/
    ├── openai.yaml                   # OpenAI/Codex adapter
    ├── cursor-rule.mdc               # Cursor rule adapter
    └── portable-loader.md            # Generic loader for other platforms
```

---

## 12. Quick Reference: Existing Skills by Category

Use these as templates when designing a new skill in a given category:

### factor
- `skill-doc-to-alphas` — Contract + validator pattern for alpha generation
- `skill-quant-factor-directional-alpha` — Factor library with per-factor SKILL.md
- `skill-factormad-debate-factor-mining` — Multi-agent debate for factor mining

### data-api
- `skill-pandadata-api` — API routing with method index + search
- `skill-pandadata-warehouse` — Local data caching with DuckDB

### analyst
- `skill-market-daily-review` — Template-driven report with section validator
- `skill-a-share-stock-dossier` — Multi-section dossier from data APIs
- `skill-options-vol-analyst` — Options analysis with multiple volatility metrics

### monitor
- `skill-event-risk-alert` — Event scanning with traceable alert reports
- `skill-macro-monitor` — Macro data routing to monitoring reports

### replication
- `skill-paper-replication` — Paper → experiment pipeline
- `skill-quant-research-replication` — Full research reproduction workflow

### tooling
- `skill-backtest` — Protocol definition, not a framework
- `skill-factor-debug` — Diagnostic manual for factor failures
- `skill-factor-evaluate` — Single-factor scoring system
- `skill-time-series-analysis` — Stationarity, cointegration diagnostics

### trader-research
- `skill-gaetano-crux-capital-research-model` — Research model from public posts
- `skill-serenity-research-model` — Structured research from X/Twitter evidence
- `skill-x-trader-builder` — Build trader-specific research models from posts
