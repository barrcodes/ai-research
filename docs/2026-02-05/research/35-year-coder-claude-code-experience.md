# I Code for 35+ Years, Now Claude Code Does 99% of the Work

| | |
|---|---|
| **Source** | Dragos Roua's Blog |
| **URL** | [dragosroua.com/vibe-coding-ios-apps-on-my-ipad-with-claude-from-my-favorite-coffee-shop](https://dragosroua.com/vibe-coding-ios-apps-on-my-ipad-with-claude-from-my-favorite-coffee-shop/) |
| **Researched** | 2026-02-05 |

## Overview

A 35+ year veteran developer shares practical workflows for using Claude Code on iPad for iOS development, emphasizing that the productivity multiplier (5x-7x) stems from combining deep architectural expertise with AI-assisted implementation. Key insight: **Claude handles coding but human experience drives prioritization, design decisions, and context management**—the limiting factor isn't technology but cognitive endurance.

## Key Points

- **Parallel project multiplexing**: Manages 3-4 concurrent iOS apps simultaneously using version branches (`version/X.x.x`) and feature gating, something younger developers typically can't sustain
- **Session-scoped cognition**: Maintains 3-4 hour daily work limit; notes that "mental clarity and strength of focus" deteriorates beyond this, requiring strategic breaks between projects
- **Branch-first workflow**: Uses remote repository branches with XCode Cloud automation, validating through GitHub PRs and TestFlight during build wait times (2-10 min cycles)
- **XCode Cloud as context glue**: Treats build pipelines as productive thinking time—enhancing prompts and writing logs while CI runs, keeping human-AI loop tight

## Technical Details

**Workflow pattern**:
1. Daily review across 4-6 project backlogs
2. Claude handles 99% of implementation (routine iOS patterns, UI scaffolding, API integration)
3. Human validates logic, reviews UX decisions, tests in TestFlight
4. Merge to master triggers XCode Cloud → production

**Resource utilization**: Leverages Apple's 25 hours/month developer plan allocation across multiple apps; infrastructure costs negligible for side projects.

**Cognitive architecture**: The claim "Claude does 99% of work" reveals a subtlety—Claude does 99% of *typing*, but the architect defines what gets typed. The 35-year context reduces decision latency and avoids architectural dead-ends that would waste tokens.

## Implications

**For experienced practitioners**:
- Claude excels at **friction removal**—eliminate boilerplate, routing, UI scaffolding—leaving you space for design intent
- **Time-to-deployment compresses from months to weeks** when you can sustain parallel projects; this requires discipline and context management
- Working from coffee shops on iPad becomes viable because most bottlenecks are cognitive (what to build next), not computational

**For hiring/team structure**:
- Juniors cannot replicate this workflow; they lack the architectural pattern library to know what to ask Claude to build
- The 3-4 hour limit suggests diminishing returns after deep focus—this might inform meeting schedules and sprint planning
- IDE mobility (iPad + remote repo) becomes feasible with agentic assistants; previously required powerful hardware

**Trade-offs**:
- Requires strong prior architecture knowledge to avoid technical debt (Claude won't auto-magically design)
- Cognitive endurance remains the bottleneck; automation doesn't compress thinking time, only implementation time
- Loss of serendipitous learning that comes from typing out implementations

## Sources

- [Dragos Roua - Vibe Coding with Claude on iPad](https://dragosroua.com/vibe-coding-ios-apps-on-my-ipad-with-claude-from-my-favorite-coffee-shop/) - Original article describing practical Claude Code workflows for iOS development
