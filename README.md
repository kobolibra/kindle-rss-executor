# Kindle RSS Executor

This repository is a **public Actions runner project** for the private canonical repository `kobolibra/kindle-rss-github`.

## Security boundary

This repository must never contain the real feed configuration, `state/delivery-state.json`, article history, SMTP values, Kindle address, or generated EPUBs. Each workflow checks out the private repository into a temporary runner directory, runs the collector or digest there, and pushes only the updated state file back to the private repository.

The required secret is `PRIVATE_REPO_TOKEN`. It must be limited to the private repository with Contents read/write and Metadata read. Do not grant administration, visibility, Actions-write, or repository-deletion permissions.

Digest also requires `GMAIL_USER`, `GMAIL_APP_PASSWORD`, `KINDLE_EMAIL`, `SMTP_HOST`, `SMTP_PORT`, `SMTP_SECURE`, and `SMTP_FROM` as Actions secrets. Do not expose these to pull-request workflows.

## Workflows

- Collector: runs four times daily at 10:35, 16:35, 22:35, and 04:35 Asia/Shanghai (UTC 02:35, 08:35, 14:35, and 20:35), with manual dry-run/feed selection.
- Digest: scheduled directly in this public repository at 07:35 and 17:35 Asia/Shanghai, with manual `workflow_dispatch` also available. Scheduled runs use the repository workflow default of real delivery (`dry_run=false`). Set `dry_run=true` only for an intentional inspection run.
- Both workflows share a private-state writer lock and never publish state as an artifact.

## First-run procedure

Configure the secrets, run the collector in dry-run mode, then validate a real state-only collection. Validate the digest separately with an intentional `dry_run=true` inspection followed by a controlled real delivery. Confirm that no public artifacts are created and that logs contain no feed URLs, article payloads, recipient addresses, or credentials. The private repository is the canonical source; the public repository is the Actions executor.

## Non-negotiable rules

- Do not commit `config/feeds.json` or `state/delivery-state.json` here.
- Do not upload private state, article content, or EPUBs as public artifacts.
- Do not print feed URLs, article payloads, SMTP values, or recipient addresses.
- Use the shared `rss-state-writer` concurrency group.
- Never allow fork pull-request workflows to access private-repository or SMTP secrets.
