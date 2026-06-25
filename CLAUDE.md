# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A **tooling-only workspace** for migrating the Weiser Center for Real Estate website (University of Michigan, Ross School of Business) from WordPress Classic Editor to Gutenberg. It contains no WordPress core, plugin, or theme code — only WP-CLI scripting infrastructure. The actual site code lives in a separate GitHub repo managed by Jesse Schmidt (schmijs@umich.edu) and is deployed via WP Engine.

## Setup

Requires PHP 7.4+ and Composer installed globally.

```bash
composer install
```

This installs WP-CLI pinned to the version in `composer.lock`. Never run `composer update` unless intentionally upgrading; always commit `composer.json` and `composer.lock`, never `vendor/`.

## WP-CLI invocation

```bash
./bin/wp <command>          # wrapper script
composer wp -- <command>   # equivalent via Composer scripts
```

Against WP Engine staging (always use staging first):

```bash
./bin/wp --ssh=<user>@<host>/path/to/wordpress <command>
```

SSH credentials are in the WP Engine portal under "SSH Gateway".

## Migration commands

```bash
# List all pages
./bin/wp post list --post_type=page --fields=ID,post_title,post_status

# Inspect raw content of a page
./bin/wp post get <ID> --field=post_content

# Run Gutenberg block migration helper (requires migration plugin active on site)
./bin/wp block migrate <ID>

# Snapshot active plugins before any changes
./bin/wp plugin list --format=csv > plugin-snapshot.csv

# Verify environment
./bin/wp --info
```

## Key constraints

- **Staging first, always.** Never run bulk WP-CLI operations against production without a successful staging run.
- **Backup before bulk ops.** Trigger a manual WP Engine backup in the portal before any batch migration commands.
- **GitHub flow**: work on `dev` branch; Jesse deploys `dev` → `prod`. No force-pushes to either branch.

## Site context

- Host: WP Engine (not Pantheon). Staging is one-click in portal.
- GitHub repo has two branches: `dev` and `prod`. PhireGroup built the original site; this migration is team-owned.
- ~40 pages need migration. Primary end user is Natalie Keene (nkkeene@umich.edu), who will edit content post-migration.
- **Deadline: June 30, 2026** — Advisory Board presentation; Gutenberg migration must be running on staging.

See `CONTEXT.md` for full project context, team contacts, and content issues to fix during migration.
