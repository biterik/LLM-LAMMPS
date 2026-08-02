# LLM-LMPS - Active sessions dashboard

Single source of truth for "who is working on what right now." Each
LLM-LMPS Cowork session self-registers on startup, updates
`last_active` on major actions, and migrates from `active` to
`recently_closed` on wrap-up.

See ARCHITECTURE.md §17 (Concurrency model) for the rules and
`canon/session-startup.md` for the startup ritual.

last_index_updated: 2026-08-02T16:29Z

---

## active

### session_id: 2026-08-02-1409-nih-resume
- mode: pilot
- scope: |
    Ni-H-PHASE-DIAGRAM-EAM-MEAM — resume per 2026-07-30-0926 handoff:
    verify cluster job state (sacct), harvest completed arrays, settle the
    run-11 site-catalogue question, loader extensions, notebooks.
- started: 2026-08-02T14:09Z
- last_active: 2026-08-02T15:50Z
- simulation_root: ~/Desktop/SIMULATIONS   # on M5; confirmed by Erik (path de-identified 2026-08-02, lint-no-identity)
- machine: M5
- owns_writes_to:
  - SIMULATIONS/Ni-H-PHASE-DIAGRAM-EAM-MEAM/**
  - cluster-mounts/cmmg/Ni-H-PHASE-DIAGRAM-EAM-MEAM/** (proposed writes, confirmed first)
- in_flight: |
    CLUSTER: run 06 gcmc-long production (14 tasks, 48 h) and run 11
    oct-only production (5 tasks, 24 h) submitted by Erik ~2026-08-02 14:30Z
    after both probes passed. Runs 08/09/10 (EAM) + thread-04 run 04 (MEAM
    600K fine) all COMPLETED and mirrored (MEAM 04 after Erik's remount).
    NEW WAVE deployed to cmmg 2026-08-02 ~15:45Z, probes handed to Erik,
    productions NOT yet cleared: run 12 EAM tet-onset (7 tasks), run 13
    gcmc collapse-traj (3 tasks, crashes EXPECTED = data), MEAM run 05
    transition-traj (6 tasks). All three carry new V_oct/V_tet columns.
- notes: |
    designer lock NOT taken; 14 proposals pending in canon/proposals-inbox.md.
    Done this session: curated mirror of the 2026-07-30 wave; nih_loaders.py
    (results_seeded + acc.dat loaders); method-cost + shelf/overcharge
    analysis scripts, figs 6-8 + 4 isotherm .dat exports; OVITO structure
    set (11 .zst) pulled to Mac; thread 03/05 records updated.

---

## recently_closed

### session_id: 2026-08-02-1614-designer-inbox-merge
- mode_at_close: designer                 # designer lock RELEASED at close — now free
- scope: |
    Framework canon only: merge the pending canon/proposals-inbox.md batch
    into lessons/learnings/preferences/clusters.yaml. Cloud Cowork session
    on M5's connected folders; no project, simulation-folder, or cluster work.
- closed: 2026-08-02T16:29Z
- machine: M5
- summary: |
    13 of 14 pending proposals MERGED (inbox statuses + designer review
    notes written). lessons.md: L32 count() 2nd arg is a REGION-ID; L33
    thermo keyword in a fix-consumed variable must be in thermo_style;
    L34 derived analysis windows + truncation-is-not-non-convergence
    (detrended drift test); L35 match MC cadence, not budget.
    learnings.md: "harvest the failure mode" (Cluster discipline);
    "verify writes, don't infer them" + "no bulk sshfs sweeps" (Process).
    preferences.md: NEW "Concentration convention" (x = N_H/N_Ni
    headline, c = N/M diagnostic only); NEW "Command hand-offs" (absolute
    paths, copy-paste blocks, state where it runs — the two duplicate
    proposals combined into one); Plot defaults + one-series-per-condition
    (filled/open markers, sampling-density corollary) + region-of-interest
    companion plots (named cut constant, plateau-aligned zoom).
    clusters.yaml cmmg: quirk sbatch_rejects_get_user_env_argument_form
    added AND the stale `get-user-env: L` REMOVED from sbatch_defaults
    (it would have kept regenerating the bug); write-side sshfs
    stale-listing variant appended to the L15 quirk.
    EXTRA (scrub boundary): lint-no-identity flagged two /Users/<name>
    simulation_root lines pilot sessions had written into SESSIONS.md;
    de-identified to ~/Desktop/SIMULATIONS. All three lints clean at
    close. Git commit prepared and handed to Erik (strict-A; not run by
    this session).
- handoff_to: null
- next_session_must: |
    ONE proposal remains pending: 2026-07-30-0934
    promote-mu-scan-loader-to-tool. Accepted in principle, but canon
    holds cards only for real installed tools — the loader needs its own
    repo + code + tests deployed to ~/bin (Mac + cluster) FIRST (designer
    session with tool-implementation scope, or Erik via Claude Code
    against a spec), then the card + tools-catalog entry follow.
    Small designer crumbs still open: (a) DEVEL/CLAUDE.md still points at
    ~/Desktop/DEVEL/ though the tree lives at ~/DEVEL/ — unreachable from
    this cloud session (DEVEL root not a connected folder); one-line fix
    from any on-computer session. (b) The four tool cards' last_verified
    stamps are still README-derived; re-stamp on first live invocation.
    (c) ARCHITECTURE §17.10 open questions (stale-session GC, register
    race) deferred by design.

### session_id: 2026-07-30-0926-nih-harvest-notebooks
- mode_at_close: pilot
- scope: |
    Ni-H-PHASE-DIAGRAM-EAM-MEAM. (1) Harvested the 2026-07-29 evening wave
    (5 arrays). (2) Rebuilt/extended the three Jupyter analysis notebooks.
    (3) Prepared a new 7-run / 49-task wave and handed the submit sequence to
    Erik. No framework edits (designer lock NOT taken).
- closed: 2026-07-30T11:44Z
- simulation_root: ~/Desktop/SIMULATIONS   # on M5; confirmed by Erik (path de-identified 2026-08-02, lint-no-identity)
- machine: M5
- summary: |
    HARVEST. 21589858 MEAM fine 300K 8/8 clean; 21589859 EAM 600K 11/11 clean;
    21589860 MEAM 600K 15/15 clean; 21589050 EAM gcmc-matched 2/6 clean +
    4/6 CRASHED on `Non-numeric pressure` (each had first reached the hydride
    shelf, x = 0.74-0.99); 21589892 EAM tet-ext walltime-truncated at step
    26120/80000 but CONVERGED (x = 2.514 +/- 0.003, a = 4.0694 A, superseding
    run 01's still-climbing 2.403 at the same mu).
    Delta_mu(gcmc - mc/sites) <= +0.175 eV; the previous [+0.26,+0.32] bracket
    WITHDRAWN. First T-dependence in the project: plateau shifts -0.098 eV (EAM)
    vs -0.038 eV (MEAM) over 300 K, but the MEAM value is NOT significant on its
    0.10 eV grid.
    NOTEBOOKS. thread-03 v3, thread-05 300 K v3, plus a NEW thread-05
    T-dependence notebook. 16 figures, all also .pdf. Per Erik's feedback:
    coarse+fine scans MERGED into one isotherm per condition with open symbols
    for unassured convergence, and new tet-excluded transition-regime plots.
    The merge exposed that the EAM has ZERO grid points inside its own 300 K
    two-phase window (MEAM has four), so "the MEAM transition is broader" is
    currently a statement about grid resolution, not about the potentials.
    Fixed a latent loader bug on the way (MEAM runs use ntrials 400, not 1000).
    NEW WAVE PREPARED, 7 runs / 49 tasks, .in + .slurm on the cmmg mount and
    mirrored to the Mac, generator kept as
    03_.../_gen_runs_2026-07-30.py. Key design point from Erik: gcmc cadence
    changed to `fix gcmc 20 1000` (50 att/MD step) so it MATCHES
    `fix mc/sites 20 1000` -- run 03 had matched only total attempts, not
    cadence. OVITO output added to every run (dump custom id type x y z at
    %.4f, 43-45 frames/task, ~1.0 GB on PTMP) plus a final write_data snapshot.
    MY TWO ERRORS THIS SESSION, both caught by controls rather than by me:
    (1) I recommended raising overlap_cutoff 1.2 -> 1.5 A on a geometric
    argument; the probe destroyed the cell in 900 steps at a DILUTE
    composition (N_H -> -10526, box to a = 3.22 A) while the 1.2 A control
    passed clean at the identical cadence. Reverted; no mechanism is claimed.
    (2) A bulk md5 sweep over the sshfs mount wedged the device bridge, the
    generation call died before writing run 11, and I reported it as "almost
    certainly completed" -- it was not, and Erik hit `No such file or
    directory` mid-submit. Both filed as canon proposals.
- state_at_close: |
    CONFIRMED RUNNING (output dirs present on the cluster):
      08 mcsites seed replicas -- 12 tasks, results_seeded/ has 12 dirs
      09 ultrafine d0.005      --  5 tasks, results/ has 5 dirs
    SUBMITTED BUT UNCONFIRMED (sshfs readdir was stale; verify with sacct):
      10 EAM 600 K fine        --  4 tasks
      04 MEAM 600 K fine       --  6 tasks
    QUEUED, NOT YET CHECKED:
      06 gcmc-long re-probe (mu -2.14, cutoff now 1.2) -- Erik queued it at
         close. Its production array (14 tasks) MUST NOT be submitted until
         that probe is checked: want ALL PHASES COMPLETE=1, MAXENERGYTEST=0.
    IN FLIGHT AT CLOSE:
      11 oct-only probe (mu -1.99, rmin 1.70) -- at step 1840/2200, x = 0.9999
    DELIBERATELY DROPPED:
      07 gcmc oc12 production -- with 06 reverted to cutoff 1.2 its 3 tasks
         would be bit-identical duplicates. Its PROBE passed and is the
         evidence that 1.2 A is safe and 1.5 A is not; keep the logs.
- handoff_to: null
- next_session_must: |
    Re-run the startup ritual. Resume as PILOT on ni-h-phase-diagram-eam-meam.

    1. CHECK STATE FIRST. `sacct` for the week; confirm whether 10 and MEAM 04
       actually got submitted (stale readdir prevented confirmation at close),
       and whether Erik submitted 06 and 11 after their probes.
       Do NOT run bulk md5/stat sweeps over the sshfs mount -- that is what
       wedged the bridge this session. Spot-check by exact path instead.

    2. THE HEADLINE RISK, check this before writing any findings. Run 11's
       probe reached x = 0.9999 at mu = -1.99 with a STABLE catalogue
       (c = N/M = 0.9995 => M ~ 10976 = exactly the octahedral sublattice),
       where run 01/05 at the SAME mu and the SAME seed gave x = 2.514.
       If the full run 11 confirms it, the tetrahedral overcharge is a
       SITE-CATALOGUE ARTEFACT, not a prediction of the EAM -- and the
       project's headline EAM-vs-MEAM discrimination has to be restated in
       thread-04 findings, thread-05 both notebooks, and project.md open
       question 2. Do not propagate the x = 2.514 result further until run 11
       is harvested.

    3. LOADER WORK REQUIRED BEFORE HARVEST -- the notebooks will silently find
       nothing otherwise:
       - run 08 writes to `results_seeded/mu<MU>_seed<SEED>/`, NOT
         `results/mu<MU>/`. `load_scan()` globs `results/mu-*` and will return
         an empty frame. Extend it, and give the seed its own column.
       - gcmc runs now also write `acc.*.dat` (step + 4 cumulative acceptance
         counters). No loader reads it yet; it carries the acceptance data that
         explains the cost ratio in Erik's (b) figure.
       - run 09 uses 3-decimal mu dirs (mu-2.285). `float(d.name[3:])` handles
         that already -- no change needed, just don't "fix" it.
       - `traj.*.lammpstrj` and `final-state.*.data` are new; they are in
         canon's "structures -- ASK FIRST" class, so they stay on the cluster
         unless Erik names the mu worth pulling (zstd them if so).

    4. STILL UNVERIFIED: the `f_GC[3..6]` index assumption behind the gcmc
       acceptance counters. Probe ave/acc windows do not close inside 2200
       steps, so no probe can settle it. Check the FIRST production
       acc.*.dat from run 06: ins_att and del_att must rise monotonically.
       Nothing else depends on them, so a wrong index cannot corrupt the
       isotherm.

    5. THEN the science, in the order the runs answer it:
       - run 06 -> Delta_mu from BELOW + the rigid-shift test (does
         c(mu_gcmc - Delta_mu) superpose on c(mu_mc/sites)?). Note Delta_mu
         contains the ideal-gas kT*ln(rho*Lambda^3) term, so it is a constant
         at fixed T and is NOT transferable to 600 K.
       - runs 06 + 08 -> Erik's (b): attempts needed by each method to reach
         the same composition at nearly-empty / in-gap / nearly-full. Both are
         80000 steps at 50 att/step, so the axes need no rescaling.
       - run 09 -> does the EAM have any intermediate composition at 300 K?
       - runs 10 + MEAM 04 -> make dmu_M/dT significant.
       - run 11 -> item 2 above.
       WAVE 2, only after Delta_mu is known: multi-seed gcmc (~12 tasks) at the
       mu matched to run 08's three target compositions, so the gcmc half of
       the (b) figure gets the same n=4 error bars the mc/sites half has.

    6. 14 proposals PENDING in canon/proposals-inbox.md for a designer session
       (the 15th `status: pending` match is the format template's placeholder,
       not a real entry). EIGHT of the 14 were filed today: derived analysis
       windows + truncation-is-not-non-convergence; harvest the failure mode;
       promote the mu-scan loader to a tool; one-series-per-condition plots;
       exclude-the-runaway-branch companion plots; match MC cadence not budget;
       sshfs write-side stale listings; verify-writes-do-not-infer-them).

    7. HOUSEKEEPING: `.mount-write-probe` left in run 06's cluster dir (rm is
       refused through the mount -- delete from a real shell).
       SIMULATIONS/_to_delete/ holds 21 superseded figures + 3 transfer
       tarballs, safe to remove.

### session_id: 2026-07-29-1154-sim-ideas-backlog
- mode_at_close: pilot
- scope: |
    (1) SIMULATION-IDEAS.md backlog created at SIMULATIONS/ root.
    (2) Ni-H-PHASE-DIAGRAM-EAM-MEAM pilot scope (taken over from
    2026-07-28-1712): harvest + analysis + next production wave.
- closed: 2026-07-29T16:52Z
- summary: |
    Big day. (1) IDEAS: SIMULATION-IDEAS.md created with four Ni-H idea
    families A-D (H diffusion; (100)-block uptake/degas; single
    dislocations incl. jogs; surface hydride islands), lit-scanned
    (closest prior art: Leon-Cazares et al. 2025 MD-GCMC nano-hydrides
    preprint overlaps C.1 - read before scoping; C.2/C.3 and D look
    open). Decisions: degassing always thin-film w/ free surfaces;
    B.1 dislocations from real load/unload.
    (2) Ni-H HARVEST: overnight wave verified 41/41 clean. Thread-01 0K
    anchors done (MEAM NiH a0=3.73000 A, new; dE/H anchors -2.383 EAM /
    -2.290 MEAM match the 300K onsets); EAM fine scan: plateau
    -2.29..-2.26, tet overcharge at -1.99 (unconverged); MEAM coarse:
    plateau -2.30..-2.20, NO tet overfill to -1.60 (first
    potential-discriminating result). 90 files curated-mirrored + 4
    mile-pebble structures pulled (zstd). Two executed notebooks
    committed (thread 03: c(mu) by method, Delta_mu, trials-axis
    filling dynamics, per-mu convergence panels; thread 05: EAM-vs-MEAM
    c(mu), a(mu), a(x), dE-per-H). x=H/Ni convention fixed
    (proposals-inbox). Run records written 01/03/04; threads -> open.
    T-ladder reduced to 300+600 K (project.md).
    (3) NEW RUNS: gcmc-matched-1M-trials array RUNNING (21589050;
    mid-run: gcmc plateau below -2.10 -> Delta_mu <~ +0.16 eV; tasks
    will hit the 12h wall ~step 300-360k, benign; mu=-1.60 box-collapse
    anomaly logged - watch at harvest). Four runs PREPPED with probes
    PASSED (MEAM fine 300K; EAM 600K; MEAM 600K; tet-ext 4x @64 ranks
    after L26 recalibration) - production sbatch commands handed to
    Erik, submission possibly tonight.
- handoff_to: null
- next_session_must: |
    Re-run the startup ritual; resume as PILOT on
    ni-h-phase-diagram-eam-meam. FIRST: harvest the five arrays
    (21589050 gcmc-matched + the four if Erik submitted: MEAM-fine-300K,
    EAM-600K, MEAM-600K, tet-ext). Expect gcmc tasks TIMED OUT near
    step 300-360k WITHOUT "JOB DONE" markers - that is expected, not a
    failure; analyze data-through-step-N from ave/trace. Check the
    mu=-1.60 box-collapse anomaly (thread-03 run-03 record). Then:
    curated mirror, extend notebook v2 with equilibrated Delta_mu +
    method comparison, 600K isotherm analysis, thread.md run records.
    Open design work (co-develop, do NOT clone): thread-01 groups B
    (E(c) random fillings) + C (dilute H, H-vacancy, 0K Cij),
    discharge/hysteresis branch, oct-only window control. Also pending:
    file SCIENCE-KICKOFF-NIH-STOICHIOMETRY.md into the project (Erik
    deferred); 2 proposals in canon/proposals-inbox.md await a designer
    session (x=H/Ni convention; absolute-paths command hand-offs).

### session_id: 2026-07-28-1712-Ni-H-isotherms
- mode_at_close: pilot
- scope: |
    Ni-H-PHASE-DIAGRAM-EAM-MEAM — resume after the MC-SITES-LAMMPS detour.
    Reconciled SCIENCE-KICKOFF-NIH-STOICHIOMETRY.md with the thread 02/03
    plans, settled the cmmg directory layout, ran the kickoff planning and
    the prepped thread-01 0K anchors.
- closed: 2026-07-29T12:39Z        # taken over by successor; conversation had ended without wrap-up
- summary: |
    Submitted and partially harvested the first production wave on cmmg
    (probes passed first; two bug classes fixed pre-production, see
    proposals-inbox): 0K anchors EAM+MEAM COMPLETE (MEAM NiH
    a0 = 3.73000 A, new number); EAM gcmc shift reference (8 pts
    -2.70..-2.00) COMPLETE; EAM 14x14x14 300K fine mu-scan (18 pts
    -2.50..-1.99 d0.03, plateau bracketing -2.26..-2.23 mid-run) and
    MEAM coarse mu-scan (15 pts -3.00..-1.60, onset -2.3..-2.2) left
    RUNNING overnight. Analysis tail (mirror, equilibration checks,
    c(mu)/a(mu) plots, gcmc shift fit) and thread.md run records NOT
    done — handed to successor.
- handoff_to: 2026-07-29-1154-sim-ideas-backlog

### session_id: 2026-07-28-consolidate-one-tree
- mode_at_close: designer                 # designer lock RELEASED at close — now free
- scope: |
    Second maintenance pass, same day. Consolidate to a single working
    tree, seed the remaining clusters, and correct two wrong claims the
    earlier pass wrote into canon.
- closed: 2026-07-28T00:00Z
- summary: |
    (1) CORRECTIONS to my own earlier canon edits, both wrong:
        - "a cloud session cannot add folders mid-session" — false. Erik
          added SIMULATIONS/ and cluster-mounts/cmmg/ mid-session with
          "Add folder" and both became visible immediately. Step 0(c) and
          the README now say so, and route a missing folder to "ask Erik
          to add it" rather than "restart elsewhere".
        - "identify the machine with hostname" — impossible. A Cowork
          shell runs in an isolated Linux VM; `hostname` returns "claude".
          Replaced by canon/local/.this-machine, a one-line per-machine
          file excluded from overlay sync.
    (2) ONE TREE. Erik chose public-repo-only over a private/public pair.
        Diffed both trees: the sole unique content in the old LLM-LMPS was
        cluster-lammps-info.txt and lammps_250722_config.txt, now in
        canon/local/reference/. Everything else was scrubbing or stale.
        Old tree moved to DEVEL/_to_delete/ with a README explaining what
        was checked (incl. a 4.6 MB Claude data export left for Erik).
    (3) clusters.yaml: raven, viper-gpu and viper-cpu seeded from the
        LAMMPS-compile-n-bench repo — hardware, module stacks, Kokkos
        backend/arch, binaries, docs URLs, scratch patterns. Plus three
        cross-cluster hard rules (never install into $HOME; MPI only
        inside a Slurm job; scratch is not backed up).
    (4) New reference card canon/tools/lammps-compile-n-bench.card.yaml
        + catalog entry: the build recipes and measured benchmarks are
        that repo's job, canon just points at it.
    (5) canon/local/ is now its own private git repo (6 files) so the
        overlay is versioned and syncable. Facts confirmed by Erik:
        notify address is the fau.de one; the cmmg <SCRATCH_ALIAS> path is
        a symlink to the ptmp scratch (same place, two names), which
        resolves the apparent conflict. Real values: canon/local/.
- handoff_to: null
- next_session_must: |
    Two things are still open and both are one-liners:
    (a) DEVEL/CLAUDE.md still says the tree is at ~/Desktop/DEVEL/ — it is
        at ~/DEVEL/. Every agent reads that file first.
    (b) 16 of the 18 projects under SIMULATIONS/ have no project.md, so
        the framework will not touch them. Only Ni-A0-CIJ-EAM-MEAM
        (succeeded) and Ni-H-PHASE-DIAGRAM-EAM-MEAM are ingested. Ingest
        on demand, one at a time, per the ARCHITECTURE ingestion rule.
    Also unfiled: SCIENCE-KICKOFF-NIH-STOICHIOMETRY.md sits loose at the
    SIMULATIONS/ root (modified 2026-07-28) with no session covering it.
    It probably belongs inside Ni-H-PHASE-DIAGRAM-EAM-MEAM.

### session_id: 2026-07-28-cowork-compat-maintenance
- mode_at_close: designer                 # designer lock RELEASED at close — now free
- scope: |
    Framework maintenance only, no pilot scope. Bring LLM-LMPS back in step
    with the current Cowork (cloud-sandbox default, device-bridge folder
    access, skills, persistent-memory MCP) after ~4 weeks dormant.
- closed: 2026-07-28T00:00Z
- summary: |
    (1) Stale-closed the phantom 2026-06-09 active entry (above).
    (2) Path drift: replaced the `~/Desktop/DEVEL/...` hardcodes in
        ARCHITECTURE §17/§17.6 and canon/session-startup.md with
        repo-relative resolution (the repo now lives at ~/DEVEL/).
    (3) Retired the `auto-memory` layer: ~10 references across
        ARCHITECTURE, session-startup, learnings, preferences and
        proposals-inbox now point at canon/learnings.md. New
        ARCHITECTURE §6.4 states the relationship to Cowork's own
        persistent memory (framework rules live in canon; the personal
        memory filesystem is not a framework store).
    (4) New local-config layer: gitignored canon/local/ holding real
        identity, with canon/local.example/ committed as the template,
        loaded as an overlay on canon/clusters.yaml. New lint
        canon/templates/lint-no-identity.py (bash wrapper alongside)
        guards the scrub boundary; Python because macOS bash 3.2 + BSD
        grep cannot run the shell version.
    (5) New skills/llm-lammps/SKILL.md so the startup ritual self-triggers
        on "simulations"/"LAMMPS"/"runs"; packaged as llm-lammps.skill.
    Nothing touched on the cluster; no jobs in flight; no project files
    written (this session could not see them — see next_session_must).
- handoff_to: null
- next_session_must: |
    Run the session-startup ritual as usual. Two things to know:
    (a) Run the session ON YOUR COMPUTER, not in the cloud sandbox — the
        cloud sandbox can only see folders connected at session start and
        cannot add more mid-session, so ~/Desktop/SIMULATIONS and
        ~/cluster-mounts/ are invisible there. Connect the simulation
        folder and the cluster mount at start.
    (b) canon/local/ must exist before cluster work: copy
        canon/local.example/ to canon/local/ and fill in the real values.
        The ritual now checks for it at step 0.
    Outstanding pilot work, free to claim: ingest EAM-DISLOCS-Ni-Cu
    (Ni-Cu dislocation/segregation study) per the stale-closed entry above;
    and the ni-h-phase-diagram-eam-meam project left at thread 01/02 by
    session 2026-06-01-1704-Ni-hydride.

### session_id: 2026-06-09-1841-ingest-dislocs-ni-cu
- mode_at_close: designer+pilot   # designer lock RELEASED
- scope: |
    PILOT: ingest the existing (not-yet-ingested) sim-project
    ~/Desktop/SIMULATIONS/EAM-DISLOCS-Ni-Cu (Ni-Cu dislocation/segregation
    study; Fischer-2018 EAM + Mishin04 Ni; oriented dislocation cells D0/D30/
    D60/D90, SGC + VC-SGC Monte Carlo, ISLANDGRAIN Sigma9 GBs, REFERENCE).
    Stamp project.md in place per ARCHITECTURE ingestion rule (nothing moves,
    nothing renames). DESIGNER: took the (free) lock to formalize a reusable
    ingestion procedure in canon if the existing ARCHITECTURE-only rule proves
    thin.
- closed: 2026-07-28T00:00Z          # stale-closed; last_active was 2026-06-09T18:41Z
- summary: |
    STALE-CLOSED, no work performed. Registered 2026-06-09 and never
    advanced (last_active == started, in_flight empty, no files written).
    Closed 2026-07-28 by the framework-maintenance session so the dashboard
    stops showing a phantom pilot lock on the Ni-Cu dislocation project.
    The ingestion of EAM-DISLOCS-Ni-Cu is still OUTSTANDING and free to
    claim. Original notes follow.
- original_notes: |
    Designer lock was FREE at startup (released by 2026-06-01-1525 at its
    16:05Z demotion). No pilot-scope collision: the lone other active session
    owns the Ni-A0-CIJ Thread-03 paths, disjoint from this project.
    Confirmed not-yet-ingested: no project.md at project root. howto_dislocs-
    Ni-Cu.txt present (Erik's own notes) — primary ingestion source.
- handoff_to: null

### session_id: 2026-06-30-1327-Ni-thread03-close
- mode_at_close: pilot
- scope: |
    Ni-A0-CIJ-EAM-MEAM / 03_LATTICE-CONSTANT-AT-300K — completion: mirror the
    finished production data cluster->Mac, run the analysis pipeline, close the
    thread, and close the project.
- closed: 2026-06-30T13:27Z
- summary: |
    Picked up the Thread-03 pilot tail left in_flight by 2026-06-01-1525.
    The three production jobs (20317577 EAM-NPT, 20317578 MEAM-NPT, 20317579
    MEAM-scan) had completed cleanly and survived in cmmg ptmp. Curated-mirrored
    all four run dirs to the backed-up Mac (~203 MB), ran analyze-a300-NPT (x2),
    analyze-a300-NVT-scan (x2), thermo-fft-check (x4), and
    compare-a300-methods-potentials. Results: Pezold-EAM a(300K)=3.5369 A,
    KoShimLee-MEAM a(300K)=3.5342 A, alpha~1.2e-5/K both; M1/M2 agree to ~1-2e-5 A
    via the L24 quadratic root. Wrote two consolidated per-potential mile-pebbles,
    closed thread.md (status succeeded, verdict), and closed project.md
    (status succeeded, results table + lessons). Two "fix the class" analysis-
    script fixes applied (numpy-2.x YAML representers in analyze-a300-NVT-scan.py;
    duplicate phase-boundary-step de-dup in thermo-fft-check.py). One cosmetic
    mislabel left in compare-a300-methods-potentials.py (noted in thread.md).
- handoff_to: null
- supersedes: 2026-06-01-1525-Ni-thread03 (completed that session's in_flight tail)

### session_id: 2026-06-01-1525-Ni-thread03
- mode_at_close: pilot                    # designer lock already free (released at 16:05Z demotion)
- scope: |
    Ni-A0-CIJ-EAM-MEAM / 03_LATTICE-CONSTANT-AT-300K (pilot: move the runs
    forward) + LLM-LMPS framework canon (designer: the two Thread-03 TODOs).
- closed: 2026-06-30T13:27Z                # retired by 2026-06-30 close session; its in_flight production tail is done
- summary: |
    Got cluster ground truth via Erik's real cmmg shell; found + fixed a 4th
    failed probe attempt (`$(step:%d)`->`$(step)`, codified as L31). Probes
    resubmitted and PASSED (20317574/75/76); production SUBMITTED
    (20317577 EAM-NPT, 20317578 MEAM-NPT, 20317579 MEAM-scan). Left in_flight
    awaiting those jobs. Designer TODO backlog cleared (probe-input.in.skel
    built; §17.5/concurrency + fix-the-class rules tightened). The await->mirror
    ->analyze->close tail was completed by session 2026-06-30-1327-Ni-thread03-close.
- handoff_to: 2026-06-30-1327-Ni-thread03-close

### session_id: 2026-06-01-1704-Ni-hydride
- mode_at_close: pilot                     # designer lock had already been released at the 17:32Z demotion; nothing to release at close
- scope: |
    Two phases. PILOT: new sim-project Ni-H-PHASE-DIAGRAM-EAM-MEAM (stability,
    composition range & properties of Ni-hydrides via Pezold-EAM + Ko/Shim/Lee
    MEAM). DESIGNER (mid-session, lock taken then released): framework canon —
    the role-tag rule + Pilot-stance rule fixes Erik requested.
- closed: 2026-06-01T19:18Z
- summary: |
    (1) DESIGNER: fixed two rule violations Erik flagged. Role tag is now
    single-hat ([Pilot] OR [Designer]; combined [Designer+Pilot] banned from
    responses, capability-only; switches announced) — edited ARCHITECTURE
    §17.5+§15, learnings.md, session-startup.md, feedback_concurrency_model +
    MEMORY.md; new lint canon/templates/lint-role-tag.sh (passes); fixed stale
    .lmps/->canon/ paths in that memory. Added HARD rule Pilot wait-and-
    co-develop (learnings.md + new feedback_pilot_wait_and_codevelop.md +
    MEMORY.md). Both lints clean.
    (2) PILOT: scoped the Ni-H study end-to-end and built the project skeleton.
    Science settled: rock-salt B1 NiH, H on octahedral sites; site-list
    GC + VC-SGC hybrid MD/MC (sites taken from the 100%-hydride build);
    stress-free P=0 + volume relaxation; 10x10x10 (4000 Ni); T ladder
    300/500/633/750/900 K; EAM first (validates vs Korbmacher 2018 — SAME
    potential family, von Pezold is co-author), MEAM second (new ground).
    Created project.md + 5 thread stubs (01 structure+0K anchors, 02 MC-driver
    +calibration, 03 EAM isotherm+elastics, 04 MEAM, 05 synthesis) +
    MC-DRIVER/MC-DRIVER-SPEC.md (full Claude-Code spec + acceptance tests) +
    STRUCTURES/ & MC-DRIVER/ READMEs. Build NiH at a=3.738 A.
    Nothing run on the cluster; no jobs in flight.
- handoff_to: null                         # next session = fresh pilot on ni-h-phase-diagram-eam-meam
- next_session_must: |
    Re-run the startup ritual. Resume as PILOT on ni-h-phase-diagram-eam-meam
    (designer lock NOT needed). First real work is one of:
    (a) thread 01 — build fcc Ni + B1 NiH @ a=3.738 A (10x10x10) and run the
        0K anchors (a0, E(c)->mu0/alpha/beta, H-solution & H-vacancy energies,
        0K Cij at c=0 and c=1), EAM/ then MEAM/; or
    (b) Erik implements the MC driver via Claude Code against
        ~/Desktop/DEVEL/MC-DRIVER/ (moved out of SIMULATIONS — it's tooling;
        spec at ~/Desktop/DEVEL/MC-DRIVER/MC-DRIVER-SPEC.md; conda env nih-mc
        has LAMMPS 29Aug2024 + python module + MANYBODY + MEAM verified). Then
        thread 02 validates + calibrates.
    No cluster jobs pending. Start with the science/build, probe before any
    production sbatch.

### session_id: 2026-06-01-1557-ARCHITECTURE-designer
- mode_at_close: designer                 # designer lock RELEASED at close — now free
- scope: |
    LLM-LMPS framework canon. Started ARCHITECTURE.md-only; became a
    canon-wide change (tool integration + the .lmps/ -> canon/ rename).
- closed: 2026-06-01T17:01Z
- summary: |
    Designed and implemented tool integration for LLM-LMPS. (1) Tool-card
    abstraction: canon holds CARDS only, tool code lives in its own repos
    on ~/bin/ (Mac+cluster). (2) Renamed hidden .lmps/ -> visible canon/;
    dropped the ~/.lmps/ home-promotion; class-wide path sweep + lint
    (canon/templates/lint-canon-paths.sh, clean); historical audit left
    intact. (3) canon/tools/: catalog + tool-card.skel + 4 verified cards
    (lego, dcreator, lego-tools 8-binary suite, afc), written from the
    DEVEL repos — all on ~/bin/ Mac+cluster, same bare invocation.
    (4) ARCHITECTURE.md §6 "tools registry" + §3/§6/§16. (5) learnings.md
    "## Tools" rules (reach-for-tool, 3+ promotion, re-probe-stale,
    card=mechanical-contract-not-recipe). Designer lock now free.
- handoff_to: null
- next_session:
  - New sim-project "Ni-hydride" planned as a PILOT session (science
    back-and-forth + feeding a POSCAR structure to lego). Does NOT need
    the designer or the lock. Promote to designer+pilot only if canon /
    a tool card needs changing mid-project (e.g. lego can't represent the
    hydride -> LEGO-repo change surfaced via proposals-inbox).
  - Card hygiene: last_verified on the 4 cards reflects repo READMEs, not
    a live --help run; re-stamp on first real invocation if anything drifted.

### session_id: 2026-06-01-1415-Ni-thread03
- mode_at_close: designer+pilot          # designer lock released at close
- scope: |
    Ni-A0-CIJ-EAM-MEAM / 03_LATTICE-CONSTANT-AT-300K (intended: prepare the
    MEAM runs) + LLM-LMPS framework canon (designer TODOs)
- closed: 2026-06-01T14:40Z (Erik rebooting machine)
- summary: |
    Ran the startup ritual (registered designer+pilot, lock was free).
    Erik asked whether the MEAM lattice-constant runs still need running
    and to prepare them. Answer: YES — MEAM has zero completed a(300K)
    data; runs 02 (NPT-MEAM) and 04 (NVT-scan-MEAM) both still needed,
    plus EAM's NPT (01); only 03 (NVT-scan-EAM, job 20285982) recorded
    complete.
    BLOCKED before any prep: the ~/cluster-mounts/cmmg sshfs mount was
    unstable (intermittent EACCES + empty listings; Erik reproduced the
    EACCES). I mis-analysed twice — wrongly inferred data loss, then a
    bogus home-vs-ptmp two-mount theory. Corrected: ~/cluster-mounts/cmmg
    IS ptmp (one mount); empty reads were the flaky mount, not reality.
    NOTHING submitted or written on the cluster this session. Erik then
    rebooted.
- handoff_to: null
- writes_this_session:
  - auto-memory: feedback_write_via_cluster_mount.md (cmmg layout +
    flakiness, corrected), feedback_unreliable_source_no_alarm.md (new),
    MEMORY.md index
  - thread.md: 2026-06-01 entry + "DO THIS FIRST next session" block
  - (no .lmps/ARCHITECTURE.md changes — the two designer TODOs were NOT
    started; still open for the next designer session)
- next_session_must:
  - re-run startup ritual; then get cluster ground truth via a REAL
    cluster ssh (not the Mac mount) per thread.md "DO THIS FIRST";
    confirm whether staged probes + run-03 data exist before any prep.
  - designer TODOs still pending: probe-input.in.skel template;
    L28-L30 / Cluster-discipline lint-promotion decision.

### session_id: 2026-05-31-1403-Ni-thread03
- mode_at_close: designer+pilot          # briefly re-promoted from pilot at 21:30Z to merge the inbox before closing
- scope: |
    Ni-A0-CIJ-EAM-MEAM / 03_LATTICE-CONSTANT-AT-300K (pilot work)
    + LLM-LMPS framework bootstrap (designer work)
- closed: 2026-05-31T21:45Z
- summary: |
    Long bootstrap session. Spanned (a) initial Thread 03 design + .in
    + .slurm authoring, (b) three failed production-attempt iterations
    on the cluster (walltime under-budget, then `fix_modify format` on
    fix ave/time, then `print "$(step:%d)"` syntax in probes),
    (c) framework expansion: concurrency model (ARCHITECTURE §17),
    runtime-estimate rule, output-precision rules, role-tag rule,
    proposals-inbox + session-startup ritual, retired CHECKPOINT.md.
    Lessons L26-L30 added to .lmps/lessons.md. Cluster discipline
    section + probe-before-production hard rule added to learnings.md.

    State at close:
    - Three production runs NOT successfully completed: 01_NPT-EAM,
      02_NPT-MEAM, 04_NVT-scan-MEAM (3 failed attempts each).
    - One production run COMPLETED: 03_NVT-scan-EAM (job 20285982,
      attempt 1).
    - Three probe sbatches PREPARED but not yet submitted (.probe.in
      and .probe.slurm files in each respective run dir; bugs from
      the failed attempt-3 probes have been fixed).

    Next session continues Thread 03 pilot work; see
    ~/Desktop/SIMULATIONS/Ni-A0-CIJ-EAM-MEAM/03_LATTICE-CONSTANT-AT-300K/
    thread.md "Notes / Reactions" -> "What tomorrow's session needs to do"
    for the step-by-step resume plan.
- handoff_to: null                       # successor session not yet started
- inbox_proposals_merged_during_session: 5 (probe rule + L28 + L29 + L30 + ritual update)
- framework_additions:
  - .lmps/lessons.md L26, L27, L28, L29, L30
  - .lmps/learnings.md "Cluster discipline" subsection
  - .lmps/preferences.md "Output precision" subsection (+erratum)
  - .lmps/preferences.md "Design-time disclosure" subsection
  - .lmps/session-startup.md (new)
  - .lmps/proposals-inbox.md (new, with 5 merged entries as audit)
  - ARCHITECTURE.md §17 "Concurrency model"
  - SESSIONS.md (this file, new)
  - CHECKPOINT.md retired to .retired-CHECKPOINT.md
  - auto-memory: feedback_runtime_estimates_and_pre_submit_summary.md,
                 feedback_output_precision_proposed_at_design_time.md,
                 feedback_concurrency_model.md
- known_TODOs_for_next_designer_session:
  - Create .lmps/templates/probe-input.in.skel from Thread-03 probes
    as the canonical clone template.
  - Optionally promote the new lessons L28-L30 / Cluster discipline
    rule to a §1.xx lint check in style/lammps.md (the bugs were
    semantic, not pattern-greppable; mechanical lint won't catch
    them, but the docs-check discipline could be wired into the
    pre-flight checklist).

### template: how a recently_closed entry looks
- session_id: 2026-mm-dd-hhmm-<short-scope-id>
- mode_at_close: pilot | designer | designer+pilot
- scope: <one line>
- closed: <ISO 8601 UTC>
- summary: <one or two lines on what was accomplished or where it stopped>
- handoff_to: <session_id if there's a successor, else null>
