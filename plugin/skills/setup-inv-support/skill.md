---
name: setup-inv-support
description: Bootstrap installer for eSky Inventory Support tools. Triggers on 'setup inventory support', 'install inv tools', 'configure inventory skills', 'inv setup'. Run this FIRST before any other inventory skills.
---

# Setup Inventory Support

Bootstrap installer — sets up all inventory diagnostic tools in one command.

## What This Does

1. Clones `eskygroup/esky-inventory-support` to `~/.local/share/esky-inv-support/`
2. Fetches credentials from GCP Secret Manager (`inv-support-env`)
3. Installs npm dependencies
4. Adds the skills directory to your Claude Code config

After setup, you'll have access to all inventory investigation skills:
- `/run triage INV-1234` — triage a Jira issue
- `/run diagnosis INV-1234` — run diagnostics
- `/run solution INV-1234` — propose a fix
- `tool-bigquery`, `tool-bizon-api`, `tool-jira`, `tool-hotway-api`, and more

## Prerequisites

- `gcloud` CLI: https://cloud.google.com/sdk/docs/install
- `node` ≥ 18: https://nodejs.org
- `git`: standard tooling
- GCP access to `esky-ets-logs-pro` with `roles/secretmanager.secretAccessor`
  *(if you don't have it, ask your team lead)*

## Run Setup

```bash
git clone https://github.com/eskygroup/esky-inventory-support.git ~/.local/share/esky-inv-support
cd ~/.local/share/esky-inv-support
./setup.sh
```

`setup.sh` will:
- Check `gcloud auth` (prompt login if needed)
- Fetch `.env` from `inv-support-env` secret in GCP
- Run `npm install` in `.github/skills/`
- Add `~/.local/share/esky-inv-support/.github/skills` to `~/.claude/settings.json`

Then **restart Claude Code** — all inventory skills will be available.

## Verify

After restart, test:

```bash
# Should list BigQuery results
node ~/.local/share/esky-inv-support/.github/skills/tool-bigquery/bigquery.js logs esky-hotels-api "$(date -d '1 hour ago' '+%Y-%m-%dT%H:%M')" "$(date '+%Y-%m-%dT%H:%M')" Error

# Should list hotel mappings
node ~/.local/share/esky-inv-support/.github/skills/tool-bizon-api/bizon-api.js hotels list 3
```

## Updating

To get the latest skills and credential updates:

```bash
cd ~/.local/share/esky-inv-support
git pull
./setup.sh
```

## Troubleshooting

Run the guided credential setup skill:

```
/tool-setup-credentials
```

Or check: `~/.local/share/esky-inv-support/.github/skills/tool-setup-credentials/skill.md`
