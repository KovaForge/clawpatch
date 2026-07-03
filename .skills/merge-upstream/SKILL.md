# clawpatch Merge Upstream

## Overview
Fetches the latest from the OpenClaw upstream (`openclaw/clawpatch`), rebases the local main branch onto it, and pushes to Aoife's fork remote. This keeps the fork in sync with upstream while preserving Aoife's commits on top.

## Prerequisites
- Fork remote `aoife` → `https://github.com/McoreD/clawpatch.git`
- Upstream remote `openclaw` → `https://github.com/openclaw/clawpatch.git`
- Aoife's GitHub token available via `gh auth token`
- Working tree clean (no uncommitted changes)

## Execution

```bash
cd "$CLAWPATCH_DIR"
git fetch openclaw --tags

LATEST=$(git describe --tags --abbrev=0 openclaw/main 2>/dev/null || echo "")
CURRENT=$(git describe --tags 2>/dev/null || echo "")

if [ "$LATEST" = "$CURRENT" ]; then
  echo "Already at latest upstream tag: $LATEST"
  exit 0
fi

echo "Upstream is at: $LATEST (currently: $CURRENT)"
echo "Rebasing Aoife commits onto openclaw/main..."

git checkout -B sync upstream/main
git checkout main
git rebase --onto openclaw/main --root

# Push aoife remote with token
git push https://oauth2:"$(gh auth token)"@github.com/KovaForge/clawpatch.git main --force-with-lease

echo "Done. Pushed to aoife/main."
```

## Verification
After running, confirm with:
```bash
git log --oneline -5
git-aoife push
```
