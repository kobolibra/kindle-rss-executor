# Kindle RSS Executor

This repository is a **public Actions runner project** for the private canonical repository `kobolibra/kindle-rss-github`.

## Security boundary

This repository must never contain the real feed configuration, `state/delivery-state.json`, article history, SMTP values, Kindle address, or generated EPUBs. Each workflow checks out the private repository into a temporary runner directory, runs the collector or digest there, and pushes only the updated state file back to the private repository.

The required secret is `PRIVATE_REPO_TOKEN`. It must be limited to the private repository with Contents read/write and Metadata read. Do not grant administration, visibility, Actions-write, or repository-deletion permissions.

Digest also requires `GMAIL_USER`, `GMAIL_APP_PASSWORD`, `KINDLE_EMAIL`, `SMTP_HOST`, `SMTP_PORT`, `SMTP_SECURE`, and `SMTP_FROM` as Actions secrets. Do not expose these to pull-request workflows.

## Planned workflows

- Collector: retain the private project's four daily collection windows and manual dry-run/feed selection.
- Digest: retain manual dispatch semantics and all existing delivery inputs.
- Both workflows share a private-state writer lock and never publish state as an artifact.

## First-run procedure

Configure the secrets, run the collector in dry-run mode, then validate a real state-only collection. Validate the digest separately with a controlled manual delivery. Confirm that no public artifacts are created and that logs contain no feed URLs, article payloads, recipient addresses, or credentials. Keep the private repository's existing workflows enabled until validation is complete; only then disable the private daily path.

## Non-negotiable rules

- Do not commit `config/feeds.json` or `state/delivery-state.json` here.
- Do not upload private state, article content, or EPUBs as public artifacts.
- Do not print feed URLs, article payloads, SMTP values, or recipient addresses.
- Use the shared `rss-state-writer` concurrency group.
- Never allow fork pull-request workflows to access private-repository or SMTP secrets.
