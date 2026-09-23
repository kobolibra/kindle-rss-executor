# Kindle RSS Executor

This repository is a **public Actions runner project** for the private canonical repository `kobolibra/kindle-rss-github`.

## Security boundary

This repository must never contain the real feed configuration, `state/delivery-state.json`, article history, SMTP values, Kindle address, or generated EPUBs. Each workflow checks out the private repository into a temporary runner directory, runs the collector or digest there, and pushes only the updated state file back to the private repository.

The required secret is `PRIVATE_REPO_TOKEN`. It must be limited to the private repository with Contents read/write and Metadata read. Digest also requires the SMTP secrets configured on this executor repository.

## Planned workflows

- Collector: retain the private project's four daily collection windows and manual dry-run/feed selection.
- Digest: retain manual dispatch semantics and all existing delivery inputs.
- Both workflows share a private-state writer lock and never publish state as an artifact.

## Non-negotiable rules

- Do not commit `config/feeds.json` or `state/delivery-state.json` here.
- Do not upload private state, article content, or EPUBs as public artifacts.
- Do not print feed URLs, article payloads, SMTP values, or recipient addresses.
- Keep the private repository's daily workflows disabled after this executor is validated, so the private repository does not consume Actions minutes.
- Never allow pull-request workflows from forks to access SMTP or private-repository secrets.
