# LLM-LMPS - proposal inbox

Pilot sessions append proposals here when they surface a new rule,
preference, lesson, or feedback memory during their work. The designer
session reviews this file in batches, merges into the canonical target
file (`canon/lessons.md`, `canon/preferences.md`, `canon/style/*.md`,
`ARCHITECTURE.md`, or `auto-memory/<name>.md`), and marks the proposal
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
target_file: <e.g., canon/lessons.md, canon/preferences.md, canon/style/*.md, ARCHITECTURE.md, auto-memory/<name>.md>
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

