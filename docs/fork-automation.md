# Fork automation

`umbrella` is synchronized weekly from `Umbrella-IT-Group/metamcp:umbrella` by
`.github/workflows/upstream-sync.yml`. The workflow creates a sync pull request
from `automation/sync-umbrella`, runs the normal Docker build check, and enables
GitHub auto-merge. A conflict produces a visible reconciliation pull request
instead of altering `umbrella`.

Set the repository secret `SYNC_TOKEN` to a fine-grained GitHub token owned by
the sync bot. It needs **Contents: read/write** and **Pull requests: read/write**
access to `alphyriver/metamcp`. A separate token is necessary because actions
created with `GITHUB_TOKEN` do not trigger the pull-request and push workflows
that provide the sync gate and release.

Every push to `umbrella` builds `linux/amd64` and `linux/arm64`, publishes
`ghcr.io/alphyriver/metamcp`, and creates an immutable `YYYY.MM.DD.N` Git tag,
matching GitHub Release, full commit-SHA image tag, and `latest` convenience tag.
Production deployments must use the calendar-version tag, not `latest`.
