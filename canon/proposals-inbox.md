# LLM-LMPS - proposal inbox

Pilot sessions append proposals here when they surface a new rule,
preference, lesson, or feedback memory during their work. The designer
session reviews this file in batches, merges into the canonical target
file (`canon/lessons.md`, `canon/preferences.md`, `canon/learnings.md`,
`canon/style/*.md`, or `ARCHITECTURE.md`), and marks the proposal
`status: merged`.

See ARCHITECTURE.md §17.7 (Proposal inbox) for the rationale and
ARCHITECTURE.md §17 (Concurrency model) for the broader context.

## Format

Each proposal is a section with a YAML-ish frontmatter and three
required body sections. Append-only; do not edit prior entries except
to change `status` and add `Designer review notes` at merge time.

```markdown
---
proposal_id: <YYYY-MM-DD-HHMM-short-slug>
session_id: <the proposing pilot session's id from SESSIONS.md>
proposed_at: <ISO 8601 UTC>
target_file: <e.g., canon/lessons.md, canon/preferences.md, canon/learnings.md, canon/style/*.md, ARCHITECTURE.md>
target_section: <e.g., "Process" in learnings.md, "new lesson L<N>" for numbered lessons, "Output precision" in preferences.md>
priority: routine                       # routine | urgent
status: pending                         # pending | merged | rejected
---

## Proposed rule

[1-2 sentence imperative statement of the rule.]

## Where it bit

[The incident or context that triggered the proposal. Cite job ids,
file paths, timestamps, prior lesson numbers as relevant.]

## Suggested wording

[Draft prose ready to drop into the target file. Use the local
section's conventions (numbered list, heading style, etc.).]

## Designer review notes

[Filled at merge time by the designer session. Records:
 - assigned L-number (if numbered lesson)
 - any prose edits between suggested wording and what landed
 - link to the resulting commit (or session_id that merged)
 - reason if rejected]
```

## Conventions

- **Routine** proposals can wait for the next designer session.
- **Urgent** proposals (priority: urgent) — rare; for cases like a
  pre-flight rule a pilot encountered but couldn't add without
  breaking work. Designer should handle these first.
- One proposal per logical rule. Don't bundle.
- Pilot must NOT pre-assign lesson numbers. Numbering happens at
  merge.
- Pilot SHOULD pre-write the prose in the target file's style so
  merging is mostly a copy-paste + section-renumber.

---

## entries

---
proposal_id: 2026-05-31-2100-probe-before-production-hard-rule
session_id: 2026-05-31-1403-Ni-thread03
proposed_at: 2026-05-31T21:00Z
target_file: .lmps/learnings.md
target_section: Workflow rules (or a new "Cluster discipline" subsection)
priority: urgent
status: merged
---

## Proposed rule

Before any production sbatch where the `.in` file has been edited (or is
new in this project), the pilot MUST submit a probe job first. Probe =
clone of the production .in with: same fix stack, same potential, tiny
step counts (10 per phase), tiny BLOCK_AVG cadence (Nevery=2 Nrepeat=5
Nfreq=10), 2-MPI-task srun, 5-min walltime. Probe must pass cleanly
(all "PROBE marker" lines reach the log, all `Performance:` lines
present, `.err` empty) before the production sbatch is proposed to
Erik. Probe doubles as L26 walltime-calibration source.

## Where it bit

Surfaced 2026-05-31 Thread 03 of ni-a0-cij-eam-meam. Twice in the same
day, the pilot shipped LAMMPS-syntax assertion bugs that were ONLY
caught at production submission time:
- attempt 2 (job 20285992-94): `fix_modify BLOCK_AVG format ...`
  unsupported on `fix ave/time`; all 3 jobs aborted at parse time.
- attempt 3 probe (job 20286003-05): `print "...$(step:%d)"` syntax
  unsupported by the standalone `print` command (only `fix print`
  supports `$(quantity:format)`); all 3 probes aborted at parse time.

In both cases the actual cluster cost was tiny (parse-time errors,
seconds of walltime). What was NOT tiny was the elapsed wall-clock
between submission and "I learned this is broken" — minutes each
time, with Erik watching the queue. The probe-before-production
pattern compresses that diagnostic latency to a 5-min budget
guaranteed regardless of queue depth, AND catches semantic bugs
that the existing `lint-lammps-input.sh` cannot detect (because
they're about whether an option exists on a specific fix, not about
syntactic patterns in the file).

## Suggested wording

Add to `.lmps/learnings.md` under a new sub-section "Cluster
discipline" (or extend "Submit-script discipline"):

> **Probe before production for any edited or new .in.** Before any
> production sbatch where the LAMMPS input has been edited since its
> last successful run on this cluster, the pilot submits a probe job
> first. The probe is a clone of the production .in with all step
> counts reduced to 10 and the fix ave/time cadence reduced
> (Nevery=2 Nrepeat=5 Nfreq=10) so all output paths fire within the
> probe length. 2 MPI tasks (catches parallel-decomp bugs), 5-min
> walltime, same partition as production. The probe must:
> - exit cleanly (`.err` empty, `.out` ends with "PROBE DONE")
> - reach the "ALL PHASES COMPLETE" marker in the log
> - emit a `Performance:` line for each `run` block (used to scale
>   the production walltime per L26)
>
> Only after the probe returns clean does the pilot propose the
> production sbatch. Probe submit scripts live alongside their
> production counterparts as `<input>.probe.in` +
> `submit-<run>.probe.slurm`. Pattern surfaced 2026-05-31 after
> two consecutive sessions of guess-and-ship LAMMPS-syntax bugs.

A template `probe-input.in.skel` should land in `.lmps/templates/`
showing the structure (header, tiny variables, fix STEP_LOG,
fix BLOCK_AVG with tiny Nfreq, per-phase static `print` markers,
unfix cleanup) so future pilots clone rather than recreate.

## Designer review notes

Merged 2026-05-31T21:35Z by session 2026-05-31-1403-Ni-thread03 (briefly re-promoted to designer+pilot for the merge).
Merged into .lmps/learnings.md as new "Cluster discipline" subsection. TODO: create .lmps/templates/probe-input.in.skel from Thread-03 probes as canonical template (deferred to next designer session).


---
proposal_id: 2026-05-31-2105-L28-fix_modify-format-not-on-ave_time
session_id: 2026-05-31-1403-Ni-thread03
proposed_at: 2026-05-31T21:05Z
target_file: .lmps/lessons.md
target_section: new lesson L<next>
priority: routine
status: merged
---

## Proposed rule

`fix_modify FIX_ID format ...` is NOT supported on `fix ave/time` in
LAMMPS 250722 (and likely earlier versions). It IS supported on
`fix ave/atom`, `fix ave/chunk`, `fix ave/grid`, `fix ave/histo`,
`fix ave/correlate`. For `fix ave/time` output, accept the LAMMPS
default `%g` format (~6 sig figs); per-column precision overrides
are not available at the fix level.

## Where it bit

Thread 03 of ni-a0-cij-eam-meam, job 20285992-94 (attempt 2):
```
ERROR: Fix BLOCK_AVG ave/time does not support fix_modify format command
(src/fix.cpp:182)
```
All three production jobs aborted at parse time. The bug came from
asserting a fix_modify pattern based on prior LAMMPS knowledge
without verifying it against the docs for `fix ave/time`
specifically.

## Suggested wording

```
## L28 — `fix_modify ... format` is unsupported on `fix ave/time`

**Rule:** The `format` keyword of `fix_modify` works on the
following fixes only:
- `fix ave/atom`
- `fix ave/chunk`
- `fix ave/grid`
- `fix ave/histo`
- `fix ave/correlate`

It does NOT work on `fix ave/time`. Attempting it produces:
`ERROR: Fix <ID> ave/time does not support fix_modify format command`
and aborts at parse time.

For `fix ave/time` output precision, accept the LAMMPS default `%g`
format. If finer control is needed, post-process the output file in
Python/awk after the run.

**Where it bit:** Thread 03 of ni-a0-cij-eam-meam, jobs
20285992/20285993/20285994 (2026-05-31). The pilot had asserted
`fix_modify BLOCK_AVG format 2 "%.6f" format 3 "%.6f" ...` based on
the general fix_modify pattern; aborted three production jobs at
parse time.

**Target:** mention in `style/lammps.md` precision-control section
when it's written; refer here.
```

## Designer review notes

Merged 2026-05-31T21:35Z by session 2026-05-31-1403-Ni-thread03 (briefly re-promoted to designer+pilot for the merge).
Merged as L28 in .lmps/lessons.md (prose adopted near-verbatim). Also added preferences.md erratum correcting earlier wrong assertion.


---
proposal_id: 2026-05-31-2110-L29-title1-title2-no-substitution
session_id: 2026-05-31-1403-Ni-thread03
proposed_at: 2026-05-31T21:10Z
target_file: .lmps/lessons.md
target_section: new lesson L<next>
priority: routine
status: merged
---

## Proposed rule

The `title1` and `title2` keywords of `fix ave/time` (and likely
other averaging fixes that support them) receive their arguments as
LITERAL strings. No `${var}` or `$(...)` substitution happens on
these. To include dynamic context in the title, embed it in the
file argument instead (via a `variable ... string` that itself
contains substituted text), since the `file` keyword's argument IS
expanded.

## Where it bit

Thread 03 of ni-a0-cij-eam-meam, attempt-2 EAM-scan output
(2026-05-31). The pilot wrote:
```
fix BLOCK_AVG all ave/time ... title1 "# ... for ${POTENTIAL_LABEL} ..."
```
expecting `${POTENTIAL_LABEL}` to expand. It did not — the literal
`${POTENTIAL_LABEL}` appeared in the .dat header. From LAMMPS docs:
"These strings are written as-is to the file with no substitution."

## Suggested wording

```
## L29 — `fix ave/time` title1/title2 are literal strings

**Rule:** The `title1` and `title2` keyword arguments of
`fix ave/time` are written as-is to the output file with no
variable substitution. `${var}` and `$(...)` will appear literally
in the .dat header.

To include dynamic context in the header, EITHER:
- hardcode the values in the title string (one .in file per
  potential, so the label is known at write time), OR
- include the context in the output FILE NAME (via a
  `variable BLOCK_AVG_FILE string ...` that itself contains
  `${VAR}` text, since file-argument substitution DOES happen).

**Where it bit:** Thread 03 of ni-a0-cij-eam-meam, attempt-2
EAM-scan output (2026-05-31). The pilot's title1 had
`${POTENTIAL_LABEL}` and `${EPS_SCAN}` which appeared literally in
the .dat header instead of substituting.
```

## Designer review notes

Merged 2026-05-31T21:35Z by session 2026-05-31-1403-Ni-thread03 (briefly re-promoted to designer+pilot for the merge).
Merged as L29 in .lmps/lessons.md.


---
proposal_id: 2026-05-31-2115-L30-print-no-format-spec
session_id: 2026-05-31-1403-Ni-thread03
proposed_at: 2026-05-31T21:15Z
target_file: .lmps/lessons.md
target_section: new lesson L<next>
priority: routine
status: merged
---

## Proposed rule

The standalone `print` command does NOT support the
`$(quantity:format)` syntax — only `fix print` does. Using
`$(step:%d)` or `$(temp:%.3f)` inside a `print` command produces:
`ERROR: Incorrect conversion in format string (src/input.cpp:649)`

For runtime values in `print`, use the bare `$(quantity)` form (no
format spec); LAMMPS will format with its internal default. Or use
a static string if the value is not essential.

## Where it bit

Thread 03 of ni-a0-cij-eam-meam, attempt-3 probe (2026-05-31, jobs
20286003-05). Phase-boundary marker prints in the probe .in used
`print "PROBE marker: end of phase 1 (NVE) at step $(step:%d)"`
and all three probes aborted at the first such line. The probe
pattern caught the bug at ~30 sec cluster cost — exactly its
purpose — but the bug itself was avoidable.

## Suggested wording

```
## L30 — standalone `print` does not support `$(quantity:format)`

**Rule:** The `$(quantity:format)` runtime-evaluation-with-format
syntax is supported by `fix print` but NOT by the standalone
`print` command. Using it produces:
`ERROR: Incorrect conversion in format string (src/input.cpp:649)`

In `print`, use either:
- `$(quantity)` — bare, LAMMPS chooses default format, OR
- `${var}` — parse-time substitution for known-at-parse-time
  string/equal variables, OR
- a fully static string with no `$(...)` and no `%` characters.

The `%` character in a `print` argument is treated as the start of
a format conversion (printf-style), which is a separate failure
mode if the format isn't matched.

**Where it bit:** Thread 03 of ni-a0-cij-eam-meam, jobs 20286003-05
(2026-05-31). Phase-boundary marker prints in the PROBE .in used
`print "...$(step:%d)"` and aborted at parse time. Probe pattern
caught it cheaply, but the bug came from the same "felt natural,
wasn't verified" failure mode as L28.
```

## Designer review notes

Merged 2026-05-31T21:35Z by session 2026-05-31-1403-Ni-thread03 (briefly re-promoted to designer+pilot for the merge).
Merged as L30 in .lmps/lessons.md.


---
proposal_id: 2026-05-31-2120-startup-ritual-warns-about-probe
session_id: 2026-05-31-1403-Ni-thread03
proposed_at: 2026-05-31T21:20Z
target_file: .lmps/session-startup.md
target_section: step 5 (Brief Erik)
priority: routine
status: merged
---

## Proposed rule

When the session-startup ritual reaches step 5 (brief Erik on mode,
scope, ownership), and the mode is `pilot` or `designer+pilot` on a
project with cluster work, the briefing must include a reminder
about the probe-before-production rule (the new hard rule from
proposal 2026-05-31-2100).

## Where it bit

Future protection. The probe-before-production rule is the actual
mechanical defense against LAMMPS-syntax bugs; making sure every
new session knows about it at startup is what keeps it durable.

## Suggested wording

In `.lmps/session-startup.md`, step 5 "Brief Erik", under "Reminders
specific to this scope", add for pilot/designer+pilot modes with
cluster scope:

> - Probe before production for any edited or new LAMMPS .in
>   (mandatory; see .lmps/learnings.md Cluster discipline).
> - Calibration probe doubles as walltime estimate source (L26).

## Designer review notes

Merged 2026-05-31T21:35Z by session 2026-05-31-1403-Ni-thread03 (briefly re-promoted to designer+pilot for the merge).
Merged into .lmps/session-startup.md step 5 (Brief Erik).


---
proposal_id: 2026-07-28-1930-no-get-user-env-on-cmmg
session_id: 2026-07-28-1712-Ni-H-isotherms
proposed_at: 2026-07-28T19:30Z
target_file: canon/clusters.yaml
target_section: cmmg quirks (+ cross-ref from canon/style/shell.md submit-script discipline)
priority: routine
status: merged                          # was pending
---

## Proposed rule

Do not use `#SBATCH --get-user-env=L` (or any `--get-user-env` form) in
cmmg submit scripts: the cmmg Slurm build rejects the argument form with
`sbatch: option '--get-user-env' doesn't allow an argument`, and the flag
is redundant anyway -- `module purge` + explicit `module load` (L11) is
the sanctioned way to make the job environment deterministic.

## Where it bit

2026-07-28, project ni-h-phase-diagram-eam-meam, thread
01_STRUCTURE-AND-0K-ANCHORS. Both anchor submit scripts staged on
2026-06-30 (`submit-relax-Pezold-EAM.slurm`,
`submit-relax-KoShimLee-MEAM.slurm`) carried `#SBATCH --get-user-env=L`;
sbatch refused both at submit time (no queue slot lost -- rejected before
queueing). Class-fixed the same evening: line deleted from both scripts
in both trees (Mac + cluster); sweep over the whole project found no
other occurrence.

## Suggested wording

For clusters.yaml cmmg quirks list:

  # Established 2026-07-28. This Slurm build rejects the argument form
  # `--get-user-env=L` at sbatch time ("option '--get-user-env' doesn't
  # allow an argument"). Do not use --get-user-env at all: job-env
  # determinism comes from `module purge` + explicit `module load` (L11).
  - sbatch_rejects_get_user_env_argument_form

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/clusters.yaml cmmg quirks. Quirk `sbatch_rejects_get_user_env_argument_form` added as proposed. ALSO removed the stale `get-user-env: L` line still sitting in sbatch_defaults, which would have kept regenerating the bug.

---
proposal_id: 2026-07-28-2010-count-second-arg-is-region
session_id: 2026-07-28-1712-Ni-H-isotherms
proposed_at: 2026-07-28T20:10Z
target_file: canon/lessons.md
target_section: new lesson L<N>
priority: routine
status: merged                          # was pending
---

## Proposed rule

In LAMMPS equal-style variables, `count(group,ID)`'s second argument is
a REGION-ID, not an atom type. To count atoms of a type, define a static
group by type after the atoms exist (`group NI_ATOMS type 1`) and use
`count(NI_ATOMS)`. Runtime failure mode: "Region <ID> in variable
formula does not exist" at first evaluation -- parse passes, so only a
run/print catches it.

## Where it bit

2026-07-28, ni-h-phase-diagram-eam-meam thread 01 anchor jobs 21570524/
21570525 (staged 2026-06-30, first actual run). All four relax_*.in used
`variable n_Ni equal count(all,1)` / `count(all,2)`. Minimizations
completed; the results epilogue aborted at the first ${n_Ni} print.
Class-fixed in all four inputs, both trees, same evening.

## Suggested wording

(as in "Proposed rule"; incident text above supplies the "where it bit")

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/lessons.md. Assigned L32; prose adopted near-verbatim, pre-flight grep added to the Target line.

---
proposal_id: 2026-07-28-2012-variable-thermo-keyword-needs-thermo-style
session_id: 2026-07-28-1712-Ni-H-isotherms
proposed_at: 2026-07-28T20:12Z
target_file: canon/lessons.md
target_section: new lesson L<N>
priority: routine
status: merged                          # was pending
---

## Proposed rule

An equal-style variable referencing an energy thermo keyword (`pe`,
etc.) evaluated by a fix (e.g. `fix ave/time ... v_pe`) requires that
keyword to appear in `thermo_style custom` -- otherwise LAMMPS aborts at
the first run with "Thermo keyword pe in variable requires thermo to
use/init potential energy". A large `thermo N` cadence (L27) does NOT
conflict: the keyword only needs to be in the style, not printed often.

## Where it bit

2026-07-28, ni-h-phase-diagram-eam-meam, probe jobs 21570516/17/18: all
three isotherm inputs carried `variable pe equal pe` + ave/time streams
(the SCIENCE-KICKOFF sec 6.2 suggested addition, never previously run)
with `thermo_style custom` lacking `pe`. All three probes aborted at
`run ${equil}` in ~8 s -- the probe rule caught it before 41 queued
production tasks inherited it. Fixed by adding `pe` to thermo_style in
all three inputs, both trees.

## Suggested wording

(as in "Proposed rule")

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/lessons.md. Assigned L33; noted explicitly that L27's huge `thermo N` cadence composes cleanly with this rule.

---
proposal_id: 2026-07-28-2040-absolute-paths-in-every-handoff
session_id: 2026-07-28-1712-Ni-H-isotherms
proposed_at: 2026-07-28T20:40Z
target_file: canon/preferences.md
target_section: new subsection "Command hand-offs"
priority: routine
status: merged                          # was pending
---

## Proposed rule

Every command block handed to Erik for execution uses full absolute
paths -- every `cd` targets an absolute directory, every file argument
that is not resolved by the script itself is absolute. No relative `cd
../..` chains, no "from the same directory as before". State where the
block runs (which machine/shell) at the top.

## Where it bit

2026-07-28, ni-h-phase-diagram-eam-meam submission hand-offs: the pilot
issued sbatch blocks with relative cd chains; Erik asked "can you tell
me exactly what to submit how and where (ALWAYS!)". The
SCIENCE-KICKOFF-NIH-STOICHIOMETRY.md header already records this as a
standing preference ("Give Erik exact absolute paths in every command");
it was not yet in canon, so a fresh session missed it.

## Suggested wording

- **Absolute paths in every command hand-off.** Each `cd` absolute; each
  block prefixed with where it runs (e.g. "in your cmmg shell"). A
  hand-off must be paste-able from any starting directory. Relative
  navigation between blocks is never assumed to survive.

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/preferences.md, new 'Command hand-offs' subsection. COMBINED with duplicate proposal 2026-07-29-1650-absolute-paths-copy-paste into a single rule (absolute paths + copy-paste block + state where it runs).

---
proposal_id: 2026-07-29-1310-h-concentration-convention
session_id: 2026-07-29-1154-sim-ideas-backlog
proposed_at: 2026-07-29T13:10Z
target_file: canon/preferences.md
target_section: new subsection "Concentration convention (Ni-H and interstitial systems)"
priority: routine
status: merged                          # was pending
---

## Proposed rule

Report and plot interstitial concentration as x = N_H / N_Ni (H per
host atom), not as the site-catalogue occupancy c = N/M, in all
analyses, plots, tables and prose. c = N/M may be carried alongside as
a diagnostic, but x is the headline quantity.

## Where it bit

2026-07-29, Ni-H-PHASE-DIAGRAM-EAM-MEAM analysis of the 2026-07-28
mu-scan arrays (jobs 21570566/67/92). The mc/sites output c = N/M is
normalized by the *dynamic* Voronoi site catalogue, so its value
depends on which site types the clearance window admits (1/3 on the
NiH shelf when tet sites are counted) — it is tool-dependent, not a
material property. Erik's explicit instruction when commissioning the
analysis notebooks: "concentration please as number of H / number of
Ni (please keep this convention in the future)".

## Suggested wording

- **Concentration convention: x = N_H/N_Ni.** For Ni-H (and any
  interstitial system), concentration in outputs, analyses, plots and
  prose is x = N_species / N_host (e.g. H per Ni). The site-catalogue
  occupancy c = N/M of `fix mc/sites` is tool-dependent (the dynamic
  catalogue's M changes with the clearance window and lattice
  expansion) and appears only as a diagnostic column, never as the
  reported concentration. (Set 2026-07-29, Erik's words: "concentration
  please as number of H / number of Ni ... keep this convention in the
  future".)

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/preferences.md, new 'Concentration convention' section. Prose adopted near-verbatim, incl. Erik's quoted instruction.

---
proposal_id: 2026-07-29-1650-absolute-paths-copy-paste
session_id: 2026-07-29-1154-sim-ideas-backlog
proposed_at: 2026-07-29T16:50Z
target_file: canon/preferences.md
target_section: new subsection "Command hand-offs"
priority: routine
status: merged                          # was pending
---

## Proposed rule

Every command handed to Erik for execution (sbatch, ssh, rsync, cd,
analysis invocations) is given with FULL ABSOLUTE PATHS and as a
ready-to-copy-paste block — never relative paths, never "cd into the
run dir" prose without the literal cd line.

## Where it bit

2026-07-29, Ni-H session: pilot presented probe sbatch commands with a
bare "from the respective run dirs" preamble; Erik could not find the
directories and had to ask ("can you please (always, remember that)
give the correct path and submit command so I can easily copy paste?").
The SCIENCE-KICKOFF-NIH-STOICHIOMETRY.md handoff (2026-07-28) had
already recorded the same standing preference ("Give Erik exact
absolute paths in every command"); it belongs in canon, not in a
per-project handoff file.

## Suggested wording

- **Command hand-offs: absolute paths, copy-paste ready.** Any command
  Erik is asked to run appears as a complete copy-pasteable block with
  full absolute paths — including the `cd` line when the command is
  cwd-sensitive (sbatch scripts resolving `SLURM_SUBMIT_DIR`). One
  block per logical action, in execution order. Erik's own words
  (2026-07-29): "please (always, remember that) give the correct path
  and submit command so I can easily copy paste". (Promotes the
  identical instruction from the 2026-07-28 kickoff handoff to canon.)

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/preferences.md, new 'Command hand-offs' subsection. COMBINED with duplicate proposal 2026-07-28-2040-absolute-paths-in-every-handoff; both Erik quotes retained.

---
proposal_id: 2026-07-30-0930-analysis-window-never-hardcoded
session_id: 2026-07-30-0926-nih-harvest-notebooks
proposed_at: 2026-07-30T09:30Z
target_file: canon/lessons.md
target_section: new lesson L<next>
priority: high
status: merged                          # was pending
---

## Proposed rule

An analysis script or notebook must derive its averaging window from each
run's OWN data extent, never from a hardcoded step number. And a run that
was cut short (walltime, cancellation) is not thereby unconverged: the
verdict comes from a drift test on the observable, not from the step count.

## Where it bit

2026-07-30, Ni-H thread 03. The v2 notebook hardcoded `step > 14000` as the
averaging window, correct for the 20 000-step runs it was written against.
The next wave contained a 400 000-step run (gcmc-matched) and a run
truncated at 26 120 of 80 000 steps (tet-extended). Applied unchanged, the
hardcoded window would have averaged the last 6 % of one run and would have
been read as "the truncated run is unusable" for the other. In fact the
truncated run was fully converged (drift -2.3e-3 per 1e5 attempts against a
1.5e-2 noise floor) and produced the thread's cleanest number, x = 2.514,
correcting an earlier value of 2.403 that had itself passed v2's check while
still climbing.

## Suggested wording

- **L<N>: analysis windows are derived, not hardcoded; truncation is not
  non-convergence.** Any averaging/summary window is computed from the run's
  own last written step (e.g. the final 30 % of the post-equilibration
  window), so the same code is correct for a 20k-step probe and a 400k-step
  production run. Whether a point counts as equilibrated is decided by a
  drift test on the observable over that window, never by whether the job
  reached its nominal step count. A job killed by the walltime whose
  observable is flat IS a usable data point; a job that finished cleanly
  whose observable is still climbing is NOT.
  Corollary on the drift test itself: judge drift against the **detrended**
  residual scatter, floored by the relevant counting noise. Comparing drift
  to the raw block scatter is circular — a drifting point scatters *because*
  it drifts, and the inflated floor then excuses the drift (exactly how the
  unconverged mu = -1.99 point passed the v2 check). Comparing drift to the
  value itself is also wrong: on a dilute branch carried by a few atoms,
  counting noise alone is a several-percent effect and would condemn
  well-converged points.

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/lessons.md. Assigned L34, incl. the detrended-drift-test corollary; learnings.md 'Harvest the failure mode' entry cross-references it.

---
proposal_id: 2026-07-30-0932-report-failure-mode-not-just-failure
session_id: 2026-07-30-0926-nih-harvest-notebooks
proposed_at: 2026-07-30T09:32Z
target_file: canon/learnings.md
target_section: "Cluster discipline"
priority: routine
status: merged                          # was pending
---

## Proposed rule

When a production task fails, the harvest records the failure MODE and what
the task had achieved before failing — not just "N/M failed". A crashed task
that got most of the way is data plus a method result; discarding it as a
failed batch throws both away.

## Where it bit

2026-07-30, Ni-H thread 03 run 03. Four of six `fix gcmc` tasks died on
`ERROR: Non-numeric pressure - simulation unstable`, preceded by
`MAXENERGYTEST` warnings. The pre-harvest expectation (recorded 2026-07-29)
was that they would hit the 12 h walltime — a benign outcome. They did not;
they crashed after first filling to the hydride shelf (x = 0.74...0.99) and
then destroying the cell (a -> ~2.0 A). Recording only "4/6 failed" would
have lost (a) the four lower-bound compositions, (b) the fact that
random-insertion GCMC is numerically unsafe at high occupancy on this
potential while the site-catalogue method cannot produce that configuration
by construction, and (c) the inference that the LOW-mu side is the safe
direction to extend the grid. All three are results.

## Suggested wording

Add to the "Cluster discipline" subsection:

- **Harvest the failure mode, not just the failure count.** For every task
  that did not complete, record: the terminating error verbatim, the step it
  died at versus its nominal length, and the state of the observables
  immediately before. Distinguish walltime truncation (data usually usable,
  see L<N>) from numerical instability (data usable up to the onset, and the
  onset itself is a finding) from a setup bug (data void). Where an
  instability is method-specific, say which method is implicated and why the
  alternative is immune — that comparison is often worth more than the run
  that was intended.

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/learnings.md 'Cluster discipline'. Adopted as proposed, with the run-03 incident condensed inline; L<N> reference resolved to L34.

---
proposal_id: 2026-07-30-0934-promote-mu-scan-loader-to-tool
session_id: 2026-07-30-0926-nih-harvest-notebooks
proposed_at: 2026-07-30T09:34Z
target_file: canon/tools/ (new tool card) + canon/tools/examples-catalog.md
target_section: new tool card, per the "3+ uses -> promote" rule in learnings.md "Tools"
priority: routine
status: pending
---

## Proposed rule

Promote the mu-scan results loader + per-mu summariser to a real tool with a
card, per the standing "hand-rolled 3+ times -> propose promotion" rule.

## Where it bit

2026-07-30: the same loader (walk `results/mu-*/`, read `ave.*.dat` and
`trace.*.dat`, attach mu from the directory name, convert MD steps to
cumulative MC attempts via the fix cadence, then summarise per mu with a
convergence verdict) is now hand-written in THREE notebooks:
`03_.../analysis_c-mu-shift-and-equilibration_EAM-300K.ipynb`,
`05_.../analysis_c-mu_a-c_EAM-vs-MEAM_300K.ipynb`, and
`05_.../analysis_T-dependence_c-mu_a-x_EAM-vs-MEAM_300K-vs-600K.ipynb`.
Three copies means three places for the convergence criterion to drift apart
— which is precisely the bug class the criterion was written to catch.

Kept as duplicated code for now on purpose: the notebooks must stay
runnable standalone from their own directory, and a pilot session does not
add tooling to canon unilaterally.

## Suggested wording

New tool (own repo, deployed to `~/bin/` Mac + cluster per the tool-card
contract) exposing:
- `load_mu_scan(results_dir, nevery, natt)` -> (ave, trace) long frames with
  `mu`, `step`, `trials`, `valid`;
- collapse detection (a_eff below a fraction of its running max) with
  truncation;
- `summarize(...)` -> per-mu means over a derived window plus the verdict
  ladder from proposal 2026-07-30-0930 (crashed / empty / window-too-short /
  noise-limited / filling / equilibrated-late / equilibrated).
Card records the column contract of `ave.*.dat` / `trace.*.dat` as produced
by the project's `fix ave/time` + `fix print` blocks, since that contract is
what the loader depends on.

## Designer review notes

Reviewed 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge: ACCEPTED IN PRINCIPLE, left pending. Not mergeable as prose: per the tools rules (learnings.md 'Tools', ARCHITECTURE.md §6) a card documents a real installed tool, so the loader needs its own repo + code + tests deployed to ~/bin (Mac + cluster) BEFORE a card can land in canon. Needs a designer session with tool-implementation scope (or Erik builds it via Claude Code against a spec), then the card + catalog entry follow.

---
proposal_id: 2026-07-30-1015-merge-scans-one-series-per-condition
session_id: 2026-07-30-0926-nih-harvest-notebooks
proposed_at: 2026-07-30T10:15Z
target_file: canon/preferences.md
target_section: "Plot defaults"
priority: routine
status: merged                          # was pending
---

## Proposed rule

A plot shows **one series per physical condition**, not one series per run or
per parameter grid. Several scans of the same condition (coarse + fine mu
windows, an original plus a longer rerun of one point) are merged into a
single curve, with the finer/longer run superseding the overlapping points.
Data quality is carried by marker style (filled = converged, open = not
assured), not by splitting the curve.

## Where it bit

2026-07-30, Ni-H threads 03/05. The first version of the notebooks drew the
MEAM coarse scan and the MEAM fine window as two separate series, and the
tet-extended rerun as a third series alongside the run-01 scan it replaced.
Erik: "separating the data into fine and the first (coarse) run does not
provide any new results, please plot them together. use however open symbols
if the convergence is not assured."

The merge also turned out to be load-bearing rather than cosmetic: once each
potential was one curve, it became visible that the EAM has **zero** grid
points inside its own two-phase window at 300 K (nothing between x = 0.005
and x = 1.000) while the MEAM has four. The apparent "MEAM transition is
broader" result was partly an artefact of unequal sampling -- which the
split-series plot had made easy to miss.

## Suggested wording

Add to the "Plot defaults" subsection:

- **One series per physical condition; quality goes in the marker.** Merge
  all scans of the same condition into a single curve, finer or longer runs
  superseding coarser ones on shared abscissa values, and keep provenance in
  a `src` column so any point can be traced to its run. Encode data quality
  with marker style -- **filled = converged/assured, open = not assured** --
  and never by drawing a second series. Erik's own words (2026-07-30):
  "separating the data into fine and the first (coarse) run does not provide
  any new results, please plot them together. use however open symbols if the
  convergence is not assured."
- **Corollary: report the sampling density of any feature you compare.** When
  a claim rests on the *shape* of a transition (width, sharpness), print the
  number of grid points that actually fall inside it for each curve. A curve
  through two points that jump from x ~ 0 to x ~ 1 is evidence about the grid,
  not about the physics.

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/preferences.md 'Plot defaults'. Adopted with the sampling-density corollary and the EAM zero-points-in-window incident noted inline.

---
proposal_id: 2026-07-30-1018-exclude-runaway-branch-companion-plot
session_id: 2026-07-30-0926-nih-harvest-notebooks
proposed_at: 2026-07-30T10:18Z
target_file: canon/preferences.md
target_section: "Plot defaults"
priority: routine
status: merged                          # was pending
---

## Proposed rule

When one branch of a dataset runs away in the plotted quantity and compresses
the region of interest, the figure set includes a companion view with the
runaway branch excluded -- and the excluded branch is visually marked (not
silently dropped) wherever it IS shown.

## Where it bit

2026-07-30, Ni-H thread 05. The EAM overcharges into tetrahedral sites at
high mu, reaching x = 2.51 where the physics of interest (the alpha -> beta
transition) lives entirely below x = 1. On a full-range c(mu) axis the
transition occupies the bottom third of the panel and the two potentials'
shapes cannot be compared at all. Erik: "Please provide additional plots that
exlude mu / concentraiton values where the tetrahedral sites get filled, so
that one can actually see the transition regime better."

## Suggested wording

Add to the "Plot defaults" subsection:

- **Give the region of interest its own axis.** If part of the data runs away
  in the plotted quantity (here: tetrahedral overcharge, x -> 2.5, against a
  transition at x < 1), add a companion figure restricted to the regime the
  question is about, using a named cut constant (e.g. `X_TET = 1.05`) so the
  same threshold is applied everywhere and is visible in the code. A
  plateau-aligned zoom (abscissa minus each curve's own plateau) is the
  version that compares shapes across curves whose absolute reference differs.
  Where the runaway branch is shown, mark it distinctly (separate colour) so
  it cannot be misread as belonging to the main regime, and never drop it
  without saying so in the caption.

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/preferences.md 'Plot defaults'. Adopted as proposed (named cut constant, plateau-aligned zoom, distinct colour for the runaway branch).

---
proposal_id: 2026-07-30-1100-match-mc-cadence-not-just-budget
session_id: 2026-07-30-0926-nih-harvest-notebooks
proposed_at: 2026-07-30T11:00Z
target_file: canon/lessons.md
target_section: new lesson L<next>
priority: high
status: merged                          # was pending
---

## Proposed rule

When two MC methods are compared inside MD, match the **attempts per MD step**,
not merely the total attempts per job. Matching only the total budget by
running one method for more MD steps changes how much the MD relaxes between
attempts, which is itself a variable.

## Where it bit

2026-07-29/30, Ni-H thread 03. Run 03 was designed as a "matched budget"
comparison: `fix gcmc 100 250` (2.5 attempts/MD step) x 400k steps = 1e6
attempts, against `fix mc/sites 20 1000` (50 attempts/MD step) x 20k steps =
1e6 attempts. Equal totals, but a factor 20 difference in cadence -- so the
gcmc run gave the lattice 20x longer to relax between exchange attempts,
which flatters random-insertion acceptance, and left the MD-step axis
meaningless across methods. Erik caught it on reading the two inputs
side by side: "To be able to have a proper comparison, they should have the
same number of attempts per the same number of MD steps!"

Consequence for interpretation, which is the part worth keeping: cadence
changes the *rate of convergence*, not the equilibrium distribution, so a
cadence mismatch does NOT bias a chemical-potential shift measured between
two EQUILIBRATED points. It biases (i) any cost/efficiency comparison and
(ii) any quantity read off points that never equilibrated -- which is exactly
what run 03's Delta_mu bound was.

## Suggested wording

- **L<N>: match the MC cadence, not the MC budget.** Any comparison of two MC
  move sets embedded in MD fixes the same `attempts per MD step` for both, and
  preferably the same total step count too, so the two runs are comparable
  step-for-step and attempt-for-attempt with the move type as the only
  difference. Matching totals while letting cadence differ silently varies the
  amount of MD relaxation per attempt. State in the run record which quantity
  is matched. Corollary: a cadence mismatch is harmless for equilibrium
  observables and fatal for cost/efficiency observables -- so before quoting a
  cost ratio, check the cadence line of both inputs, not the budget arithmetic
  in the run notes.

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/lessons.md. Assigned L35, incl. the equilibrium-vs-cost corollary and the 2026-07-30 class fix (gcmc cadence matched to mc/sites).

---
proposal_id: 2026-07-30-1102-sshfs-new-files-invisible-in-listings
session_id: 2026-07-30-0926-nih-harvest-notebooks
proposed_at: 2026-07-30T11:02Z
target_file: canon/clusters.yaml
target_section: cmmg quirks (extend the existing sshfs_default_options_show_stale_views entry)
priority: routine
status: merged                          # was pending
---

## Proposed rule

Extend the known sshfs stale-view quirk: files newly CREATED through the mount
can be absent from `ls` while being fully readable by exact path. Verify writes
by exact-path `stat`/`md5sum`, never by a directory listing, and never use
`rsync`/`find`/globs to copy freshly written files off the mount.

## Where it bit

2026-07-30: 18 new .in/.slurm files were written into six new run directories
through the cmmg mount. Every file was readable by exact path with the correct
size and md5, and the cluster side was fine -- but `ls` on each directory
returned `total 0`. Because `rsync --include=... ` and `find` both enumerate
via the directory listing, the mirror-to-Mac step silently copied ZERO files
and reported success. Copying by explicit filename worked immediately.

This is the same L15 root cause as the read-side stale views already recorded,
but the failure mode is the opposite direction (writes) and much quieter: a
read-side stale view looks like missing data and gets noticed, whereas a
write-side one looks like a successful no-op.

## Suggested wording

Extend the `sshfs_default_options_show_stale_views` quirk note with:

    # Write-side variant (2026-07-30): files just CREATED through the mount
    # may not appear in `ls` for some time, though they are complete and
    # readable by exact path. Therefore:
    #   - verify writes with `stat`/`md5sum` on the full path, not `ls`;
    #   - never enumerate freshly written files with rsync/find/globs -- they
    #     will silently copy nothing and exit 0;
    #   - copy by explicit filename when mirroring a just-written run dir.

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/clusters.yaml cmmg quirks. Extended the `sshfs_default_options_show_stale_views` quirk comment with the write-side variant as proposed.

---
proposal_id: 2026-07-30-1145-verify-writes-dont-infer-them
session_id: 2026-07-30-0926-nih-harvest-notebooks
proposed_at: 2026-07-30T11:45Z
target_file: canon/learnings.md
target_section: "Process"
priority: high
status: merged                          # was pending
---

## Proposed rule

A file write is reported as done only after it has been read back. If the
verification step could not run, the write is reported as UNVERIFIED -- never
as "almost certainly completed" on the basis of where a script died.

Corollary for this mount: never run bulk checksum/stat sweeps over the sshfs
mount. They are what wedges it.

## Where it bit

2026-07-30. A 21-file md5 sweep across the cmmg sshfs mount timed out and left
the device bridge unresponsive (even `echo` failed for several minutes). The
same call had earlier generated run 11's three files, so I reasoned that since
generation precedes verification in the script, the files "almost certainly
completed" -- while correctly hedging that I could not confirm it. They had
not: Erik pasted the run-11 probe command and got
`-bash: cd: .../11_EAM-...: No such file or directory`, wasting a round trip
in the middle of a submit sequence.

Two distinct errors worth separating:
1. **Reasoning about where a script died instead of checking.** The hedge was
   right, the probability estimate was wrong, and the estimate is what he
   acted on. "Unverified" is the whole of what was known.
2. **The sweep itself.** Verification does not require checksumming everything;
   spot-checking the files just written is enough and does not risk the mount.

## Suggested wording

Add to the "Process" subsection:

- **Verify writes, do not infer them.** Read back what you wrote -- size or
  checksum on the files you actually touched -- before reporting a write as
  complete. If the check cannot run (bridge down, mount stale, call timed out),
  say **UNVERIFIED** and name what needs confirming; do not translate "the
  write came earlier in the script than the crash" into a confidence estimate.
  A hedge attached to a wrong probability still sends the user down the wrong
  path.
- **No bulk sweeps over the sshfs mount.** Checksumming or stat-ing tens of
  files across the mount can hang it and take the whole device bridge with it
  (2026-07-30: 21 files was enough). Spot-check the files just written, one or
  two per directory, and prefer `wc -c` over `md5sum` for large ones.

## Designer review notes

Merged 2026-08-02T16:20Z by session 2026-08-02-1614-designer-inbox-merge.
Target: canon/learnings.md 'Process'. Adopted as two bullets (verify-writes / no-bulk-sweeps) with cross-ref to the clusters.yaml write-side quirk.
