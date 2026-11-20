---
name: pick-up
description: Picks up one pog by name (POGflow). Finds the pog the user named, checks that its copy is current and that no other session is on it, reads all of it and the plan it links, then carries on from Next. Use when the user types /pick-up dose-response or says "pick up dose-response", often with an answer attached ("pick up dose-response. Use vehicle"). Use unasked when a session is given work by name at its start, and again after a compaction or a long gap, to re-read the pog from disk.
argument-hint: "<pog name> [answer]"
---

# Pick up

The words (pog, face, log, stack, pick up, slam, flip, stop) are defined in the POGflow README. It is at `${CLAUDE_PLUGIN_ROOT}/README.md` (the top of the repo these skills are in), or at https://github.com/stephenholtz/POGflow. Read it first. Request: `$ARGUMENTS`

1. **Helper.** If another agent launched you (a swarm subagent, a workflow step), do not pick up. Read the pog as context only.
2. **Find the repo.** Look for a `pogs/` folder from the working directory upward, then in the repo the project's CLAUDE.md names. If there is none and the user asked for this, say POGflow is not set up here, ask where `pogs/` is, and stop. If nobody asked, carry on without POGflow and say nothing about it. From here use `git -C <repo>` and absolute paths.
3. **Update the clone.** `git fetch`, then `git merge --ff-only origin/main` (the branch's upstream, if it is not `main`). If that fails, say what blocks it and how many commits behind the clone is (`git rev-list --count HEAD..origin/main`). Read origin's copy with `git show origin/main:pogs/<file>`, and say that is what was read.
4. **Match the name.** Try the file names in `pogs/`, then the titles and Tags lines. One match: go on. Several: show each candidate's face and ask. None: show the nearest names and ask whether this is new work; if it is, go to step 7. If State is `done` or `parked`, say so and ask before reopening.
5. **Check nobody else is on it.** Look at `git status --porcelain -- pogs/<file>` and the top log line. If the file has uncommitted changes on this machine, or the top log line is from today and carries another session's id (or no id, and another machine), show the user and ask whether that session is closed. Compare ids, never names: a session's name changes. This session's own id is found as the slam skill's step 7 says. Leave what is there as it is.
6. **Read.** Read the whole pog, the plan it links, and every Q with no A.
7. **Answer or new work.** If the user's message carried an answer, use the slam skill to write the A beside its Q before starting work. If the user confirmed new work, slam at once so the pog exists on origin.
8. **Say where it stands, then carry on.** Three lines: State, what waits on the user with its dates, Next. Then do the work. From here every stop is a slam.

## Never

- Write a pog except through the slam skill.
- Pick up a pog another session is working on. If part of it is needed, ask the user about a split.
- Stash, reset or rebase to make the update work. Overwrite or revert uncommitted changes in a pog file.
- Work from the stack line alone, or from memory of the pog from before a compaction.
- Add a lock or a claim marker.
- Start on a second pog uninvited.
