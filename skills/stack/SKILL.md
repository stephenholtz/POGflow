---
name: stack
description: Prints the stack (POGflow). Pulls, then shows the face of every open pog with what waits on the user first, and writes nothing. Use when the user types /stack, /stack gui or /stack parked, or asks what is on the stack, what is open or what waits on the user. Use it only when asked, in a message or in standing instructions such as CLAUDE.md, never on your own.
argument-hint: "[tag | parked]"
---

# Stack

The terms (pog, face, log, notes, stack, pick up, slam, flip) are defined in the POGflow README. It is at `${CLAUDE_PLUGIN_ROOT}/README.md` (the top of the repo these skills are in), or at https://github.com/stephenholtz/POGflow. Read it first. Request: `$ARGUMENTS`

1. **Find the repo.** Look for a `pogs/` folder from the working directory upward, then in the repo the project's CLAUDE.md names. If there is none, say POGflow is not set up here, ask where `pogs/` is, and stop. From here use `git -C <repo>` and absolute paths.
2. **Update the clone.** `git fetch`, then `git merge --ff-only origin/main` (the branch's upstream, if it is not `main`). If that fails, read origin's copy in step 3. Make the first line of the reply say so, with how many commits behind the clone is (`git rev-list --count HEAD..origin/main`).
3. **Find the open pogs.** `grep -r -A3 '^\*\*State:\*\* open' <repo>/pogs/`. For origin's copy: `git grep -A3 '^\*\*State:\*\* open' origin/main -- pogs/`. With a tag, keep the pogs whose Tags line has it, and say how many open pogs were left out. Parked pogs (`parked` in place of `open`) only when asked.
4. **Print it short enough for a phone,** as the README's example does. First the pogs with something in Waiting, oldest date first, each with its question and date. Then the other open pogs, each with its Next. Take Waiting, Next and any count from the grep output.
5. **Unfinished slams.** Add one line for each file that `git status --porcelain -- pogs/` lists on this machine (uncommitted: a slam that did not finish, or a hand edit) and each that `git diff --name-only origin/main...HEAD -- pogs/` lists (committed, not pushed).
6. **Ask which pog to pick up, and stop.**

## Never

- Edit, commit or fix anything. A wrong face is fixed by picking the pog up.
- Write the stack to a file, or keep a second list anywhere.
- Answer from the session's memory of an earlier printout.
- Start work from the stack line.
- Reword a Waiting or Next line.
