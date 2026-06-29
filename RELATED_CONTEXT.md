# Related project: Email + Todo Helper

> Drop this file at the root of the new project's folder. The first message
> of a new Cowork session can be as light as:
> "Read RELATED_CONTEXT.md to understand the project and its relationship
>  to the email helper, then ask me what to work on first."

## What the related project is

A privacy-respecting helper for professional email + todos, running locally
on Erik's Mac. Mail content goes only through GWDG-hosted models (SAIA);
databases stay local. The "north-star" feature is a single honest overview
of projects/tasks/todos sliced by axes (urgent, important, fun, easy,
complicated, annoying) that points to relevant resources.

It tracks **projects** on disk — annotating folders the user already has,
across three roots (`~/Desktop/PROJECTS`, `~/Desktop/SIMULATIONS`,
`~/Desktop/DEVEL`) — with a `project.yaml` manifest carrying a canonical
`id`, a `parent` slug for nesting (arbitrary depth, e.g. SFB1394 → A02 →
threads), and a `locations:` map mapping Mac PROJECTS ↔ Mac SIMULATIONS ↔
Mac DEVEL ↔ cluster path. Identity rides on the `id`, not folder paths, so
rename/move within tracked roots is self-healing.

Status as of 2026-05-22: v0.0 complete (per-email LLM extraction working
end-to-end with action_required, deadline, summary, suggested next steps,
proposed task and project titles). v0.1+ (real Apple Mail reading, sqlite,
web overview, embeddings, project workspaces) continues in Claude Code.

**Full design doc, including all decisions and the v0 roadmap:**
`/Users/<MAC_USER>/Codes/AI-Assistant-for-general-projects/email-helper-design.md`

## How this new project relates to it (architecturally)

The two systems are complementary halves of one wider workflow:

- **Email helper → PROJECTS layer.** Communications, admin, ratings, the
  canonical `id` per project, "what's pending and why."
- **This new project → SIMULATIONS layer.** Where the actual compute,
  data, analysis, and documentation live, mapped to HPC clusters.

They share the canonical `id` per project. A SIMULATIONS folder managed by
this new tool can carry the lightweight `id`-marker file the email helper
already understands, so the two systems stay loosely coupled but mutually
aware. Either can be used without the other; together, a project's comms
side and compute side are linked by one stable identifier.

## What the new project is — Erik's framing (verbatim, 2026-05-22)

> The new project will be how to structure the SIMULATIONS folder and
> Subfolders and link them to the actual simulation submission and results
> directories on the various HPC Clusters. The idea is to in the future do
> all (most of the) work (= simulation scripts, input files, data analysis
> scripts, but also the documentation, including the why we do something,
> how to do it, defining and using standard vocabulary / ontologies ) done
> via Claude Cowork (or any other LLM, keeping it open!). The idea is then
> to have a dashboard which shows the status of the simulation projects
> and the easy ability to structure/shape the data for publication / upload
> to openbis,.... and reusable archiving.

## Themes to survey in the new session

- **Folder structure + cluster mapping.** SIMULATIONS layout on the Mac
  mirrored to cluster paths; canonical `id` as the anchor for both.
- **LLM-assisted research data management.** Simulation scripts, input
  files, analysis scripts and the *documentation alongside them* —
  including the "why", not only the "how" — generated/refined with an LLM.
- **Standard vocabulary / ontologies.** EMMO for materials modelling;
  NFDI-MatWerk vocabularies; PROV-O for provenance. Pick a working subset.
- **Status dashboard.** Per-project state of compute, queues, results,
  open questions.
- **Publication + FAIR archiving.** Push finished outputs to openbis;
  reusable archiving with stable identifiers and metadata.
- **Prior art worth surveying before designing from scratch.** Workflow
  managers: AiiDA, Snakemake, Nextflow. ELNs: openbis, eLabFTW. Existing
  RDM stacks in NFDI-MatWerk.

## Top-level principles to preserve

- **LLM-agnostic.** Tooling and formats must not lock into one provider.
  (This is analogous to the email helper's GWDG-only privacy constraint —
  same kind of architectural commitment, different reason.)
- **Inspectable formats.** Plain files (YAML, markdown, JSON, parquet)
  over opaque stores, so data and documentation age well.
- **Mac ↔ cluster identity preserved.** Same canonical `id` on both sides;
  no silent renaming.
- **Documentation as a first-class artifact**, not an afterthought.
- **Loose coupling with the email helper.** Either can exist alone; the
  `id`-marker file is the only contract between them.

## Suggested first move in the new session

Don't design the whole stack at once. Pick one concrete pain point Erik
has *today* (e.g. "I want to start a new simulation project for X and have
the folder + cluster dir + docs scaffold set up with one command") and
work backwards from that. Mirror the email helper's "v0.0 = one command
exercises every layer" approach.
