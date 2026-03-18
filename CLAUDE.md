# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

This is a **Claude Code plugin** (skill) that provides API guidance for the [NiiVue](https://github.com/niivue) neuroimaging visualization library. It is NOT a runnable application — there is no build step, no tests, and no dependencies. The repo contains only markdown skill files and plugin configuration.

## Repository Structure

- `.claude-plugin/plugin.json` — Plugin manifest (name, version, metadata)
- `.claude-plugin/marketplace.json` — Marketplace distribution config
- `skills/niivue/SKILL.md` — Main skill file with YAML frontmatter (name, description, version). This is what Claude Code loads when the skill triggers.
- `skills/niivue/api-reference.md` — Detailed property tables, methods, events, and constants
- `skills/niivue/examples.md` — 10 curated working code examples

## How the Skill Triggers

The `description` field in `SKILL.md` frontmatter controls when Claude activates this skill. It triggers when:
- Code imports `niivuegpu` or `niivue`
- User asks about displaying brain images, medical volumes, or neuroimaging meshes in a browser

## Automated Sync Workflow

`.github/workflows/sync-skill.yml` runs daily (8:00 AM GMT) and on manual dispatch. It:
1. Checks out the upstream `niivue/niivue` source repo
2. Uses GitHub Copilot CLI to compare the NiiVue source API against the skill files
3. Updates skill files if the API has changed
4. Bumps patch version in both `plugin.json` and `marketplace.json` if changes are made

Key NiiVue source files it scans: `NVControlBase.ts` (API surface), `NVConstants.ts` (enums), `NVEvents.ts` (events), `NVTypes.ts` (interfaces), `docs/api.md`, `docs/events.md`.

## Versioning

Version appears in three places and must stay in sync:
- `.claude-plugin/plugin.json` → `version`
- `.claude-plugin/marketplace.json` → `plugins[0].version`
- `skills/niivue/SKILL.md` → frontmatter `version`

## Editing Guidelines

- Preserve the existing structure and formatting style of skill files
- `SKILL.md` should stay concise — it's loaded into Claude's context on every trigger. Detailed reference goes in `api-reference.md` and `examples.md`.
- The SKILL.md frontmatter `description` field is critical for skill triggering accuracy — changes affect when Claude activates this skill
