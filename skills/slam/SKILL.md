---
name: slam
description: Slams the pogs this session holds (POGflow). Makes each pog's face true on disk, adds one log line, commits only those pog files, pushes, and says whether the push landed. Also flips, parks, reopens or splits a pog, and makes the pog for new work. Use when the user types /slam or says slam, flip it, park it, reopen it or split that off. Use unasked at every stop while this session holds a pog - before sending a message that asks the user something, right after the user answers and before acting on the answer, and before the last message.
argument-hint: "[flip | park | reopen | split] [pog] [note]"
---

# Slam

The words (pog, face, log, stack, pick up, slam, flip, stop) are defined in the POGflow README. It is at `${CLAUDE_PLUGIN_ROOT}/README.md` (the top of the repo these skills are in), or at https://github.com/stephenholtz/POGflow. Read it first. Request: `$ARGUMENTS`

1. **Helper.** If another agent launched you (a swarm subagent, a workflow step), you are not the player. Write nothing. Put the face and log line you would have written in your report.
2. **Find the repo.** Look for a `pogs/` folder from the working directory upward, then in the repo the project's CLAUDE.md names. If there is none and the user asked for this, say POGflow is not set up here, ask where `pogs/` is, and stop. If nobody asked, carry on without POGflow and say nothing about it. From here use `git -C <repo>` and absolute paths.
3. **Update the clone.** `git fetch`, then `git merge --no-edit origin/main` (the branch's upstream, if it is not `main`). It fast-forwards when it can; a conflict inside a pog is handled as in step 10. Then re-read each pog from disk, not from memory.
4. **Name the pogs.** They are the pogs this session holds and has worked on since its last slam. Work that is more than a one-off and has no pog gets one now. If a name in `pogs/` is a near match, ask. Otherwise make the file in the shape of the README's example, with the user's name in the Waiting label, and link its plan. The first time a plan's work gets a pog, date the plan's old status line if it has no date, and add a link to the pog.
5. **Answers first.** Write the user's answer and the date beside its Q in Notes before acting on it. If it could fit two questions, quote the Q back and ask. A new question goes in Notes as a dated Q with an empty A before it is asked in chat.
6. **Face.** Rewrite the four lines so each is true now. A Waiting item keeps the date it was first asked. It leaves only with an A in Notes, or a log line saying it was dropped. Flip, park and reopen change State; flip only on the user's word, whether the work was finished or dropped, and the log line says which. Before a flip, ask once whether anything in Notes outlives the work, and name what you would keep. Nothing: flip. Now: move it to where the repo keeps such facts (its notes, or the plan), then flip. Later: State stays `open`, Next becomes "document: <what, where>, then flip", and the flip comes when that is done, without asking again. The moved text is ordinary work in the repo, not part of the slam's commit. A split is a new pog and a line in both logs.
7. **Log.** Add one line on top, as `- <date> <machine> <id> (<name>): <what happened>`: the date from `date +%F`, the machine (the name the session was given at its start, else `hostname -s`), the session's id (the first 8 characters of `$CLAUDE_CODE_HOST_SESSION_ID` after `local_`, or of `$CLAUDE_CODE_SESSION_ID` where that is not set; left out if the session has neither), the session's name as it is now, if it knows one (the id says which session it was; the name is only a label and may differ from line to line), what happened and why it stopped, any code commits by id with "pushed" or "not pushed" (this repo's go out with the slam's push), and any work left uncommitted. A pog with nothing to say since its last log line (picked up and no work done, no answer, no face line changed) gets no log line and is left out of this slam.
8. **Check.** `git diff -- pogs/<file>` shows face lines changed, one log line added, and no old Log or Notes text lost. A new pog shows no diff: read the file. `grep -A3 '^\*\*State:\*\*' pogs/<file>` prints State, Tags, Waiting, Next in that order.
9. **Commit and push.** `git add -- <the pog files>`, then `git commit -m "pogs: <names>: <what happened>" -- <the same files>`. Naming the files commits them alone, whatever else is staged. A slam commits files under `pogs/` only; the one exception is the plan dated in step 4, and only if that line is its only change. `git show --stat HEAD` must list nothing else. Then `git push`.
10. **Push refused.** `git fetch`, `git merge --no-edit origin/main`, re-read each pog, push again. If the merge made a face untrue, fix it (steps 6 to 9) before the push. A conflict inside a pog means two sessions wrote it. Keep both sides' log lines and Notes, write one face that is true now, finish the merge with `git add -- <the pog>` and `git commit --no-edit` (no path: git refuses one during a merge), and show the user both faces.
11. **Blocked.** If the merge is stopped by an uncommitted file of this session's own work, commit that file by path and merge again. If the file is another session's (a staged one blocks even when unrelated), stop. Leave that file alone. If step 3 was blocked, still write the pogs (steps 4 to 8) and do not commit. Say which file blocks, and whether the slam is written only, or committed and not pushed.
12. **Another session's pog.** An answer for a pog this session does not hold: write the A beside its Q, take that item out of Waiting, add one log line, and change nothing else in that pog. Commit that file by path as in step 9. If that file has uncommitted changes on this machine, do not touch it, and tell the user to give the answer in the session that holds it.
13. **Report.** Confirm with `git status -sb` that nothing is ahead. Then send the question or the last message, with one line: which pogs were slammed, and pushed or not.

## Never

- `git add -A`, `git add .`, `commit -a`, or a slam commit that carries a file the slam did not write.
- Stash, reset, checkout, rebase or force-push. Discard or tidy another session's uncommitted changes.
- Write a pog another session holds, beyond the one answer in step 12.
- Rewrite or delete old log lines, to bring a session's name up to date or for any other reason.
- Add a log line that says only that the pog was picked up or that nothing was done.
- Copy a plan's content into a pog, or update a board or a plan's status as well.
- Say "slammed" when the push did not land.
- Save slams up for the end, wait for a clean tree or a finished task, or ask leave for each slam.
- Create `pogs/` where there is none, or add locks, claim lines, extra face lines or frontmatter to a pog.
