# claude-desktop-thread-clone — Agent Guide

- Global rules live in `~/.claude/CLAUDE.md` and `~/.codex/AGENTS.md` (dotfiles); this file only holds notes specific to this repo.
- CI: `.github/workflows/ci.yml` calls the shared workflow in `GongYuanCaiJi/.github`. Run `pre-commit run --all-files` before pushing.
- Baseline files come from `GongYuanCaiJi/repo-template`; change them there and run `copier update`, not by hand.
