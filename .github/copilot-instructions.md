# Copilot Instructions for gh-common-workflows

## Repository Purpose

This is a centralized GitHub Actions workflows repository that serves as a **source for syncing common workflows** to multiple target repositories. The goal is to maintain workflow configurations in one place and automatically propagate them to all dependent repositories.

## Architecture

### Workflow Structure

See [README.md](../../README.md) for the current workflow list. As of now:

**Public Workflows** (distributed to target repos):

- `linter.yml` - Runs Super-linter (`super-linter/super-linter`) directly
- `mdlink.yml` - Runs Markdown link checker (`gaurav-nelson/github-action-markdown-link-check`) directly

**Utility Workflows** (internal sync helpers, excluded from sync):

- `sync.yml` - Syncs `.github/` folder to target repositories via rsync
- `_sync_secrets.yml` - Syncs GitHub repository secrets across multiple repositories

**Reserved pattern** (excluded from sync when present):

- `wf_*.yml` - Internal reusable workflows (none currently; reserved for future use)

### Sync Process

The sync system works by:

1. **Source repo** (this repository) contains all workflow configurations
2. **Target repos** include a copy of `sync.yml` to pull changes
3. `sync.yml` uses rsync to copy `.github/` contents while **excluding**:
   - Workflows starting with `_` (underscore)
   - Workflows starting with `wf_` (internal reusable)
   - Old `markdown-link-check.yml` and related directories
4. Syncs are triggered on PR to `main` or manual `workflow_dispatch`
5. Changes are auto-committed to target repos using git bot account

## Key Conventions

### Workflow Naming

- **Public workflows**: No prefix (e.g., `linter.yml`) — synced to target repos
- **Reserved reusable (internal)**: `wf_` prefix (e.g., `wf_linter.yml`) — excluded from sync
- **Utility/Admin**: `_` prefix (e.g., `_sync_secrets.yml`) — excluded from sync

This naming scheme ensures only public workflows are synced to target repositories.

### Environment Variables

- `SOURCE_REPO`: Full repository path (e.g., `pacroy/gh-common-workflows`)
- `REPO_LIST_REGEX`: When `true`, treat repository patterns as regex expressions
- Secret patterns use regex for flexible matching (e.g., `^SYNC_PAT$`)

### File Exclusion in rsync

Patterns are defined in `sync.yml` under the "Sync files" step:

```bash
--exclude="workflows/_*.yml"
--exclude="workflows/wf_*.yml"
--exclude="copilot-instructions.md"
```

`copilot-instructions.md` is excluded because it contains repository-specific AI guidance that should remain local to each repository.

Always update the rsync `--exclude` flags in `sync.yml` when adding new internal workflows or files.

## Testing Workflows

### Dry-run Workflows

Several workflows support `workflow_dispatch` with `dry_run` input:

- `_sync_secrets.yml` - Use `dry_run: true` to preview changes without applying them
- Test on a specific target repo regex pattern before rolling out

### Linting and Link Checking

These are automatically triggered on:

- Push to `main`
- Pull requests to `main`
- Manual `workflow_dispatch`

To test locally:

- **Linting**: Super-linter is configured in `linter.yml` with `VALIDATE_ALL_CODEBASE: true`
- **Markdown links**: Configured via `.github/mdlink/mlc_config.json`
- **JSCPD**: Uses `.jscpd.json` at repo root; do NOT set `JSCPD_CONFIG_FILE` in the linter workflow

## GitHub Token & Permissions

When setting up sync in target repositories, use a Personal Access Token (`SYNC_PAT`) with:

**Classic token:**

- `repo` scope (full control of private repositories)
- `workflow` scope (update GitHub Action workflows)

**Fine-grained token:**

- Repository > Contents: Read and write
- Repository > Metadata: Read-only
- Repository > Secrets: Read and write
- Repository > Workflows: Read and write

## Configuration Files

- `.github/mdlink/mlc_config.json` - Markdown link checker configuration (retries, timeouts, headers)
- `.claude/settings.local.json` - Permissions for Claude sessions in this repo

## Making Changes

1. Create a branch from `main`
2. Update workflows (both public and internal)
3. Push and create a PR to `main`
4. Workflows auto-run on PR:
   - Linter checks code
   - Markdown link checker validates documentation
   - Sync workflow shows a preview of what will sync to target repos
5. Merge PR when ready
6. To release updates to target repos:
   - Merge the workflow updates into the source repository
   - Target repos will pull the latest changes from the source repository on their next sync trigger

## Key Dependencies

Always pin action versions. Current versions in use:

- **actions/checkout**: v7
- **actions/github-script**: v9
- **super-linter/super-linter**: v8.7.0
- **gaurav-nelson/github-action-markdown-link-check**: 1.0.17
