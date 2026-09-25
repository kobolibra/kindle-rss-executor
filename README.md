# Kindle RSS Executor

A public GitHub Actions execution project for RSS collection, EPUB generation, and email delivery.

## Workflows

- **Collector:** runs four daily collection windows at 04:35, 10:35, 16:35, and 22:35 Asia/Shanghai, with manual feed selection and inspection support.
- **Digest:** is triggered by an external scheduled task at 07:35 and 17:35 Asia/Shanghai, with manual `workflow_dispatch` also available. Production runs use `dry_run=false`; use `dry_run=true` only for an intentional inspection run.

Both workflows share a state-writer concurrency lock and avoid publishing operational data as workflow artifacts.

## Runtime security

Operational configuration, delivery state, article history, email settings, and generated EPUBs are supplied only at runtime through protected integrations and secrets. They must never be committed to this public repository or printed in logs.

The workflows are intentionally limited to the permissions required for execution and state synchronization. Do not expose runtime credentials to pull-request workflows or upload operational files as public artifacts.

## Operational guidance

Run the Collector before a Digest window when fresh RSS content is required. Use the Digest dry-run mode to inspect EPUB generation without sending email or marking content delivered. Use a normal production run for actual delivery, and verify the resulting delivery state through the application console.

## Security

Report suspected credential exposure or sensitive-data leakage privately to the repository owner. Never post tokens, passwords, recipient addresses, feed URLs, article content, delivery state, or generated EPUBs in issues or pull requests.
