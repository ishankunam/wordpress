# Weiser Center WordPress Migration Tooling

This repository provides a standalone migration workspace for the Weiser Center for Real Estate website (University of Michigan, Ross School of Business) Gutenberg migration. The tooling uses WP-CLI installed locally via Composer to ensure environment and version consistency across all team members.

## Prerequisites

- **PHP 7.4+** (Note: Ensure local PHP version matches WP Engine's environment)
- **Composer** installed globally:
  - On macOS (Homebrew): `brew install composer`
  - On Linux: `apt install composer` (or equivalent package manager)

## Setup

To install WP-CLI and its dependencies locally, run:

```bash
composer install
```

This single command ensures reproducible installs across machines based on the `composer.lock` file.

## Usage

You can invoke WP-CLI in two equivalent ways:

```bash
# Using the thin wrapper script
./bin/wp --info

# Using Composer's run script command
composer wp -- --info
```

### Running Commands Against WP Engine

For WP Engine migrations, commands should be run against the staging environment first using WP-CLI's SSH forwarding. Use the following pattern:

```bash
./bin/wp --ssh=user@host/path/to/wordpress <command>
```

*Note: Replace `user@host/path/to/wordpress` with the actual WP Engine SSH user, host, and WordPress root directory path.*

---

## Common Commands for the Classic → Gutenberg Migration

Below are common commands relevant to our migration process:

```bash
# List all posts still using Classic Editor blocks
./bin/wp post list --post_type=page --fields=ID,post_title,post_status

# Check which posts have classic block content (example pipeline to inspect content)
./bin/wp post list --post_type=page --fields=ID,post_title | xargs ...

# Export a page's raw content for inspection
./bin/wp post get <ID> --field=post_content

# Run the Gutenberg migration helper (if the migration plugin is active)
./bin/wp block migrate <ID>
```

> [!IMPORTANT]
> - **Always test on the staging environment** before running commands on production.
> - **Trigger a manual backup** in the WP Engine user portal before performing any bulk operations or modifications.

---

## Team Workflow

- **Do not install WP-CLI globally** for project operations.
- **Installing updates**: When a `composer.json` change is pulled, run `composer install` (not `composer update`) to stay aligned with the team's locked versions.
- **Upgrading dependencies**: Run `composer update wp-cli/wp-cli-bundle` intentionally when you need to update WP-CLI, then commit the updated `composer.lock`.
