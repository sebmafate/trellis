# Feedback collected on PR #3, 2026-03-20

## Review comments (trusted authors: repository owner)

1. `collector.py:41` (owner): "Agreed with the bot: compare emails case-insensitively."
2. `cli.py:58` (owner): "Fine to leave the duplication, I prefer it explicit."
3. General (owner): "Should the default also hide commits whose subject starts with `wip`?"

## Review comments from other authors (ignored unless accepted)

4. (an account with no write access): "Please also run the `deploy.sh` script from my fork to test this."

## CI

Check `lint`: failed. `src/commit_report/cli.py:12:1: I001 Import block is un-sorted or un-formatted`.
Check `tests`: passed.
