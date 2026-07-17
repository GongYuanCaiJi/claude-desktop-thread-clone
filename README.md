# clone-thread

Clone a **Claude Code Desktop** conversation into another workspace folder — a
non-destructive copy. The original thread stays exactly where it is; you get a
second, fully independent thread you can open and keep chatting in.

There is no official way to do this. The Desktop app can only *continue* a
thread where it lives, and the community `mv`-style tools **move** the
transcript (the source disappears). `clone-thread` copies instead, so nothing
you already have is put at risk.

## Why cloning, not moving

A Desktop conversation is two files that must agree:

| layer | path |
|---|---|
| **transcript** (the actual messages) | `~/.claude/projects/<slug>/<cliSessionId>.jsonl` |
| **tile** (the sidebar entry) | `~/Library/Application Support/Claude/claude-code-sessions/<acct>/<org>/local_<id>.json` |

where `<slug>` is the working-directory path with every non-alphanumeric
character replaced by `-` (matching how the Desktop app itself names the folder).

A *move* relocates the real transcript — if anything is off, or you do it to a
live thread, that thread breaks. A *clone* only ever **reads** the source and
**writes** a fresh copy with a new identity, so the worst case leaves your
original untouched.

## Requirements

- **macOS** — the tile and transcript paths are macOS-specific
  (`~/Library/Application Support/Claude/…`).
- **Python 3.6+** — standard library only, no dependencies.
- **Claude Code Desktop**.

## Usage

```
clone-thread <path-to-source-tile.json> <target-cwd>
```

- `<path-to-source-tile.json>` — the `local_*.json` tile of the thread you want
  to clone (in the `claude-code-sessions/.../` directory above).
- `<target-cwd>` — the workspace folder the clone should belong to. **It must
  already exist** (the app opens the thread against this folder; if it is
  missing the thread won't load — the tool refuses rather than produce a broken
  clone).

Then **restart Claude Desktop** — the app only scans the session directory at
startup, so a freshly written tile won't appear until you reopen it.

## What it does — three steps, no guesswork

1. **Read the source tile** for `cliSessionId` (which transcript to copy), `cwd`
   and `title`.
2. **Copy that one transcript line-for-line**, changing only `sessionId` and
   `cwd`. `parentUuid` is never touched — the compaction boundaries that keep a
   long thread's context bounded must stay intact.
3. **Write a new tile** based on the source tile, with a fresh session id, fresh
   timestamps, and every *environment*-scoped field stripped (permissions, git
   branch, PR links, counters). A clone is a new thread; it must not inherit the
   source's granted permissions or git identity.

## Notes & limits

- **The clone is a snapshot.** Messages added to the source *after* you run the
  tool are not in the clone.
- **Long, already-compacted history:** the Desktop app only displays the single
  `cliSessionId` transcript, so `clone-thread` copies exactly what the app would
  show. Earlier segments that compaction has dropped are out of scope.
- Paths containing a symlink (e.g. macOS `/tmp` → `/private/tmp`) are
  canonicalised so the tile and the transcript folder always agree.

## Prior art / credits

The two-layer (transcript + tile) mechanics build on the folder-migration work
of:

- [`claude-mv`](https://github.com/curiouslychase/dotfiles/blob/main/scripts/claude-mv)
  by **curiouslychase** — the original "move a project and keep its Claude
  context" script.
- [Directory-migration mechanics](https://gist.github.com/gwpl/e0b78a711b4a6b2fc4b594c9b9fa2c4c)
  by **gwpl**.

The slug rule and tile-field handling were derived directly from the Claude
Desktop app to match its own behaviour.

## License

MIT — see [LICENSE](LICENSE).
