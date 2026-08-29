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

- **Use the schema above verbatim.** Entries filed as bare `## <id> — title`
  headings without the `proposal_id:` frontmatter are invisible to a
  frontmatter grep and have been skipped by designer passes for weeks
  (five 2026-08-05 entries and two 2026-08-24 entries, found 2026-08-25).
  A designer pass therefore sweeps BOTH `^proposal_id:` and `^## 20` and
  reconciles every "filed as proposal" claim in SESSIONS.md against this
  file before declaring the inbox empty.
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
status: merged                          # 2026-08-25: tool BUILT + card landed (session 2026-08-25-1536)
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

Second review 2026-08-20 by session 2026-08-20-1145-cluster-status-sweep:
CONFIRMED, still pending, same reasoning -- canon holds cards, not code, and
the tool does not exist. One BLOCKING detail to settle before anyone builds it,
which was not known at the first review: the card must record the column
contract of `ave.*.dat` / `trace.*.dat`, and that contract CHANGED during the
2026-08-05 harvest -- 7- and 8-column schemas are both in circulation and are
handled by `nih_loaders.py`'s RUNS registry. Pin the schema first, or the tool
inherits the ambiguity it is meant to remove. Also fold in the collapse
GUARD BAND fix from that harvest (the detector lagged by one block-average
window and one task published a contaminated mean as clean) -- that is the
strongest single argument for having one implementation instead of three.
Recommend opening it as its own designer+dev session with `nih_loaders.py` as
the starting point rather than a green-field build.

Third review 2026-08-25 by session 2026-08-25-1105-sim-status: CONFIRMED,
still pending, unchanged reasoning -- the tool still does not exist and canon
holds cards, not code. Both blocking details from the second review stand
(pin the ave/trace column schema; fold in the collapse guard-band fix).
Flagged to Erik in-turn as needing its own dev session; this is now the only
pending item in the inbox.

Fourth review AND MERGE 2026-08-25 by session 2026-08-25-1536-nimelt-probe-fix,
designer+pilot at Erik's explicit "implement everything in your inbox": the
blocker was never approval but that no session had taken the dev scope, so
this session took it. TOOL BUILT: ~/DEVEL/MU-SCAN-LOADER (mu_scan.py library
+ mu-scan CLI + 18-test suite, git-initialised; GitHub push handed to Erik).
Both blocking details from the second review resolved: (1) the ave/trace
column contract is PINNED as a (kind, ncol) schema registry -- and pinning it
immediately paid off: run 14's traces carry a NINE-column schema (att/acc
counters appended) that nih_loaders.py cannot read at all, found only
because the tool refuses unknown counts; (2) the L43 collapse guard band is
in, verified against the real run-06 mu=-2.02 files. Equivalence-tested
against nih_loaders.py on real run-06 data (identical isotherm). Card:
canon/tools/mu-scan.card.yaml (records the pinned contract + the n_metal
and no-default-tps gotchas); catalog row added. Deploy to ~/bin (Mac +
cluster) handed to Erik -- card is live, exec verified on the Mac-side VM
(--version) and the full suite in the build environment.

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

---
proposal_id: 2026-08-02-1900-fix-group-semantics-of-global-counter-fixes
session_id: 2026-08-02-1647-ingest-eam-dislocs-ni-cu
proposed_at: 2026-08-02T19:00Z
target_file: canon/lessons.md
target_section: new lesson L<N>
priority: routine
status: merged
---

## Proposed rule

Before applying any LAMMPS fix that maintains global counters or
targets (concentrations, species counts, collective constraints) to a
GROUP other than `all`, verify from the docs — and, if the docs are
silent, from the source — that the subgroup case is actually
implemented consistently. "Parses cleanly and echoes its parameters"
is not evidence that it is.

## Where it bit

EAM-DISLOCS-Ni-Cu, VCD90-T300-c031 v1-v5 (2026-04-08..13): `fix
interior sgcmc ... variance kappa target` silently produced wrong
physics — fix_sgcmc.cpp counts species over the fix group but scales
the variance-constraint targets by atom->natoms, so with a frozen
pure-Ni boundary excluded from the group, the ~27k boundary Ni atoms
read as a Ni deficit and the outer VC test vetoed every Ni->Cu swap
(e^-19 already at kappa=200). Five run campaigns and a kappa sweep
chased "kappa too weak" for a bug that no kappa can cure; the doc page
gives no warning and every doc example uses group `all`. Diagnosed
2026-08-02 (thread 02_VCSGC-VARIANCE-CONSTRAINT-GROUP-BUG); root cause
confirmed at source level. Upstream LAMMPS issue worth filing.

## Suggested wording

**Rule:** any fix that maintains a global counter, concentration, or
collective constraint (e.g. `fix sgcmc variance`, widom/gcmc-family
bookkeeping) is verified for group-consistency before being used with a
group other than `all`: check the doc page for explicit subgroup
semantics; if silent, check the source for mismatched scopes (group-
filtered counts vs `atom->natoms`-scaled targets is the signature).
Fail-late class: the input parses, the fix echoes its parameters, and
the error appears only as physically wrong equilibria — no lint can
catch it; the check belongs in design-time review whenever `fix <ID>
<group>` has `<group> != all` and the fix doc mentions concentrations
or targets.

**Where it bit:** EAM-DISLOCS-Ni-Cu VC-SGC v1-v5 (see thread
02_VCSGC-VARIANCE-CONSTRAINT-GROUP-BUG for the full mechanism and the
patch points in fix_sgcmc.cpp).

**Target:** style/lammps.md pre-flight cross-ref.

## Designer review notes

Merged 2026-08-20 as **lessons.md L38**, wording essentially as proposed. Kept
the fail-late framing and the `atom->natoms`-vs-group-count signature verbatim --
that sentence is what makes the class recognisable in a new fix. Cross-reference
added from style/lammps.md 1.9 (preconditions clause, "does it behave the same
for a group other than `all`?").

---
proposal_id: 2026-08-03-0926-probe-task-count-scales-with-cell
session_id: 2026-08-03-0905-ni-dislocs-multipot
proposed_at: 2026-08-03T09:26Z
target_file: canon/learnings.md
target_section: "Cluster discipline" -- amend the "Probe before production" entry
priority: routine
status: merged
---

## Proposed rule

The probe's task count is not fixed at 2. It is the smallest count that keeps
the probe under a few minutes for THIS cell and THIS potential, with a floor of
2 (never 1) so the parallel decomposition is still exercised. State the chosen
count and the reason in the run's thread.md.

## Where it bit

2026-08-03, ni-dislocs-eam-meam-ace / 01_SCALE-AND-RELAX-0K. The canonical
recipe says "2 MPI tasks (catches parallel-decomp bugs), 5-min walltime". The
cells here are 5.7e5 atoms and one of the potentials is ACE at a measured
1.86e-4 core-s per atom-step. On 2 tasks a single FIRE iteration would take
~530 s, so a 10-iteration probe could not finish inside any sane probe
walltime, and the 5-min cap would be violated by the recipe itself. The probes
were written with 32 tasks (`processors 2 2 8`), which still exercises a 3D
decomposition and lands each probe at seconds-to-tens-of-seconds.

The underlying quantity the "2 tasks" number was standing in for is "cheap
enough that a bad probe costs nothing", and that is a function of
N_atoms x cost_per_atom_step, not a constant.

## Suggested wording

In `canon/learnings.md`, "Cluster discipline", replace

    2 MPI tasks (catches parallel-decomp bugs), 5-min walltime, same
    partition as production.

with

    The smallest task count that keeps the probe inside a few minutes for
    this cell and this potential -- floor of 2, never 1, so the parallel
    decomposition is still exercised. For small cells that is 2; for
    ~1e5-1e6-atom cells or an ML potential it is tens of tasks. Pick it
    from N_atoms x (measured or estimated) cost per atom-step, and record
    the count and the reason in thread.md. Walltime cap stays short
    (<= 20 min); same partition as production.

## Designer review notes

Merged 2026-08-20 into learnings.md "Cluster discipline". Accepted in full; the
replacement wording is the proposal's, plus the ACE arithmetic (530 s per FIRE
iteration on 2 tasks) kept inline because the number is what makes the point.

---
proposal_id: 2026-08-03-0927-minimize-probes-report-loop-time
session_id: 2026-08-03-0905-ni-dislocs-multipot
proposed_at: 2026-08-03T09:27Z
target_file: canon/learnings.md
target_section: "Cluster discipline" -- amend the "Probe before production" pass criteria
priority: routine
status: merged
---

## Proposed rule

For a probe whose work is a `minimize` rather than a `run`, the pass criterion
and the L26 walltime-calibration source is the `Loop time of X on N procs for M
steps` line, not a `Performance:` line. LAMMPS emits `Performance:` only from
`run`.

## Where it bit

2026-08-03, ni-dislocs-eam-meam-ace / 01_SCALE-AND-RELAX-0K. The three probes
are 10-iteration FIRE minimizations. The canonical probe pass criteria require
"a `Performance:` line for each `run` block", which these probes cannot
produce -- a probe that behaved perfectly would have been scored as failed.
Confirmed against the reference run
`DISLOCS-Ni-Cu/RELAX-D90/log.lammps` (lammps/250722): the minimize block ends
with `Loop time of 18.4291 on 128 procs for 733 steps with 568606 atoms` and
the "Minimization stats" block, and no `Performance:` line appears anywhere.

## Suggested wording

In `canon/learnings.md`, "Cluster discipline", "Probe before production",
amend the third bullet of the pass criteria:

    - emit a `Performance:` line for each `run` block, or -- where the
      work is a `minimize` rather than a `run` -- a
      `Loop time of X on N procs for M steps` line for each minimize
      block. Either is the L26 walltime-calibration source; `minimize`
      never emits `Performance:`.

## Designer review notes

Merged 2026-08-20 into the probe pass criteria. Accepted in full. Worth flagging
for the record that this bug was latent, not observed: a correct probe would have
been scored as FAILED, so it could only ever have caused a false alarm, never a
bad production run.

---
proposal_id: 2026-08-03-0928-generic-filename-lint-false-positive
session_id: 2026-08-03-0905-ni-dislocs-multipot
proposed_at: 2026-08-03T09:28Z
target_file: canon/style/lammps.md
target_section: "1.2 Generic-filename check (L3, L20)"
priority: routine
status: merged
---

## Proposed rule

Anchor the generic-filename lint patterns at both ends so a descriptively
prefixed filename does not trip it. As written, the check flags the style
guide's own canonical idiom.

## Where it bit

2026-08-03, ni-dislocs-eam-meam-ace / 01_SCALE-AND-RELAX-0K pre-flight. Every
one of the six new inputs was flagged by the 1.2 grep on the string
`Ni-disloc-DCHAR-relax0K-${POTENTIAL_LABEL}-final-snapshot.dump`. The regex
opens with `(^|[^A-Za-z])`, which a leading `-` satisfies, so any
`<descriptive-prefix>-final-snapshot.dump` matches. Section 3 of the same file
("Descriptive-naming vector") gives
`Ni-fcc-${POTENTIAL_LABEL}-final-snapshot.dump` as the recommended form -- the
guide fails its own lint. Same reasoning applies to `relaxation-log.dat`,
`a0-result.txt` and `data.lammps` in that alternation. Three sessions of
"is this a real hit or the known false positive?" is exactly the friction the
mechanical lint exists to remove.

## Suggested wording

In `canon/style/lammps.md` section 1.2, replace the grep with

    grep -E '(^|[[:space:]/])(dump\.out|restart\.data|log\.lammps|a0-result\.txt|relaxation-log\.dat|final-snapshot\.dump|tmp\.|out\.dat|data\.lammps)([[:space:]]|$)' <input>

and add below it:

    The leading and trailing anchors are load-bearing: the offence is a
    filename that IS the tutorial default, not one that ends with it.
    `Ni-fcc-Pezold-EAM-final-snapshot.dump` is correct and must not be
    flagged; a bare `final-snapshot.dump` must be.

Mirror the same change in `canon/templates/lint-lammps-input.sh`.

## Designer review notes

Merged 2026-08-20 into style/lammps.md 1.2. **Scope reduced:** the proposal asked
to mirror the change in `templates/lint-lammps-input.sh`, but the script ALREADY
carried the anchored form `(^|[[:space:]/="])...([[:space:]"]|$)`. Only the style
guide was stale. No script change made. The divergence is itself the finding --
the executable and its documentation had drifted, and three sessions trusted the
documented (broken) grep over the working one.

---
proposal_id: 2026-08-03-0938-probe-exemption-when-production-is-cheaper
session_id: 2026-08-03-0905-ni-dislocs-multipot
proposed_at: 2026-08-03T09:38Z
target_file: canon/learnings.md
target_section: "Cluster discipline" -- add an exemption clause to "Probe before production"
priority: routine
status: merged
---

## Proposed rule

The probe requirement is waived when the production job is itself no more
expensive than its own probe would be -- single node, few cores, seconds of
runtime. In that regime the production job IS the probe: a bad input fails in
seconds having consumed nothing a probe would have saved. The waiver is
recorded in thread.md with the numbers that justify it, never taken silently.

## Where it bit

2026-08-03, ni-dislocs-eam-meam-ace / 02_FISCHER-A0-AT-0K / 01_MIN-FISCHER-EAM:
a 4000-atom fcc box, 1 core, `s.cmmg`, expected ~1-2 s (the two identical runs
in ni-a0-cij-eam-meam thread 01 took 1 s and 2 s). A probe would occupy the
same partition and the same single queue slot as the production, so it would
strictly add one submit-wait-inspect round trip and remove nothing.

The rule as written is unconditional, so the honest options were "waste a round
trip" or "deviate silently". Neither is what the rule is for: its stated
purpose is that guess-and-ship LAMMPS-syntax bugs must not burn a real
allocation. Where there is no allocation to burn, the purpose is already met.

Note this is the same shape as the sibling proposal
2026-08-03-0926-probe-task-count-scales-with-cell: both come from the probe
recipe being written around one implicit cell size and cost, and both are
fixed by making the cost explicit rather than the task count.

## Suggested wording

In `canon/learnings.md`, "Cluster discipline", append to the "Probe before
production" entry:

    **Exemption -- production cheaper than its own probe.** Where the
    production job is single-node, few-core and seconds long (a bulk
    baseline minimization, a capability check, a small static
    calculation), no separate probe is required: the production job is
    the probe. The test is whether a probe would occupy a smaller
    allocation than the production -- if it would not, it buys nothing.
    Record the waiver and the numbers behind it in the run's thread.md;
    an unrecorded skip is still a violation. This exemption never applies
    to a multi-node job, an array, or anything on `p.cmmg`.

## Designer review notes

Merged 2026-08-20 into the "Probe before production" entry. Accepted in full,
including the never-applies clause (multi-node, arrays, p.cmmg) -- without it the
exemption is the kind that grows.

---
proposal_id: 2026-08-03-1002-force-norm-reporting-convention
session_id: 2026-08-03-0905-ni-dislocs-multipot
proposed_at: 2026-08-03T10:02Z
target_file: canon/preferences.md
target_section: new section "Convergence and force-norm reporting" (after "Computation defaults")
priority: routine
status: merged
---

## Proposed rule

Never report a bare `fnorm`. Every place a convergence state is stated -- input
header, thermo columns, thread.md, run.yaml, chat -- gives BOTH `fnorm/N` and
`fnorm/sqrt(3N)` and names the definition of `fnorm` it is normalizing. The
default stopping criterion for a 0 K relaxation is `fnorm/sqrt(3N) < 1e-8
eV/A`, written into the input as `ftol = 1e-8 * sqrt(3N)` computed from the
atom count, never hard-coded per cell.

## Where it bit

2026-08-03, ni-dislocs-eam-meam-ace. The pilot wrote "ftol 1e-3 is ~7.6e-7
eV/A per-atom RMS" in thread.md and run.yaml. The arithmetic was right for
`fnorm/sqrt(3N)` but the phrase "per-atom RMS" reads as `fnorm/sqrt(N)`, which
is sqrt(3) larger, and Erik reasonably read it as `fnorm/N`, which is 1300x
smaller. Three plausible readings of one sentence, and only the raw fnorm was
actually in the log.

Checked against the docs (docs.lammps.org/minimize.html, 2026-08-03):

> "the 2-norm (length) of the global force vector is less than the *ftol*"
> "the specified force tolerance *ftol* is in force units, since it is the
> length of the global force vector for all atoms, e.g. a vector of size 3N
> for N atoms."

so `fnorm = sqrt(sum_i sum_a f_ia^2)` and it is EXTENSIVE. That is what makes a
bare number meaningless across cells: the same physical convergence gives
`fnorm = 1e-3` at 5.7e5 atoms and `fnorm = 8e-5` at 4000 atoms.

The choice of 1e-8 on `fnorm/sqrt(3N)` is Erik's (2026-08-03), picked over
`fnorm/N < 1e-8` precisely because `fnorm/N` is not size-independent: it falls
as 1/sqrt(N), so a fixed `fnorm/N` target silently allows the per-atom force to
grow as sqrt(N) -- 4.4e-6 eV/A in the 5.7e5-atom dislocation cells against
3.7e-7 eV/A in the 4000-atom bulk cell, from the same number.

## Suggested wording

New section in `canon/preferences.md`, after "Computation defaults":

    ## Convergence and force-norm reporting

    - **`fnorm` is extensive; never report it bare.** LAMMPS `fnorm` is the
      2-norm (length) of the GLOBAL force vector, a vector of size 3N for N
      atoms: `fnorm = sqrt(sum_i sum_a f_ia^2)`, and `ftol` is compared
      directly against it in force units. A bare fnorm cannot be compared
      between cells of different size.
    - **Always report both normalizations, and name the definition.** Every
      LAMMPS input carries a header block defining fnorm and both
      normalizations; every `thermo_style` in a minimization carries
      `v_fnorm_per_atom` (= fnorm/N) and `v_fnorm_per_dof`
      (= fnorm/sqrt(3N)) alongside the raw `fnorm`; every thread.md,
      run.yaml and chat statement about convergence quotes both. Erik's
      words (2026-08-03): *"ALWAYS state forcenorm/natoms and state that
      you are using this definition."*
    - **Default 0 K stopping criterion: `fnorm/sqrt(3N) < 1e-8 eV/A.**
      `fnorm/sqrt(3N)` is the RMS force per degree of freedom and is the
      only one of the three that is size-independent. Write it into the
      input as

          variable n_atoms      equal count(all)
          variable sqrt_3N      equal sqrt(3.0*v_n_atoms)
          variable FTOL_PER_DOF equal 1.0e-8
          variable ftol_global  equal v_FTOL_PER_DOF*v_sqrt_3N
          minimize 0.0 ${ftol_global} <maxiter> <maxeval>

      so a rendered-per-cell input gets the right value without editing.
      Tighten when the science demands it; loosen only on the record.
    - **Cap `maxiter` at ~15-20x the expected iteration count**, not at
      800000. A minimization that runs into the walltime cap never reaches
      its `write_data` and loses everything; one that hits `maxiter` writes
      the structure and says so in the `Stopping criterion` line.

## Designer review notes

Merged 2026-08-20 as a new preferences.md section "Convergence and force-norm
reporting", placed after "Computation defaults" as proposed. Two designer notes:
(a) the ban on the phrase "per-atom RMS" is stated explicitly, since the incident
was an ambiguity rather than an arithmetic error; (b) the `maxiter` bullet
CONTRADICTS the `800000` iteration cap in "Computation defaults" above it, so the
new text says so in-line rather than leaving two live numbers in one file. A
future designer pass should reconcile them properly.

---
proposal_id: 2026-08-03-1003-output-file-extensions
session_id: 2026-08-03-0905-ni-dislocs-multipot
proposed_at: 2026-08-03T10:03Z
target_file: canon/preferences.md
target_section: new subsection under "Directory naming" -> rename to "Directory and file naming"
priority: routine
status: merged
---

## Proposed rule

Trajectory and snapshot files written by `dump` end `.dump`. Structure files
written by `write_data` end `.lmps`. This applies to structures produced by
external tools too (scalers, converters, builders) whenever the product is a
LAMMPS data file.

## Where it bit

2026-08-03, ni-dislocs-eam-meam-ace. The pilot used `.data` for every
`write_data` output and for the twelve rescaled cells, following the extension
used in ni-a0-cij-eam-meam. Erik: *"please have dumps called .dump and
writeouts *.lmps"*. His own files already follow this -- `EAM-DISLOCS-Ni-Cu`
holds `d90_Ni-Mishin04_x-101_y1-21_z111_25x10x25.lmps` and
`fire_d90_...lmps` -- so `.data` was the pilot importing a convention Erik does
not use.

Worth noting the third case in the same tree: the dcreator-built cells carry
`.fcc`, which is a LAMMPS data file with a misleading extension. Those are
pre-existing inputs and are not renamed; the rule governs what we write.

## Suggested wording

In `canon/preferences.md`, rename the "Directory naming" section to
"Directory and file naming" and append:

    - **Extensions: `.dump` for dumps, `.lmps` for write_data.** Anything
      written by a `dump` command ends `.dump`; anything written by
      `write_data`, or by an external tool whose product is a LAMMPS data
      file, ends `.lmps`. Not `.data`, not `.lammps`, not `.fcc`. Set
      2026-08-03 (Erik: *"please have dumps called .dump and writeouts
      *.lmps"*), matching his own EAM-DISLOCS-Ni-Cu files. Pre-existing
      files keep their names; the rule governs what we write.

## Designer review notes

Merged 2026-08-20; "Directory naming" renamed to "Directory and file naming".
Added a pointer that this supersedes the `.data` spelling still used in the
"Output style" section's parenthetical -- that section was not rewritten, so the
older spelling survives there and should be cleaned up next pass.

---
proposal_id: 2026-08-04-1710-sites-voronoi-metric-volume-is-the-probe-cell
session_id: 2026-08-03-1401-nih-at-dislocs-design
proposed_at: 2026-08-04T17:10Z
target_file: canon/learnings.md
target_section: "Tool behaviour" (mc/sites subsection)
priority: routine
status: merged
---

## Proposed rule

`compute sites/voronoi ... metric volume` returns the Voronoi cell volume of a
**probe particle inserted at the site**, NOT the volume of the interstice
polyhedron. It is nearly constant across an fcc lattice and cannot distinguish
octahedral from tetrahedral sites. Classify on clearance AND coordination
instead.

## Where it bit

2026-08-04, ni-h-at-dislocs-eam-meam / thread 01. The run was designed around
the textbook interstice volumes, `V_oct = a^3/6 = 7.29 A^3` against
`V_tet = a^3/24 = 1.82 A^3`, a factor 4, and the post-processing classifier was
written to split on that. It put **100 % of the probe's sites in the "oct"
class**, which was the first symptom.

Measured over all 953534 sites of the relaxed d90 cell (a = 3.52401 A), the
column spans only **5.35 .. 6.04 A^3** -- a 12 % total range -- and the two
populations OVERLAP: oct mean 5.47 (max 5.92), tet mean 5.79 (min 5.58). That
is the right answer for what the compute actually reports: the Voronoi cell of
a probe at an interstice is bounded by the surrounding host atoms, and in a
close-packed lattice every interstice has a similar amount of room around it.
The interstice polyhedron volume is a different quantity and the compute never
claimed to return it.

What does separate the families, on the same catalogue:

| discriminator | oct | tet | ideal |
|---|---|---|---|
| clearance | 1.725-1.763 A (33.3 %) | 1.500-1.538 A (66.2 %) | a/2 = 1.7620, a*sqrt(3)/4 = 1.5259 |
| coordination (`coord 4 3.0`) | 6 | 16 | integer, cleanest |
| rigid `E_ins` far field | -2.0485 eV | -1.5037 eV | 0.549 eV apart |

Requiring clearance AND coordination to agree reproduces the 1:2 oct:tet ratio
of fcc to 33.31 % : 65.92 % and leaves 0.77 % core-distorted -- which is the
population the project is about.

## Suggested wording

In `canon/learnings.md`, under the `fix mc/sites` / `compute sites/voronoi`
subsection:

    - **`metric volume` is the PROBE's Voronoi cell, not the interstice
      polyhedron.** It measures how much room a particle placed at the site
      would own, bounded by the surrounding host atoms -- not the geometric
      volume of the octahedron or tetrahedron. In fcc it is nearly constant
      (5.35-6.04 A^3 at a = 3.524 A) and the oct and tet populations overlap,
      so it CANNOT classify sites. Do not design a classifier around the
      textbook a^3/6 vs a^3/24 factor 4; that factor is not what this column
      reports. Classify on **clearance AND coordination**, requiring both to
      agree: oct = clearance ~ a/2 and coord 6, tet = clearance ~ a*sqrt(3)/4
      and coord 16, everything else core-distorted. Verified 2026-08-04 on
      953534 sites (ni-h-at-dislocs-eam-meam thread 01); the check that it is
      right is that a perfect-lattice region returns the fcc 1:2 ratio.

## Designer review notes

Merged 2026-08-20. learnings.md had no "Tool behaviour" section, so one was
created under "## Tools" as `### Tool behaviour -- fix mc/sites / compute
sites/voronoi`. Kept the fcc 1:2 ratio as the stated self-check: a rule that
comes with its own falsifier is worth more than one that does not.

---
proposal_id: 2026-08-04-1712-check-the-background-shape-not-the-fit-quality
session_id: 2026-08-03-1401-nih-at-dislocs-design
proposed_at: 2026-08-04T17:12Z
target_file: canon/lessons.md
target_section: new lesson (analysis / post-processing)
priority: routine
status: merged
---

## Proposed rule

Before subtracting a background and calling the remainder a segregation,
binding or excess energy, establish the background's **shape** from the
physics of the cell -- not from which functional form happens to fit. A fit
residual comparable in size to the effect you are about to measure is a failed
model, not a tolerance.

## Where it bit

2026-08-04, ni-h-at-dislocs-eam-meam / thread 01, the 0 K H insertion map on a
d90 edge dislocation (`boundary p p s`, three fixed (111) layers per z face).

`E_ins(z)` in the far field rose monotonically across the 140 A map zone. The
pilot fitted a straight line, got +32.7 meV (oct) / +43.4 meV (tet) with a
residual rms of 1.18 / 1.54 meV, and wrote it up as *"the plastic bending of a
free-standing slab containing one edge dislocation"* -- into thread.md,
project.md and the notebook. Erik: *"WTF! The slab should not be bent! the
boundary conditions (upper and lower boundary fixed in z, x,y periodic) should
not allow any bending! please find out where this 'bending' - if it is real -
is coming from!"*

He was right, and the dump files settled it in three measurements:

- per-atom Ni energy across the same window: `dPE/dz = +3.8e-7 eV/A`, i.e.
  **0.05 meV over 140 A**. A bent beam stores elastic energy growing away from
  the neutral plane. There is none.
- atoms per (111) layer: **4646 above the glide plane against 4600 below**,
  exactly +1.000 %, against `b/lx = 2.4919/250.431 = 0.995 %`. One extra
  atomic plane in the same fixed periodic `lx` -- the extra half-plane. The
  upper half is compressed in x; the lower half is not. A STEP.
- (111) interlayer spacing: 2.03174 A below, 2.03725 A above, each flat to
  1e-5 A, one jump over ~8 A.

The real `E_ins(z)` is an odd, **saturating** function about the glide plane:
`A(1-exp(-d/lambda))` fits with `2A = 39.1 meV`, `lambda = 53.0 A`, residual
rms **0.079 meV** -- fifteen times better than the straight line. Inside the
map zone the profile only reaches ~24 meV of that asymptote, so a straight line
through the still-rising part looks excellent and is still the wrong model.

Two things made the error survive as long as it did:

1. The **flat Ni energy was read as "no strain"**, so a strain step was never
   considered. It is not: elastic energy is quadratic in strain, so a `+-eps`
   step costs the same on both sides; `E_ins` and interlayer spacing are linear
   in strain and do show it. Flat energy and a large strain step are perfectly
   consistent.
2. The **residual was not weighed against the measurement**. 1.2 meV rms and
   2.2 meV maximum sound small, but the quantity being extracted afterwards was
   a 5 meV "bulk-like" criterion. The residual WAS the signal.

The fix was to stop fitting: the background is now the median of the same site
family at the same z, far from the defect, on a 0.5 A z grid. Non-parametric,
so whatever shape the field has is removed exactly. Far-field flatness went
from 1-2 meV of leftover structure to 0.15-0.34 meV; the reach at 2 meV went
from unmeasurable to r = 102 A. The headline trap depth moved by 0.4 meV, so
the science conclusion never depended on it -- but the tail analysis did, and
the wrong mechanism was in three files and a notebook for a day.

## Suggested wording

In `canon/lessons.md`, as a new numbered lesson:

    L<N>. **Subtract the right background SHAPE, and never let a good fit
    stand in for a mechanism.** Before calling anything a segregation,
    binding or excess energy, ask what the cell's boundary conditions and
    defect content *require* the background to look like, and check that
    prediction against the raw geometry -- atoms per layer, layer spacings,
    per-atom energy profiles -- not just against how well a polynomial
    fits. Two specific traps, both hit on 2026-08-04
    (ni-h-at-dislocs-eam-meam thread 01):
    - **A straight line through the inner part of a saturating step fits
      beautifully and means nothing.** The tell is the residual: if the fit
      residual is comparable to the effect you intend to measure next
      (there, 2.2 meV maximum against a 5 meV criterion), the model has
      failed, it is not within tolerance.
    - **A flat per-atom energy does NOT mean zero strain.** Elastic energy
      is quadratic in strain, so a `+-eps` step is invisible in the energy
      while being fully visible in any quantity that is linear in strain
      (interstitial insertion energies, layer spacings, per-atom stress).
      Read the stress or the geometry, not the energy, when asking whether
      a cell is strained.
    Prefer a **non-parametric background** -- the median of the same site
    class at the same coordinate, far from the defect -- whenever the data
    supports one. It assumes no shape and therefore cannot invent one.
    And when the operator says a boundary condition forbids what you just
    reported, believe the boundary condition first and go back to the dump
    files.

## Designer review notes

Merged 2026-08-20 as **lessons.md L39**. Accepted in full. The two sub-traps are
kept as the body rather than compressed, because each is independently
sufficient to cause the error and the second (flat energy does not mean zero
strain) is genuinely counter-intuitive. The closing line -- believe the boundary
condition before your own fit -- is retained verbatim.

---
proposal_id: 2026-08-04-1730-symmetric-colour-scales
session_id: 2026-08-03-1401-nih-at-dislocs-design
proposed_at: 2026-08-04T17:30Z
target_file: canon/preferences.md
target_section: new subsection "Plots and colour scales"
priority: routine
status: merged
---

## Proposed rule

Any map, heat map or particle colouring of a **signed** quantity gets a
**diverging colormap on a symmetric scale centred on zero**. Never a sequential
colormap, never auto-ranged limits. If outliers would flatten the rest, clip
the scale and say so in the title or caption.

## Where it bit

2026-08-04, ni-h-at-dislocs-eam-meam / thread 01, fig 3 (the H segregation map
about a d90 edge dislocation). The pilot used `viridis_r` with
`vmin = -max|E_seg|` and `vmax = max(0, E_seg.max())` -- neither diverging nor
symmetric. Erik: *"I always teach my students that if they show such images,
make sure to use symetric color scales! please do that and try to remember
it."*

The failure mode is specific and bad: an edge dislocation produces a
**dipole** -- tension below the glide plane binds H, compression above repels
it -- and the two lobes are nearly equal and opposite. With an asymmetric
sequential scale, zero lands wherever the data happens to put it, the two lobes
render in unrelated colours, and the field reads as a monopole. The physics that
matters most in the figure is exactly the thing the colour scale destroys.

Two corollaries came out of the same exchange and belong with the rule:

- **State the clip.** Here the core-distorted family reaches +0.66 eV on a
  handful of squeezed sites at the glide plane while everything of interest
  lives inside +-0.15 eV. Letting those set the limits flattens the map;
  clipping silently is worse. Clip at a stated percentile and put the true
  extreme in the title.
- **Downstream viewers do not do this for you.** OVITO's "Adjust range" returns
  `[min, max]`, which for this data is `[-0.146, +0.662]`. Anything that writes
  a file for OVITO/ParaView should print the symmetric range to set by hand.

## Suggested wording

In `canon/preferences.md`, new subsection:

    ## Plots and colour scales

    - **Symmetric colour scales for signed quantities, always.** A map, heat
      map or particle colouring of anything that can be positive or negative
      -- segregation and binding energies, stresses, displacements,
      differences of any kind -- uses a **diverging** colormap
      (`RdBu_r`, `coolwarm`, `bwr`) on a scale **symmetric about zero**
      (`vmin = -v`, `vmax = +v`). Set 2026-08-04 (Erik: *"I always teach my
      students that if they show such images, make sure to use symetric
      color scales!"*). A sequential colormap or auto-ranged limits puts
      zero at an arbitrary place on the bar, and a dipole field -- an edge
      dislocation's tension/compression lobes, for instance -- then reads as
      a monopole.
    - **Sequential colormaps are for quantities with a physical zero** at one
      end (concentration, density, count, |magnitude|). There, zero at the
      bottom of the bar is the honest choice.
    - **Clip loudly, never silently.** When a few outliers would flatten the
      structure, clip at a stated percentile and put both the clip and the
      true extreme in the title. Do not let outliers set the limits, and do
      not hide that you cut them.
    - **Print the range for external viewers.** Any script that writes a file
      for OVITO or ParaView should print the symmetric colour range to enter,
      because "Adjust range" in those tools returns `[min, max]` and will
      reintroduce exactly this problem.

## Designer review notes

Merged 2026-08-20 -- **placement changed.** The proposal asked for a new section
"Plots and colour scales"; preferences.md already has "## Plot defaults", and two
sections about plotting would guarantee that a future session reads one and not
the other. Merged into "Plot defaults" instead. Content accepted in full,
including all three corollaries (sequential-for-physical-zero, clip loudly, print
the range for OVITO/ParaView).

---
proposal_id: 2026-08-04-1850-verify-device-writes-by-listing-not-by-restaging
session_id: 2026-08-03-1401-nih-at-dislocs-design
proposed_at: 2026-08-04T18:50Z
target_file: canon/learnings.md
target_section: "Workflow rules" (device-bridge / mount verification)
priority: routine
status: merged
---

## Proposed rule

Verify a write made through the Cowork device bridge by the **size and
mtime returned by `device_list_dir`**, never by re-staging the file and
reading the staged copy. The staged path is a session-side cache and can
serve a snapshot hours older than the device.

## Where it bit

2026-08-04, ni-h-at-dislocs-eam-meam. `SESSIONS.md` was written through the
bridge and the commit reported success. Re-staging it and grepping showed the
session's own registration entry absent and `last_index_updated` still at its
two-day-old value -- twice. The pilot concluded the file was being reverted by
something on the machine (a git checkout was the hypothesis), wrote a WARNING
to that effect into `SESSIONS.md` itself and into the project's re-entry brief,
and told Erik to look at the repo.

All of it was wrong. `device_list_dir` reported 43716 bytes with a fresh
mtime -- byte-identical to what had been sent -- while the container-side
staged copy was still the 39061-byte version from two days earlier. The write
had landed correctly every time; the read was stale.

The failure mode is nasty because it is silent and it inverts: a stale READ
looks exactly like a failed WRITE, so the instinct is to re-send, and re-sending
appears to fail again. It also cost more than the time: a false claim about the
user's filesystem was written into two files that other sessions will read.

Note this is the same class as the existing cmmg sshfs quirk
`sshfs_default_options_show_stale_views` -- "verify with stat/wc/md5 on the full
path, never with ls" -- one layer further out. Worth cross-referencing the two.

## Suggested wording

In `canon/learnings.md`, under Workflow rules:

    - **Verify bridge writes by the directory listing, not by re-reading
      the staged copy.** After `device_commit_files`, confirm with the
      size and mtime `device_list_dir` reports for the destination path.
      The staged path under the session's uploads directory is a cache
      and may serve a snapshot from a previous stage of the same file --
      so re-staging and reading it back can show old content long after
      a correct write. A stale read is indistinguishable from a failed
      write unless you check the listing, and the natural response
      (re-send, re-read, see the old content again) reinforces the wrong
      conclusion. Same class as the cmmg sshfs quirk
      `sshfs_default_options_show_stale_views`, one layer further out.
      Corollary: never write a claim about the user's filesystem into a
      project or canon file on the strength of a re-read alone.

## Designer review notes

Merged 2026-08-20 into learnings.md "Workflow rules". Accepted in full. The
corollary -- never write a claim about the user's filesystem on the strength of a
re-read alone -- is the part that cost the most and is kept prominent. Note this
was independently re-confirmed by the 2026-08-20 session, which verified every
canon write in this batch by `stat` size/mtime rather than by re-staging.


---

## 2026-08-05-1100 — collapse detectors lag; block averages need a guard band

**Status: merged** — 2026-08-25 designer pass (session 2026-08-25-1105-sim-status):
merged as lessons.md **L43** ("event detectors are lagging indicators;
guard-band the block averages"), wording essentially as proposed.

**From** session 2026-08-05-1009-nih-harvest (pilot, ni-h-phase-diagram-eam-meam).
**Class** analysis / convergence machinery.

Any detector that fires on a *threshold crossing* of a slowly-degrading quantity is a
lagging indicator, and any block-averaged stream derived from the same run therefore has
at least one contaminated window BEFORE the detected event.

Concrete case: thread 03 run 06, mu = -2.02. `find_collapse_step` fires when a_eff falls
1 % below its running maximum; that happened at step 58060, one step past the run's last
2000-step block average at 58000. Without a guard band the task passed every convergence
test and would have published x = 0.9986 +- 0.0036 from a window that had already sagged
from 1.00000 to 0.98846. With the guard band it reads 0.99992 +- 0.00012 and is correctly
flagged crashed — a 4 meV-scale change in a quantity the project quotes to 5 decimals.

**Proposed rule.** When a run-level failure is detected at step S, invalidate every
block-averaged row whose averaging window ENDS within one window-length of S, not merely
those at or after S. Derive the window length from the data (median step spacing), never
hardcode it. The same applies to any post-hoc event detection on a time series
(melting, dislocation escape, box instability), not just to this collapse detector.

**Generalisation worth having in canon:** an event detector's threshold defines when the
event became *undeniable*, not when it began. Anything averaged over a window touching
that boundary is suspect.

---

## 2026-08-05-1105 — a convergence verdict is not a data-quality filter

**Status: merged** — 2026-08-25 designer pass: folded together with inbox
2026-08-24-0930 into ONE learnings.md "Process" rule ("Scalar-reducing
helpers name their convergence filter; a verdict for question A is not a
quality label for question B") — the two proposals are two halves of the
same rule (0930: unconverged points corrupt scalar reducers; 1105: a generic
`converged` filter corrupts fits it was not built for). Both instances kept.

**From** session 2026-08-05-1009-nih-harvest (pilot, ni-h-phase-diagram-eam-meam).
**Class** analysis discipline.

The v3 verdict ladder (crashed / empty / window-too-short / noise-limited / filling /
equilibrated-late / equilibrated) answers one question: *is this point's mean a
converged estimate of an equilibrium composition?* It is routinely reached for as a
generic `converged` boolean and used to filter inputs to unrelated fits.

Concrete case: the Delta_mu dilute-branch fit (thread 03 run 06). Filtering on
`converged` removed every `noise-limited` point — points with a few dozen H in a
10 976-atom cell, which are noisy in x but perfectly good in ln x. That collapsed the
mc/sites leg to 3 points spanning 0.010 eV in mu and drove the shared d ln x/d mu to
57/eV against an ideal Langmuir 38.7/eV. The notebook then described that fitted slope
as "the physically expected one", i.e. reported a failed check as a passed one. Dropping
the filter (keeping only `~crashed`) restored 43/eV and moved Delta_mu by 0.001 eV.

**Proposed rule.** Filter on the failure mode that would actually corrupt the specific
fit, named explicitly at the call site — for a log-x fit that is `crashed`, not
`noise-limited`. A verdict built for question A is not a quality label for question B.
Corollary already in preferences.md (plot defaults) but worth stating for fits too: if a
fitted nuisance parameter disagrees with its physically expected value, say so; do not
present the disagreement as corroboration.

---

## 2026-08-05-1110 — report the interval with NO converged point in it, not the count of points inside

**Status: merged** — 2026-08-25 designer pass: merged into preferences.md
"Plot defaults" as an extension of the sampling-density corollary, wording
essentially as proposed.

**From** session 2026-08-05-1009-nih-harvest (pilot, ni-h-phase-diagram-eam-meam).
**Class** preferences.md, plot defaults — extends the existing "report the sampling
density of any feature you compare" rule.

canon already requires printing how many grid points fall inside a transition. That rule
caught a real artefact on 2026-07-30 but is not sufficient, because converged points
cluster on the phase branches: the Pezold EAM at 600 K has five points inside a naive
0.05 < x < 0.95 window and they are ALL the sub-stoichiometric beta phase, not a
filled-in transition.

**Proposed extension.** For any first-order transition, report the **widest interval in
the order parameter containing no CONVERGED state**. Two reasons it is the right
statistic: a finite cell inside a two-phase region cannot converge to an intermediate
value however long it is sampled, so the emptiness is physics rather than sampling; and
the interval's endpoints ARE the coexisting phase compositions, so the diagnostic and the
binodal are the same measurement. An empty interval of order the grid spacing means no
transition is resolvable at that resolution — which is the honest way to say "the gap has
closed" without claiming a critical point.

In this project it turned four isotherms into a phase diagram with measured tie-lines and
supplied the first Tc bracket the project has had.

---

## 2026-08-05-1115 — check a derived diagnostic column against the columns you already have

**Status: merged** — 2026-08-25 designer pass: merged as style/lammps.md
**1.15** ("Derived diagnostic columns must earn their place"), with the
2026-08-04 `metric volume` cross-reference as proposed.

**From** session 2026-08-05-1009-nih-harvest (pilot, ni-h-phase-diagram-eam-meam).
**Class** style/lammps.md, output design.

The 2026-07-30 wave added `V_oct_ideal` and `V_tet_ideal` to three runs, to help resolve
octahedral-vs-tetrahedral occupancy. They are exactly a^3/6 and a^3/24 recomputed from the
instantaneous a_eff — verified to 1e-5 over all 16 256 rows that carry them. They are pure
functions of a column already being written and carry **zero** occupancy information. Four
runs' worth of extra output, and a later reader would reasonably assume they meant
something.

**Proposed rule.** Before adding a derived per-step or per-block column, state in the
input header what it measures that the existing columns do not, and verify it on the probe
by regressing it against them. If it is a deterministic function of an existing column,
either drop it or label it explicitly as a convenience restatement.

Related: this is the same failure class as the 2026-08-04 `metric volume` gotcha filed
from the dislocation project (proposals-inbox 2026-08-04-1710) — a quantity assumed to be
a structural discriminator that turns out to be geometry. Worth merging the two into one
canon lesson about interstice/volume diagnostics.


---

## 2026-08-05-1210 — L15 extension: a mount can list at the root and HANG deeper

**Status: merged** — 2026-08-25 designer pass: merged into learnings.md
"Cluster discipline" as "Health-check a mount at the depth the work needs",
all four proposed rules kept.

**From** session 2026-08-05-1009-nih-harvest (pilot, ni-h-phase-diagram-eam-meam).
**Class** learnings.md, "Cluster discipline" / L15 (sshfs flakiness).

L15 as written describes a stale sshfs as returning **empty directory listings** while
exact-path reads still work, and prescribes a remount. Today produced a third state that
the rule does not cover and that a remount did **not** fix:

- `ls` of the mount ROOT returned the three thread dirs correctly.
- `ls` at depth 3 (`<thread>/<run>/results/`) returned empty.
- `ls` at depth 4 (`.../results/mu-2.30/`) **blocked** — a retry loop over five directories
  with 2 s sleeps hit a 45 s command timeout without completing one of them.
- `stat` succeeded on every path at every depth (it needs no readdir).
- Exactly one directory resolved, `mu-2.32`, which happened to be enumerated in the first
  second after the folder was reconnected. Every file under it then read normally.
- Erik remounted, and re-added the folder to the session. Both changed which paths
  resolved; neither restored deep enumeration.

**Why it matters beyond the annoyance.** `stat` succeeding at every depth makes the tree
look present. A session that checks a mount with `ls <root>` and `stat` on a couple of
paths will conclude the mount is healthy, then silently mirror a subset — which is what
happened at 10:12: the curated rsync copied five run directories and skipped the sixth
with no error, and the gap was only caught by counting files afterwards.

**Proposed rules.**
1. **Health-check a mount at the depth the work needs**, not at its root — enumerate one
   leaf directory and count entries against expectation. A root listing proves nothing.
2. **Treat a mirror as unverified until the file count is compared** source vs
   destination per run directory. Never infer completeness from rsync exiting 0; a
   readdir that returns empty is not an error to rsync, it is an empty directory.
3. **Do not conclude a remount fixed it.** Re-test at depth. Today's remount changed the
   symptom (empty -> hanging) without fixing it.
4. When deep readdir blocks, stop and hand Erik a copy-paste command for **his own
   shell** — the bridge is the thing that is broken, and his shell talks to sshfs
   directly. Do not burn the session retrying.


---
proposal_id: 2026-08-06-1240-spell-out-abbreviations
session_id: 2026-08-06-1222-hydride-cycle-design
proposed_at: 2026-08-06T12:40Z
target_file: canon/preferences.md
target_section: "Communication style" (new subsection, or fold into an existing prose-style section)
priority: routine
status: merged
---

## Proposed rule

Spell out every abbreviation or acronym at first use in a response
(e.g. "mean-squared displacement (MSD)", "diffusion coefficient of H
as a function of temperature, D_H(T)"). Symbols and shorthand may be
reused afterwards within the same response once defined.

## Where it bit

2026-08-06, hydride-cycle-design brainstorm: the pilot wrote
"D_H(T) MSD arrays" without expansion; Erik: "Please always explain
your abbreviations: no clue what : D_H(T) MSD means?". Not the first
class of jargon-density feedback -- treat as a standing preference,
not a one-off.

## Suggested wording

- **Spell out abbreviations at first use.** Every acronym, symbol or
  shorthand gets its expansion the first time it appears in a
  response ("mean-squared displacement (MSD)"); after that the short
  form is fine. Applies to chat, project files, figure captions and
  data-file headers alike. (Set 2026-08-06, Erik: "Please always
  explain your abbreviations".)

## Designer review notes

Merged 2026-08-20 as a new preferences.md section "## Communication style",
placed immediately before "## Output style" (which is about data files, not
prose, so folding them would have buried it). Erik's quote kept verbatim.


---
proposal_id: 2026-08-06-1710-walltime-before-ranks
session_id: 2026-08-06-1222-hydride-cycle-design
proposed_at: 2026-08-06T17:10Z
target_file: canon/learnings.md
target_section: "Cluster discipline"
priority: routine
status: merged
---

## Proposed rule

When an L26 walltime check shows a task will not fit its --time budget,
the FIRST lever is extending --time toward the partition maximum (96 h
on cmmg; padding is free, unused walltime is not billed) -- NOT adding
ranks. Adding ranks is only justified while atoms/core stays above the
~1000-2000 efficiency floor; below it, extra ranks mostly buy
communication overhead.

## Where it bit

2026-08-06, ni-h-diffusivity hydride MSD arrays (7800-atom cells): probe
rates put the 24e6-step tasks at ~40-47 h against a 48 h request. The
pilot doubled ranks (8 -> 16, i.e. 975 -> 488 atoms/rank) and set 72 h
instead of simply requesting 96 h at 8 ranks. Erik: "that would just
stupidly increase the overhead of communications ... I think max
runtime is 96h." Both facts were already on record (clusters.yaml
max_time 4-00:00:00; learnings atoms-per-core guidance) -- the failure
was not consulting them at decision time.

## Suggested wording

- **Walltime before ranks.** If a task does not fit its --time, extend
  --time first (cmmg max 4-00:00:00; padding free, L26). Add ranks only
  while atoms/core stays above the ~1000-2000 efficiency floor; halving
  atoms/core below that trades wall-clock for communication overhead
  and shared-node footprint. State atoms/core whenever proposing a rank
  change. (2026-08-06, ni-h-diffusivity hydride arrays.)

## Designer review notes

Merged 2026-08-20 into learnings.md "Cluster discipline". Accepted in full. The
self-critical sentence -- both facts were already on record and the failure was
not consulting them -- is retained deliberately: it names the real defect, which
is a lookup discipline failure, not a missing rule.


---
proposal_id: 2026-08-06-2050-zstd-on-thread-close
session_id: 2026-08-06-1222-hydride-cycle-design
proposed_at: 2026-08-06T20:50Z
target_file: canon/learnings.md
target_section: "Workflow rules" (extends L17 / mile-pebble curation)
priority: routine
status: merged
---

## Proposed rule

When a thread (or a finished part of a project) is closed -- i.e. its
files are no longer being actively worked with -- the pilot ensures its
bulky artifacts are zstd-compressed as part of the closure, on both
trees: trajectories/dumps, .data snapshots, restart files, large raw
outputs. Human-readable records (project.md, thread.md, run.yaml, small
.dat tables, plots) stay uncompressed. zstd -19 for archival per L17.

## Where it bit

2026-08-06, hydride-cycle session. Erik, on the trajectory-format
discussion: "when you close one thread/part of our project, i.e. are no
longer directly working with the files, please make sure they are zstd
zipped." Until now compression was only specified for mile-pebble
curation pulls (L17); this extends it to closure hygiene generally.

## Suggested wording

- **Closure implies compression.** Closing a thread includes a zstd
  pass over its bulky artifacts (dumps/trajectories, .data, restart
  files, large raw outputs) on cluster AND Mac; `zstd -19`, `.zst`
  suffix, per L17. Text records and small analysis tables stay plain.
  Compression commands on the cluster side are strict-A (pilot
  prepares, Erik runs). (Erik, 2026-08-06.)

## Designer review notes

Merged 2026-08-20 into learnings.md "Workflow rules", directly under the existing
mile-pebble curation bullet so the two compression rules sit together. Added that
cluster-side compression commands are strict-A, which the proposal implied but
did not state.


---
proposal_id: 2026-08-20-1150-tcc-eperm-vs-stale-sshfs
session_id: 2026-08-20-1145-cluster-status-sweep
proposed_at: 2026-08-20T11:50Z
target_file: canon/learnings.md
target_section: "Cluster discipline" / extends L15 (mount flakiness); also amends canon/session-startup.md step 0(c)
priority: routine
status: merged
---

## Proposed rule

An unreadable cluster mount has TWO distinct causes with OPPOSITE fixes, and
they must be told apart before acting. If a subdirectory `stat`s fine but
reading or descending into it returns `Operation not permitted`, or the
desktop bridge reports a macOS access denial, the sshfs mount is HEALTHY and
the problem is a stale macOS TCC grant -- re-connect the folder in the desktop
app; do NOT remount. Only an empty-or-inconsistent listing with NO permission
error is L15 sshfs staleness, where remounting is the fix.

## Where it bit

2026-08-20, cluster-status-sweep session, on M5. `ls ~/cluster-mounts/cmmg`
returned all 55 top-level entries and `stat Ni-H-DIFFUSIVITY` reported a valid
directory with mtime Aug 6 16:15 -- but every descent returned
`Operation not permitted`, and `device_list_dir` returned:

    macOS denied access to ~/cluster-mounts/cmmg/Ni-H-HYDRIDE-CYCLE-EAM.
    Grant access in System Settings -> Privacy & Security -> Files and Folders,
    or pick the folder via Cowork's Connect Folder dialog (which infers consent).

Both L15 and `canon/session-startup.md` step 0(c) currently say, in effect,
"cluster mount reads as empty => the sshfs mount is down => ask Erik to
remount." Following that here would have been the wrong action: the mount was
serving names correctly the whole time. The actual cause was that the sshfs
volume had been re-mounted since the folder was connected to the session, so
the desktop app's TCC grant pointed at a dead volume id. Erik re-connected
`~/cluster-mounts/cmmg` via "Add folder" and full read access returned
immediately, with no remount and no System Settings change.

Worse, the wrong fix is not neutral: remounting sshfs replaces the volume the
existing grant points at, so a remount performed to "fix" a TCC denial can
invalidate a grant that was still good.

Second observation from the same session, worth recording alongside: after the
re-connect, textbook L15 appeared as well -- `Ni-H-DIFFUSIVITY/.../results`
listed its six temperature dirs by exact path while `ls` of its own parent
returned empty, and a subtree that listed correctly in one call returned empty
in the next. The two failure modes can be live at the same time and must be
diagnosed separately. sacct, pasted by Erik, was again the only trustworthy
account of what had run -- consistent with the 2026-08-07 hydride-cycle
RESTART-BRIEF ("Do NOT trust mount listings").

## Suggested wording

- **Two mount failures, opposite fixes (extends L15).** Before calling a
  cluster mount "down", classify the failure:
  (a) *Permission* -- `stat` succeeds but reads/descents return
  `Operation not permitted`, or the bridge reports a macOS access denial.
  The mount is fine; a macOS TCC grant is stale, usually because the sshfs
  volume was re-mounted after the folder was connected. Fix: re-connect the
  folder in the desktop app ("Add folder" re-issues consent against the
  current volume). Do NOT remount -- remounting can invalidate a good grant.
  (b) *Staleness* -- listings empty or inconsistent between calls with NO
  permission error, exact-path reads often still working. This is L15. Fix:
  remount (`command_example` in `canon/clusters.yaml`).
  Both can be live at once. In either case sacct, run by Erik, is the arbiter
  of what ran; never conclude data loss from an unreliable listing.
  (2026-08-20, cluster-status-sweep.)

- **Escape hatch for mount-heavy sessions.** A harvest or status session that
  must enumerate large cluster trees is better run as Claude Code natively on
  the Mac than as a Cowork session: a process running as the user reads the
  sshfs mount with no desktop-app TCC gate and no bridge re-export in the
  path. Cowork remains right for sessions that mainly read the Mac-side
  project tree, and for unattended/scheduled work. (2026-08-20, Erik's own
  diagnosis, confirmed by this session's blocker.)

## Designer review notes

Merged 2026-08-20 in three places: learnings.md "Cluster discipline" (the
classification and both fixes), canon/session-startup.md step 0(c) (which
previously taught the WRONG action for the permission mode), and ARCHITECTURE.md
17.4's summary of the environment gate. The Claude-Code-locally escape hatch is
kept as an operational note in learnings, not promoted to a rule -- it is a
sensible default for enumeration-heavy sessions, not an obligation.


---
proposal_id: 2026-08-20-1215-doc-check-applies-to-boring-commands
session_id: 2026-08-20-1145-cluster-status-sweep
proposed_at: 2026-08-20T12:15Z
target_file: canon/style/lammps.md
target_section: "1.9 LAMMPS version doc-check" (rewrite) + new lesson L<N> + canon/templates/lint-lammps-input.sh
priority: urgent
status: merged
---

## Proposed rule

The doc-check applies to EVERY command the session writes or edits, not to a
named list of interesting ones. Delete the enumeration in style/lammps.md 1.9.
Additionally, mechanize the cheap part: for the handful of commands whose
keyword vocabulary is small, closed and stable (`group`, `velocity`,
`delete_atoms`, `change_box`, `reset_timestep`), the lint validates the
keyword against a hardcoded list, because these are exactly the commands a
session considers too mundane to look up.

## Where it bit

2026-08-20. `relax-Hbind_Ni-disloc-d90-Pezold-EAM-0K.in` line 193 read
`group HGRP clear`. Job 21638800 aborted on the FIRST site of the loop --
"ERROR: Could not find group clear group ID HGRP (src/group.cpp:137)" -- all
256 ranks exit 1 after 56 s, 2026-08-05T00:29.

Three independent guards existed and none of them applied:

1. **style/lammps.md 1.9 enumerates its own scope out of the way.** It reads
   "for every non-trivial command in the input (`fix`, `compute`,
   `pair_style`, anything new this session)". `group` is not one of the three,
   and to a session writing its Nth `group` line it is not "new". The rule as
   written invites treating structural commands as exempt. `group` is mundane
   right up to the moment you use a keyword it does not have.

2. **The mistake is the exact analogy failure canon already names.**
   learnings.md "Assumption without verification" warns against generalizing
   one command's behaviour to another. `clear` IS a real LAMMPS command (it
   resets the whole system); `velocity` has a `zero` keyword; `group` has
   `delete`. "clear" is a plausible reset verb and the line scans as
   idiomatic. This is L8's failure mode exactly (`pair_style meam/c` from
   stale doc-memory) -- but L8 was CAUGHT, because L8 was about a
   `pair_style` and so fell inside 1.9's list.

3. **lint-lammps-input.sh cannot see it and says so.** Its header lists
   "1.9 LAMMPS version doc-check" under "Does NOT cover (still manual review
   required)". It checks L1/L2/L3/L5/L6/L8-style patterns; it has no concept
   of "is this keyword valid for this command".

So the first thing that could catch the bug was a 256-rank job. The probe
discipline DID work -- the cost was 56 s of a debug-partition probe, not a
production array. That part of canon is fine. What is not fine is that a
one-word lookup was never mandated for the class of command involved.

## CORRECTION 2026-08-20T12:40Z (same session, before merge)

The premise above is WRONG and is left standing as the record. `clear` IS a
valid `group` style in the loaded LAMMPS (`docs.lammps.org/group.html`:
"*delete* or *clear* or *empty* or *region* or *type* or *id* or *molecule* or
*variable* or *include* or *subtract* or *union* or *intersect* or *dynamic*
or *static*"; `delete` removes the group, `clear` un-assigns its atoms). The
error message says so too: the parser accepted `clear` and then failed the
group LOOKUP at `src/group.cpp:137`. The actual defect is that both `clear`
and `delete` require the group to already exist, and line 193 sits at the top
of a `jump SELF` loop, so the first pass has no HGRP.

This was caught by doc-checking the command before writing a lint to enforce a
vocabulary -- i.e. by the very rule this proposal is about, applied to the
proposal itself.

Consequences for what is merged:

1. The **closed-vocabulary lint is WITHDRAWN.** It would not have caught this
   bug, and a keyword allow-list is exactly the kind of hardcoded knowledge
   that goes stale between LAMMPS versions -- the failure mode L8 already
   records. Merging it would have added maintenance burden and false
   confidence.
2. The **1.9 rewrite stands, with the emphasis moved** from "do not invent
   keywords" to "check preconditions, not just spelling". A command can be
   spelled correctly and still be invalid in the state the script has put the
   system in. This is the same class as L37 (`reset_timestep` after a
   gcmc-like fix) and `region ... INF` requiring an existing box.
3. The lesson is merged as **L40**, rewritten accordingly.

## Suggested wording

Replace style/lammps.md 1.9 with:

    ### 1.9 LAMMPS version doc-check

    Every command line the session WRITES OR EDITS is checked against the
    docs of the LAMMPS version currently loaded on the target cluster
    (recorded in clusters.yaml and in the run's submit script) -- not the
    latest stable, and not a remembered version. There is no exempt class
    of command: `group`, `velocity`, `delete_atoms` and `set` have failed
    this way, and their very familiarity is what suppresses the lookup.
    Checking a line you did not touch is not required; checking a line you
    typed is, however boring the command looks.

    Keyword invention is the specific risk. A keyword that reads naturally
    ("clear", "reset", "none") is not evidence that the command accepts it;
    LAMMPS spells the same concept differently per command (`group ...
    delete`, `velocity ... zero`, `unfix`). When the intended keyword is a
    verb you supplied rather than one you read, look it up.

New lesson, target lessons.md:

    ## L<N> -- `group` has no `clear` keyword; the deleter is `delete`

    **Rule:** to drop a group use `group ID delete`. There is no `clear`.
    Note also that `group ID delete` FAILS if the group does not yet exist,
    so a reset-at-top-of-loop idiom needs the group pre-created before the
    label, or -- usually better -- no reset at all: after `delete_atoms
    group HGRP compress no` the group holds no live atoms, so a subsequent
    `group HGRP type 2` re-selects exactly the current members.

    **Where it bit:** 2026-08-05, ni-h-at-dislocs thread 01 run 01, job
    21638800, 256 ranks, dead on the first loop iteration.

Lint change (canon/templates/lint-lammps-input.sh), new closed-vocabulary
check, and move it out of the "does NOT cover" header list:

    # closed-keyword commands -- small, stable vocabularies
    #   group:        delete dynamic static region type id molecule variable
    #                 include subtract union intersect clear<-INVALID
    #   velocity:     create set scale ramp zero
    #   delete_atoms: group region overlap porosity bond
    # Fail on any second token not in the command's list.

## Designer review notes

Merged 2026-08-20 **after being partly refuted by its own rule** -- see the
CORRECTION block above. What merged: the style/lammps.md 1.9 rewrite (exempt-class
list removed; new preconditions clause with L5/L37/L38/L40 as the worked cases),
and lessons.md **L40**, rewritten from "there is no `clear` keyword" to "`clear`
and `delete` both require the group to exist".

What did NOT merge: the closed-vocabulary lint check. It is **WITHDRAWN**. It
would not have caught the bug that motivated it, and a hardcoded keyword
allow-list is precisely the stale-knowledge failure L8 already records -- it would
have added maintenance cost and false confidence. `templates/lint-lammps-input.sh`
is unchanged and 1.9 remains in its "does NOT cover -- manual review required"
list, which is now the honest position rather than a gap.

The episode is the strongest argument for the rule it proposes: the proposal's
own premise survived a confident post-mortem, a message to Erik, and a canon
write, and died the moment someone opened docs.lammps.org/group.html.


---
proposal_id: 2026-08-20-1218-handed-over-jobs-must-be-reconciled-at-startup
session_id: 2026-08-20-1145-cluster-status-sweep
proposed_at: 2026-08-20T12:18Z
target_file: canon/session-startup.md
target_section: new step between 1 and 2 (or an extension of step 1) + ARCHITECTURE.md 17
priority: urgent
status: merged
---

## Proposed rule

A submission handed to Erik but never confirmed is an OPEN LOOP owned by
nobody. At startup, after reading SESSIONS.md, if ANY entry -- active or
recently_closed -- carries a non-empty `in_flight`, the session asks Erik for
one `sacct` line covering that period and reconciles it BEFORE asking for
scope. This is cheap, it is bounded, and it does not depend on someone
happening to resume the right project.

## Where it bit

2026-08-20. Four projects had submissions handed over and never confirmed:
ni-h-phase-diagram run 14 (08-05), ni-h-at-dislocs x2 (08-04), ni-h-diffusivity
(08-06), ni-h-hydride-cycle-eam (08-07). Every one of those entries said some
version of "a resuming session must CHECK THE RUN DIRS FIRST". None was
checked for 13-15 days, because the rule addressed a session that never came:
the three owning entries went stale in `active` and nobody resumed those
scopes.

What the delay actually cost:

- 21638800 FAILED on 08-05T00:29 with a one-line input bug. Fifteen days of a
  dead thread that a two-minute fix would have unblocked.
- Run 14's production arrays were never submitted at all. The probes had
  passed TWICE (08-05 and again 08-06). Fifteen days of no size scan.
- 42 diffusivity tasks and 4 cycle tasks completed on 08-09..08-11 and sat
  unharvested for nine days.

Note the asymmetry: the successes were merely delayed, but the FAILURE was
invisible. A handed-over job that fails produces no signal at all under the
current rules -- no one is told, and the next session is scoped to some other
project. This is the same shape as L15's write-side variant recorded in
clusters.yaml: "a stale read looks like missing data and gets noticed; a
stale write-listing looks like a successful no-op."

## Suggested wording

Insert into canon/session-startup.md after step 1:

    ### 1b. Reconcile open loops (added 2026-08-20)

    Scan every entry in SESSIONS.md -- `active` AND `recently_closed` --
    for a non-empty `in_flight`. If any is found, before asking for mode
    and scope, ask Erik to paste ONE line:

        sacct -X -S <earliest in_flight date> -o JobID%16,JobName%42,Partition,State,Elapsed,End,ExitCode,NNodes

    Reconcile it against those entries and report, in the startup brief:
    what completed, what failed, what was never submitted. Then clear the
    reconciled `in_flight` fields. This runs regardless of what scope Erik
    then picks -- an open loop belongs to the framework, not to a project.

    Rationale: `in_flight` was designed as a note to a resuming session,
    but nothing guarantees that session ever arrives. Between 2026-08-04
    and 2026-08-20, four projects' handed-over submissions went
    unreconciled for 13-15 days, and one silent FAILURE (21638800) went
    unnoticed for the whole period.

Corollary for wrap-up (step 3 of the wrap-up procedure): a session that closes
with a non-empty `in_flight` states so in its `summary:` line, so the open
loop is visible from the dashboard without opening the entry.

## Designer review notes

Merged 2026-08-20 in four places: canon/session-startup.md as new **step 1b**
(with the full incident record, because the cost is the argument), the wrap-up
procedure step 3 (a non-empty `in_flight` at close must appear in `summary:`),
ARCHITECTURE.md 17.4 (ritual summary) and 17.6 (dashboard schema -- `in_flight`
is now documented as the one field whose obligation outlives its entry).
Accepted in full; nothing softened. This is the merge most likely to prevent a
repeat of the fortnight, and it costs one pasted `sacct` line per session.


---
proposal_id: 2026-08-20-1610-ask-what-to-write-out-and-how-often
session_id: 2026-08-20-1145-cluster-status-sweep
proposed_at: 2026-08-20T16:10Z
target_file: canon/preferences.md
target_section: "Output style" -- new FIRST bullet, ahead of the existing minimalism rule; cross-ref from canon/learnings.md "Thread design"
priority: urgent
status: merged
---

## Proposed rule

The set of quantities an input writes out, and the cadence it writes them at,
is a DESIGN-TIME QUESTION FOR ERIK. Ask it explicitly before writing any LAMMPS
input -- which per-atom fields, which global fields, and the frequency of each
-- and record the answer in the thread file. Never infer the output set from a
template, from a sibling run, or from what the planned analysis is assumed to
need. Standing answer as of 2026-08-20: **per-atom stresses and per-atom
energies are wanted** for any run whose science involves defect nucleation,
phase transformation, or coherency stress.

## Where it bit

2026-08-20, harvesting ni-h-hydride-cycle-eam thread 01. The four production
trajectories dump `id type x y z` and nothing else -- 75 GB of coordinates,
201 frames (rate A) and 401 (rate B) per run, at 4 nodes and 14-65 h per task.

Structure analysis (DXA on the Ni sublattice) is still possible from that. What
is NOT possible is any map of the hydride-induced stress field or of the
per-atom energy -- so the questions the project was built to answer, whether
the coherently constrained slab generates defects and where the stress driving
them sits, cannot be answered from the data that exists. Recovering them means
re-running at the same cost. Note the fixed-lateral leg builds ~2 GPa of
in-plane stress during charging: the stress field is not a nice-to-have here,
it is the mechanism.

Erik, 2026-08-20: *"please always ask which properties should be written out
and in what frequency. Here we would need the atomic stresses and Energies."*

**Near-miss with an existing preference, worth stating in canon explicitly.**
`preferences.md` "Output style" records Erik's minimalism rule (L18): *"I like
to have not the full menu of every thing outputted, but just what makes sense
in that context."* That rule is about not dumping the full LAMMPS menu by
reflex. It is NOT licence to omit what the question needs, and a session that
reads it as a general bias toward fewer columns produces exactly this outcome.
The two resolve cleanly once the output set is an ASKED question rather than a
defaulted one: minimalism governs what to leave out of the set Erik specified,
never what to omit from it. A designer merging this should put the two bullets
adjacent so the ordering is visible.

**Cadence belongs in the same conversation**, because content and frequency
trade against each other. Adding six per-atom stress components and a per-atom
energy to this dump roughly 2.5x the frame size: at the 20000-step cadence used
here, 64 MB/frame becomes ~160 MB/frame and the 75 GB set becomes ~190 GB. That
is a sampling decision to take at design time with Erik, not something to
discover at harvest.

## Suggested wording

In `canon/preferences.md`, "Output style", as the FIRST bullet:

    - **Ask what to write out, and how often, before writing the input.**
      The output set is Erik's to specify, not the pilot's to infer. At
      design time, ask explicitly: which PER-ATOM fields (positions, type,
      stresses, energies, coordination, velocities), which GLOBAL/thermo
      fields, and the cadence of each -- then record the answer in
      thread.md alongside the run design. Do not copy an output set from a
      template or a sibling run without re-asking; the sibling answered a
      different question.
      **Standing answer (2026-08-20): per-atom stresses and per-atom
      energies are wanted** whenever the science involves defect
      nucleation, phase transformation, or coherency stress. Erik: *"please
      always ask which properties should be written out and in what
      frequency. Here we would need the atomic stresses and Energies."*
      Decide content and cadence TOGETHER -- per-atom stress plus energy is
      roughly 2.5x the frame size, so the sampling interval usually has to
      move with it.
      The minimalism rule below (L18) applies AFTER this question is
      answered: it governs what to leave out of the set Erik asked for,
      never what to omit from it.

Cross-reference in `canon/learnings.md`, "Thread design": a run design is not
complete until the output set and its cadence are on the record in thread.md.

## Designer review notes

Merged 2026-08-20 into preferences.md "Output style" as the FIRST bullet, ahead of
the minimalism rule, with a second bullet added after the existing ones stating the
ordering explicitly (minimalism governs what to leave out of the set Erik asked for,
never what to omit from it). The proposal's warning that L18 is the rule a session
leans on when it writes `id type x y z` and stops is the reason the ordering is now
written down rather than left implicit. Cross-reference added in learnings.md
"Thread design": a run design is not complete until the output set and its cadence
are on the record in thread.md.


---
proposal_id: 2026-08-20-1650-boundary-conditions-are-eriks-spec-and-deviations-must-be-recorded
session_id: 2026-08-20-1145-cluster-status-sweep
proposed_at: 2026-08-20T16:50Z
target_file: canon/style/lammps.md
target_section: new pre-flight item "1.13 Boundary conditions against the stated design" + new lesson in canon/lessons.md + canon/learnings.md "Thread design"
priority: urgent
status: merged
---

## Proposed rule

Boundary conditions are part of the design Erik states, not an implementation
detail the pilot chooses. Pre-flight checks `boundary` against what he actually
asked for, and any substitution -- a different BC, or a mitigation standing in
for one -- is surfaced to him BEFORE submission and written into thread.md. A
free surface that faces vacuum across a PERIODIC axis is not a free surface: it
is connected to the opposite face.

## Where it bit

2026-08-20, ni-h-hydride-cycle-eam thread 01, on all four production runs
(21774658 / 21774659, 4 nodes each, 14-65 h per task).

Erik asked for a solid wall and NO periodicity along z. The input has
`boundary p p p` -- fully periodic in all three directions, confirmed in the
trajectory headers (`ITEM: BOX BOUNDS pp pp pp`). There is no `fix
wall/reflect` anywhere in either input. What was built instead, commented
"desorption-cleanup":

    region VAC  block INF INF INF INF $(131.0*v_A0) INF units box
    fix EVAP MOBILE evaporate 10000 100 VAC ${RSEED}

That substitution is inadequate on its own numbers:
- the vacuum gap is z = 424.4 .. 477.5 A (15 a0, 53 A); VAC covers only
  z > 463.3 A, the top 14 A of it;
- H at 300 K has a thermal speed of ~27 A/ps, so it crosses the 53 A gap in
  ~2 ps, while `fix evaporate` only looks every 10000 steps = 10 ps. A
  desorbed H typically crosses the periodic boundary about five times before
  the cleanup fix ever runs.

**Measured consequence.** In the fixlat rate-A final configuration, the H
z-distribution is:

    z   0- 20 A :      64 H     <-- bottom of the slab, above the FIXED layers
    z  20-360 A :       0 H
    z 360-380 A :    1814 H
    z 380-400 A :    7038 H
    z 400-420 A :    7141 H
    z 420-440 A :    8923 H

The MC zone is z = 375..456 A. No H was ever inserted below 375 A, and the
360 A of slab between 20 and 360 A contains exactly ZERO H -- so those 64 atoms
did not diffuse down. They wrapped through the periodic z boundary from the
vacuum above the top surface and landed on the bottom face, which is the frozen
`FIXED` group. 64 of 24980 H is 0.26 % at this instant, but the channel is open
for the whole run, it deposits H onto an artificial rigid surface, and it
removes H from the top surface region -- which biases the degassing rate in the
direction that makes degassing look faster than it is.

**The process failure is the bigger one.** The deviation from Erik's stated
design is nowhere on the record: not in the input header, not in thread.md, not
in project.md, not in the RESTART-BRIEF. Nothing says "wall requested,
evaporate used instead" or why. It surfaced only because Erik remembered what
he had asked for, two weeks and four production runs later. A substitution
nobody wrote down cannot be reviewed.

## Suggested wording

New pre-flight item in `canon/style/lammps.md`:

    ### 1.13 Boundary conditions against the stated design

    Read the `boundary` line and check it against what Erik specified for
    this run, not against what the template had. State the BC back to him in
    the design summary, in words ("periodic in x and y, reflecting wall at
    the top, bottom three layers frozen"), before any submit.

    Specific trap -- **vacuum across a periodic axis is not a free surface.**
    If an axis is periodic and the cell contains a vacuum gap, the two faces
    of the slab are connected through it: anything that desorbs from one
    surface re-enters at the other. A cleanup fix is NOT equivalent to a
    wall. If one is used anyway, its region must span the whole gap and its
    interval must be short compared with the transit time of the lightest
    species across that gap (for H at 300 K, ~27 A/ps -- a 50 A gap is
    crossed in ~2 ps).

    Grep:

        grep -nE '^boundary' <input>
        grep -nE 'fix .*(wall/reflect|evaporate)|^region .*(VAC|vac)' <input>

    If `boundary` has `p` on an axis that has vacuum on it, either there is a
    wall or there is a defect. Read which.

New lesson in `canon/lessons.md`:

    **L<N> -- a periodic axis with a vacuum gap connects the two slab faces.**
    Rule, where it bit, and the measured 64-H-at-the-wrong-face evidence
    above.

And in `canon/learnings.md`, "Thread design":

    - **A deviation from Erik's stated design is a reportable event.** If the
      implementation cannot do what he asked, or the pilot judges something
      else better, that is raised BEFORE submission and recorded in
      thread.md with the reason. Silently substituting a mitigation for a
      specified boundary condition, geometry or constraint puts an unreviewed
      change into production, and the record then shows only the substitute.

## Designer review notes

Merged 2026-08-20 in three places, accepted in full: style/lammps.md new **1.13**
(read `boundary` against the stated design; vacuum across a periodic axis is not a
free surface; the two greps), lessons.md new **L41** carrying the measured
z-histogram, and learnings.md "Thread design" for the process half -- a deviation
from what Erik specified is raised BEFORE submission and recorded in thread.md.

The two halves were kept separate on purpose. The physics error is recoverable by
re-running; the process error is what let it survive two weeks and four production
runs, and it would have hidden any other substitution equally well. The 64-H
z-histogram is kept verbatim in L41 because the ZERO between 20 and 360 A is the
whole argument -- transport leaves a trail, teleportation does not -- and a future
session reading a summary without it could reasonably dismiss 0.26 % as noise.


---
proposal_id: 2026-08-20-1705-per-atom-stress-needs-averaging-or-a-quench
session_id: 2026-08-20-1145-cluster-status-sweep
proposed_at: 2026-08-20T17:05Z
target_file: canon/style/lammps.md
target_section: new "1.14 Per-atom stress and energy at finite T" + cross-ref from canon/preferences.md "Output style" (extends proposal 2026-08-20-1610)
priority: routine
status: merged
---

## Proposed rule

An instantaneous per-atom stress or energy at finite temperature is mostly
thermal noise and is not a usable field. Whenever per-atom stresses or energies
are written for analysis, the input specifies HOW the noise is removed, by one
of two routes, and says which in thread.md:

1. **Time-averaging, inline.** `compute stress/atom` / `compute pe/atom` fed
   through `fix ave/atom`, averaged over a window long compared with the
   phonon period (~0.1-1 ps) and short compared with the evolution of the
   field being measured. Cheap, runs during production, keeps the finite-T
   ensemble.
2. **Quench, on snapshots.** Relax a saved configuration with
   `min_style quickmin` (or FIRE) for a short run, then compute the per-atom
   quantities on the quenched inherent structure. Much cleaner field, but it
   is a separate post-processing step and it relaxes local elastic strain, so
   it answers a slightly different question.

Doing both is usually right: inline time-averaged fields at the dump cadence
so something usable always exists, plus enough full snapshots retained that a
quench-based pass is possible afterwards.

Erik, 2026-08-20: *"for the stress calculation we would either need averaging
or a short minimization like quickmin to remove the noise."*

## Where it bit

Raised pre-emptively, 2026-08-20, while writing up ni-h-hydride-cycle-eam
thread 01. That project wrote no per-atom fields at all (proposal
2026-08-20-1610), so the point had not yet had a chance to bite -- but the
next runs will write them, and writing them raw would produce a 190 GB dataset
whose stress field is unusable. The signal being chased there is a coherency
stress of order 2 GPa and local defect fields well below that, against
instantaneous per-atom virial fluctuations of comparable magnitude at 300 K.

## Two traps to record with the rule

- **`compute stress/atom` returns stress x volume, not stress.** The output is
  a virial in pressure-volume units and must be divided by a per-atom volume
  to become a stress. Use `compute voronoi/atom` for that volume -- this
  project already carries a Voronoi computation for the site catalogue, so
  the machinery is present. Quoting `compute stress/atom` output directly in
  GPa is wrong by a factor of the atomic volume.
- **A quench must not relax the box.** `min_style quickmin` and FIRE cannot be
  combined with `fix box/relax` (L12), and for a stress map you do not want
  the box relaxed anyway -- relaxing it would discard exactly the coherency
  stress being measured. Keep the cell fixed and quench the positions only.

## Suggested wording

New pre-flight item in `canon/style/lammps.md`:

    ### 1.14 Per-atom stress and energy at finite T

    A single-snapshot per-atom stress or energy at finite temperature is
    dominated by thermal fluctuation and is not a field. If the input writes
    per-atom stresses or energies for analysis, it must also say how the
    noise is handled: inline time-averaging (`fix ave/atom` over ~0.1-1 ps
    or longer), or a short `quickmin`/FIRE quench of saved snapshots at
    FIXED box (L12 forbids pairing those minimizers with `fix box/relax`,
    and a stress map wants the box held anyway). Record which in thread.md.

    `compute stress/atom` returns stress x volume. Divide by a per-atom
    volume (`compute voronoi/atom`) before calling the result a stress.

## Designer review notes

Merged 2026-08-20 as style/lammps.md **1.14**, accepted in full including both
traps. Placed immediately after 1.13 so the boundary-condition and
per-atom-field checks sit together -- they are the two questions that decide
whether a production run's output can answer its own question.

The `compute stress/atom` returns stress x volume trap is the one most likely to
bite silently: it produces a plausible-looking number in the wrong units rather
than an error, which is the same fail-late shape as L38. The L12 cross-reference
(quickmin/FIRE cannot take `fix box/relax`) resolves the obvious way here, since a
stress map wants the box held anyway.

---

## 2026-08-24-0930 — a bracket-interpolation helper must filter on `converged`

**Status: merged** — 2026-08-25 designer pass: merged into learnings.md
"Process" jointly with 2026-08-05-1105 (see that entry's note); the
allow_unconverged escape hatch and the "diagnostic/plot helpers stay
unrestricted" clause both kept. NOTE: the same designer pass initially
declared this proposal missing — the pilot's status line "- status: pending"
below (non-schema format) is invisible to a `^proposal_id:` grep; see the
new Conventions bullet. mu_at_half itself is NOT changed here: the code fix
belongs to whoever next holds the ni-h-phase-diagram scope.

- filed_by: session 2026-08-24-0753-status-readout (pilot)
- status: merged   # 2026-08-25 designer pass
- class: analysis discipline / verdict-ladder enforcement
- found_in: ni-h-phase-diagram-eam-meam, thread 03 run 14 analysis

**The instance.** `nih_loaders.mu_at_half(iso)` picks the highest-mu point with
x < 0.5 and the lowest-mu point with x >= 0.5 and interpolates linearly. It does
not look at the `converged` column that the same module computes. At EAM 300 K
the low bracket point is run 09's mu = -2.275, which the verdict ladder flags
`filling` (x = 0.19 +- 0.34, drift 0.79 across the averaging window). The
project's headline mu(x=0.5) = -2.2731 +- 0.0025 eV is therefore interpolated
through a point that is a snapshot of an unfinished transition, and the
interpolation weight is dominated by it. Converged-only bracketing gives
-2.2750 +- 0.0050 eV. Impact here is 1.9 meV; the point is not the size of the
error.

**The class, which is what should be fixed.** This project BUILT a verdict
ladder precisely so that unconverged points could be identified, drew them as
open markers so they are visible in every figure, and then withdrew a headline
claim on 2026-08-05 because "the sigma_x peak tracks whichever point is mid-fill
at the end of the run". Three months later a different helper made the same
mistake in a different function, because the ladder is advisory: it labels
points, and each caller decides whether to care. A label that every caller must
remember to honour will eventually not be honoured.

**Proposed rule.** Any helper that REDUCES an isotherm to a scalar — an
interpolated crossing, a fitted slope, a plateau value, a coexistence mu — must
either (a) restrict to converged points, or (b) take an explicit
`allow_unconverged=True` and put the fact in the returned object so it reaches
the figure caption and the .dat header. Diagnostic and plotting helpers stay
unrestricted: the open markers are the whole point of drawing them.

Belongs in canon/style/ as an analysis-code rule, with a lesson entry pointing
at this instance and at the 2026-08-05 sigma_x withdrawal as the same class.
The one-line change to `mu_at_half` is NOT the fix; the fix is the rule.

## 2026-08-24-1558-a-cluster-side-fix-does-not-reach-the-backed-up-copy

**Status: merged** — 2026-08-25 designer pass: merged into learnings.md
"Process" as "A fix applied on one side lands on both sides, in the same
turn", including the proposed resume-after-FAILURE `cmp` clause. The
`cmp`-based checker tool is NOT built (rule first, per the proposal).

**Filed by:** session 2026-08-24-1531-nih-at-dislocs-status (pilot).
**Status:** merged (2026-08-25 designer pass).

**The instance.** `ni-h-at-dislocs-eam-meam` thread 01 run 01. Job 21638800
FAILED on 2026-08-05 on `group HGRP clear` at the top of a `jump SELF` loop.
Erik applied the fix ON THE CLUSTER and re-ran the probe successfully as
22344727 on 08-20. On 2026-08-24 the MAC-side copy of
`relax-Hbind_Ni-disloc-d90-Pezold-EAM-0K.in` was found still carrying the
broken line. For nineteen days the only BACKED-UP version of that input was
the one that cannot run.

**The class.** ARCHITECTURE's survivability rule says the project's definition
tier (scripts, notes, decisions) lives on the backed-up Mac and the cluster is
a compute layer the project temporarily uses. But an edit made on the cluster
-- which is the natural place to fix a job that just died -- flows the WRONG
WAY across that boundary, and nothing checks. The failure is silent: both
copies exist, both are plausible, and the stale one is the one that survives a
cluster wipe. It would have been found the hard way when someone re-staged the
Mac copy over the working cluster copy and re-ran 21638800's failure.

Note the asymmetry that makes this worth a rule rather than a reminder: a
missing file is noticed immediately; a file that exists in two versions and
differs by one line is noticed only when it runs.

**Proposed rule (wording for the designer to sharpen).** When a run directory's
input, submit script or helper is edited on the cluster, the same session
mirrors it back to the Mac before the thread's entry is updated -- and a
session resuming a thread whose last event was a FAILURE `cmp`s the Mac and
cluster copies of that run's inputs before proposing anything. The second half
is the cheap one and catches the case where the fixing session never came back.

**Possible mechanism, if a tool is wanted:** a `cmp`-based check over a run
dir's `*.in` / `*.slurm` / `*.py` on both sides, reporting only differences.
Not written -- proposing the rule first.

**Where the instance is written up:** SESSIONS.md, session
2026-08-24-1531-nih-at-dislocs-status, `production_setup` (a).

---
proposal_id: 2026-08-25-1200-mount-background-traversers
session_id: 2026-08-25-1105-sim-status
proposed_at: 2026-08-25T12:00Z
target_file: canon/learnings.md
target_section: "Cluster discipline" (extends the two-mount-failures / L15 entry)
priority: routine
status: merged                          # 2026-08-25 designer pass
---

## Proposed rule

Keep background traversers off the sshfs mounts: exclude
`~/cluster-mounts` from Time Machine (`sudo tmutil addexclusion -p`) and
from any antivirus scanner, and treat the Cowork desktop VM as a
persistent holder of open handles on every connected mount subtree --
disconnect the folder from the session (or end the session) BEFORE
unmounting. An "empty" mount whose sshfs process and ssh master are
still alive is the L15 stale view, not an unmount and not data loss.

## Where it bit

2026-08-25 ~12:59-13:14 CEST, M5. Erik saw the cmmg mount "disappear"
while a pilot session was harvesting run 01 of ni-h-at-dislocs-eam-meam
over the device bridge. A capture script run at 13:14:25 shows the mount
was NOT down: the macfuse mount-table entry, the sshfs process (up since
12:01:48), and the ControlMaster were all alive, and a real remote
command ran in 5.9 s. Concurrent load, all in the same window: (a) pid
3762 (`com.apple...`, 9-char-truncated; consistent with
com.apple.Virtualization.VirtualMachine = the Cowork workspace VM) held
~70 open handles across the FUSE volume -- the mount root, every
top-level directory the session had listed, and the exact eight files it
had staged minutes earlier; (b) Time Machine began a backup to TM062026
at 12:59 and failed opening `/Users/<MAC_USER>/cluster-mounts/cmmg`
(logged 12:59:03); (c) the wsavd scanner walked the TM copy of
cluster-mounts at 13:02. Open handles from (a) also explain the
historical umount "Resource busy" -> force-unmount -> stale-sshfs ->
error 35 chain.

## Suggested wording

- **Background traversers on the mount (extends "Two mount failures").**
  Time Machine and AV scanners walk `~/cluster-mounts` unless excluded;
  exclude the tree once per machine (`sudo tmutil addexclusion -p
  ~/cluster-mounts`, plus the scanner's own exclusion list). The Cowork
  desktop VM keeps open handles on every file and directory a session
  has listed or staged under a connected mount subtree for the life of
  the session; those handles pin the volume, so disconnect the folder or
  end the session before unmounting, and read a failed `umount`
  ("Resource busy") as "a session still holds it", not as a broken
  mount. Diagnostic: mount-table entry + live sshfs process + live
  master but empty listings = L15 stale view; verify with an exact-path
  `stat` before concluding anything is gone.

## Designer review notes

Merged 2026-08-25T~12:45Z by session 2026-08-25-1105-sim-status
(designer+pilot, lock held for this pass; Erik's explicit request).
Landed in learnings.md "Cluster discipline" as "Background traversers on
the mount", directly after the two-mount-failures entry. Wording updated
beyond the draft with evidence gathered after filing: pid confirmed as
com.apple.Virtualization.VirtualMachine; the ls-empty / exact-path-stat-
succeeds discriminant (verified live on M5); the System Settings GUI route
for the Time Machine exclusion; "quit the app" as the practical handle
release, since the folder-removal UI does not exist yet.


---
proposal_id: 2026-08-25-1410-macfuse53-readdir-root-cause
session_id: 2026-08-25-1105-sim-status
proposed_at: 2026-08-25T14:10Z
target_file: canon/learnings.md
target_section: "Cluster discipline" (amends the two 2026-08-25 mount entries merged earlier today)
priority: routine
status: merged                          # 2026-08-25 designer pass (2026-08-25-1536)
---

## Proposed rule

The "empty listings while exact-path stat works" mount state (L15 and the
2026-08-25 entries) has an identified upstream root cause: macFUSE 5.3.x
re-issues readdir with a non-zero offset on an SFTP directory handle sshfs
has already exhausted; sshfs answers empty and caches it (upstream fix
libfuse/sshfs PR #379, open as of 2026-08-25). Separately, macFUSE >= 5.3 no
longer daemonizes sshfs -- a mount command that seems to hang has usually
SUCCEEDED, with sshfs sitting in that terminal's foreground, where a stray
ctrl-C/ctrl-Z or a closed tab kills or freezes the mount. Mitigations, in
order: `-o dir_cache=no` (stops caching the poisoned listing); downgrade
macFUSE to 5.2.0; run sshfs with `-f`, backgrounded and disowned, and poll
the mount table for success. Re-test and drop the workarounds when a fixed
sshfs release lands.

## Where it bit

All of the 2026-08 M5 mount incidents. Diagnosed 2026-08-25 from two
capture-script runs: 13:14 (mount + sshfs + master alive, ls empty, stat
fine, ~70 Cowork-VM handles) and 13:49 (recurrence 7 min after a fresh
remount with Time Machine already excluded -- ruling out the traversers as
root cause; they only multiply the re-issued readdirs). Both captures show
sshfs with the SHELL as parent in state S+, i.e. not daemonized, which
retroactively explains the original "mount command hangs" report that opened
the investigation.

## Suggested wording

Designer's choice: either amend the two existing 2026-08-25 bullets in
place (append the root cause + the daemonization note) or add one compact
"root cause" bullet cross-referencing them. Cite libfuse/sshfs PR #379 and
the macFUSE 5.3.3 release notes.

## Designer review notes

Merged 2026-08-25 by session 2026-08-25-1536-nimelt-probe-fix: added as its
own "Root cause of the empty-listing states" bullet in learnings.md
"Cluster discipline", cross-referencing the two existing 2026-08-25 bullets
rather than amending them in place (each records a distinct diagnostic).
Kept: PR #379 citation, the daemonization note with the S+ evidence, the
traversers-as-amplifier finding, and the mitigation order.

---
proposal_id: 2026-08-25-1545-no-dollar-substitution-inside-formulas
session_id: 2026-08-25-1536-nimelt-probe-fix
proposed_at: 2026-08-25T15:45Z
target_file: canon/style/lammps.md
target_section: new numbered rule near 1.x (variables), plus one grep in the pre-handover style walk
priority: routine
status: merged                          # 2026-08-25 designer pass (same session, after promotion)
---

## Proposed rule

Never place a `${name}` substitution inside a `$(...)` immediate formula,
and never rely on `${name}` substitution inside a double-quoted string.
Inside any equal-style formula (immediate `$(...)` or `variable ... equal`),
reference variables ONLY as `v_name` (an index-style variable from `-var`
works if its string is numeric). The LAMMPS variable doc names the quoted
case verbatim: "it is a mistake to enclose a variable formula in double
quotes if it contains variables preceded by $ signs ... the quotes prevent
variable substitution."

MECHANICAL CHECK, to join the existing style walk (us-ascii, L1, L3,
every-${VAR}-passed): both greps must return nothing on every .in before
hand-over:

    grep -nE '\$\([^)]*\$\{' *.in          # ${} nested inside $(...)
    grep -nE '"[^"]*\$\{[^"]*\$\([^)]*\)'  *.in   # quoted strings mixing ${} and $()  -- inspect hits

The first is decidable and should be a hard gate; the second flags quoted
print strings where parse-time substitution is suppressed and everything
must survive print's own late evaluation.

## Where it bit

Job 22719499 (nimelt-t01-prepare-probe, 2026-08-24, ni-melting-point-eam
thread 01): probe FAILED after 18 s at the first `print` after stage-1 NPT,
"ERROR: Invalid syntax in variable formula (src/variable.cpp:2619)", from
`$(ly/${L})` inside a quoted print string. Five instances existed across
the two thread-01 inputs (prepare lines 61, 94; measure lines 58, 61 twice).
The 08-24 style walk verified every ${VAR} was DEFINED (passed by every
submit script) but had no check on WHERE a ${} may appear -- the definition
check passes while the syntax class fails. Sat undiagnosed ~23 h as the
open loop flagged by 2026-08-25-1105-sim-status. Fixed 2026-08-25 by
replacing with `v_L` (doc-verified against docs.lammps.org/variable.html);
the probe otherwise ran clean.

## Suggested wording

Designer's choice; suggest folding into style/lammps.md next to the
existing runtime-quantities rule ("these need $(...) not ${...}"), which
states when to use each mechanism but not that they must never nest. The
probe did its job -- this rule is about catching the class before it costs
a submission and a day of latency.

## Designer review notes

Merged 2026-08-25 by session 2026-08-25-1536-nimelt-probe-fix (the
proposing session, promoted to designer+pilot by Erik before the merge):
style/lammps.md 1.16 with both greps, lessons.md L44, and the hard-gate
grep added to templates/lint-lammps-input.sh (verified: fires on a
reconstruction of the 22719499 bug, passes the fixed inputs). The same
designer window also added L45 / style 1.17 / a second lint gate for the
dangling-thermo-reference class found by probe attempt 2 -- filed directly
under the held lock, no inbox round-trip.

---
proposal_id: 2026-08-25-1740-barostat-dilate-defaults-to-all
session_id: 2026-08-25-1536-nimelt-probe-fix
proposed_at: 2026-08-25T17:40Z
target_file: canon/style/lammps.md
target_section: new numbered rule (barostats + frozen groups), near 1.13 boundary-conditions
priority: routine
status: merged                          # 2026-08-26 by session 2026-08-26-1305
---

## Proposed rule

A barostat's box remap applies to ALL atoms by default (`dilate all` in
fix nph/npt and friends). Any group meant to stay "held fixed" in a
barostatted run is silently strained with the box unless the remap is
restricted (`dilate <group>` on the mobile group, fix defined on that
group). Corollary for probes: a "held fixed" claim is verified
QUANTITATIVELY from the probe snapshot -- layer spacing against a0/2 (or
displacement against the reference), never by eyeballing a picture or an
fcc percentage alone.

## Where it bit

2026-08-25, ni-melting-point-eam thread 01, prepare probe attempt 2: all
mechanical gates passed (ALL PHASES COMPLETE, clean .err, hot half molten)
but the frozen cold half came out at 8% uniaxial strain -- x layer spacing
1.94 A vs a0/2 = 1.794 A, ratio exactly the box expansion. velocity-zero +
setforce-zero looked like "fixed" but the nph remap moved the atoms
affinely anyway. thread.md had flagged this exact risk as OPEN; the
layer-spacing histogram settled it. Fixed with
`fix MELT HOT nph x ... dilate HOT` (doc-checked: the dilate <group>
example in fix_nh.html is precisely a solid substrate under a barostatted
fluid).

## Suggested wording

Designer's choice. The rule has two halves -- the LAMMPS semantics (dilate
defaults to all) and the probe discipline (quantify "fixed") -- and they
may land in different files (style/lammps.md vs learnings.md "Workflow
rules"). Not mechanically lintable: needs the design intent.

## Designer review notes

MERGED 2026-08-26 (session 2026-08-26-1305-notify-email-body), SPLIT as the
proposal invited. Both halves landed in style/lammps.md as ONE new rule 1.18
("A barostat remaps EVERY atom by default"), numbered 1.18 but placed
physically right after 1.13 so it sits with its topical neighbours -- the file
is already non-monotonic (1.11, 1.10, 1.9) and stable numbers matter more than
order, because other canon files cite them. The LAMMPS semantics half is the
rule body, with the `dilate HOT` form and the fix_nh.html substrate example;
the "quantify held-fixed" half is its corollary, sharpened with WHY the obvious
check fails -- an fcc percentage is invariant under uniform strain and reports
a perfect crystal at 8 % misfit. The probe-discipline generalisation of that
corollary lives in learnings.md "A gate is a mechanism, not a sign and not a
marker", merged in the same pass from 2026-08-25-2130, and the two cross-
reference each other. Not lintable, as the proposal said: `dilate all` is the
DEFAULT, so there is no token to grep for -- absence of `dilate` is the smell,
but it is only a smell (plenty of correct barostats have no held group at
all), and a linter that cries on every npt would be turned off within a week.

---
proposal_id: 2026-08-25-2010-empty-listing-mitigations-superseded
session_id: 2026-08-25-1105-sim-status
proposed_at: 2026-08-25T20:10Z
target_file: canon/learnings.md
target_section: "Cluster discipline" — amends the just-merged "Root cause of the empty-listing states" bullet
priority: urgent
status: merged                          # 2026-08-26 by session 2026-08-26-1305
---

## Proposed rule

Replace the mitigation list in the "Root cause of the empty-listing states"
bullet (merged 2026-08-25 by 1536-nimelt-probe-fix from proposal
2026-08-25-1410) with the evening's measured state: the bug is NOT
5.3-specific and downgrading macFUSE does NOT fix it; the working fix is
sshfs built from the PR #379 snapshot-readdir branch.

## Where it bit

Same day, hours apart. After the morning proposal was written: the
empty-listing state reproduced TWICE on macFUSE 5.2.0 (4-7 min after fresh
mounts), and `-o dir_cache=no` did not help (`ls -la` exits 0 with correct
`.` metadata and zero entries -- the empty answer is generated fresh, not
cached by sshfs). A parallel session then read the merged morning-state
bullet and prescribed "remount, downgrade to 5.2.0 if it keeps biting" --
both already done and already shown insufficient. Stale canon actively
misled a live session within hours of merging.

## Suggested wording

Amend the bullet to state: (1) reproduced on macFUSE 5.2.0; version
downgrades are not a fix (the 5.2 downgrade only restored sshfs
daemonization). (2) Working fix, deployed on M5 2026-08-25: sshfs built
from libfuse/sshfs PR #379 (snapshot readdir) via
`DEVEL/build-sshfs-pr379.sh`, installed as /opt/homebrew/bin/sshfs-pr379;
`dotfiles/shell/cluster-mounts.zsh` prefers it and falls back to stock
sshfs on machines without it. Watch upstream: return to stock when a
release contains the fix. (3) Second flavor, stale FILE CONTENT (changed
files serving old pages): fixed by `-o auto_cache` in the mount options
(invalidate on mtime change; attr_timeout=2 bounds the staleness).
(4) Diagnostic: mount-table output shows only kernel flags and can never
confirm mount options like auto_cache -- read `pgrep -fl sshfs`.

## Designer review notes

MERGED 2026-08-26 (session 2026-08-26-1305-notify-email-body), in full and
as an AMENDMENT rather than a rewrite: the original bullet's diagnosis
(macFUSE re-reads an exhausted SFTP dir handle) survives, its mitigation
list is replaced by the measured evening state. All four points landed --
not 5.3-specific, downgrade is not a fix, dir_cache=no is a softener not a
cure, PR #379 build is the fix -- plus the auto_cache content flavor and
the `pgrep -fl sshfs` diagnostic. Corroborated at merge time by Erik:
mounts have run without problems since the PR #379 build was deployed on
M5, which is the confirming evidence the proposal could not yet have. The
"stale canon misled a live session" story is kept in the bullet's
provenance line, because that is the reusable half.

## 2026-08-25-2015-wall-fixes-need-nonperiodic-dims

- filed_by: 2026-08-25-1725-nih-two-project-status (pilot)
- status: merged        # 2026-08-26, session 2026-08-26-1305-notify-email-body
- designer_review: |
    MERGED IN FULL, both halves. Rule -> style/lammps.md 1.13, appended to the
    existing L41 vacuum-gap paragraph rather than given its own number: the two
    belong together, because the wall is what the vacuum-gap rule tells you to
    add and the boundary change is what this one says must ride along in the
    SAME edit. Lint gate -> templates/lint-lammps-input.sh, implemented as
    proposed (parse the last `boundary` line, map each wall face keyword onto
    its axis, fail on p). VERIFIED BOTH DIRECTIONS: fires on a reconstruction
    of the real bug (p p p + wall zhi), and passes the two REAL fixed inputs
    (threads 03/04, `boundary p p f` + `fix WALL MOBILE wall/reflect zhi`), a
    no-wall input, and an x-axis wall on a non-periodic x. Merged first of the
    three because it is the only mechanically lintable one.
- target: canon/style/lammps.md 1.13 (+ lint-lammps-input.sh gate)
- observation: |
    fix wall/reflect (and the fix wall/* family) REFUSES to act in a
    periodic dimension: "ERROR: Cannot use fix wall/reflect in periodic
    dimension z (src/fix_wall_reflect.cpp:110)". Killed both tasks of
    probe 22728943 (ni-h-hydride-cycle-eam thread 03) and both of the
    thread-04 probe on 2026-08-25: the input added a reflecting wall at
    134 a0 to fix the L41 vacuum-gap re-entry while keeping
    `boundary p p p`. The command was doc-checked for SYNTAX but not
    for this precondition -- exactly the class style 1.9 warns about.
    Fix applied: boundary p p f, which is also the honest geometry
    (the slab's faces become genuinely disconnected).
- proposed_rule: |
    style/lammps.md 1.13 addition: "A wall is not a patch on a
    periodic axis -- it REQUIRES the axis non-periodic. If a fix
    wall/* acts in dimension D, the boundary in D must be f (or s/m).
    Conversely, when adding a wall to close the L41 vacuum-gap hole,
    change the boundary in the same edit; p-plus-wall is rejected by
    LAMMPS at init."
- lint_gate: |
    Mechanizable: parse the `boundary` line; for every `fix .* wall/`
    line, extract the face keywords (xlo/xhi/ylo/yhi/zlo/zhi); fail if
    the corresponding boundary char is p.

---
proposal_id: 2026-08-25-2130-desired-sign-is-not-a-passed-gate
session_id: 2026-08-25-1536-nimelt-probe-fix
proposed_at: 2026-08-25T21:30Z
target_file: canon/learnings.md
target_section: Workflow rules (probe discipline)
priority: routine
status: merged                          # 2026-08-26 by session 2026-08-26-1305
---

## Proposed rule

Two halves. (1) A probe observable moving in the EXPECTED direction is not
a passed gate until the MECHANISM is checked: ask where the signal comes
from spatially and whether the responsible dynamics is the one the
production run will have. (2) Any stage that equilibrates a liquid against
a HELD (rigid) crystal template must budget for epitaxial/confinement
freezing: a commensurate film < ~2-3 nm between template faces (PBC counts
as a second face) can crystallize ABOVE bulk Tm; check film thickness
against the ~1 nm/wall ordering reach and keep such stages to the few-ps
minimum, or thermalize the template instead of freezing it.

## Where it bit

2026-08-25, ni-melting-point-eam thread 01. The measure probe at 1450 K
showed fcc rising in the liquid region -- the DESIRED "freezing below Tm"
sign -- and was passed. The rise was actually epitaxial freezing of the
15 A liquid film against the rigid guarded-settle template, and the full
7-rung production ladder (1300-1600 K) then ran on cells whose liquid had
fully crystallized before measurement start (transverse-order minimum
0.38-0.54 in every final config, no melting even at 1600 K). ~3.5 h of
cluster time measured crystal annealing. The spatial check that would have
caught it at probe time -- WHERE is fcc growing, and does it grow with the
template held? -- takes one awk pass over the profile.

## Suggested wording

Designer's choice; the confinement-freezing half may fit better in
style/lammps.md near the boundary-conditions rule 1.13. Not lintable;
it is a physics-review step for the probe-gate checklist.

## Designer review notes

MERGED 2026-08-26 (session 2026-08-26-1305-notify-email-body), SPLIT across
two files exactly as the proposal suggested it might need to be.
(1) The probe-discipline half -> learnings.md "Workflow rules", as
    "A gate is a mechanism, not a sign and not a marker", with two corollaries:
    a held quantity is verified against a number not a picture (pointing at
    style 1.18, merged in the same pass), and green mechanical gates say the
    job RAN, not that it measured anything -- every probe needs at least one
    gate a physically wrong run would fail. That last clause is this session's
    addition, not the proposal's, and it is the part that generalises beyond
    melting points.
(2) The confinement-freezing half -> style/lammps.md 1.19, "A held rigid
    crystal template ORDERS the liquid beside it", keeping the ~1 nm per-wall
    reach, the <2-3 nm film threshold, the PBC-counts-as-the-second-face
    clause, and the three escapes (check the thickness, keep the stage to a
    few ps, or thermalise the template).
Kept deliberately: the proposing session's own framing that the design error
was its own. The reusable half of this proposal is the question that was not
asked -- WHERE is the signal coming from -- not the melting-point physics, and
that reads better in the first person than sanded down into canon voice.
Not lintable; it is a physics-review step on the probe-gate checklist.

### 2026-08-26-1845-gate-reference-must-be-the-same-quantity
- status: merged   # 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge)
- designer_review: |
    MERGED as filed, both halves, and both are load-bearing. Target was right:
    learnings.md "A gate is a mechanism, not a sign" gains three corollaries --
    the reference clause, the passes-near-its-own-tolerance diagnostic, and the
    denominator clause from the second instance. Also promoted to lessons.md
    L46, as the proposal suggested: two independent instances in one day, both
    with clean where-it-bit records, neither LAMMPS-specific.
    The denominator clause was kept as a separate corollary rather than folded
    into the reference one -- they fail differently (a wrong reference reports
    on nothing; a missing denominator reports on a subset) and a session
    checking one will not automatically check the other.
- from: session 2026-08-26-1713-all-projects-status (pilot)
- class: gate design
- proposal: |
    A gate's REFERENCE VALUE must be the same quantity the gate COMPUTES,
    and the tolerance must be tight enough that it could fail. State both
    in the gate's own output: what was measured, where, and what it is
    being compared against.

    Canon already says "a gate is a mechanism, not a sign" and "every
    probe needs at least one gate that a physically wrong run would FAIL".
    Both are about the gate's SUBJECT. Neither constrains its REFERENCE,
    and a gate whose reference is a different quantity is not a weak gate
    -- it is a gate that reports on nothing, in green.

    Concrete rule to add: when a gate compares a computed number against
    a value quoted from another thread, the writing session must LOCATE
    that value in the source data before using it -- confirm the region,
    the averaging window and the definition it was measured under -- and
    record the location in the gate's comment. A number quoted in prose
    ("the far-field value", "the bulk step") is a pointer to a
    measurement, not the measurement.

    Diagnostic that catches it cheaply: if the gate passes at a large
    fraction of its own tolerance, treat that as a FAILURE TO INVESTIGATE,
    not as a pass. A correct gate on correct data usually passes by orders
    of magnitude, because it is comparing a thing to itself.
- where_it_bit: |
    2026-08-26, ni-h-at-dislocs-eam-meam thread 02 run 01, the octahedral
    pre-fill. GATE 4 required the recomputed far-field background to
    reproduce thread 01's measured rigid strain step, -24.32 meV. It
    computed the step between the EXTREME (111) planes of the +-70 A map
    zone and got -27.37 meV, passing a 4 meV tolerance by 3.05 meV.

    Investigated rather than accepted. thread 01's own background is
    recoverable exactly as E_ins - E_seg from its published per-site map;
    compared plane by plane, the recomputation agreed to 0.0005 meV over
    all 99 octahedral z-planes -- the background was right. The two values
    thread 01 quoted as its far-field pair turned out to sit at z = 76.0
    and z = 180.0 A, ~50 and ~53 A from the glide plane (about one decay
    length lambda = 53 A), NOT at the +-68.5 A zone edges. Two different
    quantities, differing by exactly the amount the loose tolerance
    absorbed.

    Fixed by evaluating the background at the planes the reference was
    measured at. The gate then agrees to 0.007 meV and its tolerance drops
    from 4 meV to 0.5 meV -- a gate that can now actually fail.

    Cost: nothing, because the near-miss was investigated before the
    production submission. That is the point -- the same gate would have
    shipped ten pre-filled cells with a green light on a comparison that
    meant nothing.
- second_instance: |
    2026-08-26, SAME thread, SAME day, found hours later. The pre-fill's
    background was looked up BY BIN with a silent fallback when a bin held
    no far-field sites. GATE 3 asked "does every background LAYER have
    enough far-field sites for a median" and truthfully answered yes --
    for the 69 layers that HAD any. It could not see the 30 bins that had
    NONE, because those never entered the background dict to be counted.
    Result: 13.1 % of octahedral sites assigned a background wrong by a
    mean of 13.6 meV and up to 27.2 meV, concentrated on exactly the
    near-core sites the thread is about, with all seven gates green.
    What caught it was not a gate: it was noticing that two numbers
    describing the same thing disagreed -- 99 z-bins in the output profile
    against 69 background layers in the gate -- and refusing to let that
    pass. THE ADDITIONAL RULE THIS ARGUES FOR: a gate that quantifies a
    population must state the DENOMINATOR it covers, and any object the
    computation touches but the gate does not count is the bug. "N of the
    layers are fine" is not a gate until it says N of how many.
    Generalises: a silent fallback (dict .get with a default, a clamp, an
    except-pass) is invisible to every gate that only inspects the objects
    that took the normal path.
- target: |
    canon/learnings.md, the "a gate is a mechanism, not a sign" bullet --
    add the reference-value clause, the passes-near-its-own-tolerance
    diagnostic, and the denominator clause from the second instance.
    Probably also a lessons.md entry: two independent instances in one
    day, both with clean where-it-bit records, and neither is
    LAMMPS-specific.

## 2026-08-27-0905-job-state-and-job-marker-can-disagree-both-ways

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED, but NOT as a standalone rule: this proposal and
2026-08-28-1610 are about THE SAME JOB (22730595_2) and, taken separately,
canon would have contradicted itself. This one ends "marker present +
scheduler RUNNING -> a hung step, cancel it"; the later one records that the
same row was still printing RUNNING after the job had been cancelled and had
ceased to exist, and that acting on it produced a false alarm and an
`Invalid job id specified`. Merged TOGETHER into one bullet in learnings.md
"Cluster discipline" -- the four-case table from here, the accounting-record
mechanism and the `squeue` confirmation step from there -- and the operative
half restated in `session-startup.md` step 1b, which is where a session will
meet it. The "identical elapsed times do not mean identical states" line was
kept verbatim; it is the cheapest part of the rule.


Filed by pilot session 2026-08-27-0852-portfolio-status. Class-level, not
an instance fix.

**Proposal.** Step 1b reconciliation must cross-check the run's OWN
completion marker against the SCHEDULER's state, and treat a disagreement
as a finding in its own right. Neither source alone is sufficient, and
they fail in opposite directions:

- `.out` marker present, scheduler says COMPLETED -> genuinely done.
- `.out` marker ABSENT, scheduler says RUNNING -> genuinely running.
  Confirm by an output mtime, not by the absence of the marker: a job
  that died silently looks identical from the .out alone.
- `.out` marker ABSENT, scheduler says COMPLETED -> the job exited
  without finishing. This is the failure the 2026-08-20 sweep was written
  to catch.
- **`.out` marker PRESENT, scheduler says RUNNING -> a HUNG STEP.** The
  science is finished and harvestable; the allocation is being burned for
  nothing until the wall clock cap. Cancel it.

**Evidence, 2026-08-27.** Array 22730595 (hydride-cycle thread 05 /
phase-diagram thread 03 run 15, mu bracket 900 K, 16 ranks per task,
24 h cap). Task 2 (mu = -2.45) printed its full gate block and `JOB DONE`
-- the last line of the submit script -- at 2026-08-26T13:49Z, and wrote
a complete results dir (ave, trace, traj, final-state, ave tail at step
88000, identical in shape to the eight tasks sacct calls COMPLETED).
sacct at 2026-08-27T08:5xZ nonetheless reported the task, and its lmp
step `22730595_2.0`, as RUNNING with 22:56:30 elapsed. Sixteen cores held
for roughly nineteen hours after the calculation ended.

The same paste showed task 9 (mu = -2.10) also RUNNING at the identical
elapsed time -- and that one was real, still writing its trace file that
minute. **Identical elapsed times do not mean identical states**: array
tasks that start together always report the same elapsed, so elapsed is
useless for separating a live run from a hung one. What separated them
was an output-file mtime.

**Why the existing rules miss it.** Step 1b was written around the
asymmetry "a delayed success is merely late, but a handed-over job that
fails produces no signal at all". This is a third case with no signal:
the job SUCCEEDED, said so, and is still counted as in flight by the
scheduler. A reader who trusts the marker calls it done and never looks
at the node; a reader who trusts sacct calls it in flight and waits for
output that was written yesterday. Both are wrong in a way that costs
compute rather than correctness, which is why nothing complains.

**Suggested wording for canon/session-startup.md step 1b**, after the
sacct one-liner: "For every task the paste reports as RUNNING, check one
output file's mtime before believing it. A task whose run directory has
not been written to in hours, and whose .out already carries the run's
completion marker, is a hung step -- hand Erik a `scancel` for it. Do not
infer state from elapsed time: array tasks that start together report the
same elapsed whether they are working or wedged."

---

## 2026-08-27-1100-slab-stress-vacuum-dilution

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED into `preferences.md` as its own section, "Stress
in a cell containing vacuum", placed immediately after the concentration
convention as proposed -- same class, wrong denominator. The self-validating
argument was kept, because it is what makes this a rule rather than a note.
Cross-linked to L47, which came out of the same thread and the same figure.

*Filed by pilot session 2026-08-27-0915-hydride-cycle-continue.*

**Proposed home:** `canon/preferences.md`, near the concentration convention;
it is the same class of error (a reported number whose denominator is wrong).

**Rule.** In any cell containing vacuum -- slabs, films, free surfaces, wires --
the stress LAMMPS reports is the virial divided by the WHOLE CELL volume and is
diluted by the empty space. Every stress quoted from such a cell is corrected to
the material volume, the correction factor is stated where it is used, and the
uncorrected value is kept alongside it in the summary file so the two can never
be confused.

**Where it bit.** ni-h-hydride-cycle-eam thread 02: a 120 a0 slab in a 477.48 A
box, f = lz_cell/lz_slab = 1.1250. Uncorrected, the biaxial modulus reads
168.2 GPa and disagrees with C11 + C12 - 2 C12^2/C11 ~ 202 GPa by 17 %, which
looks like a physics problem. Corrected it is 196.4 GPa and the geometry
validates itself. Every stress in the thread was understated by 11 %.

**Why it is a rule and not a note.** The check is nearly free and it is
self-validating: for a cubic metal the corrected biaxial modulus must match the
elastic-constant combination, so getting the correction right also confirms the
slab, the strain definition and the sign convention in one comparison. A thread
that skips it has no such check.

---

## 2026-08-27-1105-yield-is-the-stress-maximum-not-a-tolerance

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED as **L47**, with the corollary ("look at the figure
before quoting the number it was made from") kept in the lesson rather than
scattered, and a pointer added from `preferences.md`. The nonlinear-elasticity
mechanism is the reusable half: a fixed tolerance against a tangent line fires
on elastic softening in ANY material with visible nonlinearity at a few percent
strain, so the rule is not Ni-specific.

*Filed by pilot session 2026-08-27-0915-hydride-cycle-continue.*

**Proposed home:** `canon/lessons.md` (analysis lesson).

**Rule.** In a defect-free cell loaded to nucleation, the onset of plasticity is
the stress MAXIMUM followed by a load drop -- never "the strain at which the
curve leaves the linear fit by more than TOL". Report sigma_max, the strain at
it, and the magnitude of the drop. If a deviation-from-linearity number is
reported at all, it is labelled as elastic softening, not as yield.

**Where it bit.** ni-h-hydride-cycle-eam thread 02: a 0.05 GPa deviation
tolerance reported "first plasticity at eps = 1.15 %, sigma = 2.38 GPa". The
actual nucleation is at eps = 5.25 %, sigma = 6.22 GPa, with a 5.74 GPa drop --
wrong by 4.6x in strain and 2.6x in stress. What the tolerance had found was the
nonlinear elasticity of nickel: at several percent strain the moduli soften
visibly, and any fixed tolerance against a tangent line fires on that first.
The error is not conservative and it is not obvious from the number alone; it
was caught only by looking at the plotted curve.

**Corollary, worth its own line.** Look at the figure before quoting the number
it was made from. Both errors in this thread survived the summary file and died
on first sight of the plot.

---

## 2026-08-27-1110-do-not-scan-multi-GB-files-through-the-mount

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED into `learnings.md` "Cluster discipline" as filed.
It sits with the other mount bullets deliberately: the observed symptom (two
following calls fail, mount looks dead) is the same symptom the mount-failure
classification bullet teaches you to diagnose, and a session that reads one
without the other will misclassify its own timeout as an L15 stale view.

*Filed by pilot session 2026-08-27-0915-hydride-cycle-continue.*

**Proposed home:** `canon/learnings.md`, "Cluster discipline".

**Rule.** Never run a whole-file scan (`grep -c`, `wc -l`, a checksum) over a
multi-GB cluster file through the sshfs mount. The device shell has a ~45 s
cap; the call times out, and it can leave the shell briefly unresponsive so the
NEXT calls fail too and look like a dead mount. Get frame counts and offsets
from the submit script's `-var` block and the log's stage markers -- both are
kilobytes and both are authoritative. `head`/`tail` on a large file are fine;
anything that must touch every byte belongs in a cluster-side job.

**Where it bit.** 2026-08-27, `grep -c "^ITEM: TIMESTEP"` over the 23.5 GB
thread-02 trajectory: timed out, then two following `device_bash` calls failed
before the shell recovered. The frame count (~152) was then derived in one
second from `-var S0 50000 -var S1 705700 ... -var DUMP_RAMP_EVERY 10000`.

---

## 2026-08-27-1115-mc-region-load-imbalance-is-a-walltime-term

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED into `learnings.md` "Cluster discipline", directly
after "walltime before ranks" as proposed. Generalised one step in the wording:
this is a property of any spatially restricted per-rank workload, not only of
`fix mc/sites`, so the rule says "a restricted MC region", not "a dome".

*Filed by pilot session 2026-08-27-0915-hydride-cycle-continue.*

**Proposed home:** `canon/learnings.md`, near the walltime-before-ranks rule.

**Rule.** A spatially RESTRICTED Monte Carlo region does not make a run cheaper
in proportion to the catalogue. Trial insertions land on the ranks that own the
region, so a small region concentrates the Monte Carlo cost on few ranks while
the rest idle. Size the walltime from the probe's measured steps/s, never from
the ratio of catalogue sizes, and say so in the thread when the two disagree.

**Where it bit.** ni-h-hydride-cycle-eam thread 04 (dome, sphere of R = 10.57 a0,
~14,500 Ni vs 201,600 in thread 03 -- a 14x smaller catalogue). Its thread note
predicted "well under the cap". The probe measured 37.0-49.4 steps/s against
thread 03's 56.8-58.3 -- the dome is 20-35 % SLOWER per step, giving a 65-84 h
projection where 14x cheaper had been assumed.

---

## 2026-08-27-1400-probe-overrides-can-silence-a-gate

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED into `learnings.md` "Cluster discipline",
immediately after the probe-before-production block and before the probe
exemption -- a reader who has just been told to shorten a run needs this on the
next line. Strongest of the batch: it is a defect in the framework's own main
mechanism, and the gate reported `0.0000`, a plausible number, not an error.
The actionable half is the last sentence, so it was kept as the rule's
imperative: gate the probe on the highest-cadence stream and print the ROW
COUNT, never a tail. NOTE FOR THE OWNING PILOT (not a designer action): run 00's
probe submit, now under ni-h-phase-diagram-eam-meam run 15, carries the same
defect.

*Filed by pilot session 2026-08-27-0915-hydride-cycle-continue.*

**Proposed home:** `canon/learnings.md`, "Cluster discipline", next to
"probe before production".

**Rule.** When a probe shortens a run by overriding step counts, every gate
must be re-checked against the SHORTENED run, not just against the production
one. A gate that reads a block-averaged output is the classic casualty: block
averaging has a period (`Nfreq`), and a probe short enough to finish before
the first complete window produces a file with headers and no data. The gate
then reports zeros while the job exits clean, `JOB DONE` and all. Gate a probe
on the highest-cadence stream it writes -- the per-block trace -- and print the
row COUNT, never just a tail, so an empty file is visible as emptiness rather
than as a zero value.

**Where it bit.** ni-h-hydride-cycle-eam thread 05, runs 01 and 02, probes
22733979 and 22733980 (2026-08-27). Both passed every real gate -- empty
`.err`, `ALL PHASES COMPLETE` twice, `JOB DONE`, healthy trace, correct
`N_Ni` -- while the gate block printed `x = 0.0000  a_eff = 0.0000 A`.

Mechanism: `fix AVE` is defined AFTER the equilibration run with
`Nevery 20 Nrepeat 200 Nfreq 4000`. Under the probe overrides
(`-var equil 400 -var sample 4000`) the run ends at step 4400. The window for
step 4000 samples back to step 20, before the fix existed, so LAMMPS skips it;
the next window at step 8000 is past the end. The file therefore holds ZERO
data rows in ANY probe of this family. Production is unaffected -- `equil 8000`
puts the first row at step 12000, exactly as run 00 shows.

**Why it deserves a rule.** The failure is silent in the dangerous direction:
`0.0000` is a plausible-looking number, not an error, and the honest reading
("this gate cannot fire here") is only available to someone who works out the
averaging arithmetic. A gate that cannot fail is worse than no gate, because
it is counted as passed. Fixed in runs 01 and 02; run 00's probe submit
(now under ni-h-phase-diagram-eam-meam run 15, other-session scope) carries
the same defect and should be fixed by whoever owns it.

---

## 2026-08-28-1610-sacct-state-can-lie-for-an-unreaped-job

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED, jointly with 2026-08-27-0905 -- see that
proposal's review note for why they could not be merged separately. Everything
substantive from here survives: the accounting-record mechanism, the two tells
(Elapsed exceeding the job's own cap; the run dir finished hours or days
earlier), `squeue` as the arbiter over `sacct`, and the explicit prohibition on
letting an `sacct` row overturn another session's correct hand-over record.
That last clause is the reason this is canon and not a lesson: the cost was not
compute, it was un-doing a good hand-over.
Confirmed live in Erik's 2026-08-29T14:5xZ paste, which still shows 22730595_2
`RUNNING` at 3-04:43:56 against a 24 h cap -- three days after the job ceased
to exist.

*Filed by pilot session 2026-08-28-1558-nih-at-dislocs-readout.*

**Proposed home:** `canon/learnings.md`, "Cluster discipline", next to the
step-1b reconciliation rule -- and step 1b itself should name the check.

**Rule.** `sacct` reports the ACCOUNTING record, which is written when a job's
epilog completes. A job whose job step hung at exit may never get that record
closed: `sacct` then goes on printing `RUNNING` with `End = Unknown` and an
Elapsed that keeps climbing past the walltime cap, for a job the controller
has already forgotten. Treat an `sacct` row as authoritative for jobs that
ENDED CLEANLY only. Before acting on a job `sacct` calls live -- above all
before telling anyone it is burning cores -- confirm against `squeue`, which
reflects the controller rather than the database. Two tells that an `sacct`
`RUNNING` row is an accounting artefact rather than a live job: Elapsed
EXCEEDS the job's own `--time` cap (Slurm would have enforced it), and the
run dir shows the science finished with its full gate block hours or days
earlier.

**Where it bit.** ni-h-hydride-cycle-eam thread 05 run 00, array task
22730595_2 (mu = -2.45). Its science finished 2026-08-26T13:49Z with `JOB DONE`
and every output on disk; the `lmp` srun step never reaped. Erik cancelled it
2026-08-27T11:4xZ and the owning session recorded the loop closed -- correctly.
An `sacct -S 2026-08-25` paste on 2026-08-28 still showed it `RUNNING` at
2-06:04:10 against a 24 h cap. THIS session read that as ground truth,
concluded the earlier record was wrong, and told Erik the job had been holding
16 cores on s.cmmg for ~54 h. `scancel -s KILL 22730595_2` answered
`Invalid job id specified`: the job had not existed for a day. The elapsed
time being more than twice the cap was visible in the same paste and should
have been the tell.

**Why it deserves a rule.** Step 1b exists to make `sacct` the reconciliation
instrument, so this failure mode sits directly on the framework's main path.
It also fails in the expensive direction for trust: it manufactures a false
alarm about wasted cluster time AND contradicts a correct record written by
another session, which is exactly the way a good hand-over gets un-done.

---

## 2026-08-28-1650-fix-the-class-not-the-instance-minimum-image

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED into `learnings.md` "Workflow rules", with one
correction to the proposal: the "fix the class, not the instance" line it names
as its neighbour **does not exist in learnings.md**. The phrase lives in
ARCHITECTURE.md, in the packaged skill's §6, and in three lint-script headers --
nowhere in canon prose. So this proposal did not amend an existing rule; it
CREATED the learnings.md entry the phrase has been pointing at all along, with
the minimum-image case as its worked example. Filed under Workflow rules rather
than Process because it governs how a fix is finished, not how files move.

*Filed by pilot session 2026-08-28-1558-nih-at-dislocs-readout.*

**Proposed home:** `canon/learnings.md`, near the "fix the class, not the
instance" line in the pilot rules -- this is the worked example that line
has been missing.

**Rule.** When a defect is found in one script, GREP THE THREAD (and its
sibling threads) for every other script that computes the same quantity,
and fix or explicitly clear each one in the same session. Record which
files were checked, not just which were changed. A defect that was found,
understood, written up and fixed in ONE file is the easiest kind to meet
again, because the write-up reads as if the class were handled.

**The specific quantity, since it recurs here:** any distance from a
dislocation line, or any "far field" test, in a PERIODIC cell must use the
minimum image. It is invisible whenever the line happens to sit near the
box centre, which is exactly the configuration such code gets written and
tested on.

**Where it bit.** ni-h-at-dislocs-eam-meam. On 2026-08-24 the missing
minimum image was found in `select-relaxation-subset.py`, fixed, verified
against d90 (bit-identical selection), and written up in BOTH thread 04 and
thread 05 thread.md under "Three ways this is NOT d90". The sibling script
`export-Hbindmap-for-ovito.py`, in the same project, computing the same
`r` from the same line, was not looked at. It was still naive on
2026-08-28 when the d0 map was finally analysed: d0's line sits at
x = 144.0 in lx = 250.33, so **7.6 % of all 956760 sites** were being
assigned the long way round the box -- including part of the far-field
population that DEFINES the background. Caught only because the new
session printed the naive and minimum-image maxima side by side.

**Why it deserves a rule.** The 08-24 write-up is good: it names the
defect, gives the verification, and appears in two thread files. It is also
exactly what made the second instance easy to miss -- a reader of either
thread.md would conclude the minimum image had been dealt with in this
project. The cheap guard is a grep at fix time, and a line in the write-up
saying which other files were checked and cleared.

---

## 2026-08-28-1830-score-minimizers-in-force-evaluations

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED into `learnings.md` "Cluster discipline", beside
the walltime rules, as proposed -- same shape as L26. Kept in full, including
the measured table and the `min_modify norm` / `hftn` caveat, because the
numbers ARE the rule: without them the ranking is just an opinion, and the
proposal's own point is that the opinion was wrong. Not promoted to a lessons.md
L-number: it is a measurement-and-method rule for a whole class of runs, not a
LAMMPS trap that bites an input, which is what the L-queue is for.
The self-report -- the session argued from condition numbers, predicted FIRE
would lose, and was wrong -- was kept in the first person. It is the part that
makes the rule stick.

*Filed by pilot session 2026-08-28-1558-nih-at-dislocs-readout.*

**Proposed home:** `canon/learnings.md`, near the L26 walltime rule -- it is
the same shape (measure the thing that costs, do not extrapolate a proxy).

**Rule.** When choosing or comparing a `min_style`, score **force
evaluations and wall time**, never iterations. LAMMPS iterations are not
commensurable across styles: `cg` and `sd` spend 2-3 force evaluations per
iteration in their line search, `fire` and `quickmin` about 1, and `hftn`
spends O(100) in its inner CG loop. The count is printed as
`Iterations, force evaluations = N M` at the end of every `minimize` and
that is the only place the evaluation number exists -- it is not available
as an equal-style variable, so a harvest must parse the log.

Two companion rules, both of which the same run exercised:

- **Give every arm the same EVALUATION budget**, not the same iteration
  budget: `minimize etol ftol <large maxiter> <equal maxeval>`. Capping
  iterations equally hands the line-search styles a factor 2-3 they have
  not earned.
- **Check that every arm actually stopped at the same force norm before
  ranking anything.** `min_modify norm` is settable for `cg`, `sd`,
  `quickmin` and `fire` but NOT for `hftn`, which ignores `min_modify`
  entirely. Record the converged `fnorm` per site per arm and refuse to
  print a cost table if they differ.

**Where it was measured.** ni-h-at-dislocs-eam-meam thread 04, job 22736776
(2026-08-28), five arms x the same 20 sites: one H inserted into a 570720-atom
cell already relaxed to fnorm/dof ~1e-8, minimized to 1e-7 per dof.
Force evaluations, relative to `fire`: **fire+abcfire 0.92, fire 1.00,
cg 1.71, hftn 2.52, quickmin 20.25.** All five agreed on the energies to
0.001 meV. Scored on ITERATIONS instead, the same run would have ranked
`hftn` first by a factor of 71 (112 iterations against fire's 7957) and
`cg` ahead of `fire` -- both conclusions exactly backwards.

**And the reason it is a rule and not a note.** The session ARGUED, in
detail and from the structure of the problem, that a near-quadratic basin
entered close to its minimum should favour `cg` or `hftn` over damped
dynamics, and predicted FIRE would lose. FIRE won. `cg` turned out to need
the same iteration count as FIRE rather than fewer, so its line search was
pure overhead; `hftn` converged in 3-4 Newton steps per site exactly as
advertised and still lost 2.5x on the constant. The argument was not
foolish and it was still wrong, which is the case a rule has to cover: a
one-hour five-node bake-off settles this class of question and reasoning
about condition numbers does not. Probe before production already covers
correctness; this covers COST.

---

## 2026-08-28-1835-lammps-log-echoes-the-command-before-its-output

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED as **L48** plus `style/lammps.md` **1.20**,
following the standing convention that style imports the rule by reference
rather than duplicating the prose. Filed as read-and-confirm, not a hard gate:
the harvest scripts live outside the `.in`, so `lint-lammps-input.sh` cannot
see them. The dangerous case named in the proposal -- a command whose text
happens to parse -- is carried into both entries, since the loud `ValueError`
is the one that needs no rule.

*Filed by pilot session 2026-08-28-1558-nih-at-dislocs-readout.*

**Proposed home:** `canon/style/lammps.md` section 1 pre-flight, as a check
on any script that harvests a value out of a `.log`.

**Rule.** LAMMPS writes each input command to the log VERBATIM, before the
output that command produces. So a regex harvesting a printed quantity
matches the **command text** first, complete with its unevaluated
`$(...)`, and only later the number. Any log-parsing harvest must take the
first match that parses as the expected type, or anchor past the echo --
never `re.search(...)` and trust it.

**Where it bit.** ni-h-at-dislocs-eam-meam thread 04, 2026-08-28. The
bake-off harvest read the convergence tolerance with
`re.search(r"ftol_loose_global_eV_per_A = (\S+)", log)` and got the string
`$(v_FTOL_LOOSE:%.6e)` from the echoed `print` command. It failed loudly
here (`ValueError` on `float()`), which is the lucky case. The dangerous
version of the same bug is a quantity whose command text happens to parse
-- a hardcoded literal in the command, say -- where the harvest returns a
plausible wrong number and nothing raises.

---

## 2026-08-28-1910-test-the-composition-not-the-piece

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1442-designer-inbox-merge).

**Designer review.** MERGED into `learnings.md` "Cluster discipline", next to
probe-before-production as proposed -- it is the pre-flight analogue for driver
scripts, and it belongs where a session goes looking before a hand-over. The
closing observation was promoted into the rule rather than left as an aside:
where a driver can choose its sample points, choose the ones the upstream data
actually HAS. That is the fix that removed the interpolation entirely, and it
generalises further than the guard it tripped.

*Filed by pilot session 2026-08-28-1558-nih-at-dislocs-readout.*

**Proposed home:** `canon/learnings.md`, beside "probe before production" --
this is the pre-flight analogue for driver scripts that FEED an existing
validated script.

**Rule.** When a new driver computes inputs for an existing script, testing
the driver's arithmetic against the same data files is NOT a test of the
pipeline. The existing script has its own admissibility guards, and they are
usually STRICTER than the new code's, because they were written against
failures the new code has never seen. Before hand-over, run the values the
driver will actually produce through the CALLEE's own entry points -- import
its parser and its input-validation function and call them on the real list.
It costs seconds and it tests the join, which is where the defect lives.

**Where it bit.** ni-h-at-dislocs-eam-meam thread 02, job 22736814
(2026-08-28). A new driver computed a(300 K, mu) for a six-point mu bracket
and handed each mu to the validated run-01 pre-fill script. The driver's
a(mu) block was tested locally against the real isotherm and handled every
point, including mu = -2.45, by flooring x at 1e-12 before a log
interpolation. That flooring is CORRECT for a lattice constant -- a(mu)
there equals a0(300 K) to nine decimals -- and WRONG for a fill probability.
The pre-fill's own `x_of_mu` refuses that mu outright: "brackets a tabulated
x = 0 point; log interpolation is undefined. Use a tabulated mu." The job
died on its first point.

The guard did its job perfectly and the cost was one queue slot: everything
downstream had already passed on the same run (catalogue/map join 953534
rows 0 mismatches, class fractions exact to 0.00 %, background step
-24.330 meV against a measured -24.323 with a 0.500 meV tolerance, 507058
fill sites, 568606 Ni carried verbatim). But nothing about the test that was
run could have caught it, because the test never touched the callee.

**The fix that came out of it is worth stating too:** the corrected bracket
uses the isotherm's OWN tabulated mu (0.03 eV spacing) rather than a round
0.04 grid, so both x(mu) and a(mu) come straight from measured rows and
nothing is interpolated anywhere in the pre-fill path. Where a driver can
choose its sample points, choosing the ones the upstream data actually has
beats interpolating onto a tidier grid.

---

## 2026-08-29-1525-name-the-aim-not-just-the-number

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1915-designer-inbox-merge).

**Designer review.** MERGED into `preferences.md` "Communication style", beside
the spell-out-abbreviations rule -- both are about not making Erik reconstruct
context the session already has. Taken essentially as proposed; the worked
example was kept, because the rule is hard to follow from an abstract statement
and trivial once the two phrasings sit side by side. The proposal's closing
observation was promoted into the rule rather than left as rationale: a reader
who does not know the aim cannot tell a decisive result from a routine one, so
every unlabelled number is read at the same weight.

The note about `scope:` fields in SESSIONS.md and the hand-off template was
folded into the rule's list of places a first mention occurs, rather than made a
separate item -- it is the same rule applied to a different surface, and a
second bullet would have invited sessions to treat chat and files differently.


*Filed by pilot session 2026-08-29-1520-hydride-cycle-status.*

**Proposed home:** `canon/preferences.md`, with the other output/reporting
rules. It is a standing preference of Erik's, not a lesson from a failure.

**Rule.** A thread or run is NEVER referred to by its number alone. Every
first mention in a response, a status readout, a table row, a figure caption
or a hand-off carries its AIM in the same breath -- one clause, what the run
is meant to answer, not what it is named:

> not "thread 05 runs 01 and 02 -- all ten tasks complete"
> but "thread 05 runs 01+02, the fine 900 K mu bracket and its L14 size
>     check (is the 900 K transition still first order, and is the gap
>     physical or finite-size?) -- all ten tasks complete"

The directory name is not the aim. `05_CHARGE-DISCHARGE-RATEB-900K` says
what the run IS; it does not say what it is FOR, and the aim is the part
Erik needs in order to judge whether a result matters. Subsequent mentions
in the same response may use the short number once the aim is on the record.

Where the aim is genuinely unknown to the session, the honest form is to say
so ("thread 04, aim not stated in thread.md") rather than to quote the
folder name as if it were a purpose. That is also a signal the thread.md
needs a one-line aim at the top.

**Erik, 2026-08-29:** "always mention the aim of a thread and not just the
thread and run number."

**Where it bit.** This session's own opening status readout for
ni-h-hydride-cycle-eam. It reported "Thread 05 runs 01 and 02 -- all ten
tasks complete, gates pass, and they answer the question", gave the full
x(mu) table for both, and drew the conclusion that the empty interval was a
grid artefact -- without ever saying that run 01 is the 0.01 eV fine bracket
inside run 00's (-2.50, -2.45) and run 02 is the same six mu repeated at
L = 14 to test whether the interval WIDTH is finite-size. Erik had to ask
what the runs were for before the result meant anything, and the readout's
most load-bearing sentence ("not finite size either") was unreadable until
he did.

The cost is asymmetric and that is why this is worth a rule: a reader who
does not know the aim cannot tell a decisive result from a routine one, so
every unlabelled number is read at the same weight -- which is exactly the
failure mode a status readout exists to prevent.

**Note for the merging designer.** Consider a companion line in
`canon/session-startup.md` step 5 (the startup brief) and in the hand-off
template: the scope summary has the same defect, since `scope:` fields in
SESSIONS.md are also written as thread numbers plus protocol names.

---

## 2026-08-29-1610-index-before-you-trust-a-frame-walker

**status: merged** -- 2026-08-29 designer pass (session 2026-08-29-1915-designer-inbox-merge), as **L49**.

**Designer review.** MERGED to `canon/lessons.md` as a numbered lesson rather
than to `learnings.md` as the proposal suggested. Reason: it prescribes a
concrete technique against a concrete failure mode, which is what lessons.md is
for; `learnings.md` holds process-level meta-lessons. The cross-reference to
"test the composition not the piece" is kept, and the proposal's argument for
why that rule does not already cover this -- a frame walker has no callee and no
guard, so its wrong answer is a valid file -- was promoted into the lesson body,
since it is the reason the lesson exists.

**ABSORBED FROM PROPOSAL 2026-08-29-1905:** its second half ("a stated
expectation is part of the command") landed here, not in preferences.md. That
half is not about hand-offs at all -- it is the same defect as the frame-skip,
one layer up: the tool did not do what its own documentation said. Splitting it
this way keeps the hand-off rule about hand-offs and gives both halves of the
extractor incident one home. Both incidents are recorded in L49's "where it
bit", because a single tool producing the same class of silent error twice is
the evidence, not two separate anecdotes.


*Filed by pilot session 2026-08-29-1520-hydride-cycle-status.*

**Proposed home:** `canon/lessons.md` as a numbered lesson, cross-referenced
from `learnings.md` next to "test the composition not the piece" (merged
2026-08-29) -- this is the same family, but the failure is silent where that
one was loud.

**Rule.** Any code that walks a trajectory frame by frame -- an extractor, a
decimator, a per-frame analysis loop -- must be validated against a file whose
frame count is known INDEPENDENTLY of the walker, before it is used or handed
over. Two cheap fixtures, and both are needed:

1. a synthetic file with a handful of frames at known timesteps and the real
   column set, written by the test itself;
2. a real file from the same family, whose expected frame count comes from the
   submit script's dump cadence and step bounds, not from the walker.

And the walker should offer an `--list`/index mode that prints
`N frames, M atoms, steps a .. b` and writes a step/offset index. Run that FIRST
on any new trajectory: it is one sequential read, it makes the walker state its
own understanding of the file out loud, and the index makes every later cut a
seek instead of a scan.

**Where it bit.** ni-h-hydride-cycle-eam, 2026-08-29, writing
`tools/extract-ovito-frames_ni-h-cycle.py` to cut OVITO-sized subsets out of the
7-25 GB cycle trajectories. The header parser consumed **ten** lines for the
nine-line LAMMPS dump header, eating the first atom of every frame. The caller
then skipped `natoms` body lines, ran one line into the next frame's header, and
resynchronised only at the frame after -- so the tool silently kept **every other
frame**.

Nothing about the output looked wrong. The extracted dump was well-formed, every
column present, timesteps monotonic and evenly spaced. A `--list` on a real
isotherm dump reported "23 frames, steps 0 .. 88000" -- entirely plausible, and
it was 23 of the 45 that were there. What exposed it was the synthetic fixture:
four frames written, two found. Had this gone to the cluster as handed, it would
have produced half-length animations and a `--steps` request that quietly
returned the wrong nearest frame, with a `.frameindex` sidecar attesting to it.

**Why it deserves its own lesson rather than a note on the existing one.** The
merged 2026-08-29 rule is about testing a driver against the CALLEE's guards --
it works because the callee refuses bad input loudly. Here there is no callee
and no guard: a frame walker's wrong answer is a valid file. The only defence is
a case where the right answer is known before the code runs, which is why the
fixture, not the review, is the deliverable.

**Generalises to:** anything that parses a self-delimiting record format by
counting lines -- XDATCAR, xyz frames, multi-frame CFG, and the per-atom blocks
inside a single dump frame.

---

## 2026-08-29-1905-a-handoff-command-runs-in-a-shell-you-are-not-in

**status: merged in part** -- 2026-08-29 designer pass (session 2026-08-29-1915-designer-inbox-merge). SPLIT.

**Designer review.** The proposal bundled two rules that do not belong together,
so it was split at the merge rather than merged whole:

- **The glob/quoting half MERGED** into `preferences.md` "Command hand-offs",
  after the new-tree `mkdir` bullet. Strengthened from the proposal's "prefer no
  globs" to a flat "no globs in hand-off commands", with the `--include`/
  `--exclude` filter form given as a copy-pasteable skeleton -- the section's
  other rules are all imperatives, and a hedge here would have left the failing
  form available. The zsh-specific fact (an unmatched glob is an error, not a
  pass-through) is stated explicitly, because it is the part a session composing
  from bash habits will not know. The requirement to test the form against a
  mock tree before offering it was added at merge; it is what actually caught
  the replacement.

- **The stated-expectation half MOVED** to L49, where the other half of the same
  incident already lives. See that entry's review note.

The proposal's closing point -- that both failures share L42's shape, something
true in the writing session's head and false in the shell that had to run it --
is kept in the preferences.md bullet, since that framing is what makes the rule
memorable rather than arbitrary.


*Filed by pilot session 2026-08-29-1520-hydride-cycle-status.*

**Proposed home:** `canon/preferences.md`, "Command hand-offs" -- next to the
absolute-paths rule and the machine-tag rule, which exist for the same reason.

**Rule.** A hand-off command is written by a session that will never run it, in
a shell it is not in. Two consequences, both cheap to honour:

- **Erik's login shell is zsh.** An unquoted glob that matches nothing locally
  is an ERROR in zsh (`no matches found`), not a pass-through as in bash. So a
  remote glob in an `rsync`/`scp` source cannot be written bare -- and quoting
  the whole source to protect it from zsh ALSO stops the remote shell expanding
  it, and merges multiple space-separated paths into one literal filename.
  **Prefer no globs at all**: explicit absolute paths, or `--include`/`--exclude`
  filter rules with a single source directory. Filter rules are the robust form
  for "pull this subtree from several places", and they need no shell quoting
  anywhere.
- **A stated expectation is part of the command.** If the hand-off says what the
  output should look like, that sentence must be true of the code as written,
  not of the code as intended.

**Where it bit.** Both halves, in the same hand-off, on the same day.

*The glob half.* `HANDOFF-2026-08-29.md` block 3 offered
`rsync ... host:'/…/./0[34]_*/results/*/for-ovito /…/*.frameindex' dest/`. It
failed with `link_stat "…0[34]_*…for-ovito /…*.frameindex" failed: No such file
or directory` -- one quoted argument, so no expansion on either side, and the
space read as part of the filename. Replaced with
`--include='*/' --include='for-ovito/***' --include='*.frameindex' --exclude='*'`
against a single source root, tested against a mock tree of the same shape
before it was offered a second time.

*The stated-expectation half.* The same hand-off, and the submit script's own
comment, both said the extractor keeps "the phase boundaries **+ the last frame,
always**". The `--steps` code path never appended the last frame; only `--every`
did. Job 22738636 therefore wrote 5 frames per run instead of 6 and silently
dropped step 4300000 -- thread 03 fixlat's final state, 37k steps before the
`mc/sites` abort, and the single most interesting frame in the run. It cost a
re-submit. Nobody would have noticed from the output alone: five frames is a
perfectly plausible number.

**The generalisable bit.** Both failures are the same shape as L42 (a cluster
name is not a host): the session emitted something that was true in its own head
and false in the shell that had to run it. The mitigation is the same too --
resolve or test the thing in the environment that will execute it, and where
that is impossible, prefer the form that has no environment-dependent behaviour
left in it.
