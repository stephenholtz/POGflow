---
name: pick-up
description: Picks up one pog by name (POGflow). Finds the pog the user named, checks that its copy is current and that no other session is on it, reads all of it and the plan it links, then carries on from Next. Use when the user types /pick-up sorter-tags or says "pick up sorter-tags", often with an answer attached ("pick up sorter-tags. One tag per unit"). Use it only when asked, in a message or in standing instructions such as CLAUDE.md, never on your own. A session that already holds a pog also uses it after a compaction or a long gap, to re-read the pog from disk.
argument-hint: "<pog name> [answer]"
---

# Pick up

The terms (pog, face, log, notes, stack, pick up, slam, flip) are defined in the POGflow README. It is at `${CLAUDE_PLUGIN_ROOT}/README.md` (the top of the repo these skills are in), or at https://github.com/stephenholtz/POGflow. Read it first. Request: `$ARGUMENTS`

1. **Helper.** If another agent launched you (a swarm subagent, a workflow step), do not pick up. Read the pog as context only.
2. **Find the repo.** Look for a `pogs/` folder from the working directory upward, then in the repo the project's CLAUDE.md names. If there is none, say POGflow is not set up here, ask where `pogs/` is, and stop. From here use `git -C <repo>` and absolute paths.
3. **Update the clone.** `git fetch`, then `git merge --ff-only origin/main` (the branch's upstream, if it is not `main`). If that fails, say what blocks it and how many commits behind the clone is (`git rev-list --count HEAD..origin/main`). Read origin's copy with `git show origin/main:pogs/<file>`, and say that is what was read.
4. **Match the name.** Try the file names in `pogs/`, then the titles and Tags lines. One match: go on. Several: show each candidate's face and ask. None: show the nearest names and ask whether this is new work that should get a pog; only if the user says yes, go to step 7. If State is `done` or `parked`, say so and ask before reopening.
5. **Check nobody else is on it.** Look at `git status --porcelain -- pogs/<file>` and every log line from today, not only the top one. If the file has uncommitted changes on this machine, or one of those lines carries another session's id (or no id, and another machine), show the user each such line and ask whether another session is still on this pog (a line may be an answer written by a session that does not hold it). If one is, say the pog is in use and do not pick it up, unless the user says to go ahead anyway; an answer that came with the request still goes into the pog, through the slam skill's step 12. This session's own id is found as the slam skill's step 7 says. Leave what is there as it is.
6. **Read.** Read the whole pog, the plan it links, and every Q with no A.
7. **Answer or new work.** If the user's message carried an answer, use the slam skill to write the A beside its Q before starting work. If the user confirmed new work, slam at once so the pog exists on origin.
8. **Say where it stands, then carry on.** Three lines: State, what waits on the user with its dates, Next. Then do the work. From here the slam skill runs at each of the moments its description names.

## Never

- Write a pog except through the slam skill.
- Pick up a pog another session is working on, unless the user says to go ahead anyway. If part of it is needed, ask the user about a split, which the session that holds it makes.
- Stash, reset or rebase to make the update work. Overwrite or revert uncommitted changes in a pog file.
- Work from the stack line alone, or from memory of the pog from before a compaction.
- Add a lock or a claim marker.
- Start on a second pog uninvited.
