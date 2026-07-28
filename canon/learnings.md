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

## Cluster discipline

- **Probe before production for any edited or new LAMMPS .in.**
  Before any production sbatch where the LAMMPS input has been
  edited since its last successful run on this cluster, the pilot
  submits a probe job first. The probe is a clone of the production
  .in with all step counts reduced to 10 and the fix ave/time
  cadence reduced (Nevery=2 Nrepeat=5 Nfreq=10) so all output paths
  fire within the probe length. 2 MPI tasks (catches parallel-decomp
  bugs), 5-min walltime, same partition as production. The probe
  must:
  - exit cleanly (`.err` empty, `.out` ends with "PROBE DONE")
  - reach the "PROBE marker: ALL PHASES COMPLETE" line in the log
  - emit a `Performance:` line for each `run` block (used to scale
    the production walltime per L26)

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
