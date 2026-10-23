# Working Directory Rule

Applies to backend and frontend.
Each sub-project's `workspace.md` adds only what is specific to it.

## What is `.workspace/`

`.workspace/` exists at the root of each sub-project.
It is the agent's working directory for every task: plans, reports, progress files, and scratch work all go here.
Content is not tracked by git; each developer has their own copy.
Writing here does not affect the project or other developers.

## First action - every session, every task

1. Check `.workspace/progress/` for any `*_progress.md` files.
   If one exists for the current task, read it to determine what is complete and what is pending before doing anything else.
2. Check `.workspace/plans/` for pending work that has not yet started.

## All output goes to `.workspace/`

Do not write plans, reports, or intermediate files to the project root or to any project-owned directory unless the file genuinely belongs to the project: source code, tests, migrations, or official documentation.

`.workspace/` is untracked by rule: `.workspace/.gitignore` lets only `.gitignore`, `.gitkeep` and `README.md` through.
A script that a future session must be able to run therefore does not belong in `.workspace/`: it is gone for the next session and for every other machine.
Such a script goes in the tracked `scripts/` directory at the repository root.

## Default subdirectories

| Folder | Contents |
|--------|---------|
| `plans/` | Upcoming work not yet started |
| `progress/` | Active session state - progress tracking files |
| `reports/` | Completed outputs - scans, summaries, PR text |
| `tmp/` | Scratch work; do not rely on these persisting |

Agents may create additional subfolders within any of the above as needed.

## File naming

Name a file as if explaining it to a colleague with no prior session context: `{task-name}_progress.md`, `{task-name}_plan.md`, `{topic}_report.md`.
Other agents in future sessions navigate this folder to work out what has been done.
