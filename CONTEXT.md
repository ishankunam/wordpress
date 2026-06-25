# CONTEXT.md — Weiser Center WordPress: Classic → Gutenberg Migration

> Agent context for the Weiser Center WordPress migration workspace.
> Keep this file updated as decisions are made. Last updated: 2026-06-24.

---

## What this repo is

A migration workspace for porting the Weiser Center for Real Estate website
(University of Michigan, Ross School of Business) from WordPress Classic Editor
to Gutenberg. This is a tooling and scripting directory — it does not contain
WordPress core, plugin, or theme code.

---

## The site

- **Host**: WP Engine (not Pantheon)
- **Build**: Custom block-based WordPress, Advanced Custom Fields, built by
  PhireGroup (Casey Wood's team). Classic Editor plugin was active; migrating
  away from it.
- **Repo**: GitHub, two branches — `dev` and `prod`. Deploy goes through
  Jesse Schmidt (schmijs@umich.edu). Team has merge access; no formal code
  review process.
- **Page count**: ~40 pages on current WordPress that need migration.
- **Staging**: WP Engine has a one-click staging environment. Always use staging
  before touching production.

---

## Why we're migrating

The current site uses Classic Editor with custom markdown/HTML blocks. Natalie
Keene (Weiser marketing, nkkeene@umich.edu) has complained about raw HTML and
span tags appearing in editable content areas. She previously used Gutenberg
before the team switched back to Classic, so the migration should feel familiar
to her. The goal is for Natalie to update pages without developer help.

PhireGroup built the site on Classic Editor — Gutenberg is outside their
support contract. The team owns this migration fully.

---

## Approach

Build the new Gutenberg site in parallel on staging, keep the live site
running on `prod`, then swap when ready.

Agreed sequence:
1. Trigger a full WP Engine backup in the portal before doing anything.
2. Copy live site to WP Engine staging (one click in portal).
3. On staging: deactivate Classic Editor plugin, install the three Gutenberg
   migration plugins, see what breaks.
4. Fix broken blocks, test all ~40 pages.
5. When stable on staging, merge to `prod` via GitHub.

---

## WP-CLI setup

WP-CLI is installed as a Composer dev dependency — not globally, not as a PHAR.
This keeps the version pinned in `composer.lock` so all team members run the
same binary.

**Install:**
```bash
composer install
```

**Invoke (two equivalent forms):**
```bash
./bin/wp <command>
composer wp -- <command>
```

**Verify install:**
```bash
./bin/wp --info
```

**For WP Engine SSH (staging):**
```bash
./bin/wp --ssh=<wpengine-user>@<wpengine-host>/path/to/wordpress <command>
```
Substitute actual WP Engine SSH credentials. Check the WP Engine portal under
"SSH Gateway" for the host and username. SSH access requires a WP Engine plan
that includes it — confirm this before assuming remote CLI access works.

**Common commands for this migration:**
```bash
# List all pages and their status
./bin/wp post list --post_type=page --fields=ID,post_title,post_status

# Inspect raw content of a specific page
./bin/wp post get <ID> --field=post_content

# Run Gutenberg block migration helper (requires plugin active)
./bin/wp block migrate <ID>

# Export active plugin list (run before any changes as a snapshot)
./bin/wp plugin list --format=csv > plugin-snapshot.csv

# Check PHP + WP environment
./bin/wp --info
```

---

## Team

| Person | Role | Email |
|---|---|---|
| Ishan Kunam | Fullstack / tooling / alumni search | ikunam@umich.edu |
| Justin-Frederic Lagman | Design + WordPress management (capped 10 hrs/wk) | jlagman@umich.edu |
| Kieran Llarena | Fullstack / job postings | kllarena@umich.edu |
| Evan Stocker | PM | stockere@umich.edu |
| Natalie Keene | Marketing / content (primary end user of CMS) | nkkeene@umich.edu |
| Jesse Schmidt | GitHub repo + WP Engine deployment | schmijs@umich.edu |
| Amy Merrick | Managing Director, Weiser Center (client) | merricka@umich.edu |

Justin owns design and WordPress management but is time-capped. Ishan and
Kieran are absorbing implementation work. Ishan and Justin have a standing
sync (Tuesdays, 15–30 min) on migration progress.

---

## Key deadline

**June 30, 2026** — Advisory Board presentation to Amy's senior stakeholders.
Gutenberg migration must be "up and running" on staging for this date.
Each engineer gives a 30-second screen-share demo of their feature.
Ishan's demo: alumni search UI (mock frontend, fake SQLite data).

---

## Content issues to fix during migration

Pulled from Amy's feedback and the Notion content cleanup task:

- Remove markdown requirements from editable content areas
- Fix broken content blocks (caused by Classic → Gutenberg format mismatch)
- Navigation bar: currently looks like a browser bar — Justin redesigning,
  modeling on Ross website nav
- Footer: Ross logo stacked above Tauber; address = K2520, 701 Tappan
- Advisory board page: inconsistent image sizes; should be alphabetical order
- Podcast/socials: move to an "About" section
- Networking events: rename to "Regional Networking Clubs"
- Track pages: add under "For Students" and "Programs" as "Immersive Tracks"

---

## Conventions

- **Staging first, always.** Never run bulk WP-CLI operations against
  production without a successful staging run.
- **Backup before bulk ops.** Trigger a manual WP Engine backup in the portal
  before any batch migration commands.
- **Composer install, not update.** Run `composer install` to stay on locked
  versions. Only run `composer update wp-cli/wp-cli-bundle` intentionally when
  upgrading, then commit the updated `composer.lock`.
- **Commit `composer.json` and `composer.lock`.** Do not commit `vendor/`.
- **GitHub flow**: work on `dev`, Jesse deploys to `prod`. No force-pushes.

---

## What is not in this repo

- WordPress core, plugins, or theme code (lives in the WP Engine / GitHub
  site repo managed by Jesse)
- Alumni directory database or search code (separate project, separate repo)
- Job postings integration (Kieran's workstream, separate RFC)