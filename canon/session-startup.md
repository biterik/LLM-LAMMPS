# LLM-LMPS - Session-startup ritual

Every Cowork session, on first interaction with Erik, runs through the
following ritual before doing any project or framework work. Step 0 is
the environment gate; steps 1-6 are the original ritual. The
canonical concurrency rules and rationale are in ARCHITECTURE.md §17.

## This framework is the entry point for "simulations"

When Erik refers to **"simulations"**, **"LAMMPS"**, **"runs"**, or
asks about which simulation projects are open/active, this framework is
the entry point — engage it and run the ritual below. Do **not** browse
the `DEVEL/` tree (or any other tree) to guess at simulation state.
`DEVEL/` holds the *framework code* (this repo) and assorted tooling;
it is **not** where the simulations live. The simulation projects and
their data live in a separate **simulation folder**, whose location is
**asked at startup, every session** (step 2 below) — never assumed.

## When to run the ritual

- **First substantive turn of a new conversation** — always.
- **Mid-session, if Erik asks to switch scope** to a project/thread
  outside the current pilot scope (or to add a framework-design phase
  that would promote the session to designer+pilot). Re-run steps 3-5
  to reconfirm mode + scope + ownership.

## The steps

### 0. Environment + local-config check (added 2026-07-28)

Before anything else, establish that this session can actually do the
work. Three checks, in order — all cheap, and each one has cost a session
before.

**(a) Which machine?** Read `canon/local/.this-machine` — a one-line file
naming the machine (`M5`, `M2`, `M1`, `mini`), then look that key up in
`canon/local/local.yaml`'s `machines:` map.

Do **not** try to identify the machine by running `hostname`. A Cowork
session's shell runs in an isolated Linux VM, not on macOS — `hostname`
there returns the VM's name and tells you nothing about which Mac you are
attached to. `.this-machine` is written once per machine by hand and is
excluded from the private-overlay sync for exactly that reason. If it is
missing, ASK which machine this is rather than guessing; `M1` (travel
laptop) and `mini` hold no simulation data, and `has_simulations: false`
machines must never be proposed as a mirror target.

**(b) Does `canon/local/` exist?** If not, the repo is a fresh clone and
every cluster user, host and scratch path is still a `<PLACEHOLDER>`.
Stop and tell Erik:

```
git clone <PRIVATE_OVERLAY_REPO> <REPO_ROOT>/canon/local
# or, first machine only:
cp -R <REPO_ROOT>/canon/local.example <REPO_ROOT>/canon/local
```

Framework (designer) work can proceed without it; **cluster or pilot work
cannot**.

**(c) Can this session see the working set?** Try to list the simulation
root and the cluster mount from `local.yaml`'s `roots:`. Both must be
readable before any pilot work.

If either is missing, the usual cause is simply that the folder is **not
connected to this session** — ask Erik to click **"Add folder"** in the
desktop app and add it. Folders CAN be added at any point, including
mid-session; they become visible immediately. This applies whether the
session runs in the cloud or on his computer.

If the cluster mount is connected but reads as *empty*, the sshfs mount
itself is down — ask Erik to remount (`command_example` in
`canon/clusters.yaml`) rather than concluding the data is gone. A flaky
mount has twice been misread as data loss (2026-06-01); an unreliable
source is not evidence of absence.

Either way: say plainly what is missing and **do not improvise a
substitute** or narrow the scope to whatever happens to be visible. That
is how a session writes a project file into the wrong tree.

### 1. Read SESSIONS.md

Open the framework repo's `SESSIONS.md` (at `<REPO_ROOT>/SESSIONS.md`).
**Resolve it relative to the repo you are reading this file from** — never
from a remembered absolute path. The tree has moved before and will move
again; a hardcoded `~/Desktop/...` is always a bug. Parse the `active`
section. Note:

- Which sessions are currently active and in what mode
- Who holds the designer lock (if any)
- What scopes are owned (pilot scopes), so we can detect collisions
- What's in flight (cluster jobs, ongoing operations) per session

### 2. Ask Erik mode + scope

Use `AskUserQuestion` (preferred — clean multiple-choice UX) or plain
chat to ask:

- **Simulation folder**: where do the simulation projects/data live
  this session? **Always ask** — never assume `DEVEL` or any fixed
  path. `DEVEL/` is the framework code, not the simulations. Record the
  confirmed folder for the session (e.g. in the SESSIONS.md entry's
  `notes`) and treat it as the working root for all pilot file ops.
- **Mode**: `pilot` | `designer` | `designer+pilot`
- **Scope** (depends on mode):
  - pilot: which project? which thread (if a specific one)?
  - designer: which area of the framework (lessons.md, preferences,
    ARCHITECTURE.md section, etc.)? — or "open" if
    Erik wants to merge inbox proposals or do broad framework work
  - designer+pilot: both a project/thread (pilot scope) and a
    framework area (designer scope), if known up front

### 3. Cross-check against SESSIONS.md

Apply the invariants from ARCHITECTURE.md §17.2:

- **If the requested pilot scope is already owned** by another active
  session, surface this to Erik with options:
  - take over (close the other session)
  - work on a different thread of the same project (sibling-disjoint)
  - work on a different project entirely
  - abort and pick something else
- **If the requested mode is designer or designer+pilot and the
  designer lock is held** by another active session, refuse to
  proceed until that session closes or demotes. Tell Erik who holds
  the lock, what their scope is, what their `last_active` time is.
  Offer to wait, or ask Erik to close the other session.

### 4. Self-register in SESSIONS.md

Automatic — do not ask Erik to confirm. Append (or update if
re-running mid-session) an entry under `## active`:

```yaml
### session_id: <YYYY-MM-DD-HHMM-short-scope-id>
- mode: <pilot|designer|designer+pilot>
- scope: <one or two lines>
- started: <ISO 8601 UTC>
- last_active: <ISO 8601 UTC>
- owns_writes_to:
  - <path 1>
  - <path 2>
  - ...
- in_flight: <empty initially; updated as work progresses>
- notes: <empty initially>
```

Also update `last_index_updated` at the top of SESSIONS.md.

### 5. Brief Erik

Send a startup-summary message that confirms the registration and
makes ownership visible. Format:

```
**[<Mode>]** Session registered as <session_id>.

Scope: <one-line summary>
Other active sessions:
- <session_id_other_1>: <mode_1>, <scope_1>, last active <when>
- <session_id_other_2>: ...

This session OWNS writes to:
- <path 1>
- <path 2>

This session WILL NOT touch:
- <framework canon paths, if mode = pilot>
- <other projects' files>
- <other threads of the same project, if pilot scope is one thread>

Reminders specific to this scope:
- <e.g., "no ssh wrap in phase A" for cluster-bound pilot scopes>
- <e.g., "use the proposals-inbox to surface new rules" for pilots>
- <whatever feedback memories or rules apply>
```

For **pilot or designer+pilot modes with cluster scope** (any project
that submits LAMMPS jobs), the briefing MUST additionally include:

- **Probe before production** for any edited or new LAMMPS .in
  (mandatory; see `canon/learnings.md` "Cluster discipline"). Submit
  the probe sbatch first, verify clean exit + "ALL PHASES COMPLETE"
  marker + all `Performance:` lines present, THEN propose the
  production sbatch.
- The probe doubles as the **walltime-calibration source** per L26 —
  scale production `--time` from the probe's measured timesteps/sec.

### 6. Proceed

Now operate within scope. Tag every response with exactly one visible
hat — `**[Pilot]**` or `**[Designer]**` (never the combined
`[Designer+Pilot]`, which is a SESSIONS.md capability only, not a <!-- lint-ok:role-tag -->
response tag). The hat follows the work and switches are announced on
their own line; see ARCHITECTURE.md §17.5 for the full rule.
Update `last_active` on SESSIONS.md every major action (run a job,
write a substantive file, complete a phase). Append to the
`in_flight` field when starting cluster jobs or long ops; clear when
they finish.

## Mid-session scope-switch trigger

If Erik says something like "now let's work on X" or "switch to
project Y" or "I want to also touch the framework" where X/Y is
outside the current registered scope, re-run steps 3-5 (cross-check,
update SESSIONS.md, re-brief). If the new scope requires the
designer lock and it's held, refuse and offer alternatives.

## Mode transitions during a session

Mid-session mode transitions follow ARCHITECTURE.md §17.3:

- **pilot -> designer+pilot** (promotion): re-run step 3 (check
  designer lock); if free, update mode in SESSIONS.md.
- **designer+pilot -> pilot** (demotion): always allowed; update
  SESSIONS.md and (optionally) prune `owns_writes_to` to the pilot
  subset. Releases the designer lock.

## Wrap-up procedure

When Erik signals end of session ("wrap up", "close session", "we're
done for today") or the conversation is clearly complete:

1. Move this session's entry from `## active` to `## recently_closed`
   in SESSIONS.md.
2. Add a `closed: <ISO 8601>` timestamp.
3. Add a `summary:` line (one or two lines on what was accomplished
   or where it stopped).
4. If there's a natural successor session that should pick this work
   up, add `handoff_to: <successor session_id>` (or `null`).
5. Update relevant `thread.md` or `project.md` so future sessions
   resume cleanly.
6. Update `last_index_updated`.

## Notes

- The startup ritual is mandatory for new conversations going forward.
  Sessions that started before the concurrency model existed (the
  first 2026-05-31 session, for example) are grandfathered in via
  their existing SESSIONS.md entry; no retroactive ritual.
- If SESSIONS.md doesn't exist yet (very fresh install), create it
  with the schema and proceed.
- The packaged skill (`skills/llm-lammps/SKILL.md`) is what makes this
  ritual self-triggering: it fires on "simulations", "LAMMPS", "runs",
  or a project/thread mention and points the session here. If the skill
  is not installed, the ritual depends on Erik naming the framework.
