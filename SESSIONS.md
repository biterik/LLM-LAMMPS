# LLM-LMPS - Active sessions dashboard

Single source of truth for "who is working on what right now." Each
LLM-LMPS Cowork session self-registers on startup, updates
`last_active` on major actions, and migrates from `active` to
`recently_closed` on wrap-up.

See ARCHITECTURE.md §17 (Concurrency model) for the rules and
`canon/session-startup.md` for the startup ritual.

last_index_updated: 2026-08-20T14:55Z

---

## active

### session_id: 2026-08-20-1145-cluster-status-sweep
- mode: designer+pilot
- designer_pass_2: |
    SECOND DESIGNER PASS 2026-08-20T17:30Z at Erik's request. Lock re-verified
    free (all other active entries still record "designer lock NOT taken").
    Merged the three proposals filed by this session after the first pass:
      2026-08-20-1610 output set + cadence  -> preferences.md "Output style"
        (new FIRST bullet + an explicit ordering note that minimalism applies
        only AFTER the set is agreed) + learnings.md "Thread design" cross-ref
      2026-08-20-1650 boundary conditions   -> style/lammps.md new 1.13,
        lessons.md new L41 (with the measured 64-H z-histogram), learnings.md
        "Thread design" deviation-is-reportable rule
      2026-08-20-1705 per-atom stress noise -> style/lammps.md new 1.14
    Inbox now: 38 merged, 2 pending (the format template, and 2026-07-30-0934
    mu-scan loader which needs a tool built before a card can exist).
    lint-canon-paths and lint-no-identity both clean.
    NOTE: this session did not write SESSIONS.md between 14:29Z and 17:30Z;
    edits stamped 13:55Z/14:20Z/14:55Z in this entry came from another writer
    and were left untouched. Flagged to Erik.
- scope: |
    Cross-project status sweep after Erik's 08-07..08-17 absence. READ-ONLY on
    all project trees: reconcile `sacct -X -S 2026-08-04` (pasted by Erik,
    2026-08-20T11:47Z) against the four projects that had submissions handed
    over but never confirmed -- ni-h-hydride-cycle-eam, ni-h-diffusivity,
    ni-h-phase-diagram-eam-meam (thread 03 run 14), ni-h-at-dislocs-eam-meam.
    Report what ran, what failed and what was never submitted. No harvest,
    no analysis, no new submissions in this scope.
- started: 2026-08-20T11:45Z
- last_active: 2026-08-20T14:55Z
- simulation_root: ~/Desktop/SIMULATIONS   # on M5; confirmed by Erik 2026-08-20
- machine: M5
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - LLM-LAMMPS-public/canon/proposals-inbox.md (append-only)
  - SIMULATIONS/Ni-H-DIFFUSIVITY/**
  - SIMULATIONS/Ni-DISLOCS-EAM-MEAM-ACE/01_SCALE-AND-RELAX-0K/**   (added 13:55Z at Erik's request)
  - SIMULATIONS/Ni-H-HYDRIDE-CYCLE-EAM/**   (added 14:20Z at Erik's request)
- designer_scope: |
    PROMOTED to designer+pilot 2026-08-20T12:20Z at Erik's request, after the
    `group HGRP clear` post-mortem. DESIGNER LOCK TAKEN (verified free: all
    other active entries record "designer lock NOT taken"). Area: the
    doc-check rule (style/lammps.md 1.9), the input lint
    (canon/templates/lint-lammps-input.sh), and the handed-over-job
    reconciliation gap in canon/session-startup.md. Two proposals filed.
    INBOX PASS COMPLETE 2026-08-20T13:05Z at Erik's request ("let's get all the
    designer jobs done"): 18 pending proposals reviewed, 17 MERGED, 1 left
    pending with a second review (2026-07-30-0934 mu-scan loader -- needs a
    tool built before a card can exist). Files touched: canon/lessons.md
    (+L38/L39/L40), canon/learnings.md (probe task count, minimize Loop-time
    criterion, probe exemption, walltime-before-ranks, mount classification,
    zstd-on-close, bridge-write verification, new mc/sites Tool-behaviour
    subsection), canon/preferences.md (new Convergence-and-force-norm section,
    Directory-and-file naming + .dump/.lmps, new Communication style,
    colour-scale rules folded into Plot defaults), canon/style/lammps.md
    (1.2 anchored grep, 1.9 rewritten), canon/session-startup.md (0(c)
    classification, new step 1b, wrap-up corollary), ARCHITECTURE.md
    (17.4, 17.6). canon/templates/lint-lammps-input.sh UNCHANGED -- see below.
    ONE SELF-CORRECTION ON THE RECORD: this session's own proposal
    2026-08-20-1215 asserted that `clear` is not a valid `group` keyword. It
    IS valid (docs.lammps.org/group.html). Caught by doc-checking the command
    before writing a lint to enforce a vocabulary -- i.e. by the rule the
    proposal was about. The closed-vocabulary lint was WITHDRAWN as a result
    (it would not have caught the bug, and a keyword allow-list is the stale-
    knowledge failure L8 already records); the 1.9 rewrite merged with its
    emphasis moved from spelling to PRECONDITIONS. L40 rewritten accordingly.
    Erik was told of the correction in-turn.
- in_flight: |
    RUN 14 SUBMITTED BY ERIK AND RUNNING as of 2026-08-20T13:50Z (squeue):
      22344607  NiH-EAM-mcsites-sizescan-L6   tasks _2.._5 RUNNING on cmmg001, 1:48:40 elapsed
      22344608  NiH-EAM-mcsites-sizescan-L10  tasks _0.._5 RUNNING on cmmg053, 1:48:40 elapsed
    Probe-calibrated estimate ~2.6 h (L6) / ~2.7 h (L10) against caps of 6 h and
    12 h, so both are on track. UNRESOLVED: L6 tasks _0 and _1 are NOT in the
    RUNNING list while _2.._5 are, and all four running tasks show identical
    elapsed time, so _0/_1 did not simply start later. RESOLVED 2026-08-20T13:55Z by
    `sacct -j 22344607`: both COMPLETED cleanly, 01:10:11 and 01:08:27, exit
    0:0 -- faster than the 2.6 h probe estimate, not a failure. Harvest target: results/L6/ and results/L10/ under
    Ni-H-PHASE-DIAGRAM-EAM-MEAM/03_EAM-ISOTHERM-AND-ELASTICS/14_EAM-SIZE-SCAN-300K-MCSITES.

    OPEN LOOP FOUND ELSEWHERE, 2026-08-20T13:45Z, from `sacct -S 2026-08-01
    -E 2026-08-04` (a window the first sweep did not cover):
    ni-dislocs-eam-meam-ace THREAD 01 ALREADY RAN AND SUCCEEDED on 2026-08-03,
    and its thread.md still says `status: prepared`. All 12 production
    relaxations (4 characters d0/d30/d60/d90 x 3 potentials Pezold-EAM,
    KoShimLee-MEAM, CuNi-ACE) COMPLETED, jobs 21623859/21623860/21623861,
    preceded by three clean probes 21623590/21623591/21623593. Verified on the
    mount by exact path, not by listing: each of the 12 run dirs carries two
    `Loop time of` lines (the two-stage minimize), `Stopping criterion = force
    tolerance` on the first stage, and an ~82 MB `relaxed0K_*.lmps`. Spot-checked
    convergence: d0/d90 x all three potentials all reached force tolerance;
    Pezold-EAM 1402/2557 steps at 128 ranks, KoShimLee-MEAM 1195/1988 at 128,
    CuNi-ACE 1280/931 at 256.
    NOTHING of this is on the Mac -- that tree holds 25 files, inputs and
    run.yaml only, no outputs. Seventeen days unharvested.
    These are exactly the cells ni-h-at-dislocs-eam-meam declares it consumes,
    so this blocks that project too.
    HARVESTED ni-dislocs-eam-meam-ace thread 01 at Erik's explicit request,
    2026-08-20T13:55Z. Scope check: no active session owned it (the 08-03
    session is not in `active`), no collision. thread.md status corrected
    `prepared` -> `open`; summary table generated from the 12 logs (not
    transcribed) and one figure written to the thread dir.
    RESULTS: cost per minimizer iteration, rank-normalised, 1.85 / 9.75 /
    102.11 core-s for Pezold-EAM / KoShimLee-MEAM / CuNi-ACE -- ACE is 55x EAM
    on this cell, measured not extrapolated. Relaxation energy from the
    Volterra start is dominated by d30 in every potential.
    NEW OPEN QUESTION: the d60 cell relaxes to P_xx = -1.53 / -1.39 / -1.55 GPa
    against -0.02..-0.34 GPa for d0/d30/d90. Three independent potentials agree
    to 0.16 GPa on it, so it is the CELL, not the model -- something is off in
    the d60 Volterra construction or its box. d90, which ni-h-at-dislocs
    consumes, is unaffected.
    NOT PULLED: the 12 relaxed `.lmps` structures and 12 snapshot dumps
    (~1 GB each set) are ask-first class and stay on the cluster; the core-
    structure / DXA comparison that is the thread's actual scientific point is
    therefore still not done.
    ALSO from the same sacct window, still unhandled: NiH-0K-Hsite-anchors-
    KoShimLee-MEAM TIMEOUT twice (21623127, 21623145, ~30 min each), never
    retried, in ni-h-phase-diagram thread 01.
- notes: |
    designer lock NOT taken. Cloud Cowork session on M5's connected folders;
    DEVEL/LLM-LAMMPS-public and cluster-mounts/cmmg granted mid-session.
    READ-ONLY on all four project trees -- this session writes no project files.
    THREE STALE `active` ENTRIES were found and deliberately NOT closed by this
    session (2026-08-05-1425-mcsites-presentation, 2026-08-03-1401-nih-at-dislocs-design,
    2026-08-02-1647-ingest-eam-dislocs-ni-cu; all last_active 08-05 or earlier).
    Flagged to Erik for a decision; closing them belongs to whoever takes over
    those scopes.
    MOUNT: cmmg was TCC-blocked at session start (top-level listing fine, every
    descent EPERM, device_list_dir reporting a macOS access denial). Re-connecting
    the folder in the desktop app fixed it with no remount -- see proposal
    2026-08-20-1150-tcc-eperm-vs-stale-sshfs. L15 flakiness (exact-path reads OK,
    parent listings empty, intermittent between calls) was present AFTERWARDS and
    is why no enumeration-based harvest was attempted.
- progress: |
    SACCT RECONCILIATION COMPLETE, 2026-08-20T11:52Z. Ground truth = sacct, not
    the mount.

    ni-h-diffusivity -- ALL 42 TASKS COMPLETED, exit 0:0, no timeouts.
      21738194_0-5   t01r00 aNiT calib          ~1h55 each   ended 08-06T20:39
      21738201_0-5   t01r01 msd-dilute-iso      ~1d00:10     ended 08-07T18:55
      21738210_0-5   t01r02 msd-dilute-biax     ~1d00:30     ended 08-07T19:17
      21738217_0-11  t02r01 msd-hydride-iso     ~3d05        ended 08-09/10
      21738232_0-11  t02r02 msd-hydride-biax    ~3d02        ended 08-09/10
      6+6+6+12+12 = 42. No continuation arrays needed. UNHARVESTED.

    ni-h-hydride-cycle-eam -- ALL 4 TASKS COMPLETED, exit 0:0, NO TIMEOUT.
      21774658_0 fixlat rateA  1-07:28:45 (4 nodes)  ended 08-09T21:22
      21774658_1 fixlat rateB  2-13:57:53            ended 08-11T05:45
      21774659_0 freelat rateA 1-08:08:14            ended 08-10T00:03
      21774659_1 freelat rateB 2-17:06:04            ended 08-11T09:07
      Gates verified on the mount before it went flaky: JOB DONE present in all
      four .out files; ALL PHASES COMPLETE present in all four named .log files
      (results/rateA|rateB in both run dirs); no non-empty .err found.
      The submit-restart-cycle-* LEG=2 continuations are NOT needed.
      NOTE on the estimate: actual 31.5 h / 62 h (fixlat) and 32 h / 65 h
      (freelat) against an estimate of 14 h / 28 h -- a factor ~2.3 low. The
      96 h cap absorbed it. Worth a walltime-estimation lesson if it recurs.
      UNHARVESTED.

    ni-h-phase-diagram-eam-meam thread 03 run 14 -- NEVER SUBMITTED.
      Probes ran twice and passed both times (21692779/80 on 08-05T18:58;
      21730533/34 again on 08-06T13:58). NO production array (--array=0-5)
      appears anywhere in sacct since 08-04. The size scan does not exist.

    ni-h-at-dislocs-eam-meam -- ONE RAN, ONE FAILED.
      21638799 NiH-STRAINREF-A300K-EAM   COMPLETED 00:00:08  08-04T21:21
        (thread 02 / 00_SCALED-AND-PREFILLED-CELLS strain reference; 8 s is
        short enough that the log needs eyeballing before the pre-fill is
        built on it)
      21638800 NiH-HBIND-RELAX-D90-EAM-PROBE  FAILED exit 1:0, 00:00:56,
        08-05T00:29 (thread 01 run 01, relaxed binding subset). Undiagnosed.
      Thread 01 run 00 confirmed COMPLETE on the record: 21638175, 04:49:21.

    ALSO on the record, from the already-harvested 08-02..04 wave:
      21623099_13 NiH-EAM-gcmc-long-14c FAILED exit 1:0 -- the known gcmc
      shelf collapse, already accounted for in the 08-05 harvest.

- findings: |
    DIAGNOSIS, 2026-08-20T12:05Z -- root cause of the FAILED Hbind probe
    (21638800): `relax-Hbind_Ni-disloc-d90-Pezold-EAM-0K.in` line 193 is
    `group HGRP clear`. `clear` is not a valid `group` keyword; LAMMPS aborts
    with "ERROR: Could not find group clear group ID HGRP (src/group.cpp:137)"
    on the FIRST site of the loop, which is why all 256 ranks exited 1 after
    56 s. Step 1 (site selection) had already succeeded -- the site-{i,x,y,z}.dat
    feed files and the subset table are on the cluster and are fine.
    FIX = DELETE line 193 (or pre-create HGRP before the `label`). The line is
    redundant -- `delete_atoms group HGRP compress no` at the end of each pass
    removes the H, so `group HGRP type 2` on the next pass re-selects exactly
    the one live H.
    CORRECTED 2026-08-20T12:40Z: this session first told Erik that `clear` is
    not a valid `group` keyword. That was WRONG. `clear` is valid
    (docs.lammps.org/group.html) and un-assigns a group's atoms without
    deleting the group; `delete` removes the group. BOTH require the group to
    exist, which is the actual defect -- line 193 is at the top of a
    `jump SELF` loop and the first pass has no HGRP. The error message was the
    tell and was misread: `src/group.cpp:137` is the group LOOKUP, so the
    parser had already accepted `clear` as a style. The recommended fix is
    unchanged and still correct; the diagnosis behind it was not.

    MIRROR BLOCKED, same session. The curated mirror of ni-h-diffusivity and
    ni-h-hydride-cycle-eam could NOT be done through the sshfs mount. Positively
    classified as L15 staleness (not the TCC/permission mode fixed earlier):
    `Ni-H-DIFFUSIVITY/01_D-H-IN-DILUTE-Ni/01_MSD-DILUTE-STRESS-FREE/results`
    lists its six T dirs, while its SIBLING `02_MSD-DILUTE-BIAXIAL/results` and
    all four cycle `results/rate*` dirs return empty -- 8 retries with backoff,
    no recovery, so it is per-subtree stale caching, not jitter. `du -sh` on the
    whole diffusivity tree reported 2.0M when a single T dir holds 47M, which is
    the same defect seen through a different command. Per L15, rsync/find/globs
    over these subtrees would copy NOTHING and exit 0, so no partial mirror was
    attempted. Mirror lines handed to Erik as rsync-over-ssh (enumeration happens
    cluster-side, bypassing sshfs entirely) 2026-08-20T12:05Z.
    UNVERIFIED, deferred: the 8 s NiH-STRAINREF-A300K-EAM job (21638799) -- its
    log sits in one of the stale subtrees and could not be read.

- cycle_concentration_basis: |
    CONCENTRATION BASIS CORRECTED 2026-08-20T14:55Z, on Erik's question ("the
    concentration is over the entire sample, right? but we only inserted in a
    certain volume"). He was right. The trace's x is N_H/N_Ni_TOTAL (1,728,000)
    while insertion happens only in MCZONE = z 106-129 a0, of which Ni occupies
    106-120 a0 = 201,600 atoms. Scale factor 8.5714.
    ON THAT BASIS THE MC ZONE FORMS STOICHIOMETRIC NiH: x_local peaks at 1.041
    / 1.066 (fixlat rate A/B) and 0.978 / 0.981 (freelat). The whole-slab x of
    0.11-0.12 was hiding a hydride.
    THIS SUPERSEDES the earlier "coherency makes no difference" reading: the
    difference is 9 % in stoichiometry, not 6 % of a small number. BUT it is
    NOT established -- the fixed 201,600 denominator assumes the zone's Ni
    content is constant and the dynamically rebuilt catalogue (M: 173k -> 256k,
    derived as x*N_Ni/c) shows it is not. The coherent slab cannot expand
    laterally so it pushes Ni up into the 120-129 a0 headroom, which is inside
    MCZONE, giving it more sites and more H. Part of the gap is the denominator.
    Settling it needs a per-frame Ni count inside MCZONE from the trajectory.
    Defensible statement for now: both boundary conditions load the MC zone to
    approximately NiH; the 9 % split between them is not established.
    TRAJECTORY HANDLING PREPARED, not run (cluster writes strict-A): a zstd
    -T0 -12 sbatch job and a frame-subsampling script written into the cluster
    thread dir. ~75 GB raw across four runs, 201/401 frames, ~64 MB/frame.

- cycle_analysis: |
    CYCLE ANALYSED 2026-08-20T14:20Z (ni-h-hydride-cycle-eam thread 01).
    Charging saturates the site catalogue: peak occupancy c = N/M reaches
    0.939/0.973 (fixlat rate A/B) and 0.953/0.974 (freelat).
    HYSTERESIS IS KINETIC, NOT THERMODYNAMIC. mu_half(charge) - mu_half(discharge)
    = 0.379/0.272 eV (fixlat A/B) and 0.393/0.287 eV (freelat A/B). Halving the
    ramp rate narrows the loop by ~0.11 eV in BOTH boundary conditions, so the
    equilibrium loop is narrower than anything measured. A converged loop width
    needs a rate ladder, not two points.
    COHERENCY IS A NULL RESULT HERE. The fixed-lateral slab builds P_xx ~ +2.0
    GPa at peak loading; the free-lateral one sits at 0 by construction and
    expands laterally instead (l_x +0.127 % at the end of rate A). Despite that
    ~2 GPa difference, x_max differs by 6 %, mu_half by 0.01-0.02 eV and the
    hysteresis width by 0.015 eV.
    HEADLINE CAVEAT: THE DEGAS HOLD NEVER PLATEAUS. x is still falling at the
    end of all four runs (dx/dt = -3.0e-2 and -3.3e-2 /ns at rate A, -1.9e-3
    /ns at rate B). The apparent retained fractions -- 11.6/13.0 % at rate A
    against 3.6/4.0 % at rate B -- are UNFINISHED DEGASSING, not trapping:
    rate B simply had twice the degas time. No trapping claim is supported by
    these four runs. Fix = a long constant-mu hold from the rolling restarts;
    the LEG=2 continuation machinery exists and is verified.
    Suggestive but NOT a measurement: the instantaneous relative decay rate at
    the end differs ~5x between rates (2.1 /ns vs 0.43 /ns), the shape expected
    if an easy population has left and a bound one remains.
    RECORDED DEFECT: the .in header still documents the pre-decision cadence
    (MCS_NEVERY 20 / MCS_NTRIALS 1000) while the submit line actually ran
    1000/500 -- a reader of the input alone is off by a factor 50.
    NOT DONE: DXA / defect-population analysis. Trajectories are ~77 GB across
    the four tasks (12.9 GB fixlat rate A, 25.9 GB rate B) and stay on the
    cluster; frames are ask-first.
    PROCESS NOTE: the first cycle rsync used --include='trace*.dat' and matched
    NOTHING, because the files are named <TAG>-trace.dat. Same unanchored-pattern
    class as the style/lammps.md 1.2 lint bug merged this morning; traces were
    read directly off the mount instead.
    PROCESS NOTE 2: this session's own SESSIONS.md timestamp updates had been
    silently no-oping since the 12:20Z designer promotion -- str.replace() with
    a stale anchor and no assertion. Found and corrected 14:20Z; all later
    edits now assert their anchor count. Same class as the canon rule
    "verify writes by the listing, not by assuming".

### session_id: 2026-08-05-1425-mcsites-presentation
- mode: pilot
- scope: |
    mc-sites presentation: brainstorm story + figures for a presentation on
    the fix mc/sites method (MC-SITES-LAMMPS) with Ni-H-PHASE-DIAGRAM-EAM-MEAM
    as the science showcase; then verify that all needed data exists, is
    analysed and plotted, and prepare/launch new runs as needed.
- started: 2026-08-05T14:25Z
- last_active: 2026-08-05T15:55Z
- simulation_root: ~/Desktop/SIMULATIONS   # on M5; confirmed by Erik
- machine: M5
- owns_writes_to:
  - SIMULATIONS/Ni-H-PHASE-DIAGRAM-EAM-MEAM/**
  - cluster-mounts/cmmg/Ni-H-PHASE-DIAGRAM-EAM-MEAM/** (proposed writes, confirmed first)
- in_flight: |
    THREAD 03 RUN 14 (EAM 300 K size scan, L=6/10, 6 mu each, mc/sites with
    NEW acceptance-counter columns f_MC[1..3,7]).
    PROBES 21692779 (L6) / 21692780 (L10) PASSED all gates ~15:52Z: clean
    exit, ALL PHASES COMPLETE, Performance lines, f_MC[1] exactly
    1000/block and (ins+del)/att == acc_ratio. Measured rates: L6 4.75
    steps/s @4 ranks, L10 4.52 steps/s @8 ranks => ~2.6-2.8 h/task at
    44k steps; caps 6 h / 12 h hold, no script edits.
    Probe physics note: L6 filled to x=1.000 (a_eff 3.756) INSIDE the
    2200-step probe; L10 at x=0.021 still filling.
    PRODUCTION ARRAYS (--array=0-5 both) HANDED TO ERIK ~15:55Z, not yet
    confirmed submitted. A resuming session must CHECK results/L6/ and
    results/L10/ on the cluster first.
- notes: |
    designer lock NOT taken. Took over Ni-H-PHASE-DIAGRAM-EAM-MEAM ownership
    from 2026-08-05-1009-nih-harvest (clean close today 14:13Z, handoff null).
    READ-ONLY on DEVEL/MC-SITES-LAMMPS* repos (method/status source material
    for the presentation). Disjoint from 2026-08-03-1401-nih-at-dislocs-design
    (Ni-H-AT-DISLOCS) and 2026-08-02-1647-ingest-eam-dislocs-ni-cu.
    Presentation decisions so far: master deck w/ swappable depth modules,
    7-section spine (problem / Voronoi-sites idea / in-LAMMPS+validation /
    cost+Delta_mu / Ni-H phase diagram / catalogue cautionary tale / outlook).
    Delta_mu figure exists (thread 03 fig8; polished v2 planned). Size scan =
    run 14 (kept cheap: no 18^3, no 14^3 acceptance re-runs).
    Device bridge dropped ~14:32Z (desktop app restart by Erik fixed it).
    NOTE: this entry was re-applied 14:40Z onto a stale staged body after the
    restart -- the 14:26Z device version contained the same registration
    (split-brain staging: fresh metadata, stale content; see 08-04 false-alarm
    note in the nih-at-dislocs entry).

### session_id: 2026-08-03-1401-nih-at-dislocs-design
- mode: pilot
- scope: |
    NEW PROJECT ni-h-at-dislocs-eam-meam: does a hydride nucleate at a Ni
    dislocation core, at which chemical potential, and does it depend on
    dislocation character. Consumes the relaxed 0 K cells of
    ni-dislocs-eam-meam-ace thread 01 and the mu-sweep method of
    ni-h-phase-diagram-eam-meam. T = 300 K, `fix mc/sites` grand-canonical,
    mu sweep; outputs = c(mu,T) plus trajectory dumps carrying per-atom
    stresses. In scope now: d90 x Pezold-EAM. ACE deliberately OUT of scope.
- started: 2026-08-03T14:01Z
- last_active: 2026-08-04T18:40Z
- simulation_root: ~/Desktop/SIMULATIONS   # on M5; confirmed by Erik
- machine: M5
- owns_writes_to:
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/**
  - cluster-mounts/cmmg/Ni-H-AT-DISLOCS-EAM-MEAM/**
- in_flight: |
    TWO JOBS WRITTEN AND STAGED ON THE CLUSTER, NOT YET SUBMITTED. Submit
    commands were handed to Erik 2026-08-04; whether he ran them is unknown
    to this session. A resuming session must CHECK THE RUN DIRS FIRST.
      1. thread 02 / 00_SCALED-AND-PREFILLED-CELLS/
         submit-strainref-Eins_Ni-H-oct-tet-a300K-Pezold-EAM.slurm
         (8 ranks, s.cmmg, 00:20:00)
      2. thread 01 / 01_RELAXED-BINDING-SUBSET/
         submit-Hbind-relaxed_Ni-disloc-d90-Pezold-EAM-0K.probe.slurm
         (256 ranks, p.cmmg, 01:00:00)
- notes: |
    designer lock NOT taken (taken and released once on 2026-08-03 to correct
    L36). READ-ONLY on ni-dislocs-eam-meam-ace (source structures) and on
    ni-h-phase-diagram-eam-meam (mu-sweep reference); the latter is owned by
    the active session 2026-08-02-1409-nih-resume, so no writes there.
    Disjoint from 2026-08-02-1647-ingest-eam-dislocs-ni-cu.
    Cloud Cowork session on M5's connected folders; DEVEL/LLM-LAMMPS-public
    and DEVEL/MC-SITES-LAMMPS-release granted to this session.
    NOTE on a false alarm, 2026-08-04: this entry appeared to have gone
    missing from SESSIONS.md twice. It had not. The device-side file was
    correct both times; what was stale was the CONTAINER-side copy of the
    staged path, which the session kept re-reading. Verify a device write
    by the size/mtime device_list_dir reports, not by re-staging and
    reading the staged file back.
- progress: |
    FULL RE-ENTRY BRIEF:
      ~/Desktop/SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/RESTART-BRIEF.md
    Read that first; it is maintained as the single re-entry document and is
    more detailed than this entry.

    THREAD 01 (0 K H binding map, d90 x Pezold-EAM)
      run 00 COMPLETE. 4 h 47 min at 256 ranks, all 953534 sites, no H
      leakage. Deepest RIGID trap E_seg = -0.1464 eV at an OCTAHEDRAL site,
      r = 9.31 A, tension side below the glide plane, at both partial cores,
      all 23 y-images identical. => mu_core ~ -2.421 eV, an UPPER bound.
      Field bulk-like (< 10 meV) beyond r = 72 A, which vindicates the
      +-70 A MC region. Notebook + 4 figures + OVITO file on the Mac.
      run 01 (relaxed subset, 372 sites) WRITTEN, probe not yet submitted.

    THREAD 02 (300 K mu sweep) DESIGNED, inputs not written. The rescaled
      cell exists. Erik decided 2026-08-04 that the pre-fill must carry the
      strain offset; recipe is in the thread file. The strain-reference
      mini-calculation that the pre-fill depends on is WRITTEN and staged.

    THREE CORRECTIONS MADE 2026-08-04, all on the record:
      (a) oct/tet classifier -- `metric volume` returns the PROBE Voronoi
          cell volume, not the interstice polyhedron; the assumed factor-4
          discriminator does not exist. Now clearance AND coordination.
      (b) the "slab bending" claim is WITHDRAWN. Erik challenged it and was
          right: the Ni per-atom energy is flat to 0.05 meV over the 140 A
          MAPZONE. What is there is an in-plane STRAIN STEP from the extra
          half-plane (4646 vs 4600 atoms per (111) layer, +1.000 %, against
          b/lx = 0.995 %) inside the fixed periodic lx. A straight line
          through the resulting saturating step manufactured the fake
          gradient. Background is now non-parametric, per z layer per site
          family. Verification archived in run 00 as
          `verify-slab-bending_d90-Pezold-EAM-0K.py` / `.txt`.
      (c) figures now use SYMMETRIC diverging colour scales (Erik's standing
          rule, 2026-08-04), and a new fig 4 shows the RAW E_ins where the
          strain step is actually visible -- fig 3 has it divided out by
          construction.

    OWED TO CANON: three proposals filed in canon/proposals-inbox.md
      (2026-08-04-1710 metric-volume gotcha; 2026-08-04-1712 background
      shape; 2026-08-04-1730 symmetric colour scales). None merged.

### session_id: 2026-08-02-1647-ingest-eam-dislocs-ni-cu
- mode: pilot
- scope: |
    EAM-DISLOCS-Ni-Cu — ingest the legacy (not-yet-ingested) project per the
    ARCHITECTURE ingestion rule: stamp project.md in place (nothing moves,
    nothing renames), report status, propose next steps. Scope is the EAM
    (Fischer-2018) study only; ACE-DISLOCS-Ni-Cu noted as related but NOT
    ingested; ISLANDGRAIN recorded as a side excursion, not a thread.
- started: 2026-08-02T16:47Z
- last_active: 2026-08-03T10:05Z
- simulation_root: ~/Desktop/SIMULATIONS   # on M5; confirmed by Erik
- machine: M5
- owns_writes_to:
  - SIMULATIONS/EAM-DISLOCS-Ni-Cu/**
  - cluster-mounts/cmmg/DISLOCS-Ni-Cu/** (proposed writes, confirmed first)
- in_flight: (none)
- notes: |
    designer lock NOT taken. Disjoint from 2026-08-02-1409-nih-resume
    (Ni-H project). Cloud Cowork session on M5's connected folders.
    project.md STAMPED 17:05Z, then threads 01/02/03 created, worked and
    CLOSED (all files verified on device 19:55Z):
    01_CU-SEGREGATION-VS-CHARACTER-T300 — four-character campaign
    harvested + analysed (screw: nothing; d30: weak atmosphere; d60:
    core-nucleated growing platelet; d90: Cu tube wetting the core).
    02_VCSGC-VARIANCE-CONSTRAINT-GROUP-BUG — root cause CONFIRMED at
    source level: fix sgcmc scales variance targets by atom->natoms but
    counts species over the fix group; group=interior => permanent veto
    of Ni->Cu swaps. v1-v5 outputs void. Canon proposal filed
    (2026-08-02-1900-fix-group-semantics-of-global-counter-fixes).
    03_PRECIPITATE-ONSET-D90-T300 — edge core nucleates the Cu-rich
    phase at every probed dmu <= bulk jump; +0.9260/+0.9313 runs are
    runaway transformations; no stable enrichment window at 300 K.
    Curated mirror of campaign timeseries/snapshot logs to Mac done;
    snapshot dumps left on cluster (ask-first class, not pulled).
    2026-08-03: inventory pass on Erik's questions + NEW thread
    04_BULK-REFERENCE-EQUILIBRATION-QA (JupyterLab equilibration
    notebook, executed on 37 bulk timeseries, + figures + L34 verdict
    table). Key facts: wprec seed = 8 Cu atoms (r=3 A sphere,
    subcritical — thread 03 + project.md wording corrected);
    RELAX-D0..D90 hold pure-Ni per-atom-stress dumps
    (peratom_stress.out: hydro, vM, full tensor, Voronoi vol); NO
    full-Cu-start runs exist (open question 5 added). Mount served
    exact-path reads but returned empty dir listings (L15 flakiness).



---

## recently_closed

### session_id: 2026-08-06-1222-hydride-cycle-design
- mode_at_close: pilot
- scope: |
    Hydride-cycle brainstorm -> TWO new projects created, designed and
    deployed end-to-end: ni-h-diffusivity (D_H(T), 42 tasks) and
    ni-h-hydride-cycle-eam ((100)-slab charge-discharge mu cycle,
    4 tasks x 4 nodes). Sources read-only; designer lock never taken.
- closed: 2026-08-07T14:55Z
- machine: M5
- re_entry: ~/Desktop/SIMULATIONS/Ni-H-HYDRIDE-CYCLE-EAM/RESTART-BRIEF.md
- handoff_to: null
- summary: |
    Brainstormed the hydride formation/dissolution science with Erik
    (ideas A/B/D + NI-HYDRATE folded in), then built and deployed two
    projects in ~24 h. ni-h-diffusivity: dilute-Ni + hydride MSD arrays,
    stress-free + biaxial, all probes passed, 42 tasks handed for
    submission 08-06. ni-h-hydride-cycle-eam: 1.74 M-atom (100) slab,
    region-restricted oct-only mc/sites, piecewise-linear mu(step)
    charge-discharge, 1 ps MC cadence for defect relaxation, rolling
    restarts + VERIFIED continuation inputs; probe iteration caught 2
    bugs at zero queue cost and measured the full-energy MC cost; all
    gates green 08-07, production (2 arrays, est. 14-28 h/task vs 96 h)
    handed before Erik's noon deadline; Erik away until 08-17.
    4 canon proposals filed. A resuming session reads the RESTART-BRIEF
    first and trusts sacct, not the mount.

### session_id: 2026-08-05-1009-nih-harvest
- mode: pilot
- scope: |
    Ni-H-PHASE-DIAGRAM-EAM-MEAM — harvest and analyse the 2026-08-02..04 wave
    that ran unattended: thread 03 runs 06 / 11 / 12 / 13, thread 04 runs 04 / 05.
    Mirror from cmmg, extend nih_loaders.py, build the analysis notebooks, and
    restate the two provisional findings flagged at thread 05's 2026-07-30 close
    (tetrahedral overcharge as site-catalogue artefact; transition width).
    Then the mu -> p_H2 map, now that Delta_mu is bracketed from below by run 06.
- started: 2026-08-05T10:09Z
- last_active: 2026-08-05T11:20Z
- simulation_root: ~/Desktop/SIMULATIONS   # on M5; confirmed by Erik
- machine: M5
- owns_writes_to:
  - SIMULATIONS/Ni-H-PHASE-DIAGRAM-EAM-MEAM/**
  - cluster-mounts/cmmg/Ni-H-PHASE-DIAGRAM-EAM-MEAM/** (proposed writes, confirmed first)
- in_flight: |
    NO cluster jobs owned by this session. Nothing is running: every task of
    runs 06/11/12/13 and MEAM 04/05 has exited (clean, collapsed or walltimed).
    Resubmission candidates identified but NOT written: MEAM run 05 tasks 3-5
    (-2.26/-2.24/-2.00, CANCELLED at the 24 h wall) and run 06 tasks 12-13
    (-2.04/-2.02, exit code 1 = the known gcmc shelf collapse).
- notes: |
    designer lock NOT taken. Took ownership from 2026-08-02-1409-nih-resume,
    which was 3 days stale; that entry is now in recently_closed with a handoff.
    Disjoint from 2026-08-03-1401-nih-at-dislocs-design, which holds
    Ni-H-AT-DISLOCS-EAM-MEAM and reads this project READ-ONLY as its mu-sweep
    reference — so its reference numbers change under it once this session
    restates the overcharge finding; flagged, not silently altered.
    Cloud Cowork session on M5; DEVEL/LLM-LAMMPS-public granted mid-session.
    14+ proposals were pending in canon/proposals-inbox.md; this session filed 4 more
    (2026-08-05-1100/1105/1110/1115).
- progress: |
    HARVEST + ANALYSIS COMPLETE, 2026-08-05T11:20Z.
    Mirrored runs 03/06, 03/11, 03/12, 03/13, 04/04, 04/05 from cmmg (curated set).
    Extended nih_loaders.py: 7/8-column schemas, RUNS registry, mu_window, isotherm,
    p_H2_from_mu, and a COLLAPSE GUARD BAND on block averages (run 06 mu=-2.02 was
    silently publishing a contaminated window).
    Three executed notebooks + 8 figures + 13 .dat exports written to the Mac:
      03/ analysis_site-catalogue-onset-and-gcmc-reference_EAM-300K.ipynb
      04/ analysis_600K-fine-and-transition-equilibration_MEAM.ipynb
      05/ analysis_synthesis-v4_binodal-and-pressure-axis_EAM-vs-MEAM.ipynb
    HEADLINE RESULTS
      - Tetrahedral overcharge is a SITE-CATALOGUE ARTEFACT (run 11, paired seeds).
        The project's headline EAM-vs-MEAM discrimination is WITHDRAWN.
      - The MEAM's "broader transition" was unfinished filling (run 05). WITHDRAWN.
      - Delta_mu(gcmc - mc/sites) = +0.101 +- 0.014 eV, rigid shift. Pressure axis built.
      - MEAM miscibility gap CLOSED by 600 K => 300 K < Tc(MEAM) < 600 K;
        EAM gap still 0.87 wide at 600 K => Tc(EAM) > 600 K (one-sided).
        This REPLACES the withdrawn discrimination result.
      - Both potentials over-bind H by ~0.2 eV/H; MEAM gets the heat-of-solution
        sign wrong. EAM 300 K plateau pressure 4e-4..1.1 bar vs ~5e3 experimental.
      - NEW discrepancy: our EAM plateau is 0.135 eV above Korbmacher's, though the
        two mu scales agree to 6 meV at the dilute end.
    Records updated: threads 03/04/05 thread.md, project.md (open questions 1-3 and 5
    CLOSED, 6-8 added, 5 project lessons).
    KNOWN GAP: thread 04 run 05's trace.*.dat did NOT mirror -- cmmg sshfs went stale
    mid-session (L15: exact-path reads AND listings empty for that one subtree while
    siblings rsynced fine). ave files are on the Mac and carry the isotherm; filling
    dynamics for that run are pending a remount.
    ALSO: device file-staging hit `session_stale_relogin` at ~10:45Z; the desktop app
    on M5 needs a re-login. Writeback was done via device_commit_files, which still
    worked.
    An adversarial verification pass (subagent, 8 claims re-derived from the raw .dat)
    found and fixed: the guard-band bug, a misattributed x=2.51, a false "matching to
    the fourth decimal", the converged-filter contamination of the dilute fit, a
    "four decades" that was 1.8, 0.11 vs 0.156, and three different EAM 300 K mu_coex
    in circulation in one summary table (now generated, not transcribed).
- closed: 2026-08-05T14:13Z
- handoff_to: null
- re_entry: ~/Desktop/SIMULATIONS/Ni-H-PHASE-DIAGRAM-EAM-MEAM/RESTART-BRIEF.md
- summary: |
    Took ownership from the stale 2026-08-02-1409-nih-resume and harvested the whole
    2026-08-02..04 wave (thread 03 runs 06/11/12/13, thread 04 runs 04/05), which had
    run unattended and never been opened. Five analysis notebooks now current across
    threads 03/04/05; 12 figures; ~20 .dat exports; 5 OVITO structures pulled.
    TWO HEADLINE CLAIMS WITHDRAWN: the EAM's tetrahedral overcharge is a site-catalogue
    artefact (run 11, paired seeds; confirmed from atom positions), and the MEAM's
    'broader transition' was unfinished filling (run 05 at 4x the attempts).
    REPLACED BY a stronger result: the MEAM's miscibility gap has closed by 600 K while
    the EAM's has not, so 300 K < Tc(MEAM) < 600 K and Tc(EAM) > 600 K (one-sided),
    against an experimental 633 K. Binodal now measured at both T for both potentials.
    Delta_mu(gcmc - mc/sites) = +0.101 +- 0.014 eV, rigid shift -> pressure axis built:
    EAM 300 K plateau 4e-4..1.1 bar vs ~5e3 experimental, both potentials over-binding H
    by ~0.2 eV/H (MEAM with the wrong sign of the heat of solution).
    Method comparison redone at matched cadence: MD is <1% of cost, gcmc needs 8.2x the
    attempts at 2.2x the cost per attempt (17.6x CPU) and fails outright above
    mu ~ -2.04. The two samplers give the SAME structure where both work -- the
    structural difference in this project is between catalogues, not samplers.
    project.md open questions 1/2/3 closed, 5 answered, 6/7/8 added, 5 project lessons.
    Nothing left running on the cluster. 4 canon proposals filed, none merged.
    Loader bug found and fixed by an adversarial verification pass: the collapse
    detector lagged by one block-average window and one task was publishing a
    contaminated mean as clean.

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
- closed: 2026-08-05T10:09Z
- closed_by: 2026-08-05-1009-nih-harvest (took over ownership; entry was 3 days stale)
- handoff_to: 2026-08-05-1009-nih-harvest
- summary: |
    Deployed the second half of the 2026-07-30 wave and handed the submit
    commands to Erik, then went quiet. Erik DID submit them: run 06 (gcmc-long,
    14 tasks), run 11 (oct-only), run 12 (tet-onset), run 13 (gcmc collapse-traj)
    and MEAM run 05 (transition-traj) all ran 2026-08-02..04. None of it was
    harvested, mirrored or analysed by this session; MEAM run 04 (600 K fine)
    also stayed unmirrored after the sshfs went stale. All of it is picked up by
    the successor session.

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
