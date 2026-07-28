# scalingo-docs — Agent Skill

An [Agent Skill](https://agentskills.io) that answers questions about
[Scalingo](https://scalingo.com) grounded in the **official documentation**
rather than from the model's memory.

On first use it shallow-clones `github.com/scalingo/documentation`, cleans it into
a compact, grep-able corpus (resolving internal links to real `doc.scalingo.com`
URLs, inlining shared partials, flattening Jekyll templating), and then answers
from that corpus — citing the source URL each time. The corpus is cached under
`~/.claude/scalingo-docs/` (override with `$SCALINGO_DOCS_HOME`).

Requires `git` and `python3` (standard library only — nothing to `pip install`).

## Repository layout

```
.
├── .claude-plugin/
│   └── marketplace.json                    # Claude Code marketplace manifest
└── plugins/
    └── scalingo-docs/
        ├── .claude-plugin/
        │   └── plugin.json                 # plugin manifest
        └── skills/
            └── scalingo-docs/              # the Agent Skill itself
                ├── SKILL.md
                └── scripts/setup_docs.py
```

The skill (`plugins/scalingo-docs/skills/scalingo-docs/`) is a self-contained
Agent Skill and can be used on its own; the surrounding `plugins/` and
`.claude-plugin/` wrapping only exists to make the repo installable as a Claude
Code plugin marketplace.

## Installation

### 1. Claude Code — as a plugin marketplace (one command)

```
/plugin marketplace add jquagliatini/scalingo-skills
/plugin install scalingo-docs@scalingo-skills
```

`scalingo-skills` is the marketplace `name` (from `marketplace.json`);
`scalingo-docs` is the plugin `name`.

### 2. Any agent — drop the skill folder in place

Copy the skill folder to wherever your agent loads skills from:

```bash
# Personal, all projects (Claude Code / Claude.ai desktop)
cp -r plugins/scalingo-docs/skills/scalingo-docs ~/.claude/skills/

# Project-scoped, shared with your team via git
cp -r plugins/scalingo-docs/skills/scalingo-docs .claude/skills/
```

The same folder works for other agents that implement the SKILL.md standard
(e.g. `~/.codex/skills/`).

### 3. Claude API / Claude.ai

Upload the skill folder as a custom skill — see the
[Skills API guide](https://docs.claude.com/en/api/skills-guide) and
[Using skills in Claude](https://support.claude.com/en/articles/12512180-using-skills-in-claude).

## Maintaining the corpus

```bash
python3 scripts/setup_docs.py                 # build if missing (first run)
python3 scripts/setup_docs.py --refresh       # git pull + rebuild
python3 scripts/setup_docs.py --refresh --keep-changelog   # also index the changelog
```

## Notes

- The Scalingo documentation repository is **English only**; the skill answers in
  the user's language but the underlying facts come from the English source.
- The corpus is a cache: deleting `~/.claude/scalingo-docs/corpus` and rebuilding
  is always safe.
- This skill is unofficial and not affiliated with Scalingo. Documentation
  content belongs to Scalingo under CC BY 4.0 — see *Licensing & attribution* below.

## Licensing & attribution

*Not legal advice.* Two separate things live in this repo, licensed separately:

- **The skill code** (`setup_docs.py`, `SKILL.md`, the manifests) is your own
  work. Code is best released under a software license rather than a Creative
  Commons one — [Creative Commons itself recommends against using CC for
  software](https://creativecommons.org/faq/#can-i-apply-a-creative-commons-license-to-software).
  This repo defaults to the **MIT License** (see `LICENSE`); Apache-2.0 or the
  French **Licence Ouverte 2.0 (Etalab)** are equally reasonable. Replace the
  placeholder copyright holder before publishing.

- **The Scalingo documentation** the skill builds on is © Scalingo, released
  under **CC BY 4.0**. The skill does not bundle the docs — it fetches them at
  build time — and reformats them into a local corpus (an adaptation). CC BY has
  **no ShareAlike or NonCommercial clause**, so it imposes no licensing obligation
  on the skill code; its only requirement is attribution.

### Attribution

Documentation content is © Scalingo, adapted (cleaned and reformatted) from
<https://github.com/scalingo/documentation> under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). The skill cites the
original `doc.scalingo.com` page for every answer.

