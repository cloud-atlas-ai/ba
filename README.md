# ba

Simple task tracking for LLM sessions.

```
ba - because sometimes you need to go back before bd
```

A spiritual fork of [beads](https://github.com/steveyegge/beads) (`bd`), keeping the simplicity of v0.9.6 with added session-based claiming for multi-agent coordination.

## Philosophy

- **Dead simple**: Single binary, minimal dependencies
- **Plain text**: JSONL files, human-readable, git-friendly
- **Multi-agent**: Sessions claim issues, obvious ownership
- **Zero infrastructure**: No SQLite, no daemon - just files

## Installation

```bash
cargo install ba
# or build from source
cargo build --release
```

## Quick Start

```bash
# Show quick start guide for LLMs
ba quickstart

# Initialize in your project
ba init

# Create issues
ba create "Fix auth bug" -t bug -p 1
ba create "Add feature" -t feature -d "Description here"

# List issues (excludes closed by default)
ba list
ba list --all              # Include closed
ba list --status open      # Filter by status

# Show issue details
ba show ab-x7k2

# Update issues
ba update ab-x7k2 --status in_progress
ba update ab-x7k2 --priority 0
ba update ab-x7k2 --assignee alice

# Close issues
ba close ab-x7k2 --reason "Fixed in commit abc123"
```

## Dependencies

Track blocking relationships between issues:

```bash
# Add a blocking dependency (blocker blocks id)
ba block ab-x7k2 ab-y8m3    # ab-x7k2 is now blocked by ab-y8m3

# Remove a blocking dependency
ba unblock ab-x7k2 ab-y8m3

# Visualize dependency tree
ba tree ab-x7k2
# Output:
# ab-x7k2: Fix auth bug [OPEN]
# └── ab-y8m3: Add user model [IN_PROGRESS]

# Detect circular dependencies
ba cycles
```

## Ready Queue

Show issues ready to work on (open + not blocked):

```bash
ba ready
# Output:
#   ID        P  TYPE     TITLE
#   ------------------------------------------------------------
#   ab-x7k2   0  bug      Fix critical auth bug
#   ab-z9n4   1  feature  Add dashboard
#   ab-a1b2   2  task     Write tests
#
# 3 issue(s) ready
```

An issue is "ready" when:
- Status is `open` (not `in_progress` or `closed`)
- All blocking issues are `closed` (or has no blockers)

## Multi-Agent Coordination

When multiple LLM agents work on the same codebase, use claims to coordinate:

```bash
# Claim an issue (caller provides their session ID)
ba claim ab-x7k2 --session claude-abc123
# Sets status to in_progress, records session_id

# See what you've claimed
ba mine --session claude-abc123

# Release when done or switching
ba release ab-x7k2
# Sets status back to open, clears session_id
```

Claiming prevents other agents from working on the same issue. The session ID is whatever the caller provides (e.g., Claude's session ID).

## Labels and Comments

Organize and discuss issues:

```bash
# Add/remove labels
ba label ab-x7k2 add urgent
ba label ab-x7k2 add backend
ba label ab-x7k2 remove urgent

# Add comments
ba comment ab-x7k2 "Found the root cause" --author claude
ba comment ab-x7k2 "Fixed in commit abc123"  # defaults to anonymous
```

Labels and comments are shown in `ba show` output.

## Importing from Beads

Migrate issues from a beads (`bd`) export file:

```bash
# Import with new IDs (uses project prefix)
ba import .beads/issues.jsonl

# Keep original beads IDs
ba import .beads/issues.jsonl --keep-ids
```

The import handles dependencies automatically and provides clear error messages:

```
Imported 112 issues (0 skipped, 1 errors)

Errors:
  Line 46: Issue 'as-9q7' - issue_type: Unknown type 'merge-request', expected bug/feature/task/epic/chore/refactor/spike
```

Only `blocks` dependencies are imported (other types like `related`, `parent-child`, `discovered-from` are skipped).

## Issue Types

- `bug` - Something broken
- `feature` - New functionality
- `task` - Work item
- `epic` - Large feature composed of subtasks
- `chore` - Maintenance work
- `refactor` - Code restructuring
- `spike` - Research/investigation

## Priorities

- `0` - Critical (security, data loss, broken builds)
- `1` - High (major features, important bugs)
- `2` - Medium (default - nice-to-have features, minor bugs)
- `3` - Low (polish, optimization)
- `4` - Backlog (future ideas)

## Storage

Data stored in `.ba/` directory:
- `config.json` - Project config (version, ID prefix)
- `issues.jsonl` - One issue per line, sorted by ID

### Why JSONL?

- **Git-friendly**: One issue per line = conflicts are per-issue
- **Human-readable**: Easy to inspect with standard tools
- **Grep-able**: `grep ab-x7k2 .ba/issues.jsonl`

## IDs

Format: `{prefix}-{random}` (e.g., `ab-x7k2`)

- **prefix**: 2 chars derived from project path hash
- **random**: 4 chars lowercase alphanumeric

Same project always gets same prefix, different projects get different prefixes.

## JSON Output

All commands support `--json` for programmatic use:

```bash
ba --json list
ba --json show ab-x7k2
ba --json create "New issue" -t task
```

## Acknowledgment

`ac` is inspired by [beads](https://github.com/steveyegge/beads) by Steve Yegge - an excellent issue tracker for AI-assisted development. We loved beads v0.9.6's simplicity before it evolved into a full messaging/routing system. `ac` takes that original simplicity and adds session-based claiming for multi-agent coordination.

## License

Source-available. See [LICENSE](LICENSE) for details.
