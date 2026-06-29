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
examples/                Illustrative material.
```

## Configuration — placeholders to fill in

This repo has been **scrubbed of personal/cluster identity**. Before using it,
replace these placeholders (chiefly in `canon/clusters.yaml`) with your own:

| Placeholder | Meaning |
|---|---|
| `<CLUSTER_USER>` | Your username on the HPC cluster |
| `<CLUSTER_HOST>` | The cluster login host (e.g. `login.mycluster.edu`) |
| `<NOTIFY_EMAIL>` | Address for Slurm job mail (`--mail-user`) |
| `<MAC_USER>` | Local macOS username, where it appears in example paths |

The reference configuration targets an [MPCDF](https://www.mpcdf.mpg.de/)
Slurm cluster; adapt `clusters.yaml` to yours.

## License

MIT — see [LICENSE](LICENSE).
