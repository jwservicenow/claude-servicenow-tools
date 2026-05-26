# claude-servicenow-tools

Claude Code slash commands that ground ServiceNow answers in official documentation. Built for ITOM practitioners doing customer-facing work where every claim needs a real citation.

## What's included

- **`/servicenow_rag`** — routes ServiceNow technical questions through the official GitHub markdown mirror (`ServiceNow/ServiceNowDocs`), supplements with Community for operational context, falls back through a trust hierarchy (KB → Community → third-party with explicit flags), and halts cleanly if nothing retrievable. No fabricated table names, no uncitable claims.

## Why use it

- **Verifiable citations.** Every claim has a URL that was actually retrieved at query time, not pattern-matched from training data.
- **No more wrong table names.** `cmdb_ci_win_server` (real) instead of the hallucinated `cmdb_ci_windows_server`. RHEL 6+ (documented) instead of the invented RHEL 7/8/9.
- **Zero infrastructure.** No vector DB, no embedding pipeline, no scheduled crawl. ServiceNow publishes the mirror; the skill just retrieves from it.

## Install

```bash
mkdir -p ~/.claude/commands
curl -o ~/.claude/commands/servicenow_rag.md \
  https://raw.githubusercontent.com/jwservicenow/claude-servicenow-tools/main/servicenow_rag.md
```

If you run separate Claude CLI profiles (e.g. `claudep`, `claudew`), copy into each:

```bash
mkdir -p ~/.claude-personal/commands ~/.claude-work/commands
cp ~/.claude/commands/servicenow_rag.md ~/.claude-personal/commands/
cp ~/.claude/commands/servicenow_rag.md ~/.claude-work/commands/
```

## Use

```
/servicenow_rag what sys_property controls Discovery IP range exclusions?
/servicenow_rag system requirements for Service Mapping
/servicenow_rag difference between cmdb_rel_ci and svc_ci_assoc
```

## Update later

Re-run the same `curl` command. Overwrites your local copy with the latest version from this repo.

## Verify it's working

Ask something too specific to fake from memory:

```
/servicenow_rag what sys_property controls Discovery IP range exclusions?
```

If Claude fetches `llms.txt` before answering, the skill fired. If it answers immediately with no fetch, something prevented activation.

## Constraints

- **Claude Code only.** Claude Desktop's fetch policy blocks the raw GitHub URLs this skill needs.
- **Default branch is `australia`** (current GA). For release-scoped work, mention the branch in your question (e.g., "on xanadu, what changed in...").
- **Mirror has known bugs.** Some files miss `canonical_url` frontmatter — the skill constructs the docsite URL manually. Some internal `../reference/` links resolve to 404s — the skill ignores them and navigates via the bundle index instead.

## License

MIT — see [LICENSE](LICENSE).
