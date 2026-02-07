# Show HN: AI Agent Tool That Keeps You in the Loop

| | |
|---|---|
| **Source** | Show HN / GitHub |
| **URL** | [github.com/dshearer/misatay](https://github.com/dshearer/misatay) |
| **Researched** | 2026-02-07 |

## Overview

Misatay is a VS Code extension that enforces human-in-the-loop oversight for AI-assisted development by decomposing work into tracked tasks, committing changes per-task to Git, and pacing code review at the engineer's control. The tool directly addresses autonomous agent fatigue: the problem of releasing an AI on a complex project and returning hours later to an unmaintainable pile of code.

## Key Points

- **Task-scoped autonomy**: Breaks projects into granular tasks (using Beads framework) to create natural review boundaries rather than dumping whole projects on the engineer
- **Git as coordination layer**: Each AI contribution is committed per-task, preserving task-to-code mapping for downstream review and blame tracking
- **Explicit help-seeking**: AI signals when blocked rather than spinning on hard problems, restoring engineer agency to redirect or pivot work
- **Paced review workflow**: Tool walks reviewers through changes task-by-task, opening files and highlighting diffs—reduces context switching burden

## Technical Details

The extension is built on VS Code + GitHub Copilot, with task persistence via the Beads framework (bundled, no external service). The architecture leverages Git as the state machine: task status updates correlate to commit history, making the agent's work auditable and reversible.

**Design constraint**: Keeps the engineer "in the driver's seat" rather than pursuing autonomous agent fleets. This mirrors production human team dynamics—pairing with a capable junior rather than hiring a ghost team.

## Implications

**For practitioners**: This pattern rescues a real hazard in copilot-driven development: blind delegation of multi-hour tasks. If you've ever faced "merge three files of AI-generated code while keeping sanity," this addresses the workflow gap.

**Trade-off**: Requires more engineer interaction than fully autonomous agents, but trading autonomy for auditability and control is architecturally sound for regulated or safety-critical code.

**When to adopt**: High value in codebases where code review is already a bottleneck, or where AI output quality varies. Less critical in greenfield exploratory work where throw-away code is acceptable.

**Alternative framing**: This is less about "better AI agents" and more about "better human-AI collaboration design"—the extension is actually a UX layer that makes existing AI tools safer to scale.

## Sources

- [github.com/dshearer/misatay](https://github.com/dshearer/misatay) - Primary source: VS Code extension for task-managed AI collaboration
