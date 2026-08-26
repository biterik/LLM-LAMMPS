# LLM-LMPS — Pilot learnings

Canonical source. Anthropic-side memory mirrors. Additive; pilot proposes
new entries, Erik confirms.

## Hard rules (architecture §2)

- The LLM owns the write channel. Erik never types into project files —
  not project.md, not thread.md, not scripts. He speaks in chat; pilot
  transcribes.
- Descriptive file names always. No `dump.out`, no `run1/`, no
  `restart.data`. Pilot picks descriptive names; Erik shouldn't have to nag.
- Cluster access is gated. **Sbatch is strict-A always**: pilot prepares
  the command, Erik runs it in his terminal. Cluster file operations use
  the mount with read-free / write-requires-Erik-OK.
- Brainstorm mode is the default for new design questions. Don't push for
  scoping or "what's v0?" until Erik signals.
- **Pilot default stance: wait-and-co-develop (hard rule).** The Pilot
  answers what Erik actually asked and then *stops*. No unsolicited
  option-dumping, no menus of choices, no laying out "the landscape"
  uninvited. At most ONE question, and only when genuinely blocked from
  proceeding. The project is developed *together*, one step at a time,
  led by Erik — the Pilot waits for his input rather than racing ahead
  with suggestions. This overrides any host-environment bias toward
  proactivity. (2026-06-01 Ni-hydride: Erik flagged the Pilot dumping
  stoichiometry/potential/lattice options + three questions on a
  "start with the science" opener. Behavioral rule — not greppable, so
  no lint; lives here.)
- Plain files only. YAML, markdown, JSON, parquet. No databases.

- **Local tooling runs on every Mac, or it is not a guard.** macOS ships
  bash 3.2 and BSD grep: no `grep -P`, no `grep -o` with PCRE, and `case`
  inside `$(...)` misparses. The shell style guide governs *cluster*
  scripts, where the interpreter is known and modern. Anything meant to
  run on Erik's Macs (lints, pre-push hooks, local helpers) is written in
  Python 3 with stdlib only, and is actually executed once on a Mac before
  it counts as working. (2026-07-28: lint-no-identity.sh passed on Linux
  and died on Erik's shell with a syntax error; rewritten as .py with a
  wrapper.)

## Workflow rules

- Three-layer iteration: pre-flight (silent layer 1) / mechanical fix
  (silent layer 2) / scientific closure (logged layer 3). Mechanical-vs-
  scientific heuristic: fail-early at start-up / syntax / missing file =
  mechanical; fail-late or at-specific-parameter = scientific.
- Closed threads stay closed. Reopening = a new thread that explicitly
  `consumes:` the old closure's verdict.
- One mile-pebble per thread (default). Verdict prose for failed threads,
  no mile-pebble.
- Mile-pebble curation pulls a compressed copy to Mac storage on closure.
  Mount is a window, not a duplicate.
- **Closure implies compression.** Closing a thread (or a finished part of a
  project) includes a zstd pass over its bulky artifacts on BOTH trees --
  dumps and trajectories, `.data`/`.lmps` snapshots, restart files, large raw
  outputs; `zstd -19`, `.zst` suffix, per L17. Human-readable records
  (project.md, thread.md, run.yaml, small `.dat` tables, plots) stay
  uncompressed. Compression commands on the cluster side are strict-A: the
  pilot prepares, Erik runs. (Erik, 2026-08-06.)
- **Verify bridge writes by the directory listing, not by re-reading the
  staged copy.** After `device_commit_files`, confirm with the size and mtime
  `device_list_dir` reports for the destination path. The staged path under
  the session's uploads directory is a cache and may serve a snapshot from a
  previous stage of the same file, so re-staging and reading it back can show
  old content long after a correct write. A stale read is indistinguishable
  from a failed write unless you check the listing, and the natural response
  -- re-send, re-read, see the old content again -- reinforces the wrong
  conclusion. Same class as the cmmg quirk
  `sshfs_default_options_show_stale_views`, one layer further out.
  **Corollary:** never write a claim about the user's filesystem into a project
  or canon file on the strength of a re-read alone. (2026-08-04: a correct
  43716-byte write was twice read back as the 39061-byte two-day-old version;
  the pilot wrote a false "something is reverting this file" warning into
  SESSIONS.md and a re-entry brief before checking the listing.)

- **A gate is a mechanism, not a sign and not a marker.** A probe observable
  moving in the EXPECTED direction is not a passed gate until the mechanism is
  checked: ask WHERE the signal comes from spatially, and whether the dynamics
  responsible for it is the dynamics the production run will have. The
  question is cheap -- usually one `awk` pass over a profile the probe already
  wrote -- and it is the only thing standing between a green probe and a
  production run that measures the wrong process. Two corollaries, both paid
  for in cluster time:
  - **A quantity that is "held" is verified against a number, not a picture.**
    See `style/lammps.md` 1.18: an fcc percentage is invariant under uniform
    strain and will report a perfect crystal at 8 % misfit.
  - **Green mechanical gates say the job ran, not that it measured anything.**
    ALL PHASES COMPLETE, an empty `.err` and a full set of `Performance:`
    lines are necessary and nowhere near sufficient. Every probe needs at
    least one gate that a physically wrong run would FAIL.

  Where it bit: 2026-08-25, ni-melting-point-eam thread 01. The measure probe
  at 1450 K showed fcc rising in the liquid region -- the desired
  "freezing below Tm" sign -- and was passed. The rise was epitaxial freezing
  of a 15 A liquid film against the rigid guarded-settle template
  (`style/lammps.md` 1.19). The 7-rung production ladder then ran on cells
  whose liquid had crystallized BEFORE measurement start, and ~3.5 h of
  cluster time measured crystal annealing. The design error was the proposing
  session's own; it is recorded here in its own words because the reusable
  half is the question it did not ask, not the physics it got wrong.

## Pilot reasoning failures from the 2026-05-29 walk

These are specific reasoning errors Erik called out, distinct from the
bug-pattern meta-lessons below. Each one is "you brought up X without
first checking Y."

- **Don't bring up parallel decomposition arguments before checking
  the rank count.** During cell-sizing, the pilot argued "4×4×4 is
  also nice for parallel decomposition (e.g., 2×2×2 → 8 ranks easily)."
  Erik runs these baselines on 1 core. The argument was irrelevant and
  patronizing. Check `n_cores` in the run plan / preferences before
  invoking parallel-efficiency reasoning.
- **Read the docs before listing options.** The pilot listed FIRE as
  one of three real minimizer choices for static bulk minimization
  with box/relax. FIRE/quickmin are damped-dynamics integrators
  without line search and cannot be paired with `fix box/relax`.
  Erik: "read the docu before suggesting something that might not
  work!" Enumerating choices from priors is fine; doing so without
  checking the constraint set (here: box/relax in play) is not. See
  L12.
- **Pin down semantics before naming numerical values.** The pilot
  proposed `ftol 1e-8` for N=4000 without first explaining that
  `ftol` is the global force 2-norm (length 3N, scales as √(3N)) vs.
  a per-atom RMS quantity. Erik flagged the missing semantics. Result:
  the proposed value was below double-precision floor — would have
  iterated to maxiter without converging. See L9.
- **Reference values without their temperature context are
  half-information.** Experimental lattice constants quoted in
  tables and abstracts are typically room-temperature (300K) values,
  not 0K. Comparing a 0K minimization against a 300K experimental
  reference expects a slight under-prediction at 0K due to thermal
  expansion (~0.1–0.4% for fcc metals). Mention this context when
  reporting agreement or disagreement.
- **Don't carry over partition assumptions from prior examples without
  checking task count.** Erik's existing example submit scripts used
  `p.cmmg` (multi-node partition) with 128 tasks. For 1-core baseline
  runs, the correct partition is `s.cmmg` (≤1 node). Same gotcha
  family as the parallel-decomposition lesson above: don't reach for
  patterns from larger-scale examples when the current job is small.
- **Out-of-fit-target observables are predictions, not constraints.**
  A potential's reported a₀ and Cij are not necessarily fit targets.
  The Pezold EAM was fitted to H-H interactions and local hydride
  formation at dislocations — bulk pure-Ni a₀ and Cij are
  predictions/byproducts. Divergence from experimental Ni values
  isn't a "wrong potential" verdict; it's expected, and is exactly
  the baseline we're measuring.

## Meta-lessons from the 2026-05-29 walk (root causes, not symptoms)

The walk surfaced six LAMMPS input bugs (L1–L6 in `lessons.md`). The
*bugs* are minor; the *pattern that produced them* is the load-bearing
lesson:

- **Rules-as-prose-only fail at write time.** A rule that lives only as
  paragraph text in ARCHITECTURE.md or in chat memory does not get
  consulted before drafting. Rules must land in a file the pilot
  literally walks through before Write. That file is `style/<topic>.md`.
- **Layer 1 needs a literal checklist, not a description.** Architecture
  §12 Layer 1 is paragraph text. Attention is the wrong tool for "did
  you use a generic filename on line 47 of a 75-line input." A grep-able
  checklist (see `style/lammps.md` §1) catches what attention misses.
- **Docs-first must be proactive, not reactive.** Looking up the LAMMPS
  doc page for a command *after* it errored costs a queue slot. Looking
  it up *before* writing the command costs a web fetch. The rule
  "consult docs of the loaded version" applies at write time, not at
  debug time.
- **Tutorial-default filenames win by inertia unless explicitly
  grep'd against.** `log.lammps`, `dump.out`, `restart.data`,
  `relaxation-log.dat`, `a0-result.txt` are all what the LAMMPS
  quickstart prints. Without a literal grep pass against the
  descriptive-names rule, they sneak through.
- **Assumption without verification.** Generalizing from one command's
  behavior to another's ("thermo and dump fire during minimize, so
  `fix print` presumably does too") is exactly the failure mode the
  docs-first rule is meant to prevent. For every command used in a new
  context (especially: any fix used during minimize), consult its doc
  page for the "supported during minimize?" / equivalent compatibility
  note before assuming.

## LAMMPS-specific

- Look up the LAMMPS docs of the **version currently loaded** before
  writing syntax; do not guess. Commands and defaults change between
  versions (e.g. `lammps/250722` vs `lammps/241119`).
- Bare ALL CAPS placeholders for sed (`STRUCTURE`, not `__STRUCTURE__`
  or `{STRUCTURE}`).
- Never use multiline `&` continuation in input scripts.
- Declare expected placeholders in the input skeleton header comment
  (e.g. `# NEED TO SET: STRUCTURE, FUPPER, FLOWER`). Pilot uses this list
  for layer-1 pre-flight.

## Submit-script discipline

- Always: `set -euo pipefail`, `module purge` before any `module load`,
  `cd "$SLURM_SUBMIT_DIR"`, pre-run existence check for every referenced
  input file, descriptive `--job-name`, `LMP_BIN="${LMP_BIN:-lmp}"`.
- `--reservation=Erik` only when Erik says "urgent".
- **The job sends its own notification mail; Slurm's is only a backstop.**
  Slurm's `--mail-type` mail is subject-only with an empty body, and the
  subject leads with `Slurm Job_id=...` boilerplate, so at typical mail-list
  width the job name is truncated away and the status and submission
  directory never arrive at all. Every submit script therefore sources
  `slurm-notify.sh` and calls `lmps_notify_arm` before the `srun`: subject
  `[LMPS] <STATUS> <job name> <job id>`, body carrying the full job name,
  the ABSOLUTE submission directory, status + exit code, resources, wall
  time and the tails of stdout/stderr. Slurm's own mail is cut back to
  `--mail-type=FAIL,TIME_LIMIT` -- the cases where the helper cannot run
  (node failure, OOM-kill, SIGKILL). The helper is pre-flighted with the
  other inputs and a missing one aborts the submit script; it is never
  sourced with `|| true`, because a notification that quietly stops
  arriving is indistinguishable from a quiet queue. Rule and skeleton:
  `canon/style/shell.md` 6; helper: `canon/templates/slurm-notify.sh`.
  (Surfaced 2026-08-26 by Erik: "the emails sent do not contain a body,
  and their subject line is too long to be displayed.")

## Cluster discipline

- **Probe before production for any edited or new LAMMPS .in.**
  Before any production sbatch where the LAMMPS input has been
  edited since its last successful run on this cluster, the pilot
  submits a probe job first. The probe is a clone of the production
  .in with all step counts reduced to 10 and the fix ave/time
  cadence reduced (Nevery=2 Nrepeat=5 Nfreq=10) so all output paths
  fire within the probe length. Task count is **the smallest that keeps
  the probe inside a few minutes for this cell and this potential** --
  floor of 2, never 1, so the parallel decomposition is still
  exercised. For small cells that is 2; for 1e5-1e6-atom cells or an ML
  potential it is tens of tasks. Pick it from N_atoms x (measured or
  estimated) cost per atom-step, and record the count and the reason in
  thread.md. Walltime cap stays short (<= 20 min); same partition as
  production. (Amended 2026-08-03: the "2 tasks" constant stood in for
  "cheap enough that a bad probe costs nothing", which is a function of
  N_atoms x cost_per_atom_step, not a constant. At 5.7e5 atoms with ACE
  at 1.86e-4 core-s per atom-step, one FIRE iteration on 2 tasks takes
  ~530 s and the 5-min cap is violated by the recipe itself.) The probe
  must:
  - exit cleanly (`.err` empty, `.out` ends with "PROBE DONE")
  - reach the "PROBE marker: ALL PHASES COMPLETE" line in the log
  - emit a `Performance:` line for each `run` block, or -- where the
    work is a `minimize` rather than a `run` -- a `Loop time of X on N
    procs for M steps` line for each minimize block. Either is the L26
    walltime-calibration source; `minimize` never emits `Performance:`.
    (Added 2026-08-03: three 10-iteration FIRE probes in
    ni-dislocs-eam-meam-ace would have been scored as failed by the
    original wording despite behaving perfectly.)

  Only after the probe returns clean does the pilot propose the
  production sbatch. Probe files live alongside their production
  counterparts as `<input>.probe.in` and `submit-<run>.probe.slurm`.
  Pattern surfaced 2026-05-31 after two consecutive sessions of
  guess-and-ship LAMMPS-syntax bugs that the existing lint script
  could not catch (semantic invalidity, not syntactic patterns).

  Probe template lives at `canon/templates/probe-input.in.skel`
  (created 2026-06-01 from the verified-clean Thread-03 probes). It is
  the canonical clone source: render the ALL-CAPS tokens with sed, then
  pick one POTENTIAL BLOCK (EAM/MEAM) and one PHASE BLOCK (NPT / NVT-scan)
  variant. It bakes in L19/L27/L28/L29/L30/L31 so a fresh probe can't
  reintroduce those bugs. The probe doubles as the L26
  walltime-calibration source -- one job, two purposes.

- **Probe exemption -- production cheaper than its own probe.** Where the
  production job is single-node, few-core and seconds long (a bulk baseline
  minimization, a capability check, a small static calculation), no separate
  probe is required: the production job IS the probe. The test is whether a
  probe would occupy a smaller allocation than the production -- if it would
  not, it buys nothing but a submit-wait-inspect round trip. Record the waiver
  and the numbers behind it in the run's thread.md; an unrecorded skip is still
  a violation. This exemption NEVER applies to a multi-node job, an array, or
  anything on `p.cmmg`. (2026-08-03, ni-dislocs-eam-meam-ace
  02_FISCHER-A0-AT-0K: 4000-atom fcc box, 1 core, s.cmmg, ~1-2 s.)

- **Walltime before ranks.** If a task does not fit its `--time`, extend
  `--time` first (cmmg max 4-00:00:00; padding is free and unused walltime is
  not billed -- L26). Add ranks only while atoms/core stays above the
  ~1000-2000 efficiency floor; halving atoms/core below that trades wall-clock
  for communication overhead and shared-node footprint. State atoms/core
  whenever proposing a rank change. (2026-08-06, ni-h-diffusivity hydride MSD
  arrays: the pilot doubled ranks 8 -> 16, i.e. 975 -> 488 atoms/rank, and set
  72 h rather than simply requesting 96 h at 8 ranks. Erik: "that would just
  stupidly increase the overhead of communications ... I think max runtime is
  96h." Both facts were already on record; the failure was not consulting them
  at decision time.)

- **Two mount failures, opposite fixes (extends L15).** Before calling a
  cluster mount "down", classify the failure:
  - *Permission* -- `stat` succeeds but reads or descents return `Operation
    not permitted`, or the device bridge reports a macOS access denial. The
    mount is FINE; a macOS TCC grant is stale, usually because the sshfs volume
    was re-mounted after the folder was connected to the session. Fix:
    re-connect the folder in the desktop app ("Add folder" re-issues consent
    against the current volume). Do NOT remount -- remounting replaces the
    volume a still-good grant points at, so it can invalidate a good grant.
  - *Staleness* -- listings empty or inconsistent between calls with NO
    permission error, exact-path reads often still working. This is L15. Fix:
    remount (`command_example` in `canon/clusters.yaml`).

  Both can be live at once. On 2026-08-20 the permission mode presented as a
  full top-level listing (55 entries) with EPERM on every descent; after the
  re-connect, textbook L15 appeared underneath it -- one `results` dir listing
  its six temperature subdirs by exact path while its sibling returned empty
  through 8 retries with backoff, and `du -sh` reporting 2.0M for a tree
  holding 47M in a single subdirectory. In either mode `sacct`, run by Erik,
  is the arbiter of what ran; never conclude data loss from an unreliable
  listing. **Escape hatch:** a harvest or status session that must enumerate
  large cluster trees is better run as Claude Code natively on the Mac (no
  desktop-app TCC gate, no bridge re-export), or driven by an
  `rsync`-over-ssh that enumerates cluster-side instead of through sshfs.

- **Background traversers on the mount (extends "Two mount failures").**
  Time Machine and antivirus scanners walk `~/cluster-mounts` unless
  excluded; exclude the tree once per machine (System Settings -> General ->
  Time Machine -> Options -> "+", or `sudo tmutil addexclusion -p
  ~/cluster-mounts`, plus the scanner's own exclusion list). The Cowork
  desktop VM (`com.apple.Virtualization.VirtualMachine`) keeps open handles
  on every file and directory a session has listed or staged under a
  connected mount subtree for the life of the session; those handles pin the
  volume, so quit the app (or disconnect the folder, when the UI allows it)
  BEFORE unmounting, and read a failed `umount` ("Resource busy") as "a
  session still holds it", not as a broken mount. Diagnostic that settles
  "the mount disappeared": mount-table entry + live sshfs process + live
  master + empty listing + a SUCCESSFUL exact-path `stat` = the L15 stale
  view -- remount and re-connect the folder; nothing is lost. (2026-08-25,
  M5: capture during a "disappearance" showed the transport fully alive, ~70
  VM handles on the volume including the files a session had staged minutes
  before, a Time Machine backup walking cluster-mounts from 12:59, and the
  wsavd scanner behind it; `ls` empty while `stat` on the project dir
  succeeded. Merged from proposal 2026-08-25-1200.)

- **Root cause of the empty-listing states (closes the loop on "Two mount
  failures" and "Background traversers").** macFUSE 5.3.x re-issues readdir
  with a non-zero offset on an SFTP directory handle sshfs has already
  exhausted; sshfs answers EMPTY and caches that answer (upstream fix:
  libfuse/sshfs PR #379, open as of 2026-08-25 -- re-test and drop the
  workarounds when a fixed release lands). The background traversers are an
  amplifier, not the cause: a recurrence 7 min after a fresh remount with
  Time Machine already excluded ruled them out as root cause. Separately,
  macFUSE >= 5.3 no longer daemonizes sshfs -- a mount command that seems to
  hang has usually SUCCEEDED, with sshfs sitting in that terminal's
  foreground, where a stray ctrl-C/ctrl-Z or a closed tab kills or freezes
  the mount (this retroactively explains the original "mount command hangs"
  report; both 2026-08-25 captures show sshfs with the shell as parent, S+).
  **THE FIX, deployed and holding (amended 2026-08-26, proposal
  2026-08-25-2010).** Do not prescribe the mitigations this bullet originally
  carried -- the evening of the same day measured them false:

  - The bug is **NOT macFUSE-5.3-specific**: it reproduced TWICE on macFUSE
    5.2.0, 4-7 min after fresh mounts. **A version downgrade is not a fix**
    (the 5.2 downgrade only restored sshfs daemonization).
  - **`-o dir_cache=no` does not fix it either.** `ls -la` exits 0 with
    correct `.` metadata and zero entries: the empty answer is generated
    fresh, not served from sshfs's cache. Keep the option (a blanked listing
    recovers on the next `ls` instead of staying blind), but it is a
    softener, not a cure.
  - **What actually works**: sshfs built from libfuse/sshfs **PR #379**
    (snapshot readdir), built by `DEVEL/build-sshfs-pr379.sh` and installed
    as `/opt/homebrew/bin/sshfs-pr379` alongside the untouched stock binary;
    `dotfiles/shell/cluster-mounts.zsh` prefers it and falls back to stock
    on machines that lack it. Deployed on M5 2026-08-25.
    **Confirmed by Erik 2026-08-26: mounts have worked without problems
    since.** Watch upstream and return to stock when a release carries the
    fix -- and re-test at depth before believing it.
  - Second, distinct flavor -- **stale file CONTENT** (a changed file serving
    old pages) -- is fixed by `-o auto_cache`; `attr_timeout=2` bounds the
    staleness to ~2 s past the next access.
  - **Diagnostic**: the mount table shows only kernel flags and can NEVER
    confirm mount options like `auto_cache`. Read `pgrep -fl sshfs` instead.

  (Diagnosed 2026-08-25 on M5 from two capture-script runs, 13:14 and 13:49.
  Merged from proposals 2026-08-25-1410 and 2026-08-25-2010. The amendment
  exists because the morning state of this bullet, merged hours earlier,
  actively misled a live session into re-prescribing a remount and a
  downgrade that were already known insufficient -- canon that is stale in
  the direction of "we fixed it" is worse than canon that is silent.)

- **Health-check a mount at the depth the work needs; a root listing proves
  nothing (extends L15).** A stale mount has been observed in a THIRD state:
  root lists correctly, depth-3 listings return empty, depth-4 listings
  BLOCK, while `stat` succeeds at every depth (it needs no readdir) -- and a
  remount changed the symptom without fixing it. Rules: (1) before a harvest
  or mirror, enumerate one LEAF directory of the tree the work touches and
  count entries against expectation; (2) a mirror is unverified until file
  counts are compared source vs destination per run directory -- rsync exits
  0 on an empty readdir; (3) after a remount, re-test at depth, do not
  assume; (4) when deep readdir blocks through the bridge, stop retrying and
  hand Erik a command for his own shell -- his shell talks to sshfs
  directly. (2026-08-05, ni-h-phase-diagram harvest: five of six run dirs
  mirrored, the sixth silently skipped, caught only by the file count.
  Merged 2026-08-25 from inbox 2026-08-05-1210.)

- **Harvest the failure mode, not just the failure count.** For every
  task that did not complete, the harvest records: the terminating
  error verbatim, the step it died at versus its nominal length, and
  the state of the observables immediately before. Distinguish
  walltime truncation (data usually usable, see L34) from numerical
  instability (data usable up to the onset, and the onset itself is a
  finding) from a setup bug (data void). Where an instability is
  method-specific, say which method is implicated and why the
  alternative is immune — that comparison is often worth more than the
  run that was intended. (2026-07-30, Ni-H thread 03 run 03: 4/6
  `fix gcmc` tasks died on `Non-numeric pressure` after first filling
  to the hydride shelf, x = 0.74–0.99. "4/6 failed" alone would have
  lost the four lower-bound compositions, the method-safety contrast
  with `fix mc/sites`, and the inference that low-mu is the safe
  direction to extend the grid — all three are results.)

## Thread design

- **List-of-co-equal mile-pebbles** is the right shape when a thread's
  scientific output is intrinsically a comparison between alternatives
  (e.g., two potentials, two cell sizes, two cutoffs). Architecture
  §13's "default one mile-pebble per thread, list permitted for
  legitimately co-equal artifacts" applies. The walk's framing
  (transcript 2026-05-29): *"Comparing two potentials is exactly that
  case — the two relaxed structures aren't a coincidence of grouping,
  they ARE the comparison."* For a comparison of N potentials at the
  same observable, prefer one thread with N co-equal mile-pebbles over
  N parallel threads with one mile-pebble each — keeps the comparison
  visible in one place.
- **NN_ prefix should encode real sequence, not parallel
  alternatives.** If `01_` precedes `02_` it should mean "02 depends
  on or follows 01". For genuinely parallel work (EAM vs MEAM in
  Thread 01's case), use co-equal mile-pebbles within one thread
  instead of parallel NN_ threads.

- **A deviation from Erik's stated design is a reportable event.** If the
  implementation cannot do what he asked, or the pilot judges something else
  better, that is raised BEFORE submission and recorded in thread.md with the
  reason. Silently substituting a mitigation for a specified boundary
  condition, geometry or constraint puts an unreviewed change into production,
  and the record afterwards shows only the substitute -- so the deviation
  cannot be found by reading the project, only by remembering the
  conversation. (2026-08-20, ni-h-hydride-cycle-eam: a requested wall and
  non-periodic z became `boundary p p p` plus a `fix evaporate` cleanup,
  unrecorded, across four production runs. L41.)
- **A run design is not complete until the output set and its cadence are on
  the record in thread.md** -- see preferences.md "Output style".

## Process

- **Mac is the durable home; cluster mount is the active workspace
  (bidirectional rule, with curated inbound).**
  When the cluster mount is available (e.g., cmmg under
  `~/cluster-mounts/cmmg/`):
  - **Outbound (Mac → cluster):** cluster-bound files an sbatch will
    read — LAMMPS inputs `.in`, submit scripts `.slurm`, parameter
    files — get written directly into the mounted cluster tree.
    If unavailable, stop and tell Erik.
  - **Inbound (cluster → Mac) — CURATED, not kitchen-sink.** Pull only
    inputs, run scripts, results data tables (`.dat`), summaries
    (`.yaml`), plots (`.pdf`), and comparison reports (`.md/.pdf`).
    Skip per-iteration LAMMPS `.log` files, `log.lammps`, slurm
    `.err`/`.out`, `start_time.txt`/`end_time.txt`, macOS AppleDouble
    sidecars. Use
    `canon/templates/mirror-cluster-to-mac-curated.sh` (rsync with
    filter rules so noise can't sneak in). Do NOT use plain
    `rsync -av <cluster>/ <mac>/`.
  - **Structures (`.data`, `.data.zst`, `.dump`) — ASK FIRST.**
    Mile-pebble structures are pulled to Mac compressed via
    `zstd -19` per the Thread-01 pattern, but only after explicit
    confirmation from Erik that the given structure is worth
    archiving. Default: not copied.
  - **Mount missing?** Stop, ask Erik to mount. Don't silently
    work Mac-only and defer.

  Surfaced 2026-05-31 (Thread 02 of ni-a0-cij-eam-meam): pilot got
  outbound wrong (Mac-only writes), then over-corrected on inbound
  (kitchen-sink `rsync -av` brought in 78 per-iteration logs +
  slurm noise + AppleDouble sidecars). See clusters.yaml cmmg quirks
  `cluster_bound_files_write_via_mount` (outbound) and
  `important_outputs_mirrored_to_mac` (inbound, curated).
- **A fix applied on one side lands on both sides, in the same turn.** When
  an input, script or parameter file is corrected on the cluster (or on the
  Mac), the corrected file is mirrored to the other tree immediately and the
  two copies `cmp`'d -- the survivability tier must never hold only the
  broken version. A later session that finds the two copies divergent treats
  the divergence itself as a defect to record, not as noise. (2026-08-20/24,
  ni-h-at-dislocs: the `group HGRP clear` fix that unblocked probe 22344727
  was applied on the cluster on 08-20; the backed-up Mac .in still carried
  the exact defect four days later, so the only backed-up copy of that input
  was the broken one. Merged 2026-08-25 from inbox 2026-08-24-1558.)
  Cheap second half that catches the case where the fixing session never
  comes back: a session RESUMING a thread whose last recorded event was a
  FAILURE `cmp`s the Mac and cluster copies of that run's inputs before
  proposing anything.
- **Analysis plan before data-collection plan.** When the work involves
  extracting a number (or set of numbers) from simulation data —
  Cij from σ-ε slopes, a₀ from box length at minimum, anything
  curve-fit — propose the *analysis pipeline first* (what's being
  fit, to what function, what's being verified, what residuals are
  reported) **before** proposing the simulation setup (strain
  magnitudes, sample counts, output schema). The simulation setup
  follows from the analysis pipeline, not the other way around.
  Surfaced 2026-05-31 during Thread 02 design: pilot proposed strain
  magnitudes + counts before stating the fit function. Erik flagged
  it. The correct order is *what would I fit?* → *what data does
  that fit need?* → *what runs produce that data?*. Cross-reference
  L24 (fit one order higher than target coefficient).
- **Scalar-reducing helpers name their convergence filter; a verdict for
  question A is not a quality label for question B.** Any helper that
  REDUCES a data set to a headline scalar -- an interpolated crossing, a
  fitted slope, a plateau value, a coexistence mu -- either restricts its
  inputs to points free of the failure modes that corrupt THAT quantity
  (named explicitly at the call site), or takes an explicit
  `allow_unconverged=True` and carries that fact in its returned object so
  it reaches the figure caption and the .dat header. The filter is
  per-quantity, not a generic `converged` boolean: `filling` corrupts an
  x(mu) bracket but `noise-limited` does not corrupt a ln-x fit -- on
  2026-08-05 filtering a dilute-branch fit on generic `converged` removed
  every noise-limited point and turned a failed slope check into a
  reported pass. Diagnostic and plotting helpers stay unrestricted: the
  open markers are the point of drawing them. Fix the helper (the rule),
  not the one number. (Merged 2026-08-25 from inbox 2026-08-24-0930 --
  `nih_loaders.mu_at_half()` interpolated the project's mu(x=0.5) through
  run 09's `filling`-flagged point, 1.9 meV -- and 2026-08-05-1105, the
  ln-x fit above. Same class as the 2026-08-05 sigma_x withdrawal.)
- **Per-timestep thermo dump for FFT diagnostics on dynamics runs.**
  Whenever a thread runs NVE / NVT / NPT (or any MD that's coupled to
  a thermostat or barostat), include a per-timestep dump of temperature,
  pressure components, and box dimensions — separate from the block-
  averaged production thermo. The high-frequency series enables a
  post-hoc FFT to detect spurious frequencies introduced by the
  thermo/baro coupling (Nose-Hoover chain ringing, barostat
  oscillations, near-resonant Tdamp/Pdamp choices). Channels to
  always include: `step`, `temp`, `press`, `pxx`, `pyy`, `pzz`,
  `lx`, `ly`, `lz`, `pe`, `ke`. Use `fix print` (no averaging,
  every step) writing to a descriptively named file (e.g.
  `Ni-fcc-...-NPT300K-thermo-per-step.dat`). Keep this separate from
  the `fix ave/time` block-averaged stream used for production
  statistics. Surfaced 2026-05-31 (Thread 03 design, ni-a0-cij-eam-meam).
- **Fix the class, not the instance.** When a bug is diagnosed in an
  input or script, the failing line is evidence the *pattern* exists,
  not a one-off. The fix is not complete until all three of:
  1. **Sweep** — grep the exact anti-pattern across every input/script
     in the thread (and the project where the same idiom is reused) and
     fix every hit in one pass, not just the one that aborted.
  2. **Record exemptions** — any instance deliberately left unfixed is
     named with its reason (e.g. a completed run's `.in` whose data
     predates the bug and won't be re-run), so "still present" reads as
     a decision, not an oversight.
  3. **Net** — add or extend a mechanical pre-flight/lint check in
     `style/*` so the pattern is caught before submit next time.
  Surfaced 2026-06-01 (Thread 03 of ni-a0-cij-eam-meam): the
  `$(step:%d)` format bug (L31) had bitten across all four runs; Erik
  flagged that patching the single failing line is the wrong frame —
  the sweep + lint must be standing practice, not a lucky habit. That
  session did sweep the six re-run inputs, exempted run 03's `.in` on
  the record, and added the §1.12 lint; this rule promotes that to
  mandatory. Cross-ref L30/L31 and `style/lammps.md` §1.12.
- **When suggesting a simulation duration, include the wall-time scaling
  context.** Whenever the pilot proposes "X ps of MD" or "Y steps" in a
  design discussion, the proposal must come with: (a) planned number of
  cores, (b) atoms per core (= N_atoms / n_cores), (c) expected wall-time
  on those cores based on a known or estimated step-rate, and (d) an
  explicit invitation to trade: more cores -> shorter wall, fewer atoms
  per core, possibly with worse parallel efficiency once below
  ~1000-2000 atoms/core. Format:

    "100 ps NPT for 4000 atoms on 1 core (4000 atoms/core, EAM ~70 ts/s
     observed -> ~24 min wall). On 4 cores (1000 atoms/core) the same
     work would run in ~6-8 min depending on parallel efficiency; let me
     know if you want to trade duration for cores."

  This lets Erik decide simulation length vs. core count BEFORE the
  sbatch is written. Surfaced 2026-05-31 Thread 03 (ni-a0-cij-eam-meam)
  — he flagged that the bare "100 ps NPT" suggestion lacked the
  hardware context needed to plan around result-arrival time.

- **Pre-submit summary precedes every sbatch hand-off.** Before
  presenting sbatch commands for Erik to run, the pilot includes a
  short summary block stating, per job:
    - `--time` (requested walltime, the SBATCH cap)
    - estimated real run-time (based on observed steps/sec)
    - whether the job is array (and # tasks; tasks run in parallel)
  Followed by an aggregate line: total elapsed wall-clock from sbatch
  -> last job done (accounting for parallel tasks), and a "come back
  at ~HH:MM" hint. Only after this block come the `sbatch ...` lines.

  Surfaced 2026-05-31 Thread 03: Erik couldn't tell from the resubmit
  hand-off when to check back. The right hand-off format is:

    "Jobs to submit:
       NPT-EAM         --time 1h30m, ~34 min est.
       NPT-MEAM        --time 5h,    ~3.3 h est.
       NVT-MEAM-scan   --time 4h/task, ~2.4 h est. per task (array 0-4 in parallel)
     Longest path dominates: NPT-MEAM (~3.3 h after the job starts).
     Come back around HH:MM (assuming ~0 queue delay on s.cmmg)."

  (Rule surfaced 2026-05-31 Thread 03.)

- Don't ask about deadlines unprompted (it adds pressure).
- Don't reframe LLM-LMPS as RDM or as a workflow tool.
- **Tag every response with exactly one hat — `**[Pilot]**` or
  `**[Designer]**`.** The combined `[Designer+Pilot]` tag is BANNED from <!-- lint-ok:role-tag -->
  responses; `designer+pilot` is only a SESSIONS.md capability (= "may do
  both"), never a response tag. The hat follows the work (project/cluster
  → Pilot; canon/ARCHITECTURE/tool-cards → Designer). One
  response = one hat: if a turn needs both, do the dominant part, tag it,
  and queue the other for a switch — never blend them. Announce every
  switch on its own line under the tag (`↳ switched Pilot→Designer: …`).
  Switch triggers: Erik-explicit, or auto when the next action belongs to
  the other role and the session holds that capability. Canonical in
  ARCHITECTURE.md §17.5; lint `canon/templates/lint-role-tag.sh`.
  (2026-06-01 Ni-hydride: Erik rejected the combined tag — the old canon
  had sanctioned it. Supersedes the 2026-05-31 three-tag wording.)
- **Run the session-startup ritual at the first substantive turn of
  every new conversation.** See `canon/session-startup.md` for the
  six steps (read SESSIONS.md, ask mode+scope, cross-check, self-
  register, brief Erik, proceed) and ARCHITECTURE.md §17 for the
  concurrency rules. Re-run on mid-session scope switches.
- **Pilots use the proposals-inbox, not direct edits, to surface
  new rules.** A pilot session that discovers a new lesson,
  preference, style rule, or feedback memory writes a proposal to
  `canon/proposals-inbox.md` with the schema documented there. The
  designer session merges proposals into canonical files in batches.
  Exception: a designer+pilot session writes directly. See
  ARCHITECTURE.md §17.7.
- **Verify writes, do not infer them.** A file write is reported as
  done only after it has been read back — size or checksum on the
  files actually touched. If the check cannot run (bridge down, mount
  stale, call timed out), say **UNVERIFIED** and name what needs
  confirming; never translate "the write came earlier in the script
  than the crash" into a confidence estimate. A hedge attached to a
  wrong probability still sends Erik down the wrong path.
  (2026-07-30: run 11's generation call died mid-script; the pilot
  reported the files "almost certainly completed" — they did not
  exist, and Erik hit `No such file or directory` mid-submit.)
- **No bulk sweeps over the sshfs mount.** Checksumming or stat-ing
  tens of files across the mount can hang it and take the whole device
  bridge with it (2026-07-30: a 21-file md5 sweep was enough — even
  `echo` failed for minutes afterward). Verification does not require
  checksumming everything: spot-check the files just written, one or
  two per directory, by exact path, and prefer `wc -c` over `md5sum`
  for large files. See also the write-side stale-listing quirk in
  `clusters.yaml` (never enumerate freshly written files with
  rsync/find/globs).
- **The Cowork task list is not durable storage.** Task entries evaporate
  between conversations. When a lesson, decision, or design note surfaces,
  write it to its destination plain file (`canon/lessons.md`,
  `canon/style/*.md`, `ARCHITECTURE.md`, or `thread.md`) at
  the moment it surfaces — never park it in a task list with a "drain
  later" plan. Incident: 2026-05-29 → ~7 of 21 walk lessons lost between
  sessions because they were only in task #9.

## Tools

- **Reach for a registered tool before hand-rolling.** At session start
  the `canon/tools/tools-catalog.md` index is in context. If a tool
  covers the operation, read its `canon/tools/<id>.card.yaml` and use it
  via the card's `exec.mac` / `exec.cluster` invocation — don't re-derive
  usage or re-implement the operation by hand. Respect the card's
  `run_where`: `large-data->cluster` tools (lego, dcreator on real data)
  never run on the Mac; prepare a cluster invocation through the §8 bridge.
- **Promote a repeated hand-operation to a tool after 3 uses.** If the
  pilot hand-rolls the same operation (a file/log parse, a structure
  transform, a curated mirror, a check) **3+ times** across threads or
  projects, file a `canon/proposals-inbox.md` entry proposing promotion to
  a tool. The designer session then writes the tool card (and, for an
  internal tool, creates/extends its own repo with code + tests, deployed
  to `~/bin/` on Mac and cluster). **Canon holds only cards, never tool
  code** (ARCHITECTURE.md §6 "tools registry"). Same pilot→inbox→designer
  flow as lessons/style. Established 2026-06-01.
- **Re-probe a stale tool card before trusting it.** External tools
  (LEGO, LEGO-TOOLS, dcreator) keep changing. If a card's `last_verified`
  is old or an invocation fails unexpectedly, re-probe (`tool --version` /
  `--help`) and re-stamp the card rather than guessing — the same
  "look it up, don't guess" discipline as for LAMMPS syntax.
- **A tool card is a mechanical contract, not a usage recipe.** Cards
  carry invocation, parameters, and I/O only. Do NOT bake in domain
  strategy ("to do X, run the tool twice like so…") — that is Erik's to
  supply per-thread, or lives in lessons/preferences once he confirms it.
  Likewise, only document real installed tools: example scripts shipped
  in a repo's `examples/`/`scripts/` subfolder are not tools and don't go
  on a card. Both established 2026-06-01 (Erik, on the lego/dcreator cards).

### Tool behaviour -- `fix mc/sites` / `compute sites/voronoi`

- **`metric volume` is the PROBE's Voronoi cell, not the interstice
  polyhedron.** It measures how much room a particle placed at the site would
  own, bounded by the surrounding host atoms -- not the geometric volume of the
  octahedron or tetrahedron. In fcc it is nearly constant (5.35-6.04 A^3 at
  a = 3.524 A) and the oct and tet populations OVERLAP (oct mean 5.47, max 5.92;
  tet mean 5.79, min 5.58), so it CANNOT classify sites. Do not design a
  classifier around the textbook `a^3/6` vs `a^3/24` factor 4; that factor is
  not what this column reports. Classify on **clearance AND coordination**,
  requiring both to agree: oct = clearance ~ a/2 (1.725-1.763 A) and
  `coord 4 3.0` = 6; tet = clearance ~ a*sqrt(3)/4 (1.500-1.538 A) and
  coord = 16; everything else core-distorted. The check that it is right is
  that a perfect-lattice region returns the fcc 1:2 oct:tet ratio -- measured
  33.31 % : 65.92 %, leaving 0.77 % core-distorted, which is the population the
  science is usually about. Verified 2026-08-04 on 953534 sites
  (ni-h-at-dislocs-eam-meam thread 01); the first symptom of the wrong
  classifier was 100 % of sites landing in "oct".
