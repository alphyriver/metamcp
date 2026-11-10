# Fork automation

`umbrella` is synchronized weekly from `Umbrella-IT-Group/metamcp:umbrella` by
`.github/workflows/upstream-sync.yml`. The workflow creates a sync pull request
from `automation/sync-umbrella`, runs the normal Docker build check, and enables
GitHub auto-merge. A conflict produces a visible reconciliation pull request
instead of altering `umbrella`.

Paths the fork deleted on purpose are listed in `.github/sync-keep-deleted.txt`.
When upstream changes one of them, the sync keeps it deleted rather than failing
on a modify/delete conflict. Only those exact paths, and only modify/delete
conflicts on them, are resolved automatically. Before this list existed,
`.github/workflows/docker-publish.yml` failed the sync every week from
2026-10-18 to 2026-11-08.

Any other conflict opens (or refreshes) a reconciliation pull request from
`automation/reconcile-umbrella`, a branch this repository owns, which points at
the upstream commit that failed to merge. Resolve it on that branch:

```bash
git fetch origin
git switch automation/reconcile-umbrella
git merge origin/umbrella   # fix the conflicts, commit
git push
```

The PR body lists the conflicting paths. The run that found them fails, so the
failure is visible in Actions until the reconciliation PR is merged.

Set the repository secret `SYNC_TOKEN` to a fine-grained GitHub token owned by
the sync bot. It needs **Contents: read/write** and **Pull requests: read/write**
access to `alphyriver/metamcp`. A separate token is necessary because actions
created with `GITHUB_TOKEN` do not trigger the pull-request and push workflows
that provide the sync gate and release.

Every push to `umbrella` builds `linux/amd64` and `linux/arm64`, publishes
`ghcr.io/alphyriver/metamcp`, and creates an immutable `YYYY.MM.DD.N` Git tag,
matching GitHub Release, full commit-SHA image tag, and `latest` convenience tag.
Production deployments must use the calendar-version tag, not `latest`.
