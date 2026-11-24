![POGflow: a gloved hand has just thrown a slammer onto a stack of pogs, which scatter](docs/banner.png)

# POGflow

**POG Over Guesswork.** A small convention (shoehorned into 90s nostalgia) for keeping track of open work across coding-agent sessions, one after another or several at once.

A session is one conversation with a coding agent, such as one Claude Code window. A new session does not know what the last one did, or what it was waiting on you for. Keeping one shared status file does not fix that: several sessions at once get in each other's way, and the file goes out of date.

POGflow gives each piece of work its own file, a pog, that says where the work stands. You ask a session to make a pog or pick one up. From then on it keeps the pog saved and pushed on its own, so the next session, on any computer, carries on from there. It comes as three skills for Claude Code.

It was designed for a meta repository: one repo you use to coordinate work across several others, holding the plans and the pogs for all of them. It works the same way inside a single repo. Either way it needs a branch that every session can push to directly, such as `main`: it does not work with a branch per session, or a protected one.

**Status:** prototype

> Where a piece of work stands is written in its pog and nowhere else. Lists of open work are printed from the pogs, not kept by hand. A session makes or picks up a pog only when you ask, and the only files it commits on its own are pogs.

That is all it does. It does not plan the work or make any decision for you, and it is not an issue tracker. Next to tools you may already use:

- **[Plans](#pogs-and-plans)** say what to build. A pog says where that work stands.
- **CLAUDE.md and memory** tell a session how to work in your repo. A pog tells it how far one piece of work has got.
- **A handoff note or a shared status file:** a pog replaces both.

## A pog

A pog is one file under `pogs/`, a flat folder at the top of your repo. The file is named for the work. This one is `pogs/sorter-tags.md`, for tagging units in a spike sorter (a GUI that sorts recorded spikes into units, one per neuron):

```markdown
# Unit tags in the spike sorter

**State:** open
**Tags:** gui
**Waiting on Stephen:** pick the tag rule, one tag per unit or several (2026-11-09)
**Next:** the summary view, once the tag rule is set

## Log
- 2026-11-09 desktop 3f9c21ab: tag buttons and keys work; stopped for the tag rule
- 2026-11-09 desktop 3f9c21ab: made; first the tag buttons

## Notes
Plan: `plans/sorter-tags.md`

**Q (2026-11-09):** can a unit take one tag, or several? **A:**
```

The four lines under the title are the **face**: the State (`open`, `parked` or `done`), Tags for topics, what waits on you, and the next step. The Waiting label carries your name. The **log** gets a dated line whenever something happened, newest first, with the computer and the session that wrote it. **Notes** hold the rest: the plan link, and each question with its answer.

## Make, stack, pick up, slam, flip

- **make:** you ask for a pog when new work starts, and the session writes the file. A session may suggest one. A one-off, such as a typo fix, needs none.
- **stack:** a list of every open pog, those waiting on you first. Ask for it when you start with nothing in mind.
- **pick up:** the session reads the pog you name and carries on from its next step.
- **slam:** the session saves the pog. It pulls, updates the face and log, then commits and pushes the pog file. It does this on its own each time it hands back to you, with a question or with its work done, and again when you answer one of its questions, until you tell it not to. If it cannot push, it tells you.
- **flip:** you say a pog is finished or dropped. The session asks once whether any of its notes are worth keeping. If so, it copies them into the repo's docs or the plan first, and the pog stays open until that is done. Then it sets State to `done`. The file stays as the record.
- **park, reopen:** set a pog aside, or bring it back.

## An example

Stephen starts new work, unit tags for a spike-sorting GUI:

> **Stephen:** start on the unit tags in `plans/sorter-tags.md`, and make a pog for it.
>
> *(The session makes `pogs/sorter-tags.md`, links it from the plan, and adds the tag buttons. Stephen tries them in the GUI and says what to change.)*

Then the session needs a decision. It writes the question in the pog, slams, and asks:

> **Session:** can a unit take one tag, or several?

The pog now reads as in [A pog](#a-pog) above. The next day, in a new session on the laptop:

> **Stephen:** what's on the stack?
>
> **Session:** One thing waits on you. sorter-tags: pick the tag rule, one tag per unit or several (2026-11-09).
>
> **Stephen:** pick up sorter-tags. One tag per unit.
>
> *(The session reads the pog, writes the answer in it and slams. It builds the summary view, and Stephen tries it.)*
>
> **Stephen:** looks right. Flip it.

The session asks what in the notes to keep. Stephen says nothing needs keeping, and the session flips. sorter-tags is off the stack.

## Pogs and plans

Plans are Markdown files under `plans/`, like the ones Claude Code writes in plan mode but kept in the repo. A pog does not need a plan, and it can be work of any size.

- **Status goes in the pog, not the plan.** When a plan's work gets a pog, the session adds a link to the pog in the plan.
- **A pog links to its plan,** if there is one. Several pogs can link to one plan. What was built is in their logs. When a pog's notes grow long, the session moves them to a plan.

## Pogs and sessions

- **One session per pog.** The session that makes a pog or picks it up holds it, until that session is closed or you have another one pick the pog up. A session can hold several pogs.
- **You can answer a pog's question in any session.** That session saves your answer in the pog, even if a different session holds that pog.
- **A session asks before picking up a pog that may be in use.** If a different session wrote in the pog's log today, the new session asks you whether the other one is closed. If the other is still open, the new session leaves the pog alone unless you say otherwise.
- **A pog can be split** when part of its work should go on separately, for example in a second session. Ask the session that holds the pog to split it. You and that session decide what moves to the new pog.

## Install

The skills are for Claude Code.

1. On each computer, clone this repo into your skills folder:

   ```sh
   git clone https://github.com/stephenholtz/POGflow ~/.claude/skills/pogflow
   ```

2. Make a `pogs/` folder in the repo that will hold the pogs.
3. Give sessions permission to run `git commit` and `git push` there. Slam needs both.

New sessions then have `/stack`, `/pick-up` and `/slam`. If another command already uses a short name, the full ones are `/pogflow:stack` and so on. To update, run `git pull` in `~/.claude/skills/pogflow`.

- **Ask in plain words or type a command.** Making a pog, flip, park, reopen and split have no command of their own: say "make a pog for this", "flip it", "park it", "split that off".
- **POGflow commits only pog files,** plus a plan when it adds a new pog's link there. Your other work is committed as before, when you say. The push also sends any commits already on that branch.
- **A session started in another repo** finds the meta repository's `pogs/` when that repo's own CLAUDE.md says where it is, for example "Open work is in `~/repos/meta/pogs/`".
- **To have every new session print the stack,** say so in your CLAUDE.md.

## Licence

Written by Stephen Holtz. MIT ([LICENSE](LICENSE)).
