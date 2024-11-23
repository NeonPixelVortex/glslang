# Sync with Upstream Workflow Documentation

This workflow automatically synchronizes our fork with it's upstream repository at periodic intervals.

## Details

Default config:

- **File**: `.github/workflows/sync.yml`
- **Schedule**: Runs weekly {Sundays at midnight (UTC)}
- **Manual Trigger**: Also supported
- **Target Branch**: `main`
- **Upstream Repository**: `https://github.com/KhronosGroup/glslang.git`

## Customization

#### Sync Schedule

Via cron:

- '0 0 * * *'  # Run every day at midnight
- '0 0 * * 0'  # Run every Sunday at midnight
- '0 0 1-7 * 0' # Run on the first Sunday of every month
...

#### Sync Branch and Repo

Via evironment variables:

- `SYNC_BRANCH`: Target branch for synchronization
- `UPSTREAM_REPO`: URL of the upstream repository

## Process

#### ⚠️ May overwrite modified files on merge conflict due to conflict resolution strategy, which prefers upstream changes, keep in mind while using.

```mermaid
flowchart TD
    A[Start] -- Every Sunday --> B[Checkout Repository]
    B --> C[Set up Git Config]
    C --> D[Add/Fetch Upstream Repo]
    D --> E[Check if Sync Needed]
    E -- No Changes --> F[End]
    E -- Changes Needed --> G[Attempt Merge]
    G -- Success --> I[Push Changes]
    G -- Conflict --> H[Resolve Conflicts with -X theirs]
    H --> I
    I -- Success --> F
    I -- Failure --> J[Pull/Rebase and Retry Push]
    J --> F
    F -- On Any Failure --> K[Create/Update Issue]
```

## Troubleshooting

If sync fails, the workflow will:
1. Create an issue with label `sync` and `bot`
2. Include failure details and workflow log link
3. Update existing issue if one already exists with a comment

***P.S.*** **Check the workflow logs for more details.**
_The latest commit SHA of the local and upstream branch along with the outputs of git status and git diff on merge is logged._
