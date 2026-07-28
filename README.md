# LLM-LAMMPS

A plain-file, LLM-agnostic framework for running atomistic simulation projects
(LAMMPS on HPC clusters) **end-to-end through an LLM assistant** — input decks,
analysis, and the *documentation and reasoning* alongside them — while keeping a
human firmly in the loop for anything that touches a cluster.

> **Status: research preview / work in progress.** This is an evolving design,
> not a finished tool. The canon and architecture docs are the real artifact;
> expect them to change.

## What it is

The framework is a set of **plain text/markdown/YAML files** that an LLM reads at
the start of a session and treats as ground truth. There is deliberately no
bespoke software layer: project state lives in files the LLM re-reads, so the
system stays inspectable, version-controllable, and portable across LLM
providers.

Core ideas:

- **Identity rides on a canonical `id`, not folder paths.** Mac and cluster
  paths are *observations* of one project; folders may diverge, the `id` is the
  anchor. Rename/move within tracked roots is self-healing.
- **The laptop is the safe record.** Cluster scratch is fragile and unbacked;
  the Mac is backed up. Expensive-to-regenerate artifacts are pulled
  (compressed) to the backed-up side on closure, so a project is *survivable
  from the laptop alone*.
- **Propose → human runs, for the cluster.** The LLM never reaches HPC directly
  or around the user's auth. It drafts commands; the user executes them in their
  own SSH session. Probe before any production submission.
- **Concurrency model.** Sessions self-register in `SESSIONS.md` and take a
  `pilot` (move a project forward) and/or `designer` (change the framework
  itself) role, with a single designer write-lock.
- **Tools as cards, not code.** Canon holds only *tool cards* (mechanical
  contracts); tool implementations live in their own repos and binaries.

## Repository layout

```
ARCHITECTURE.md          The framework spec (start here). §17 = concurrency model.
brainstorm-notes.md      Design rationale and the "why" behind the decisions.
SESSIONS.md              The active-sessions dashboard (example of it in use).
RELATED_CONTEXT.md       Lineage: relationship to a sibling email/todo helper.
.retired-CHECKPOINT.md   Archived earlier state-tracking doc.
canon/                   The runtime substrate the LLM reads as ground truth:
  clusters.yaml            Cluster definitions (SSH, mounts, modules, queues).
  learnings.md             Durable rules learned over time.
  lessons.md               Numbered, specific gotchas (Lxx).
  preferences.md           Output/format/style preferences.
  session-startup.md       The startup ritual each session runs.
  proposals-inbox.md       Framework-change proposals awaiting merge.
  style/                   LAMMPS-input and shell style guides.
  templates/               Lint scripts, skeletons, the curated-mirror script.
  tools/                   Tool catalog + tool cards (lego, dcreator, afc, …).
  local.example/           Template for the gitignored identity overlay.
  local/                   YOUR real cluster identity. Gitignored, never committed.
skills/llm-lammps/       Packaged entry point so the ritual self-triggers.
examples/                Illustrative material.
```

## Setup

### 1. Install the skill

The framework only works if a session actually *engages* it. `skills/llm-lammps/SKILL.md`
is the trigger: it fires on "simulations", "LAMMPS", "runs", a project or thread,
a cluster name, a potential, an `sbatch` — and routes the session into
`canon/session-startup.md`.

**Claude Cowork (desktop app).** Two ways:

- *Packaged.* Zip the skill folder's contents so that `SKILL.md` sits at the root
  of the archive, and name it `llm-lammps.skill`:

  ```
  cd <REPO_ROOT>/skills/llm-lammps && zip -X ~/Desktop/llm-lammps.skill SKILL.md
  ```

  Send that file into a Cowork conversation; you will be offered the option to
  save it to your account. Once saved it triggers in **every** session, on any
  machine, whether or not this repo folder happens to be connected. Your
  organization's settings decide whether saving is permitted.

- *In-repo.* Connect this repo as a folder in the session. `skills/llm-lammps/SKILL.md`
  is then picked up while that folder is connected. Version-controlled, but
  scoped to sessions that have the folder.

Doing both is reasonable: the repo copy is the source of truth you edit and
commit, the packaged copy is what actually triggers everywhere.

**Other LLM hosts.** The skill file is plain markdown with YAML frontmatter.
Paste its body into whatever the host calls a system/project instruction. Nothing
in the framework depends on the skill mechanism — it is a doorbell, not a
dependency.

### 2. Create your local identity overlay

This repo is **scrubbed**. Real cluster usernames, hostnames, notification
addresses and Mac home paths appear only as placeholders:

| Placeholder | Meaning |
|---|---|
| `<CLUSTER_USER>` | Your username on the HPC cluster |
| `<CLUSTER_HOST>` | The cluster login host (e.g. `login.mycluster.edu`) |
| `<NOTIFY_EMAIL>` | Address for Slurm job mail (`--mail-user`) |
| `<MAC_USER>` | Local macOS username, where it appears in example paths |
| `<DEVEL_ROOT>` | Your dev tree (where the tool repos live) |
| `<REPO_ROOT>` | The root of *this* repo, resolved from wherever it is checked out |

The real values go in `canon/local/`, which is gitignored:

```
cp -R <REPO_ROOT>/canon/local.example <REPO_ROOT>/canon/local
$EDITOR <REPO_ROOT>/canon/local/local.yaml            # machine map + local roots
$EDITOR <REPO_ROOT>/canon/local/clusters.local.yaml   # ssh user/host/scratch, notify email
```

`canon/clusters.yaml` keeps the *structure* (partitions, modules, sshfs options,
scratch policy) and stays public; `canon/local/` supplies only the substitutions.
The startup ritual checks for it at step 0 and refuses cluster work without it.
Full detail: `canon/local.example/README.md` and `ARCHITECTURE.md` §6.

### 3. Keep the overlay in step across machines

`canon/local/` is a few kB of text that must agree on every Mac you work from.
Preferred: a **private git repo** cloned into `canon/local/` — the path is
gitignored by this repo, so a nested checkout is invisible to it and nothing can
leak into public history.

```
# once, on the first machine
cd <REPO_ROOT>/canon/local && git init && git add -A
git commit -m "local identity overlay"
gh repo create __GH__/llm-lammps-local --private --source=. --push

# on every other machine
git clone git@github.com:__GH__/llm-lammps-local.git <REPO_ROOT>/canon/local
```

Alternative (no second repo): keep the folder in iCloud Drive and symlink it in.
Config-sized, so it does not violate the "research data never goes in iCloud"
rule — but no history, and iCloud can serve a stale copy right after an edit
elsewhere. Both are documented in `canon/local.example/README.md`.

### 4. Guard the scrub boundary

```
python3 <REPO_ROOT>/canon/templates/lint-no-identity.py
```

Fails if any *tracked* file contains a value from `canon/local/`, or matches a
generic identity pattern (email, `/Users/<name>`, `ssh user@host`, an MPCDF
hostname, a real scratch path). Run it before every push; wire it as a
`pre-push` hook if you want it enforced.

## Running a session

The framework needs to see three trees: this repo, the simulation folder, and
the cluster mount. In Claude Cowork, connect all three with **"Add folder"** —
this works at any point, including mid-session, and applies equally to sessions
running in the cloud and on your own machine. Mount the cluster over sshfs
*before* connecting it, or the folder connects but reads as empty.

Step 0 of the startup ritual verifies all three and says what is missing rather
than improvising a substitute.

The reference configuration targets an [MPCDF](https://www.mpcdf.mpg.de/)
Slurm cluster; adapt `canon/clusters.yaml` to yours.

## License

MIT — see [LICENSE](LICENSE).
