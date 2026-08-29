# LLM-LMPS - Active sessions dashboard

Single source of truth for "who is working on what right now." Each
LLM-LMPS Cowork session self-registers on startup, updates
`last_active` on major actions, and migrates from `active` to
`recently_closed` on wrap-up.

See ARCHITECTURE.md §17 (Concurrency model) for the rules and
`canon/session-startup.md` for the startup ritual.

last_index_updated: 2026-08-29T15:40Z   # real UTC

---

## active


### session_id: 2026-08-29-1442-designer-inbox-merge
- mode: designer
- scope: |
    DESIGNER, inbox pass. Review the 12 pending proposals in
    canon/proposals-inbox.md (1 from 08-26, 6 from 08-27, 5 from 08-28) against
    existing canon and merge the ones that hold, flagging any to reject or
    rewrite before writing. No project work, no cluster work, no submissions.
- started: 2026-08-29T14:42Z
- last_active: 2026-08-29T15:40Z
- simulation_root: ~/Desktop/SIMULATIONS   # connected, but out of scope this session
- machine: M5   # canon/local/.this-machine
- cluster_identity: |
    Not resolved -- designer scope, no cluster work. Mount
    ~/cluster-mounts/cmmg is connected but this session does not read it.
- designer_lock: |
    TAKEN 2026-08-29T14:45Z. Previous holder 2026-08-20-1145-cluster-status-sweep
    (last_active 2026-08-20T14:55Z, 9 days stale) CLOSED at Erik's instruction in
    the same edit; see its entry in recently_closed. No other active entry claims
    the lock -- all record "designer lock NOT taken".
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - LLM-LAMMPS-public/canon/proposals-inbox.md
  - LLM-LAMMPS-public/canon/learnings.md
  - LLM-LAMMPS-public/canon/lessons.md
  - LLM-LAMMPS-public/canon/preferences.md
  - LLM-LAMMPS-public/canon/style/lammps.md
  - LLM-LAMMPS-public/canon/session-startup.md
  - LLM-LAMMPS-public/skills/llm-lammps/SKILL.md   # added 15:40Z, notify bullet
- scope_collisions: |
    None. Every other active entry is pilot mode on a project tree; this session
    touches no project tree.
- step_1b: |
    DISCHARGED 2026-08-29T15:0xZ against Erik's `sacct -X -S 2026-08-20` paste.
    CLOSED:
    * 22344607_[0-5] / 22344608_[0-5] (run 14 size scans, the last open loop of
      2026-08-20-1145) -- all 12 COMPLETED 2026-08-20, exit 0:0.
    * 22730595 (mu bracket 900 K) -- tasks 0-8 COMPLETED, task 9 TIMEOUT at the
      24 h cap as designed (L34). Array closed.
    * 22736814 (prefill a(mu) driver) FAILED 08-28T21:04 and was re-run clean as
      22736816 -- the incident behind inbox 2026-08-28-1910, already fixed.
    CONFIRMS THE RULE MERGED THIS SESSION:
    * 22730595_2 still prints RUNNING at 3-04:43:56 against its own 24 h cap,
      three days after Erik cancelled it and the job ceased to exist. Textbook
      accounting artefact -- NO ACTION, do not scancel, do not report burned
      cores. See learnings.md "The scheduler's state and the run's own marker
      can disagree in BOTH directions".
    STILL OPEN -- NOT THIS SESSION'S SCOPE, flagged to Erik:
    * 22733464_0 and _1 (NiH-CYC2-RATEB-EAM, thread 03 cycle rate B) BOTH FAILED
      with ExitCode 1:0 after 1-08:49:28 and 1-07:39:05, ended 2026-08-28T22:25
      and 21:14. Recorded as submitted-and-running by
      2026-08-27-0915-hydride-cycle-continue; never reconciled. ~32 core-days
      per task spent before the failure -- needs the .err read before anything
      is resubmitted.
    * 22733466_0 and _1 (thread 04 DOME) RUNNING at 2-03:07 and 1-23:24 against
      a 4-day cap -- consistent with the 65-84 h projection, genuinely live.
    * 22732087_[0-6] (nimelt-t02-measure) PENDING with 00:00:00 elapsed since
      2026-08-26 -- three days queued, no owning entry claims it.
    * 22736818 (NiH-MUSWEEP-D90-EAM-PROBE) FAILED 08-28T21:16 -- a probe, and
      unrecorded; needs its .err read.
    CORRECTION to this session's first reading of the paste: 22731708_1
    (LMPS-notify-probe) FAILED 08-26 is BY DESIGN, not an open loop. The probe
    is an array of two, task 1 exits 1 on purpose to exercise the FAILED mail
    path (canon/templates/slurm-notify.probe.slurm, lines 11-13). All four
    mails arrived with bodies; clusters.yaml records the verification.
    ODDITY, harmless: 22730592 reports Elapsed 00:00:00 though its owning entry
    measured 25921 s wall and the run completed cleanly. Another accounting
    artefact of the same family; the run dir is the arbiter.
- notify_helper_gap: |
    Erik, 2026-08-29: the job-notification mail was designed and deployed on
    2026-08-26 but the DOORBELL never learned about it. Canon was complete
    (style/shell.md 6, learnings.md, clusters.yaml cmmg notify block,
    templates/slurm-notify.sh + .probe.slurm, notify_email/notify_helper in
    canon/local); skills/llm-lammps/SKILL.md said nothing, so a session that
    wrote a submit script from the doorbell alone would omit it. Fixed here:
    SKILL.md section 4 is now "Three rules that have cost real jobs".
    STILL OPEN, not doable from a session: the INSTALLED Cowork skill is a
    synced copy of Erik's account skill -- the repo edit does not reach a
    session until he re-installs it. And only cmmg has a `notify:` block;
    raven, viper-cpu and viper-gpu need deploy + probe before the rule can
    be honoured there.
- merged_this_session: |
    All 12 pending proposals MERGED 2026-08-29, none rejected. 2026-08-27-0905
    and 2026-08-28-1610 were merged JOINTLY (same job, contradictory as
    standalone rules). New canon: learnings.md gains 6 bullets (gate corollaries;
    fix-the-class + minimum image; probe overrides silence gates; test the
    composition; restricted-MC load imbalance; minimizer scoring; multi-GB mount
    scans; scheduler-vs-marker), lessons.md gains L46/L47/L48, preferences.md
    gains "Stress in a cell containing vacuum", style/lammps.md gains 1.20,
    session-startup.md step 1b gains the RUNNING-row verification. All three
    linters clean. NOT COMMITTED -- left in the working tree for Erik.
- in_flight: (none)
- notes: |
    STALE ACTIVE ENTRIES CLEARED 2026-08-29T15:20Z at Erik's instruction. All
    three -- 2026-08-05-1425-mcsites-presentation,
    2026-08-03-1401-nih-at-dislocs-design and
    2026-08-02-1647-ingest-eam-dislocs-ni-cu -- moved to recently_closed with
    reconciled in_flight and a summary; flagged unclosed since 2026-08-20.
    Together with 2026-08-20-1145-cluster-status-sweep, closed earlier this
    session, `active` now holds no entry older than 2026-08-24.


### session_id: 2026-08-27-0915-hydride-cycle-continue
- mode: pilot
- scope: |
    CONTINUE ni-h-hydride-cycle-eam (Ni (100) film, H charge-discharge).
    Erik, 2026-08-27T09:1xZ: "prepare everything which is easily done";
    FIRST DELIVERABLE = the dislocated slab from thread 02 (biaxial misfit
    strain, job 22730592) -- mirror its dumps to the Mac. Then: harvest +
    analyse thread 02; analyse the 900 K mu bracket (array 22730595) and
    report mu_t(900 K); draft the thread 03 + 04 production sbatch
    hand-offs (both re-probes passed). Threads 05/06 cycle inputs are
    unblocked by the bracket but NOT written this session unless asked.
- started: 2026-08-27T09:15Z
- last_active: 2026-08-27T14:00Z
- simulation_root: ~/Desktop/SIMULATIONS   # connected folder, matches local.yaml default
- machine: M5   # canon/local/.this-machine
- cluster_identity: |
    cmmg -> <CLUSTER_USER>@<CLUSTER_HOST>, scratch /cmmc/ptmp/<CLUSTER_USER>
    (resolved from canon/local/clusters.local.yaml, echoed in chat only).
    Mount ~/cluster-mounts/cmmg CONNECTED and HEALTHY -- listings and
    exact-path reads both work. NOTE: a `grep -c` over the 23.5 GB thread-02
    trajectory through the mount TIMED OUT the 45 s device shell twice and
    briefly wedged it; frame counts are computed from the submit -var block
    instead. Do not scan multi-GB files through sshfs.
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - SIMULATIONS/Ni-H-HYDRIDE-CYCLE-EAM/**
- designer_lock: NOT taken -- pilot mode. Lock is FREE.
- scope_collisions: |
    TAKEOVER, ON THE RECORD: SIMULATIONS/Ni-H-HYDRIDE-CYCLE-EAM/** is
    declared by 2026-08-25-1725-nih-two-project-status (added 08-25T18:15Z,
    last_active 2026-08-26T16:05Z, conversation ended). This session takes
    the cycle scope ONLY; that entry keeps its at-dislocs threads 04/05 and
    the phase-diagram run-15 path, and is NOT closed here. Erik told in-turn.
    ALSO ACTIVE, no collision: 2026-08-27-0852-portfolio-status (READ-ONLY
    portfolio readout, owns no project tree).
- step_1b: |
    DISCHARGED. Every open loop touching this scope was checked in its own
    run dir on the mount, 2026-08-27T09:0x-09:2xZ:
    * thread 02 PRODUCTION 22730592 -- COMPLETE 2026-08-27T00:52Z. Gates
      PASS: empty .err, 2x ALL PHASES COMPLETE, 10 STAGE COMPLETE, 5 Loop
      time, wall 25921 s (7.2 h vs the 12 h cap), 124.9-143.7 steps/s.
      23.5 GB traj (~152 frames incl. per-atom stress), blockavg + every100
      thermo, 233 MB final stress-free cell. UNHARVESTED -> this session.
    * thread 03 re-probes 22730593_[0-1] -- BOTH PASS (boundary p p f fix
      confirmed, H-above-wall check clean, 56.8-58.3 steps/s in the MC
      phases). Production UNBLOCKED, NOT submitted.
    * thread 04 re-probes 22730594_[0-1] -- BOTH PASS, dome check clean,
      37.0-58.6 steps/s. Production UNBLOCKED, NOT submitted.
    * thread 05 run 00 / phase-diagram run 15 mu-bracket 900 K
      22730595_[0-9] -- tasks 0-8 (mu -2.55..-2.15) COMPLETE with gates;
      task 9 (mu = -2.10) alive at 09:1xZ, 24 h cap ~09:59Z, timeout
      accepted by design (L34). Task 2 (mu = -2.45) is a ZOMBIE: science
      finished 08-26T13:49Z with the full gate block and JOB DONE, but the
      lmp job step never reaped; scancel handed to Erik (strict-A).
- in_flight: |
    * 22730595_9 (mu = -2.10) -- TIMED OUT at the 24 h cap 2026-08-27T09:58Z,
      as designed (L34): x = 17.4, a_eff = 11.8 A, cell dissolved. Excluded
      as non-physical, NOT retried. The array is now finished.
    * scancel 22730595_2 DONE by Erik 2026-08-27T11:4xZ -- zombie job step
      released. The 900 K bracket array 22730595 is fully closed.
    * SUBMITTED by Erik 2026-08-27T11:4xZ, both confirmed with job IDs:
      - 22733464  thread 03 CYCLE rate B corrected, array 0-1 (fixlat/freelat),
        1024 ranks p.cmmg, 11.21 ns, 4-day cap. Projection ~80 h / ~84 h.
      - 22733466  thread 04 CYCLE rate B DOME, array 0-1, same shape.
        Projection 65-84 h (MC load imbalance, NOT 14x cheaper).
      Harvest gates for both: empty .err, JOB DONE, 2x ALL PHASES COMPLETE,
      22 phase markers, 5 Loop time lines, BC CHECK matches task, zero H
      above the 134 a0 wall, trace cols 7-9 with nNizone ~201600 (t03) /
      ~14500 (t04) and x_local rising on the charge leg, finite averaged
      stress columns in frames. TIMEOUT is recoverable: rolling restarts
      + absolute-step mu logic are armed in both inputs.
    * HANDED 2026-08-27T10:5xZ (strict-A), all in HANDOFF-2026-08-27.md at
      the project root, lint-handoff clean:
      - thread 03 production: sbatch submit-cycle-rateB-corrected_*.slurm
        (~80 h fixlat / ~84 h freelat vs the 96 h cap; degas NOT split)
      - thread 04 production: sbatch submit-cycle-rateB-dome_*.slurm
        (65-84 h; dome is NOT cheaper -- MC load imbalance, see notes)
      - two rsync blocks mirroring the thread-02 final cell (233 MB) and
        trajectory (23.5 GB) to the Mac
      Threads 03 + 04 ARE NOW SUBMITTED (IDs above).
    * THREAD 05 RUN 02 ADDED 2026-08-27T12:0xZ at Erik's request, after the
      finite-size caveat was raised in chat: 02_SIZE-SCAN-900K, the same six
      mu (run 00's bracket edges + run 01's four fine points) at L = 14
      (10,976 Ni, 2.74x), 32 ranks s.cmmg, probe + array, 24 h cap
      provisional pending the probe's steps/s. Tests whether the EMPTY
      INTERVAL WIDTH (0.477, the "gap has halved since 300 K" claim) is
      physical or finite-size; mu_t's POSITION is not at risk either way.
      Input body diff-verified IDENTICAL to runs 00 and 01; lint-lammps-input
      and bash -n clean on all three files.
    * Runs 01 and 02 STAGED and PROBES SUBMITTED by Erik 2026-08-27T13:3xZ:
      - 22733979  run 01 fine bracket PROBE, 1 task mu = -2.46, L10,
        16 ranks s.cmmg, 30 min cap. RUNNING: trace filling, step 200 of
        4400 at 13:35Z, x = 0.0043 and rising, acceptance 0.39 %.
      - 22733980  run 02 size-scan PROBE, 1 task mu = -2.45, L14,
        32 ranks s.cmmg, 30 min cap. RUNNING: step 120 of 4400,
        x = 0.00073, acceptance 0.13 %.
        CELL SIZE CONFIRMED: pe(step 0) = -49698.2 eV at the same
        -4.528 eV/atom as the L10 run's -18111.6 / 4000, so
        N_Ni = 10,976 exactly as designed.
      BOTH PROBES COMPLETED by 13:5xZ. Gates: empty .err, ALL PHASES
      COMPLETE x2, JOB DONE, healthy trace at step 4400 (run 01
      x = 0.434, a_eff 3.697, acceptance 10.2 %; run 02 x = 0.281,
      a_eff 3.661, acceptance 6.4 %), N_Ni = 10,976 confirmed for L14.
      L26 measured: run 01 6.5 steps/s (L10, 16 ranks), run 02 5.7
      steps/s (L14, 32 ranks) -> 88,000 production steps project 3.7 h
      and 4.3 h before the occupancy slowdown, against caps of 12 h and
      24 h. Both caps stand with room. NOTE the scaling result: L14 on
      32 ranks is only 1.14x slower per step than L10 on 16, against
      1.37x expected from 2.74x atoms on 2x ranks.
      PROBE GATE DEFECT FOUND AND FIXED (mine): the gate block read the
      block-averaged ave file, which holds ZERO data rows in ANY probe of
      this family (fix AVE has Nfreq 4000 and is defined after equil, so
      under -var equil 400 -var sample 4000 the first complete window
      falls past the end of the run). Both probes therefore printed
      "x = 0.0000" while passing everything real. Production unaffected
      (equil 8000 -> first ave row at step 12000). Both probe submits
      re-gated on the TRACE with an explicit row count; run 00's probe
      submit has the same defect and is in another session's scope.
      Canon proposal 2026-08-27-1400-probe-overrides-can-silence-a-gate.
    * PRODUCTIONS SUBMITTED by Erik 2026-08-27T13:5xZ, before the probe
      .out files were read -- both turned out fine, and the risk was low
      (identical input bodies to run 00, which ran 10 tasks clean), but it
      is on the record that the gate check happened after the fact.
      - 22734031  run 01 fine bracket, array 0-3 (mu -2.49..-2.46), L10.
        All four RUNNING at 13:5xZ, step 700, x = 0.014-0.021.
      - 22734032  run 02 size scan, array 0-5 (mu -2.50..-2.45), L14.
        All six RUNNING, step 500-560, x = 0.0067-0.011.
    * Thread 04 array 22733466: only task _0 has started; task _1 was still
      PENDING at 13:35Z. Normal, not a fault -- but a harvest session must
      confirm BOTH tasks ran before comparing the two boundary conditions.
    * ALSO HANDED 2026-08-27T11:3xZ (strict-A): thread 05 RUN 01, the FINE
      900 K bracket -- 4 points at 0.01 eV inside run 00's (-2.50, -2.45),
      probe then array, staging block included (new remote dir, so
      --rsync-path mkdir per the first-hand-off-into-a-new-tree rule).
      Input body verified IDENTICAL to run 00's by diff; bash -n clean.
    * DOWNLOAD, PARTIAL AND DELIBERATELY SO: sshfs mount measured at
      7.9 MB/s (bs=8M; 3.7 at bs=1M), and the device shell is capped at
      45 s and KILLS BACKGROUND PROCESSES on return, so 23.5 GB would be
      ~120 round-trips. Pulled instead: the 233 MB final cell, and five
      complete frames (steps 570000-610000) extracted by SEEKING to the
      byte range around the avalanche. Full trajectory left to Erik's
      direct rsync (block 2b), which does not pay the sshfs penalty.
- work_done: |
    * THREAD 02 HARVESTED AND ANALYSED. Curated mirror on the Mac (thermo,
      log, .out). Stress corrected for vacuum dilution (f = 1.1250; the raw
      whole-cell number understates every stress by 11 %). Biaxial modulus
      196.4 GPa (checks against C11+C12-2C12^2/C11 ~ 202 GPa). NUCLEATION at
      eps = 5.25 %, sigma = 6.22 GPa (step 580000), single avalanche with a
      5.74 GPa load drop -- NOT the 1.15 % that a deviation-from-linearity
      tolerance reports (that is nonlinear elasticity). Hold relaxes 0.25 GPa.
      Plastic strain 5.46 % at peak -> 1.25 % after unload (4.21 % recovered);
      residual 1.00 % permanent, lx 212.21 -> 214.34 A.
    * THE DISLOCATED SLAB IS CHARACTERISED. DXA on the final stress-free cell:
      610 segments, L = 9455 A, rho = 4.9e16 m^-2 (4.5 nm spacing). Shockley
      partials on wide faults, 1/6<011> stair-rod locks, 1/3<111> Frank loops;
      piled against the fixed base, clean under the free surface. Figures +
      viz cards + .ovito session + prov sidecars in analysis/.
      FLAG FOR ERIK: 4.9e16 m^-2 may be too dense to charge as a "film".
    * 900 K MU BRACKET REPORTED, thread 05 run 00. mu_t(900 K) = -2.475 +-
      0.025 eV, bracket (-2.50, -2.45); shift -0.200 eV vs mu_t(300 K).
      Widest empty x interval 0.477 with 0 converged points inside (~1 at
      300 K) -- the two-phase gap has halved; the 0.05 eV grid cannot decide
      whether it is still first order. All x > 1.05 points still need a
      crystallinity check (final-state files mirrored, no cluster time).
      THREADS 05 AND 06 ARE UNBLOCKED. The thread-03 mu window (-2.45 ->
      -2.10) must be SHIFTED DOWN for 900 K, not reused.
    * NUCLEATION EVENT CAUGHT from the extracted frames: DXA gives 0
      segments at step 570000 (pristine) and exactly 1 segment, L = 77 A,
      at step 580000 -- a Shockley partial b = 1/6[-1-12], xi = [1-10],
      88 deg edge. So the 5.74 GPa load drop follows a SINGLE surface
      emission event 10 ps earlier, and 6.22 GPa is a measurement of the
      homogeneous surface-emission stress at 300 K / 1e8 1/s.
      CAVEAT ON RECORD: PTM cannot classify these frames (everything reads
      "Other" at 5.25 % strain; rmsd_cutoff 0.25 and 0.0 both applied per
      the prov sidecars and neither helped), so the RENDERED IMAGES of the
      nucleation frames carry no structural information. DXA is unaffected
      -- it works on the elastically mapped Burgers circuit, not PTM labels.
      Frames 590000/600000/610000 are on disk and NOT yet analysed.
    * Artefacts written: analysis/analyse-biaxial-misfit-cycle_*.py,
      biaxial-misfit-cycle-summary_*.dat, fig3_*.{pdf,png},
      analyse-mu-bracket-900K_*.py, mu-bracket-900K-summary_*.dat,
      fig4_*.{pdf,png}, the two DXA viz cards + PNGs + .ovito + prov,
      HANDOFF-2026-08-27.md (219 lines, lint-handoff clean); thread.md updated
      for threads 02 and 05; thread 05 run 01 (fine bracket) input + probe +
      production submits written; 4 canon proposals filed.
- notes: |
    Cloud Cowork session on M5's connected folders (DEVEL, SIMULATIONS,
    cluster-mounts/cmmg). WALLTIME RE-DERIVATION done from thread 01's
    MEASURED per-phase Loop times (fixlat rate B: settle 66.8 s / charge
    68571 s / hold 33478 s / discharge 107342 s / degas 13566 s = 61.9 h
    for 8.01 ns) rather than a fresh estimate -- thread 03 projects ~80 h
    (fixlat) / ~84 h (freelat) against the 96 h cap, so the degas does NOT
    need splitting. Thread 04's dome is NOT cheaper than the full layer
    despite a 14x smaller catalogue (probe MC phases 37-49 steps/s vs
    thread 03's 57-58) -- suspected MPI load imbalance on the small MC
    region; flagged to Erik and to be recorded in thread 04's thread.md.

### session_id: 2026-08-27-0852-portfolio-status
- mode: pilot
- scope: |
    READ-ONLY portfolio readout across ALL simulation projects in
    ~/Desktop/SIMULATIONS: which projects/threads are open and ongoing,
    where results are sitting ready to harvest and analyse, and where
    runs still have to be started. NO writes to any project.md or
    thread.md, no cluster writes, no sbatch drafting beyond quoting
    hand-overs already written by the owning sessions.
- started: 2026-08-27T08:52Z
- last_active: 2026-08-27T10:25Z
- simulation_root: ~/Desktop/SIMULATIONS   # connected folder, matches local.yaml default
- machine: M5   # canon/local/.this-machine
- cluster_identity: |
    cmmg -> <CLUSTER_USER>@<CLUSTER_HOST>, scratch /cmmc/ptmp/<CLUSTER_USER>
    (resolved from canon/local/clusters.local.yaml, echoed in chat only).
    Mount ~/cluster-mounts/cmmg CONNECTED and HEALTHY this session --
    listings and exact-path reads both work, including the run dirs that
    were stale on 2026-08-26.
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
- designer_lock: NOT taken -- pilot mode. Lock is FREE.
- scope_collisions: |
    NONE -- read-only on every tree. No takeover of any stale entry.
- step_1b: |
    DISCHARGED FROM THE MOUNT, 2026-08-27T08:5xZ, not from sacct. Every
    open loop carried by an earlier entry was checked in its own run dir:
    * at-dislocs t02 run 00b (whole-cell catalogue) -- SUBMITTED overnight
      and COMPLETE. Jobs 22733000 + 22733029 (two submits, same run),
      finished 2026-08-27T00:2xZ. Gates PASS: JOB DONE, n_sites 1702230
      (> thread 01's 953534), z extent -0.02..247.71 A reaching both free
      surfaces, classes 33.05/65.44/1.51 %. UNHARVESTED on the Mac.
    * hydride-cycle t02 PRODUCTION 22730592 -- COMPLETE 2026-08-26T22:5xZ.
      JOB DONE, 2x ALL PHASES COMPLETE, 10 STAGE COMPLETE, 5 Loop time,
      wall 25921 s (7.2 h vs the 12 h cap), 124.9-143.7 steps/s.
      24.7 GB traj + blockavg + final stress-free cell. UNHARVESTED.
    * hydride-cycle t03 re-probes 22730593_[0-1] -- BOTH PASS (p p f fix
      confirmed, H-above-wall check clean, 56.8-58.3 steps/s in the MC
      phases). Production UNBLOCKED, NOT submitted.
    * hydride-cycle t04 re-probes 22730594_[0-1] -- BOTH PASS (same
      shape, 37.0-58.6 steps/s). Production UNBLOCKED, NOT submitted.
    * hydride-cycle t05 / phase-diagram t03 run 15 mu-bracket 900 K
      22730595_[0-9] -- tasks 0-8 (mu -2.55..-2.15) COMPLETE with gates;
      task 9 (mu = -2.10) has module-load output only and no final-state
      file: RUNNING or TIMED OUT, needs one squeue/sacct line from Erik.
    * at-dislocs t04/t05 run 00 (22728805 / 22728806) -- COMPLETE since
      2026-08-25, still UNHARVESTED (Mac dirs hold inputs only).
    * phase-diagram t03 run 14 -- HARVESTED and analysed (fig13 +
      finite-size-scan .dat present locally); loop closed, no action.
    * ni-melting-point t01 + t02 -- both at status RESULT
      (Tm 1425 +- 25 K Angelo, 1475 +- 25 K Pezold); loop closed.
- in_flight: |
    RECONCILED 2026-08-27T09:0xZ from Erik's sacct paste, and it found
    something the .out files alone could not:
    * 22730595_9 (mu = -2.10) RUNNING, 22:56:30 elapsed against a 24 h
      cap, and GENUINELY ALIVE -- trace.dat mtime 08:56Z, traj 08:45Z.
      Hits the wall ~09:59Z today. Timeout is accepted by design (L34):
      this is the overcharged end of the bracket, ~2.4 steps/s.
    * 22730595_2 (mu = -2.45) SLURM says RUNNING, 22:56:30 elapsed --
      BUT ITS SCIENCE FINISHED 2026-08-26T13:49Z. The .out carries the
      full gate block and `JOB DONE`, which is the LAST LINE of the
      submit script, and results/mu-2.45/ holds ave + trace + traj +
      final-state, all mtime 13:49Z, ave tail at step 88000 exactly like
      the eight COMPLETED tasks. sacct shows step 22730595_2.0 (the lmp
      srun) still RUNNING: the script ran to its end and the job step
      never reaped -- a hung rank at exit, not a running calculation.
      IT HAS HELD 16 CORES ON s.cmmg FOR ~19 h DOING NOTHING and will
      hold them until the 24 h cap unless cancelled.
      HANDED TO ERIK (strict-A): scancel 22730595_2
      Nothing is lost by cancelling -- every output is on disk and
      complete. Task 9 is NOT to be cancelled; it is producing.
    NET: the 900 K bracket is 9/10 harvestable NOW (mu -2.55..-2.15);
    only mu = -2.10 is still being measured.
- deliverables: |
    GROUP TALK on H in Ni, built 2026-08-27T09:1x-09:5xZ at Erik's request
    (45 min deadline, met). SIMULATIONS/20260827_H-IN-Ni_GROUP-TALK/ holds
    deck.yaml, _data/ (extraction + plot scripts) and three figures; the
    .pptx itself Erik moved to SIMULATIONS/ and edited (added three
    fix mc/sites method slides at the front, 12 -> 15 slides).
    TWO FIGURES RENDERED FROM RAW DATA, no OVITO (not installed on M5's
    VM; the container's ovito wheel lacks libOpenGL). Cross-sections cut
    with awk on the Mac, plotted with matplotlib there:
      * fig_H-loading_keyframes.png -- 4 key frames of the (100) slab
        charge-discharge, from the 08-20 extracted key frames on cmmg.
      * fig_H-at-dislocation_prefilled-cells.png -- the run-01 pre-filled
        cells at mu -2.40/-2.32/-2.28. LABELLED AS THE STARTING STATE:
        the pre-fill is statistical from the z-profile, so it shows no
        core decoration and would misread as "no segregation".
      * fig_H-binding-d90_rigid-vs-relaxed.png (added 10:2xZ, Erik asked
        for the binding energies after the pre-fill slide) -- (a) E_seg vs
        r for all 41458 sites of the 1-period map, (b) the 372-site
        relaxed-vs-rigid pair plot.
    NUMBERS ON THE RECORD from the two thread-01 data files:
      rigid 1-period map: oct min -146.3 meV at r = 9.31 A, tet -133.0,
      core-distorted -88.1; medians ~0 (background subtraction verified).
      relaxed 372-site subset: deepest E_seg_relaxed -150.0 meV, deepest
      E_seg_reservoir -131.1 meV = mu_core. By group, rigid -> relaxed
      minima: deep -146.4 -> -150.0, radial -97.4 -> -131.1, bg -0.3 -> 0.0.
      Many distinct near-core sites relax INTO THE SAME core basin at
      -131.1 meV -- the horizontal cluster in panel (b).
    DEFECT FOUND IN THE HARVESTED FIGURES, flagged to Erik, not fixed:
    fig2_Eseg-vs-distance-from-line and fig3_Eseg-map-xz at the top of
    thread 01 are PROBE (stride-40) plots, not the production map. fig3's
    panels are effectively blank (colour scale spans 4e-4 eV) and fig2
    shows only the far field, r = 70..145 A, with a numerical noise floor
    -- which is why Erik could not read the slide built on it. The
    production STRIDE=1 map has never been plotted. Both figures should be
    regenerated from Hbindmap-sites-1period_*.dat before either is used
    again.
- notes: |
    Cloud Cowork session on M5's connected folders (DEVEL, SIMULATIONS,
    cluster-mounts/cmmg). Stale entries still sitting in `active` and NOT
    closed by this session (read-only): 2026-08-02-1647-ingest-eam-dislocs-
    ni-cu (25 d), 2026-08-03-1401-nih-at-dislocs-design (23 d),
    2026-08-05-1425-mcsites-presentation (22 d), 2026-08-20-1145-cluster-
    status-sweep (7 d), 2026-08-24-0753-status-readout (3 d),
    2026-08-24-1531-nih-at-dislocs-status (3 d).

### session_id: 2026-08-26-1608-convexhull-0k-brainstorm
- mode: pilot
- scope: |
    BRAINSTORM + DESIGN of a NEW project: 0 K energies of candidate
    crystal structures for binary systems with LAMMPS (EAM / ACE; ASE
    considered as a driver), convex hull as the stable set, distance to
    hull as the metastability measure -- plus the constrained variants
    Erik asked about (imposed strain, interface / boundary-condition
    constrained hulls). This is the "separate project" that
    ace-ni-nb-potprops moved its 0 K convex hull out to (2026-08-26).
    Erik picked Ni-Cu (EAM + ACE) as the FIRST system -- workflow
    validation on a system with no stable intermetallics -- before Ni-Nb
    consumes it. DISCUSSION FIRST: no project tree, no .in files, no
    submissions until the design is agreed in chat.
- started: 2026-08-26T16:08Z
- last_active: 2026-08-26T17:05Z
- design_decisions_so_far: |
    (chat record pending project.md creation)
    * Ni-Cu (EAM + ACE) first, as workflow validation -- correct hull is
      trivially the two fcc end members; distance-to-hull of the known
      orderings (L12, L10, B2, ...) is the deliverable.
    * DISORDERED solid solutions INCLUDED (Erik, 16:3xZ): 0 K special
      quasirandom structures (SQS) at a few compositions.
    * Point defects / structural vacancies raised by Erik 16:4xZ --
      discussion in chat this turn.
- simulation_root: ~/Desktop/SIMULATIONS   # confirmed by Erik (AskUserQuestion)
- machine: M5   # canon/local/.this-machine
- cluster_identity: |
    cmmg resolves per canon/local/clusters.local.yaml; echoed in chat
    only. Mount ~/cluster-mounts/cmmg connected; not yet exercised this
    session (design turn, no cluster work).
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - DEVEL/CONVEX-HULL-0K/**   (NEW tool repo, created 17:0xZ, holds the
    Claude Code brief; code to be written by a native Claude Code session)
  - SIMULATIONS/CONVEX-HULL-0K-Ni-Cu/**  (name fixed 17:0xZ; tree itself
    will be CREATED BY the Claude Code session per the brief)
- work_done: |
    DESIGN COMPLETE for the validation pass, all decisions Erik's, on
    the record in chat + the brief:
    * Ni-Cu (EAM Fischer-2018 + ACE_CuNi.yaml) first, as validation --
      expected hull = the two fcc end members; e_above_hull spectrum of
      the orderings is the deliverable. SQS solid solutions (icet,
      x = 0.25/0.5/0.75) and point defects (vac Ni, vac Cu, both dilute
      substitutionals) INCLUDED; defects in compounds/SQS deferred.
    * Tool survey answered in chat (16:2xZ): hull construction itself =
      pymatgen/scipy (solved); candidate sourcing = AFLOW prototypes +
      Materials Project (Erik will provide an API key); turnkey
      frameworks (iprPy, matcalc, pyiron, atomate2) noted but not
      adopted; NOTHING off-the-shelf does strained/interface hulls.
    * Erik's requirements: ASE allowed BUT LAMMPS is the only energy
      engine (ASE builds/orchestrates only; every number from lmp) with
      an explicit 1e-8 eV/atom single-point equivalence gate; Claude
      Code (native Mac session) executes autonomously Mac-local, builds
      LAMMPS+ML-PACE if the Mac binary lacks pace; strained-hull
      phase 4 specced in the same brief (biaxial (001), Ni substrate,
      -4..+4 %, BOTH reference conventions).
    * DELIVERABLE WRITTEN + verified (329 lines):
      DEVEL/CONVEX-HULL-0K/BRIEF_hull0k_convex-hull-0K_Ni-Cu-validation.md
      -- standalone brief incl. canon pointers, phase gates, interchange
      schema (contract for the ACE-Ni-Nb_POTPROPS 0 K handover table),
      stop conditions (QUESTIONS.md), no-cluster + no-canon-edit rules.
- designer_lock: |
    NOT taken -- pilot mode. Lock is FREE (released 16:00Z by
    2026-08-26-1305-notify-email-body). If the discussion ends in
    building a reusable hull tool, promotion to designer+pilot will be
    re-checked then.
- scope_collisions: |
    NONE. No existing tree is claimed. Read-only references expected:
    ACE-Ni-Nb_POTPROPS/project.md (handover-table contract),
    EAM-DISLOCS-Ni-Cu / ACE-DISLOCS-Ni-Cu (potentials + prior Ni-Cu
    numbers). No writes proposed into any of them.
- in_flight: (none)
- notes: |
    Cloud Cowork session on M5's connected folders (DEVEL, SIMULATIONS,
    cluster-mounts/cmmg).
    STEP 1b NOT DISCHARGED HERE: non-empty in_flight remains in
    2026-08-25-1725-nih-two-project-status (jobs were RUNNING/PENDING at
    its 15:2xZ reconciliation) and 2026-08-25-1536-nimelt-probe-fix
    (Pezold large-box ladder handed 12:15Z, unreconciled). Both sessions
    were live TODAY; the sacct one-liner is offered in this session's
    startup brief but not gated on -- this session owns no jobs and
    touches no other project tree.

### session_id: 2026-08-26-1424-nicu-phasediagram-status
- mode: pilot
- scope: |
    STATUS READOUT of ALL Ni-Cu phase-diagram work -- both potentials:
    ACE-DISLOCS-Ni-Cu (ACE_CuNi.yaml, stages 0_a0_Evac / 1_a0_T /
    2_sgcmc / 2b_sgcmc) and EAM-DISLOCS-Ni-Cu + cluster DISLOCS-Ni-Cu
    (Fischer-2018 EAM bulk reference sweeps T300/500/700/900) -- plus a
    SLIDE DECK on the MPI-SusMat template showing a(T), the elastic
    constants vs mu / concentration / T, x(mu), and the phase diagram
    (phases vs T and concentration). Erik also asked which runs still
    need to be submitted. Both Ni-Cu trees are READ-ONLY here; the deck
    and its figures are written to a NEW neutral folder because the
    deliverable spans two projects. Erik picked "also design the missing
    runs" (AskUserQuestion 14:2xZ): after the deck, the gap runs are
    DISCUSSED and drafted, nothing submitted without his sbatch.
- started: 2026-08-26T14:24Z
- last_active: 2026-08-26T16:20Z
- simulation_root: ~/Desktop/SIMULATIONS   # confirmed by Erik (AskUserQuestion)
- machine: M5   # canon/local/.this-machine
- cluster_identity: |
    cmmg resolves to <CLUSTER_USER>@<CLUSTER_HOST>, scratch /cmmc/ptmp/<CLUSTER_USER>
    (canon/local/clusters.local.yaml). Mount ~/cluster-mounts/cmmg IS
    connected and readable at survey depth this session.
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - SIMULATIONS/NI-CU-PHASE-DIAGRAM-STATUS/**   (new neutral tree, created here)
- designer_lock: |
    NOT taken and not needed -- pilot mode. Held by
    2026-08-26-1305-notify-email-body (taken 13:05Z, active today).
    LOCK RELEASED 16:00Z when that session closed (noted by it, in this
    entry, so a reader here is not told to wait on a closed session).
    Canon is READ-ONLY here; new rules go to canon/proposals-inbox.md.
- scope_collisions: |
    EAM-DISLOCS-Ni-Cu/** is declared by 2026-08-02-1647-ingest-eam-dislocs-
    ni-cu (active but 24 days stale, in_flight empty). NO takeover: that
    tree is read ONLY here and nothing is written into it. ACE-DISLOCS-
    Ni-Cu is not ingested and is declared by nobody; also read-only.
    2026-08-26-1410-ace-ni-nb-potprops surveyed the same two trees
    read-only 30 min ago -- no conflict, and its survey findings in
    SIMULATIONS/ACE-Ni-Nb_POTPROPS/project.md are reused here rather
    than re-derived.
- work_done: |
    READOUT + DECK COMPLETE, 14:24-14:40Z. Deliverable in
    SIMULATIONS/NI-CU-PHASE-DIAGRAM-STATUS/ (new neutral tree):
    Ni-Cu-phase-diagram-status_2026-08-26.pptx (12 slides, MPI-SusMat 16:9),
    deck.yaml, the four analysis scripts, data/ (7 harvested isotherm files)
    and fig/ (6 PNGs), plus README.md carrying the provenance of every number.
    A copy of the same content also sits there as the sources tarball --
    device_bash cannot delete, so it was left in place, redundant but harmless.
    FINDINGS ON THE RECORD:
    * Both x(mu) isotherm campaigns are DATA-COMPLETE: ACE 51 mu points at
      300/600/1000 K (2b_sgcmc, all 51 harvested), EAM 64 mu points at
      300/500/700/900 K. a(x,T) comes free with them.
    * ACE a(T) is NOT properly measured: 1_a0_T timed out at 96 ps of 190 ps,
      56 ps into the stage-3 NPT equil, so fix ave/time never ran and no
      a0_T_*_ave.dat exists. Recomputed here as the mean of the last 40 thermo
      rows (t = 57-96 ps): 3.53204 / 3.54334 / 3.56160 A at 300/600/1000 K,
      alpha = 1.20e-5 /K. Cross-checks against the c->0 limit of the 300 K
      isotherm (3.53212) to 1e-4 A. Still a redo candidate.
    * EAM pure-Ni a(T) derived by extrapolating each dilute branch to x=0:
      3.52818 / 3.53436 / 3.54109 / 3.54840 A at 300/500/700/900 K,
      alpha = 9.55e-6 /K. The 300 K value reproduces Erik's own direct fit in
      howto_dislocs-Ni-Cu.txt (3.52821 A) to 3e-5 A -- the extrapolation is
      validated, not assumed.
    * ELASTIC CONSTANTS DO NOT EXIST for Ni-Cu, either potential, Mac or
      cluster. Searched both trees by name and content. The only Cij in
      SIMULATIONS is Ni-A0-CIJ-EAM-MEAM, which is pure Ni for the Ni-H work.
      A dedicated bulk thread was designed on the deck (composition ladder +
      -/+ eps_11 and eps_23, C_ij(dmu) by composition with the measured
      x(dmu)); NOTHING WRITTEN, Erik discusses first.
    * PHASE DIAGRAM, headline: the two potentials disagree about Tc. EAM is
      first-order at 300/500 K and continuous at 700/900 K -> Tc(EAM) in
      (500, 700) K. ACE is still two-branch at 1000 K -> Tc(ACE) >> 1000 K.
      Both bands are OUTER bounds: every isotherm is an up-scan from pure Ni,
      so each jump is a limit of metastability, not the binodal. The ACE
      Ni-rich edge is non-monotonic in T (0.21 / 0.28 / 0.23) -- hysteresis.
    * Four ACE 300 K points returned x_Cu = 0 EXACTLY (no swap ever accepted;
      dE_raw - dmu = +0.34 eV = 13 kT, acceptance ~2e-6). That dilute branch
      is unmeasured, not zero.
- work_done_2: |
    SECOND PASS 14:45-16:20Z, at Erik's request: COMPARE TO DFT AND EXPERIMENT.
    Deck grown 12 -> 16 slides; three new figures (07 benchmark table, 08
    Vegard excess, 09 Tc vs E_sub) plus reference.py, which carries every
    reference value with its source. Erik's steer mid-pass: "just look up
    materialsproject, no need to get all the references" -- literature search
    was stopped there; MP itself refused fetch on mp-23/mp-30 (403), so the
    DFT column is MP where it was reachable (Cu Cij) and labelled peer-reviewed
    PBE elsewhere.
    EAM 0 K ROW CLOSED WITHOUT A CLUSTER RUN. a0, E_coh and B for Ni and Cu were
    obtained by evaluating the setfl table of Cu_Ni_Fischer_2018.eam.alloy
    directly over fcc neighbour shells (script mirrored as
    eam_setfl_properties.py in the deliverable folder; pure post-processing of
    the potential file, no LAMMPS, no cluster):
      Ni a0 3.520098 A, E_coh -4.450000 eV/at, B 181.0 GPa
      Cu a0 3.615162 A, E_coh -3.540000 eV/at, B 138.6 GPa
      E_sub(Cu in Ni) = 1.0584 - (4.450 - 3.540) = +0.1484 eV
    BENCHMARK FINDINGS ON THE RECORD:
    * The two potentials answer to DIFFERENT references and must be scored
      against different columns. ACE is a DFT-PBE surrogate: it reproduces PBE
      a0 to 0.1 %, E_vac to +4 %, E_sub to +21 %. The Fischer EAM is fitted to
      EXPERIMENT: a0 to 0.1 %, E_coh exactly, B to 2-4 %.
    * T_c OF THE MISCIBILITY GAP. Real system 605-630 K (CALPHAD Turchanin 2007
      605 K at x_Cu 0.40; ASM Chakrabarti 1991 627.7 K at x_Cu 0.327; APT Duran
      2022 608 K at x_Cu 0.55). EAM 500-700 K -- it BRACKETS the real value.
      ACE still two-branch at 1000 K -- far too high. DFT-PBE cluster expansion
      gives 662 K, so PBE is itself on the high side: ACE inherits and amplifies
      a real DFT error rather than inventing one.
    * E_sub(Cu in Ni) at 0 K is the number behind that: CALPHAD-derived
      0.097 eV, EAM 0.148, DFT-PBE 0.194, ACE 0.234. The regular-solution
      relation Tc = dH_inf/2R orders them but does NOT predict them -- the EAM
      lands on the real Tc with an E_sub 50 % too high, because the 0 K number
      carries no vibrational or configurational entropy. Stated as a scaling
      guide on the slide, not as a fit.
    * THERMAL EXPANSION. ACE alpha 12.0e-6/K vs 12.6-13.4e-6/K measured (-4 %);
      EAM 9.55e-6/K (-24 %). Neither potential is magnetic, so neither can show
      the lambda anomaly in Ni's alpha at the Curie point (~630 K) -- flagged
      on the slide rather than scored as an error.
    * VEGARD DEVIATION, measured against each isotherm's OWN end members so
      thermal expansion is removed: EAM bows UP, +0.029 A (+0.8 %) at x ~ 0.44
      (700/900 K, where the isotherm is continuous and samples the middle);
      ACE bows DOWN, -0.005 to -0.011 A. Experiment reports a slight NEGATIVE
      volume deviation, so ACE has the right sign -- but its gap removes every
      mid composition, so the place where the deviation is largest cannot be
      measured at all. A second, independent cost of ACE's too-high Tc.
      Caveat carried on the figure: the points at the very ends of each branch
      sit at the limit of metastability and may already be partly demixed.
    NOT RETRIEVED, flagged on the slide as an open comparison: the two measured
    Cij(x) papers for Cu-Ni single crystals (Schmunk & Smith, Acta Metall. 8,
    396 (1960); Epstein & Carlson, Acta Metall. 13, 487 (1965)) are paywalled.
    SUBMIT LIST REORDERED: elastic constants first (cheap on EAM, the only
    asked-for quantity with no data at all), ACE a(T) redo second, EAM 600 K
    isotherm third -- the last is now a direct test of the one thing this
    potential appears to get right.
- in_flight: (none of this session's own)
- notes: |
    Cloud Cowork session on M5's connected folders (DEVEL, SIMULATIONS,
    cluster-mounts/cmmg).
    MOUNT HEALTH: same mixed state as the 14:10Z survey -- exact-path
    reads work at the top of ACE-DISLOCS-Ni-Cu/1_a0_T, but the per-run
    subdirectories there return "Operation not permitted" on cat
    (stale macOS TCC grant). Worked around WITHOUT a remount: the Mac
    twin under ACE-DISLOCS-Ni-Cu/FROM-CLUSTER/1_a0_T/ carries identical
    logs and was read instead. If a later step needs the cluster copies,
    Erik re-connects the folder ("Add folder"), NOT a remount.
    STEP 1b NOT DISCHARGED BY THIS SESSION: non-empty in_flight remains in
    2026-08-25-1725-nih-two-project-status (reconciled 08-26T15:2xZ CEST)
    and 2026-08-25-1536-nimelt-probe-fix (Pezold probe pair handed
    08-26T10:45Z, never reconciled). The sacct one-liner was put to Erik
    in-turn. Not gated on: this session owns no job, submits nothing, and
    touches no other project tree.

### session_id: 2026-08-26-1410-ace-ni-nb-potprops
- mode: pilot
- scope: |
    NEW PROJECT ace-ni-nb-potprops: potential properties of the Ni-Nb ACE
    potential (/cmmc/ptmp/<CLUSTER_USER>/POTENTIALS/ace_NiNb.yaml) -- a0, a(T),
    E_vac(Ni), substitution energies (Nb in Ni, Ni in Nb), melting point of
    Ni, and the semi-grand-canonical isotherms x(mu) at 300/500/1000 K.
    Preceded by a READ-ONLY survey of the existing Ni-Cu phase-diagram work
    (ACE-DISLOCS-Ni-Cu and DISLOCS-Ni-Cu / EAM-DISLOCS-Ni-Cu) as the
    template. Erik's standing instruction for this session: DISCUSS EVERY
    SIMULATION BEFORE IT IS WRITTEN OR SUBMITTED. No .in files, no submit
    scripts and no cluster writes until each is agreed in chat.
- started: 2026-08-26T14:10Z
- last_active: 2026-08-26T14:26Z
- simulation_root: ~/Desktop/SIMULATIONS   # confirmed by Erik (AskUserQuestion)
- machine: M5   # canon/local/.this-machine
- cluster_identity: |
    cmmg resolves to <CLUSTER_USER>@<CLUSTER_HOST>, scratch /cmmc/ptmp/<CLUSTER_USER>
    (canon/local/clusters.local.yaml). Mount ~/cluster-mounts/cmmg IS
    connected and readable this session.
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - SIMULATIONS/ACE-Ni-Nb_POTPROPS/**   (new tree, created by this session,
    incl. 01_STATIC-BASELINE-0K/ opened 2026-08-26T16:00Z)
- designer_lock: |
    NOT taken and not needed -- pilot mode. The lock is held by
    2026-08-26-1305-notify-email-body (taken 13:05Z, active today).
    LOCK RELEASED 16:00Z when that session closed (noted by it, in this
    entry, so a reader here is not told to wait on a closed session).
    Canon is READ-ONLY for this session; new rules go to
    canon/proposals-inbox.md.
- scope_collisions: |
    NONE. ACE-Ni-Nb_POTPROPS does not exist yet and is declared by no
    entry. The Ni-Cu trees are touched READ-ONLY for the survey:
    2026-08-02-1647-ingest-eam-dislocs-ni-cu (stale, active) declares
    SIMULATIONS/EAM-DISLOCS-Ni-Cu/**; no write is proposed there.
    ACE-DISLOCS-Ni-Cu is not ingested (no project.md) and is declared by
    nobody.
- work_done: |
    SURVEY (read-only) of the Ni-Cu phase-diagram work, 14:10-14:35Z, and the
    ACE-Ni-Nb_POTPROPS definition tier written. Findings on the record in
    SIMULATIONS/ACE-Ni-Nb_POTPROPS/project.md:
    * ACE-DISLOCS-Ni-Cu 0_a0_Evac COMPLETE; 1_a0_T ALL THREE TIMED OUT at
      ~116/190 ps (no ave file ever written, downstream a(T) read off logs by
      hand); 2_sgcmc ALL 36 TIMED OUT at ~4 ps into the MC stage; 2b_sgcmc
      38/42 hit the 48 h cap but at ~600k of 600k steps -- data complete,
      harvested to ACE_SGv2_summary_{300,600,1000}K.dat (17/15/19 mu points).
    * ROOT CAUSE of the 2_sgcmc failure, quantitative: fix atom/swap
      semi-grand calls energy_full() per attempt, so cost multiplies by
      (1 + ncycles/N). v1 (20 1000) -> 51x predicted / 50x measured;
      v2 (20 10) -> 1.5x predicted / 1.41x measured. The runs were
      under-provisioned (32 ranks = 1000 atoms/core, an EAM-calibrated band;
      ACE is ~60x more arithmetic per atom, cmmg has 256 cores/node).
    * fix sgcmc (VC-SGC, used in the EAM Ni-Cu work) is EAM-only in its fast
      path; with ACE it falls back to full-system energy per swap AND single
      MPI process. Not usable here.
    * Erik's NPT question ANSWERED on the record: iso only for the pure end
      members; aniso for every mixed-composition Ni-Nb cell, because D0a
      Ni3Nb is orthorhombic and an iso cell suppresses the ordering it is
      supposed to find. tri held in reserve.
    ROUND 2 (15:05Z), all in project.md: the FactSage figure MEASURED in
    pixels rather than eyeballed -- the three line compounds sit at
    x_Ni = 0.4614 (= 6/13, mu-Nb7Ni6), 0.7500 (Ni3Nb D0a) and 0.8897
    (= 8/9, Ni8Nb). Erik read the third as NbNi6; NbNi6 would be 6/7 =
    0.857, 27 px away. It is Ni8Nb, and its upper stability limit is
    ~512 degC = 785 K (the axis is CELSIUS), so it is the equilibrium
    Ni-rich second phase at 500 K and not at 1000 K. Erik CONFIRMED the
    Ni3Nb cell and both dilute limits; thread 01 expanded to the full 0 K
    convex hull (fcc Ni, bcc Nb, Ni3Nb, Ni8Nb, mu-Nb7Ni6 + the two point
    defects). His "constrain from the other side" idea assessed and
    accepted on the record as lattice-constrained semi-grand + common
    tangent, with the honest cost named: three of the five branches have
    no pure limit, so each needs an absolute free energy (Frenkel-Ladd +
    reversible scaling; calphy). 300 K recommendation on the record:
    formula for the dilute branch, MC only for the metastability limit.
    Structure sourcing identified (AFLOW prototype labels recorded);
    Mac-side network verified working.
    ROUND 3 (15:45Z) -- ERIK REDIRECTED THE METHOD, project.md RESTRUCTURED.
    His decisions: phase diagram via CALPHY over the solid range to 1250 degC;
    0 K convex hull moved OUT to a separate project; Tm of Nb added alongside
    Tm of Ni; static baseline and a(T) unchanged.
    HIS QUESTION -- "do I still need x(mu), or does it come out of calphy?"
    ANSWERED: it comes out of calphy. The ICAMS tutorial notebook sitting on
    HIS OWN cluster (CALPHY-TUTORIAL/calphy-tutorial/01_phase_transitions.ipynb)
    states the workflow verbatim -- free energies per phase per composition,
    dF_mix vs the pure-element references, COMMON TANGENT, traced over T. The
    common tangent IS the solubility limit. My previous turn had proposed using
    calphy only to supply integration constants for semi-grand branches; that
    is superseded and recorded as a project lesson (read the tool's own docs
    before designing around it). Superseded version kept as
    project-superseded-sgc-anchored-design-2026-08-26.md.
    THE DECISIVE NUMBER: one Ni-Cu-style semi-grand mu point = 1633 core-h;
    the ENTIRE calphy diagram = ~300-500 core-h. ~100x, and structural: MC
    samples the composition (big cell, long time, a full ACE energy eval per
    swap attempt), TI imposes it and reversible scaling removes the T axis.
    x(mu) RESIZED to a cross-check: 1000 K, fcc only, ~8 points, 8000-atom
    cell, ~23 node-h -- justified as (a) the Delta-mu scale any later
    segregation project needs, (b) independent check of the calphy solvus,
    (c) the SRO probe calphy's alchemy cannot provide.
    MEASURED from the figure and recorded: phase homogeneity ranges at 800 degC
    (fcc 8.3 at% Nb, bcc 2.2 at% Ni, mu 3.5 at%, D0a 1.3 at%, Ni8Nb a line),
    and the two invariants at 1177 and 1243 degC -- so 1250 degC is ABOVE both
    eutectics and the liquid must be included (recommended; it is nearly free
    because calphy computes it for Tm anyway, and it yields the two eutectic
    temperatures as validation targets).
    OPEN DEPLOYMENT GATES, flagged to Erik, blocking threads 03-04 only: the
    cmmg CALPHY-TUTORIAL .sub files still carry the Paderborn workshop queue
    block (-A hpc-prf-mlpfits, --gres=gpu:a100:1, source /pc2/users/...), no
    calphy env in /cmmc/ptmp/<CLUSTER_USER>/conda-envs/, and calphy drives LAMMPS as
    a PYTHON LIBRARY while the cmmg lammps/250722 module ships lmp+msi2lmp
    only. fix ti/spring is present (EXTRA-FIX is built). Threads 01-02 are
    unaffected -- plain lmp.
    ROUND 4 (16:05Z) -- ERIK'S DECISIONS: include the liquid, keep the 1250 degC
    ceiling; Tm both ways agreed; x(Delta-mu) CONFIRMED as a real deliverable
    ("I will still need later x(Delta mu) for the segregation work at
    dislocations and maybe grain boundaries") so thread 05 is no longer only a
    cross-check; calphy installation becomes a SEPARATE project; START THREAD 01
    NOW. He also cautioned against assuming the potential reproduces the
    assessed diagram -- folded into open question 1 as three concrete workflow
    constraints (stability-check every reference structure, take the common
    tangent over whatever phases we supply, treat the assessment as a comparison
    target never an input).
    THREAD 01 OPENED, status DESIGN. Done without compute: the three
    intermetallic reference cells BUILT AND VERIFIED from AFLOW prototypes
    (A3B_oP8_59_ae_b -> Ni3Nb D0a, 8 atoms; A7B6_hR13_166_ah_3c -> mu Nb7Ni6,
    39 atoms hex; A8B_tI18_139_hi_a -> Ni8Nb Pt8Ti, 18 atoms) by a stdlib-only
    Python builder that expands each CIF's own symmetry operations, with three
    fatal self-checks (orbit multiplicity vs the CIF, exact composition, minimum
    interatomic distance). All passed first run. Compositions came out 0.4615 /
    0.7500 / 0.8889 against 0.4614 / 0.7500 / 0.8897 measured off the FactSage
    figure -- two independent confirmations of the phase assignment, and AFLOW's
    D0a page names NbNi3 explicitly.
    thread.md specifies NINE 0 K calculations (2 end members, 4 point defects,
    3 intermetallics), the box coupling per run WITH the reasoning (iso for the
    cubic cells; aniso for D0a and Pt8Ti; TRI for mu, because aniso holds xy
    fixed while relaxing lx/ly independently and would silently break a = b on a
    hexagonal cell), cell sizes and why they may be small for perfect crystals,
    the fnorm/sqrt(3N) < 1e-8 convention with the box/relax risk flagged and a
    two-stage fallback named, explicit definitions of every derived quantity,
    and a proposed relaxation-volume addition (free; it is the size-misfit term
    that feeds the segregation work).
    PROBE JUSTIFIED ON SUBSTANCE, not only the rule: first load of
    ace_NiNb.yaml, yaml speciesblock order Nb,Ni vs pair_coeff Ni Nb, the mu
    cell's xy = -lx/2 exactly on LAMMPS's tilt boundary, and box/relax tri with
    min_modify line quadratic. One 9-task array at 10 cg iterations, ~1
    core-minute.
    THREE OPEN QUESTIONS PUT TO ERIK: the output set + cadence (canon-mandated,
    proposal given), whether to include a vacancy in Nb, and whether to add the
    relaxation volumes.
    NOTHING WRITTEN TO THE CLUSTER. No .in, no .slurm, no threads -- Erik's
    standing instruction is that every simulation is discussed first.
- in_flight: (none of this session's own)
- notes: |
    Cloud Cowork session on M5's connected folders (DEVEL, SIMULATIONS,
    cluster-mounts/cmmg).
    MOUNT HEALTH, mixed state observed at survey depth: exact-path reads
    work throughout, but ~15 entries under ACE-DISLOCS-Ni-Cu/2b_sgcmc/
    return "Operation not permitted" on ls/cat (permission mode of the
    two-mount-failures rule -- stale macOS TCC grant), and 1_a0_T run dirs
    reported "No such file or directory" for files the same ls then listed
    (L15 staleness). Both live at once, as on 2026-08-20. The survey below
    is complete enough to be quantitative anyway; if a later step needs
    those 2b_sgcmc files, Erik re-connects the folder ("Add folder"), NOT a
    remount.
    STEP 1b NOT DISCHARGED BY THIS SESSION: open in_flight exists in
    2026-08-25-1725-nih-two-project-status (nine cmmg jobs, reconciled
    08-26T15:2xZ CEST) and 2026-08-25-1536-nimelt-probe-fix (thread-02
    Pezold probe pair handed 08-26T10:45Z, never reconciled). Both belong
    to sessions that were live today; the sacct one-liner was put to Erik
    in-turn. Not gated on, because this session owns no job and touches no
    other project tree.

### session_id: 2026-08-25-1725-nih-two-project-status
- mode: pilot
- scope: |
    Status readout of ni-h-at-dislocs-eam-meam and ni-h-hydride-cycle-eam
    at Erik's request. Read-only on both trees for the readout; Erik has
    said he wants to CONTINUE WORK afterwards -- a write scope will be
    added to this entry once he picks the thread. No submissions yet.
- started: 2026-08-25T17:25Z
- last_active: 2026-08-26T16:05Z
- simulation_root: ~/Desktop/SIMULATIONS   # confirmed by Erik (AskUserQuestion)
- machine: M5
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/04_H-BINDING-MAP-D0-0K/**   (added 17:45Z)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/05_H-BINDING-MAP-D30-0K/**  (added 17:45Z)
  - SIMULATIONS/Ni-H-HYDRIDE-CYCLE-EAM/**                            (added 18:15Z)
  - SIMULATIONS/Ni-H-PHASE-DIAGRAM-EAM-MEAM/03_EAM-ISOTHERM-AND-ELASTICS/15_EAM-ISOTHERM-900K-MCSITES/**  (added 08-26T15:4xZ; run TRANSFERRED in from cycle thread 05 at Erik's request, plus one run-15 section appended to that thread's thread.md -- the rest of the phase-diagram tree is NOT taken)
- scope_change_2: |
    SCOPE ADDED 2026-08-25T18:15Z at Erik's request ("now switching to
    Ni-H-HYDRIDE-CYCLE-EAM"). TAKEOVER, ON THE RECORD: the tree is
    declared by 2026-08-20-1145-cluster-status-sweep (owns_writes_to,
    added 08-20T14:20Z). That entry is 5 days stale (last_active
    08-20T14:55Z) and was flagged stale by both the 08-24 and 08-25
    readouts; its in_flight was fully reconciled by 08-24's sacct.
    This session takes the cycle scope; that entry is NOT closed here.
    Erik told, in-turn. FIRST ACT under the scope: corrected the stale
    thread.md line "TRAJECTORY HANDLING PREPARED (not yet run)" -- the
    zstd + frame jobs 22346669/22346875 COMPLETED 08-20 (per the 08-24
    sacct paste), 20 key frames are on the Mac in key-frames/; flagged
    for correction by two sessions, now done. Threads 04/05 (at-dislocs)
    stay owned: their two production jobs are queued (see in_flight).
    NEW THREAD CREATED 18:40Z: 02_BIAXIAL-MISFIT-STRAIN-NUCLEATION-300K
    (biaxial misfit strain cycle on the H-free slab; Erik's design,
    decisions on the record in its thread.md: +6.56 % = measured
    fcc->hydride in-plane misfit, 1e8 1/s, 1 ns hold, unload, in-plane
    NPT to stress-free; dumps = positions + per-atom stress every 0.1 %
    strain, 100 ps in hold/NPT). Status: open; input + both submits
    WRITTEN on the Mac 18:0xZ, lint clean, probe handed (see in_flight).
    THREAD 03 CREATED 18:4xZ: CHARGE-DISCHARGE-RATEB-CORRECTED-300K --
    Erik's corrected redo of the thread-01 cycle (rate B only, both
    BCs, one parameterized input). Corrections C1-C6 on the record in
    its thread.md: per-atom stress (1 ps averaged), reflecting wall at
    134 a0 (L41 fix), zone-count trace columns (x_local measured),
    honest cadence header, degas 4 ns, dumps 100 ps (~112 frames,
    ~18 GB/task). Probe handed (see in_flight).
    THREAD 04 CREATED 19:0xZ: CHARGE-DISCHARGE-DOME-RATEB-300K -- the
    corrected cycle with an indenter-shaped MC zone (sphere R = 10.5714
    a0, d = 14 a0, imprint Lx/3; ~14.5k Ni loaded vs 201.6k). Input is
    a diff-verified controlled derivative of thread 03's. Probe handed
    (see in_flight).
- scope_change: |
    PROMOTED readout -> pilot write ownership of threads 04/05 (d0/d30),
    2026-08-25T17:45Z, Erik's pick ("can we do the two production
    batches"). TAKEOVER, ON THE RECORD: 2026-08-24-1531-nih-at-dislocs-
    status declares these two thread paths; its conversation ended 08-24
    and its in_flight was reconciled by 2026-08-25-1105. Only threads
    04/05 are taken; its thread-01/02 declarations are NOT. Erik told,
    in-turn. PROBE GATES CHECKED before the hand-over: jobs 22719558 (d0)
    / 22719559 (d30) both COMPLETED, .err empty, JOB DONE, ALL PHASES
    COMPLETE in both named logs each, map rows = n_sites+7, site window
    TRANSFERS (oct/tet/distorted 32.76/66.68/0.56 % d0, 33.19/66.09/0.72 %
    d30 vs d90 33.31/65.92/0.77). L26: 0.0217 / 0.0203 s/site at 256
    ranks -> ~5.8 h / ~5.4 h vs -t 12:00:00 (~2.1x, scripts unchanged).
    Mac<->cluster copies cmp-identical, no staging needed. thread.md 04+05
    updated (status open, probe-verdict section, run-00 READY).
- in_flight: |
    RECONCILED AGAIN 2026-08-26T15:2xZ from Erik's sacct+squeue paste --
    every hand-over is SUBMITTED and has its job ID:
    * 22730592  NiH-BIAXMISFIT-EAM        thread-02 PRODUCTION, PENDING
      (Resources). 12 h cap vs 6.8 h measured.
    * 22730593_[0-1]  CYC2-RATEB re-probe  thread-03, PENDING (Priority)
    * 22730594_[0-1]  CYC2-DOME re-probe   thread-04, PENDING (Priority)
      -> both re-probes run the p-p-f fixed inputs; productions stay
      blocked on their gate blocks.
    * 22730595_[0-9]  NiH-MUBRACKET-900K-EAM production: task 0
      (mu=-2.55) COMPLETED in 2:43:55; tasks 1-9 RUNNING ~2:45 at
      15:2xZ. Cheapest-first ordering as designed; 24 h cap.
    * CLOSED: 22728805 (d0, 4:59:52) / 22728806 (d30, 4:46:56) run-00
      productions COMPLETED overnight -- harvest + run-01 pending.
    NOT MINE, noted in passing: the nimelt-t01 prepare/measure family
    (22728719..22731060) belongs to 2026-08-25-1536-nimelt-probe-fix.
    PREVIOUS RECONCILIATION (2026-08-25T20:0xZ, from the mount):
    * at-dislocs d0/d30 run-00 productions 22728805/22728806 COMPLETE:
      JOB DONE, rows = n_sites+7 (956767/951034), splits 33.35/66.06/
      0.59 and 33.28/66.01/0.70 -- UNHARVESTED; next: harvest + run-01
      probes (thread.md status tables updated).
    * cycle thread 02 probe 22728919 PASSED all gates (verdict in
      thread.md; 137 steps/s, frames 246 MB). PRODUCTION HANDED with
      -t bumped 8->12 h: sbatch submit-biaxial-misfit-cycle_*.slurm.
    * cycle threads 03+04 probes 22728943/+dome FAILED at init:
      wall/reflect in periodic z (precondition; canon proposal
      2026-08-25-2015 filed). FIXED: boundary p p f in both inputs,
      re-linted. RE-STAGE (2 inputs) + RE-PROBE handed.
    * thread 05 run 00 bracket probe 22728969 TIMED OUT = the
      measurement (L34): mu=-2.10/900 K overcharges to x=1.86,
      a_eff 3.99 A, ~2.4 steps/s. PRODUCTION HANDED with -t 8->24 h
      and timeout-accepted-by-design header; crystallinity check
      required for any x>1 point at analysis.
    PREVIOUS RECORD (for provenance): submitted by Erik, squeue paste
    2026-08-25T18:0xZ:
      22728805  NiH-BINDMAP-D0-EAM   PENDING (Resources)
      22728806  NiH-BINDMAP-D30-EAM  PENDING (Priority)
    Run 00 STRIDE=1 full rigid maps, 256 ranks p.cmmg, -t 12:00:00:
      04_H-BINDING-MAP-D0-0K/00_SITE-CATALOGUE-AND-RIGID-MAP:
        sbatch submit-Hbindmap-rigid_Ni-disloc-d0-Pezold-EAM-0K.slurm
      05_H-BINDING-MAP-D30-0K/00_SITE-CATALOGUE-AND-RIGID-MAP:
        sbatch submit-Hbindmap-rigid_Ni-disloc-d30-Pezold-EAM-0K.slurm
    Expected ~5.8 h (d0, 956760 sites) / ~5.4 h (d30, 951027 sites).
    ALSO HANDED 2026-08-25T18:0xZ (strict-A): hydride-cycle thread 02
    PROBE -- stage the three thread-02 files by rsync-over-ssh to
    /cmmc/ptmp/<CLUSTER_USER>/Ni-H-HYDRIDE-CYCLE-EAM/02_BIAXIAL-MISFIT-STRAIN-
    NUCLEATION-300K/, then
    sbatch submit-biaxial-misfit-cycle_Ni-slab-100_Pezold-EAM.probe.slurm
    (full-size cell, 1024 ranks p.cmmg, 110k steps, cap 00:30:00).
    Probe gates in the submit header; production ONLY after they pass,
    with -t re-derived from the probe steps/s (L26).
    ALSO HANDED 2026-08-25T18:4xZ (strict-A): thread 03
    (CHARGE-DISCHARGE-RATEB-CORRECTED) PROBE, array 0-1 fixlat/freelat,
    full-size cell, 1024 ranks, mcsites fork binary, 110k steps, cap
    1 h: stage the three thread-03 files, then
    sbatch submit-cycle-rateB-corrected_Ni-slab-100_Pezold-EAM.probe.slurm
    Production (array 0-1, 11.21 ns, 4-day cap, ~90 h projected) blocked
    on both probe gate blocks; corrections C1-C6 in thread.md.
    ALSO HANDED 2026-08-25T19:1xZ (strict-A): thread 04
    (CHARGE-DISCHARGE-DOME-RATEB) PROBE, same shape (array 0-1
    fixlat/freelat, 1024 ranks, fork binary, 110k steps, cap 1 h):
    stage the three thread-04 files, then
    sbatch submit-cycle-rateB-dome_Ni-slab-100_Pezold-EAM.probe.slurm
    Zone = spherical indenter volume R = 10.5714 a0 centred
    (30,30,116.5714) a0 (d = 14 a0, imprint = Lx/3); input verified by
    diff to differ from thread 03's ONLY in header + MCZONE block.
    Production blocked on both probe gates; expected well under cap.
    ALSO HANDED 2026-08-25T19:4xZ (strict-A): thread 05 run 00
    MU-BRACKET-900K PROBE (1 task, L10 bulk mc/sites at 900 K, 16
    ranks s.cmmg, cap 30 min): stage the three run-00 files, then
    sbatch submit-mu-bracket_NiH-EAM-Pezold_900K.probe.slurm
    Production = 10-mu array (-2.55..-2.10, 0.05 steps, cap 8 h),
    blocked on the probe. The 900 K CYCLE runs (threads 05/06, both
    BCs, dt 0.5 fs, same rate-B physical schedule, 50 ps dumps,
    2-3-job restart chains) are BLOCKED on this bracket's mu_t verdict
    -- decisions and a0(900 K) = 3.57429 A on the record in thread 05's
    thread.md; thread 06 is a design stub.
    A resuming session: check gates in the STRIDE=1 run dirs (JOB DONE,
    ALL PHASES COMPLETE x2, .err empty, rows = n_sites+7), then harvest
    the .dat + logs and run the d90-style run-00 analysis (deepest trap,
    reach, oct/tet split of the deep set); run 01 probe comes after.
- notes: |
    Cloud Cowork session on M5's connected folders (SIMULATIONS, DEVEL,
    cluster-mounts/cmmg). Mount readable at readout depth this session.
    NOT touched: 2026-08-25-1536-nimelt-probe-fix is active TODAY with a
    non-empty in_flight (nimelt production prepare handed); that loop is
    owned by a live session and is not reconciled here, only flagged.
    Open loops INSIDE this readout's scope, from SESSIONS.md: (a) d0/d30
    run-00 probes 22719558/22719559 COMPLETED 08-24, gates never checked,
    productions not submitted (threads 04/05, unowned); (b) hydride-cycle
    thread.md line "TRAJECTORY HANDLING PREPARED (not yet run)" is STALE
    -- zstd + frame jobs 22346669/22346875 COMPLETED 08-20, 20 key frames
    already on the Mac (flagged 08-24, still uncorrected; correction
    belongs to whoever takes that scope).

### session_id: 2026-08-24-1531-nih-at-dislocs-status
- mode: pilot
- scope: |
    READ-ONLY status readout of ni-h-at-dislocs-eam-meam at Erik's request
    ("what is the status of the Hydrogen at Dislocations project?").
    No writes to the project tree, no harvest, no analysis artefacts, no
    submissions. Scope to be promoted if Erik picks up the thread.
- started: 2026-08-24T15:31Z
- last_active: 2026-08-24T17:20Z
- simulation_root: ~/Desktop/SIMULATIONS   # M5, from canon/local/.this-machine
- machine: M5
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/**            (added 2026-08-24T15:58Z)
  - cluster-mounts/cmmg/Ni-H-AT-DISLOCS-EAM-MEAM/**    (added 2026-08-24T15:58Z)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/04_H-BINDING-MAP-D0-0K/**   (NEW THREAD)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/05_H-BINDING-MAP-D30-0K/**  (NEW THREAD)
- scope_change: |
    PROMOTED from read-only to WRITE OWNERSHIP of ni-h-at-dislocs-eam-meam,
    2026-08-24T15:58Z, at Erik's request ("let's stay with 1e-7. can you setup
    the runs?"). Scope: thread 01 run 01 production only -- write the submit
    script, propose the staging, hand over the sbatch. NOT thread 02.
    TAKEOVER, ON THE RECORD: session 2026-08-03-1401-nih-at-dislocs-design still
    sits in `active` declaring `owns_writes_to: SIMULATIONS/
    Ni-H-AT-DISLOCS-EAM-MEAM/**` and cluster-mounts/cmmg/<same>. It is 20 days
    stale (last_active 2026-08-04T18:40Z) and was flagged stale by both the
    08-20 and 08-24 sweeps. This session takes the write scope. Its `in_flight`
    (the two staged submits) is DISCHARGED -- both ran, both are read; cleared
    below. The entry is NOT closed by this session: its design scope is broader
    than run 01 and is not mine to declare finished. Erik told, in-turn.
- erik_decision: |
    FIRE TOLERANCE FOR PRODUCTION = 1e-7 per dof (Erik, 2026-08-24T15:5xZ,
    answering probe gate 3). E_ins_loose is the production answer.
- in_flight: |
    RECONCILED 2026-08-25T11:05Z by session 2026-08-25-1105-sim-status,
    from the run outputs on the mount (step 1b): PRODUCTION COMPLETED.
    Ran 2026-08-24T18:45:23-20:02:10Z (~4.5 h queue wait after the
    ~16:1xZ submit, then 77 min against the 3 h budget; loop srun wall
    4595.52 s, 12.354 s/site at 256 ranks). `JOB DONE` present, .err
    empty (0 bytes), .dat has 387 rows = 372 sites + 15 headers.
    GATES, from NiH-HBIND-RELAX-D90-EAM.out:
      GATE 1 restore: 0.0000 meV max drift -- PASS, loop design valid.
      GATE 2 cost: FIRE iters to 1e-7/dof min 147 / median 161 /
        MAX 20000 -- at least one site HIT THE ITERATION CEILING
        (probe max was 1261). Identify the row(s) before quoting them.
      GATE 3 |E_tight - E_loose|: deep max 0.046 meV (n=43), bg max
        0.0010 meV (n=206) -- 1e-7 SHIPS WITH NO CAVEAT FOR THE TRAPS.
        BUT radial max 439.8 meV (median 0.0050): >=1 radial-ladder
        site diverged (almost certainly the 20000-iter site; likely a
        basin hop in the tight stage). E_seg(r) fits must drop or
        re-examine that row; the trap-depth headline is unaffected.
    deepest E_ins_loose: -2.242993 eV. JOB ID 22719302 (Erik's sacct
    paste, 2026-08-25). HARVESTED AND ANALYSED 2026-08-25 by session
    2026-08-25-1105-sim-status -- see that entry's work_done; mu_core
    = -2.4061 eV, thread-02 grid confirmed.
    WHAT A RESUMING SESSION MUST DO, in this order:
      1. Read NiH-HBIND-RELAX-D90-EAM.out -- it prints all three gates.
         GATE 1 (restore_meV at the floor) decides whether the .dat is
         usable at all. GATE 3 is now broken down BY SITE CLASS and is the
         evidence for or against the 1e-7 choice.
      2. Check `JOB DONE` and `ALL PHASES COMPLETE`, and that the .dat has
         372 rows + 15 header lines.
      3. If TRUNCATED: the subset is sorted deepest-first, so the traps are
         done and the .dat is still usable. Do not discard it.
      4. Harvest to the Mac; then re-derive mu_core from E_ins_loose with the
         per-z-layer background subtracted, and re-centre the thread-02 mu
         grid (current proposal -2.471..-2.291).
    Expected ~64 min against a 3 h budget.
- work_done: |
    BOTH 2026-08-04 HAND-OVERS ARE NOW ACCOUNTED FOR AND THEIR OUTPUT READ.
    Mount is healthy this session (no EPERM, no L15 emptiness) -- every file
    below was read by exact path off ~/cluster-mounts/cmmg/.

    (1) THREAD 02 RUN 00, STRAIN REFERENCE -- COMPLETE AND CLEAN, and now
    EYEBALLED, which the 08-04 hand-over asked for and nobody had done.
    Job 21638799, 8 s, 08-04T21:21. `JOB DONE` present, .err empty.
    Coordination gate PASSES on all six rows: 6/6 oct, 4/16 tet.
    Numbers (E_ins, eV, at a0(300 K) = 3.536955 A):
      oct  below -2.099994 / unstrained -2.084526 / above -2.068502  (rigid)
      oct  below -2.166516 / unstrained -2.153235 / above -2.139557  (relaxed)
      tet  below -1.572174 / unstrained -1.551486 / above -1.529998  (rigid)
      tet  below -1.775177 / unstrained -1.757999 / above -1.740652  (relaxed)
    ASSUMPTION 1 REMOVED -- the unstrained reference DOES sit at the midpoint
    of the step: |midpoint - unstrained| = 0.28 (oct rigid), 0.40 (tet rigid),
    0.20 (oct relaxed), 0.08 (tet relaxed) meV. All far under kT/100.
    ASSUMPTION 2, the number the pre-fill actually needs: at a0(300 K) the
    RELAXED oct step is 26.96 meV below-to-above (rigid 31.49; tet 34.53
    relaxed / 42.18 rigid). The pre-fill recipe in the thread file was built
    on the 0 K RIGID 24.2 meV, giving c_below/c_above = 2.55; the relaxed
    300 K value gives exp(26.96/25.85) = 2.85. NOT reconciled here -- flagged
    for whoever writes the pre-fill. Note also that thread 01's rigid
    ASYMPTOTE was 39.1 meV against 31.49 measured here for the full strain
    state; the two are not obviously the same quantity and should be made to
    agree before either is used.

    (2) THREAD 01 RUN 01, RELAXED-BINDING PROBE -- PASSED, ALL THREE GATES,
    and its .dat had never been read (it was EPERM for the 08-24 morning
    session, which is why production was blocked). Job 22344727, lmp 3:26,
    08-20T14:57, after Erik applied the `group HGRP clear` fix.
      GATE 1 restore: restore_meV = -0.0000 on ALL 20 rows. At the numerical
        floor. The coordinate-restore loop design is VALID.
      GATE 2 FIRE cost: 149 / 162 / 1261 (min/median/max) to 1e-7 per dof;
        191 / 252 / 3254 extra to 1e-8. Loop wall 194.91 s for 20 sites at
        256 ranks = 9.746 s/site. This IS the L26 calibration source (loop
        timer, setup excluded). 372 sites -> 60.4 min, x1.5 -> ~91 min.
        CAVEAT, unresolved: the probe drew 12 of 20 sites from the far-field
        background (median 162 iterations) and only 2 deep ones (1261, 1223).
        Production is 43 deep + 206 bg + 123 radial, so the per-site mean is
        NOT the probe's mean. Size from the deep-site cost, not from 9.746 s.
      GATE 3 tolerance: max |E_ins_tight - E_ins_loose| = 0.9970 meV. The gate
        as written was "well under 1 meV". 0.997 meV is NOT well under 1 meV
        -- it is exactly at it. THIS GATE IS AMBIGUOUS AND IS ERIK'S CALL:
        run production at 1e-7 and accept ~1 meV, or pay the ~1.7x iteration
        cost for 1e-8. The worst offender is index 490064 (566 loose / 3254
        tight iterations), a near-core site, so the error is not uniformly
        distributed -- it is largest where the physics is.
    PRELIMINARY SCIENCE, FROM 2 DEEP SITES ONLY -- DO NOT QUOTE:
      RELAXATION APPEARS TO MAKE THE DEEPEST TRAP SHALLOWER, NOT DEEPER,
      which is the opposite of what RESTART-BRIEF section 3 assumes when it
      calls mu_core ~ -2.421 eV an UPPER bound.
      Site 372285 (x 117.56, z 123.10, one partial): E_seg_rigid -0.1402 eV
      (reproduces the probe .out's -0.1401 exactly, so the z-layer background
      interpolation used here matches theirs) -> E_seg_relaxed -0.1311 eV.
      Site 490271 (x 139.33, z 123.08, the other partial) goes the other way:
      -0.1025 rigid -> -0.1310 relaxed. The two partials land within 0.15 meV
      of each other after relaxation, which is the symmetry check passing.
      If -0.131 eV survives the 372-site run, mu_core moves UP ~15 meV to
      ~ -2.406 eV and the thread-02 mu grid (-2.471..-2.291) should be
      re-centred accordingly. The probe's deepest selected site was -0.1401
      against the map's global -0.1464, so the global minimum was not sampled.
      THE 372-SITE PRODUCTION IS WHAT SETTLES THIS. Nothing was written to the
      project tree; this is a readout, not an analysis.
- production_setup: |
    RUN 01 PRODUCTION SET UP, 2026-08-24T15:58Z. Written on the Mac, NOT yet
    staged to the cluster and NOT yet submitted (cluster writes and sbatch are
    both Erik's to run).

    (a) MAC-SIDE .in WAS STALE AND IS NOW FIXED -- a survivability defect, not
    a cosmetic one. `SIMULATIONS/.../01_RELAXED-BINDING-SUBSET/relax-Hbind_
    Ni-disloc-d90-Pezold-EAM-0K.in` still carried `group HGRP clear` on line
    193, i.e. the exact defect that killed job 21638800. Erik fixed it on the
    CLUSTER on 08-20; the backed-up copy was never updated, so the only
    backed-up version of this input was the broken one. Line deleted; the Mac
    file now `cmp`s IDENTICAL to the cluster file that passed probe 22344727.
    Previous content preserved as .in.pre-hgrpfix.bak.
    CLASS OF DEFECT, worth a canon proposal: a fix applied on the cluster does
    not propagate to the backed-up side by itself, and nothing checks. Filed as
    a proposal rather than fixed here (pilot may not merge canon).

    (b) NEW FILE: 01_RELAXED-BINDING-SUBSET/submit-Hbind-relaxed_Ni-disloc-d90-
    Pezold-EAM-0K.slurm. Production sibling of the .probe.slurm. Output dir
    `relaxedbind-d90-Pezold-EAM-0K/` (cannot collide with probe/). Selector run
    with NO --max. bash -n and ast.parse both clean; the embedded gate/timing
    block was DRY-RUN against the probe's own output files and produces correct
    output (see (d)).

    (c) WALLTIME 03:00:00, measured basis (L26 route (a), probe 22344727):
    7.862 ms per FIRE iteration at 256 ranks (40 minimize loops in the probe
    log, spread 7.72-7.76 ms) plus 2.49 s/site of non-minimize overhead
    (create_atoms / delete_atoms / `set` over 568606 atoms / reneighbor),
    derived as loop wall 194.91 s minus summed LAMMPS Loop times 145.12 s.
    That overhead is a QUARTER of the run and does not shrink if a minimize
    stage is dropped -- the probe's flat 9.746 s/site mean hides it.
    Per class, iterations (loose/tight): deep 1242.0/984.5, bg 157.5/225.2,
    radial 341.2/1227.8. Against 43/206/123 that is 127814 loose + 239742
    tight iterations = 48.2 min compute + 15.4 min overhead = 63.6 min.
    3 h is ~2.8x, not the usual 1.5x, because the deep mean rests on 2 probe
    sites and the radial tight mean on 6 (one of which, 3254 iterations,
    carries the class). Padding is free; the subset is sorted deepest-first so
    a timeout still has the traps.

    (d) BOTH MINIMIZE STAGES KEPT, .in BYTE-IDENTICAL TO THE PROBE'S. Reasons
    in the script header. Short version: deleting the tight stage would make
    production the first run of an unprobed file, for a saving of ~32 min; and
    the loose-vs-tight column over all 372 sites answers whether the gap
    correlates with trap depth, which cannot be recovered later without
    re-running. The script's gate 3 now breaks the gap down BY SITE CLASS.
    THE DRY RUN ALREADY LARGELY ANSWERS IT, and it favours Erik's choice:
    on the probe's 20 sites the |E_tight - E_loose| median is 0.0010 meV, and
    by class -- deep max 0.0080, bg max 0.0010, radial max 0.9970. THE 0.997 meV
    OUTLIER IS A RADIAL-LADDER SITE (490064), NOT A TRAP. So 1e-7 looks safe
    for the trap depths (the thread's headline number) and the residual risk
    sits in E_seg(r) on the ladder. This is a 20-site read and production is
    what confirms it -- but the 08-24 framing of gate 3 as "ambiguous, and
    worst exactly where the physics is" was too pessimistic: the worst case is
    NOT at a trap. Corrected here rather than left standing.

    (e) STAGING AND SUBMIT HANDED TO ERIK (strict-A). One file to copy; the .in
    and the selector are already on the cluster and correct. Commands given
    in-turn. STAGED AND SUBMITTED BY ERIK ~16:1xZ -- see in_flight.
- other_characters_check: |
    2026-08-24T16:40Z, Erik: "set up the same runs (also the unrelaxed
    insertion energy) for the other dislocation characters. Please check if
    they relaxed ok." READ-ONLY CHECK DONE; NO INPUTS WRITTEN YET (one
    decision is Erik's, put to him in-turn).

    (1) ALL FOUR PEZOLD-EAM PARENT RELAXATIONS CONVERGED. Every one reports
    `Stopping criterion = force tolerance`, fnorm_per_dof ~1e-8:
      d0   1402 steps, 570720 atoms, fnorm/dof 9.99e-9
      d30  1691 steps, 567203 atoms, fnorm/dof 9.92e-9
      d60  1413 steps, 568280 atoms, fnorm/dof 9.81e-9
      d90  2557 steps, 568606 atoms, fnorm/dof 6.21e-9   (the one in use)
    So "did they relax ok" is YES on the convergence criterion for all four.

    (2) BUT d60's CELL IS WRONG, AND CONVERGENCE DOES NOT COVER IT.
    Final in-plane stresses, read off the last thermo row of each log:
      char   pxx (GPa)   pyy (GPa)
      d0      -0.179      -0.214
      d30     -0.337      -0.259
      d60     -1.534      -0.809     <-- 4-8x the others, in BOTH directions
      d90     -0.184      -0.207
    This CONFIRMS the open question raised by 2026-08-20-1145-cluster-status-
    sweep (which saw -1.53/-1.39/-1.55 GPa across three independent potentials
    and concluded it is the CELL, not the model). Now quantified for what it
    does to THIS project, using the project's OWN strain reference rather than
    a literature number:
      thread 02 run 00 gives dE_ins/d(eps_vol) = -13.28 meV / 0.36 % =
      -3.689 eV per unit volumetric strain (oct, relaxed, a0(300 K)).
      With K ~ 180 GPa that implies an H relaxation volume of ~3.3 A^3,
      which is the right ballpark and makes the slope trustworthy.
      d60 carries ~0.65 GPa of EXTRA hydrostatic tension over d90
      (p_h = (pxx+pyy+pzz)/3, pzz ~ 0 under `boundary p p s`), so
      E_ins in d60 is offset by about **-13 meV** relative to the other cells.
    WHY THAT IS AND IS NOT FATAL:
      - For E_seg it LARGELY CANCELS, because E_seg is referenced to the same
        cell's own far-field background. Thread 01's headline number survives.
      - It does NOT cancel for the thing the character series EXISTS to do.
        13 meV is ~9 % of d90's trap depth, and it is a systematic offset
        CORRELATED WITH CHARACTER -- i.e. exactly the confound that makes
        "does the answer depend on character" unanswerable. Comparing one
        -1.5 GPa cell against three -0.2 GPa cells measures stress state as
        much as character.
      - It does NOT cancel for thread 02 at all: a cell-wide 0.65 GPa tension
        shifts the mu at which the local transition happens, which is the
        quantity thread 02 measures.
    Two further d60 oddities found here, consistent with a bad cell:
      - its glide plane sits at z = 124.25 A against 126.25 A for all three
        others (the construction is 2 A off, not just stressed);
      - its SF ribbon is the only one whose measured width MOVES with the
        detection threshold (92-132 A at 15/25 %, 102-130 A at 40 %); the
        other three are threshold-stable to the Angstrom.

    (3) GEOMETRY MEASURED FOR ALL FOUR, from the final-snapshot dumps
    (per-atom PE excess, free (111) surfaces excluded -- they dominate any
    PE criterion and a first attempt located the surfaces, not the line).
    VALIDATED ON d90: the method returns line_x = 125.5 A and glide
    z = 126.25 A against project.md's 125.5 / 126.5. Line centre is the
    MIDPOINT OF THE SF RIBBON, not the peak of the PE histogram -- the peak
    finder gave 136.0 A and was wrong.
      char  glide_z   SF ribbon x    width   line_x   x to periodic edge
      d90    126.25   102 - 148 A    46.0    125.5    124.9
      d30    126.25   116 - 152 A    36.0    133.5    116.4
      d0     126.25   128 - 160 A    31.0    144.0    106.3
      d60    124.25    92 - 132 A    40.0    112.5    112.5
    The dissociation widths order screw < d30 < d60 < edge (31/36/40/46 A),
    which is the textbook ordering and is the check that the extraction is
    physical.

    (4) THE CELLS ARE TWO DIFFERENT FAMILIES, AND d0/d60 ARE NOT d90 WITH A
    NEW PATH. From 00_SCALED-CELLS/scaled-cells-manifest.dat:
      d30, d90:  x[-101] y[1-21] z[111], ly = 99.268303 A
                 = 23.0000 periods of a*sqrt(6)/2 = 4.316013 A
      d0,  d60:  x[-211] y[0-11] z[111], ly = 99.674055 A
                 = 40.0000 periods of a/sqrt(2)  = 2.491853 A
    Both are exactly commensurate, so the y-image collapse works for both --
    but the FACTOR IS 40 FOR d0/d60, NOT 23. The selector keys on
    (round(x*100), round(z*100)) and so adapts with no code change; the
    factor-23 language in the .in and selector headers is d90-specific and
    must not be copied verbatim into the new characters' files.
    CONSEQUENCE FOR COST: d0/d60 run 01 subsets are CHEAPER than d90's
    (a 40x collapse instead of 23x), while their run 00 maps cost the same.
    CONSEQUENCE FOR d0's FAR FIELD: only 106.3 A from line to periodic x
    edge, against 124.9 A for d90. The d90 selector ran `--x-far 100.0`;
    at 106.3 A that leaves a thin sliver and may starve the per-z-layer
    background ladder. d90's measured reach (bulk-like beyond r = 72 A)
    says ~85-90 A is still safe, but --x-far MUST be re-chosen per character
    and the realised bg count per z layer checked, not assumed.

    (5b) ERIK'S DECISIONS, 2026-08-24T16:5xZ (AskUserQuestion):
      * d60 HELD. Set up d0 + d30 only; d60 waits for its cell to be fixed.
      * Trap threshold stays -0.100 eV for comparability, PLUS an
        N-deepest fallback so a screw that binds nothing still gets its
        strongest sites relaxed.

    (5c) SELECTOR PATCHED -- AND IT CARRIED A LATENT BUG d90 WAS IMMUNE TO.
    `select-relaxation-subset.py` computed r = hypot(x - x_core, z - z_core)
    with NO minimum image, although x is periodic under `boundary p p s`.
    Harmless for d90, whose line sits 0.28 A from the box centre, so no site
    is ever more than lx/2 away. WRONG for an off-centre line: d0 is 19 A off
    centre and d30 8 A, where the raw difference overstates r by up to twice
    the offset for sites on the far side, corrupting the radial ladder and
    E_seg(r). Fixed; `--lx` added and now passed explicitly.
    VERIFIED AGAINST THE REAL d90 DATA rather than argued: patched and
    original selectors were both run on run 00's 953534-site catalogue and
    map. Same 372 indices in the same order, same 43/206/123 split, same
    deepest site (index 490236, E_seg -0.1464 eV, r 9.31 A). ONLY r_line
    differs, by at most 0.5627 A. So d90's in-flight production is NOT
    affected and nothing needs re-running.
    Also added `--deep-min-count` per Erik's decision. Both new threads get
    the patched copy; the d90 copy is LEFT ALONE because its production job
    is in flight against the on-cluster version.

    (5d) WRITTEN, NOTHING SUBMITTED. 16 files across two new threads:
      04_H-BINDING-MAP-D0-0K/  and  05_H-BINDING-MAP-D30-0K/
        thread.md
        00_SITE-CATALOGUE-AND-RIGID-MAP/
          catalogue_*.in, map-rigid_*.in, make-site-list.py,
          submit-*.slurm (STRIDE=1, -t 12:00:00),
          submit-*.probe.slurm (STRIDE=40, -t 00:30:00)
        01_RELAXED-BINDING-SUBSET/
          relax-Hbind_*.in, select-relaxation-subset.py (patched),
          submit-*.probe.slurm
    The run-01 .in COMMAND BODIES are byte-identical to the d90 version that
    passed probe 22344727, apart from cell path, output names and expected
    atom count -- the loop is the risky part and is already validated. Only
    the HEADERS were rewritten, deliberately: d90's headers state measured
    d90 facts (23 images, 108-141 A partial spread, "relaxation only deepens
    traps") that are FALSE for these cells, and substituting them would have
    propagated wrong numbers into two new threads.
    Checks run on every generated file: `bash -n` on all four slurm scripts,
    `ast.parse` on both selectors and on both embedded python blocks, and a
    per-file body audit (2 minimize commands, no `group HGRP clear`,
    store/state present, `compress no` present, correct cell path, correct
    atom count, no stray "d90" in any command body).
    project.md updated: new `cell_facts_by_character` block for all four
    characters, `d60_status` recording the block, and the threads listed.
    Frontmatter re-parsed as YAML after editing.

    (5) SEQUENCING, which Erik should know before choosing scope: run 01
    CONSUMES run 00's map, so each new character needs its own run 00
    (catalogue + rigid map, the 4 h 47 min / 256-rank job) FIRST, then a
    run 01 probe, then run 01 production. Run 01's .in is character-generic
    apart from paths and can be written now; it cannot be probed until that
    character's run 00 has landed.
- notes: |
    designer lock NOT taken. Read-only; no project file touched.
    STALE ENTRY STILL OPEN: 2026-08-03-1401-nih-at-dislocs-design declares
    `owns_writes_to: SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/**` and has not been
    active since 2026-08-04T18:40Z -- 20 days. Its `in_flight` (the two staged
    submits) is DISCHARGED by this entry's work_done and can be cleared when
    someone takes the write scope. This session did NOT clear it: clearing it
    is a write to another session's entry and the scope here is read-only.
    Also still open and stale: 2026-08-05-1425-mcsites-presentation,
    2026-08-02-1647-ingest-eam-dislocs-ni-cu, 2026-08-20-1145-cluster-status-
    sweep. Concurrent today: 2026-08-24-0753-status-readout (ni-h-phase-
    diagram, ni-h-diffusivity, ni-melting-point-eam) -- DISJOINT from this
    scope, no collision.

### session_id: 2026-08-24-0753-status-readout
- mode: pilot
- scope: |
    READ-ONLY status readout across all open projects, at Erik's request
    ("what is the status of our simulations?"). No writes to any project
    tree, no harvest, no analysis, no submissions. Scope will be narrowed
    or promoted once Erik picks a project; this entry is updated then.
- started: 2026-08-24T07:53Z
- last_active: 2026-08-24T17:35Z
- simulation_root: ~/Desktop/SIMULATIONS   # M5, from canon/local/.this-machine
- machine: M5
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - LLM-LAMMPS-public/canon/**                   (DESIGNER 16:50-17:10Z; re-taken 17:30-17:35Z)
  - SIMULATIONS/Ni-H-PHASE-DIAGRAM-EAM-MEAM/**   (added 2026-08-24T08:30Z)
  - SIMULATIONS/Ni-H-DIFFUSIVITY/**              (added 2026-08-24T11:00Z)
  - SIMULATIONS/NI-MELTING-POINT-EAM/**          (NEW PROJECT, 2026-08-24T15:00Z)
- scope_change: |
    PROMOTED from read-only to WRITE OWNERSHIP of ni-h-phase-diagram-eam-meam,
    2026-08-24T08:30Z, at Erik's request ("ok, can we start with the Ni-H phase
    diagram. can you make a presentation of the analysed data?").
    AGREED SHAPE (AskUserQuestion, 2026-08-24): a NI-H SCIENCE talk -- the phase
    diagram itself, NOT the mc/sites method talk; slides for an update, no
    duration target; run 14 to be HARVESTED AND ANALYSED FIRST and included as
    real content; deliverable a .pptx on the MPI-SusMat corporate template.
    TAKEOVER, ON THE RECORD: session 2026-08-05-1425-mcsites-presentation still
    sits in `active` and declares `owns_writes_to: SIMULATIONS/
    Ni-H-PHASE-DIAGRAM-EAM-MEAM/**`. It is 19 days stale (last_active
    2026-08-05T15:55Z) and was already flagged as stale by the 08-20 sweep.
    This session takes the write scope. NOT CLOSED by this session -- its
    presentation scope (the mc/sites method deck, 7-section spine) is a
    DIFFERENT deliverable from the one Erik asked for today and is not mine to
    declare finished. Erik told, in-turn.
- work_done: |
    RUN 14 HARVESTED, ANALYSED AND WRITTEN UP, 2026-08-24T08:30-09:20Z.
    Harvest: 24 files (ave + trace per task) into
    03_EAM-ISOTHERM-AND-ELASTICS/14_EAM-SIZE-SCAN-300K-MCSITES/results/{L6,L10}/,
    plus the .in for provenance. Verified BY FILE COUNT PER RUN DIR (2 each in
    all 12), not by exit code -- the 2026-08-05 rsync-exits-0-on-empty-readdir
    trap. Gates checked first on the mount: ALL PHASES COMPLETE and both
    Performance lines in all 12 named logs, no ERROR, empty .err, 20 block
    averages through 44000 steps.
    RESULTS (thread.md run 14 section has the full write-up):
      1. NO RESOLVED FINITE-SIZE SHIFT in the transition. mu_t = -2.2825
         +-0.0075 (L6) / -2.2725 +-0.0025 (L10) / -2.2750 +-0.0050 (L14, from
         run 09). All three brackets overlap in [-2.280,-2.275]. The +- is half
         the bracket width -- a mu-grid resolution, not a fit error. This is a
         failure to DETECT size dependence at a 5 meV grid, not a proof.
      2. L=6 CANNOT MEASURE THE ALPHA BRANCH: 5.7 H atoms at mu=-2.290, below
         the cell's H-counting resolution.
      3. BETA-SHELF LATTICE CONSTANT IS SIZE-INDEPENDENT: 3.76915 +-0.00021 (L6)
         and 3.76887 +-0.00009 (L10), agreeing with the 3.768 A already quoted.
         Run 09 contributes NONE -- both its beta points were still expanding at
         84k steps, so a_beta must not be read off the ultrafine scan.
      4. METHOD DEFECT: nih_loaders.mu_at_half() does not filter on `converged`.
         The project's mu(x=0.5) = -2.2731 +-0.0025 eV is interpolated through
         run 09's mu=-2.275, which the ladder flags `filling`. Converged-only
         bracketing gives -2.2750 +-0.0050. Impact 1.9 meV -- no headline claim
         moves -- but it is the same class as the 2026-08-05 sigma_x withdrawal.
         Filed as proposals-inbox 2026-08-24-0930 (fix the RULE, not the line).
    Artefacts written to 03_EAM-ISOTHERM-AND-ELASTICS/:
      analyse-size-scan_run14_EAM-300K.py
      fig13_finite-size_transition-and-shelf_EAM-300K.pdf
      finite-size-scan_transition-and-shelf_EAM-300K.dat
    thread.md updated (run 14 section + header note).

    DECK BUILT AND DELIVERED, 2026-08-24T09:50Z. 13 slides, MPI-SusMat 16:9
    corporate template, in the project root:
      20260824_Ni-H-phase-diagram_group-update_EAM-vs-MEAM.pptx
      ...-preview.pdf and ...deck.yaml (rebuild from the yaml, do not patch the
      pptx). Science talk, not the mc/sites method talk.
    Every number cross-checked against RESTART-BRIEF sections 2 and 3. Both
    withdrawn claims appear ONLY as withdrawals, each on its own slide. Figures
    are the v4/2026-08-05 set only; no v3 figure is used. Slide 11 carries the
    run-14 mu(x=0.5) caveat explicitly rather than repeating the number silently.
    Rendered to PNG and read slide by slide before delivery; three overflow
    fixes made.
- method_slides: |
    THREE MC/SITES METHOD SLIDES BUILT AND DELIVERED, 2026-08-24T10:10Z, at
    Erik's request (theory + sanity checks / the two mu scales / walltime gain).
    In SIMULATIONS/Ni-H-PHASE-DIAGRAM-EAM-MEAM/20260824_mcsites-method-slides/:
      mcsites_theory.pptx, mcsites_delta-mu.pptx, mcsites_walltime.pptx
        -- one slide each, standalone
      mcsites_method-slides_3up.pptx + _preview.pdf -- all three in one file
      theory.yaml / delta-mu.yaml / walltime.yaml / all-three.yaml -- the specs
      figS1_validation_geometry-and-langmuir_mc-sites.pdf   (NEW)
      figS3_cost_mc-sites-vs-gcmc_EAM-300K.pdf              (NEW)
      make-method-figs.py -- makes both, from the sources named in its header
    Erik's phrasing ("make each one slide deck") was ambiguous between three
    files and one three-slide file, so BOTH were built rather than blocking on
    the question. Rebuild from the yaml, never patch the pptx.
    TWO NEW FIGURES, both derived not invented:
      figS1 (a) the ideal-fcc Voronoi clearance spectrum -- 324 sites = 12/cell,
        1.524197 = sqrt(3)a/4 x216 and 1.760000 = a/2 x108, rmin 1.6 window;
        (b) the Langmuir validation, 9 MC points on the analytic curves at
        300/600 K, worst error 0.0039 vs tolerance 0.02.
        SOURCE: DEVEL/MC-SITES-LAMMPS/PROGRESS.md sections 8.1 and 8.2
        (validation suite, 2026-07-07). READ ONLY -- nothing written to that
        repo; it is outside this session's write scope.
      figS3 (a) measured core-hours to 95 % of final x vs mu for both samplers,
        with the 5 gcmc cell-destruction tasks marked; (b) the 8.2x / 2.15x /
        17.6x decomposition. SOURCE: thread 03
        method-cost-v2_mcsites-vs-gcmc_EAM-300K.dat (2026-08-05).
    SLIDE CONTENT, provenance for whoever revises it:
      theory   -- SPEC-MC-SITES.md section 3 (settled design decisions 2,4,5,6)
                  for the method; PROGRESS.md 8.1-8.9 for the checks. The
                  rebuild-invariance test is presented as what QUANTIFIES the
                  one approximation (detailed balance is exact within a block,
                  not across the block-to-block rebuild) -- that framing is
                  SPEC section 3 decision 8, not a gloss.
      delta-mu -- delta-mu_gcmc-vs-mcsites_EAM-300K.dat: transition-matching
                  +0.1050 +-0.0112, dilute-matching +0.0971 +-0.0020, adopted
                  +0.1010 +-0.0137. The "agree to 0.008 eV" claim is the
                  difference of the two route means (0.0079). Figure is thread
                  03 fig8 (rigid-shift test), unchanged.
      walltime -- thread 03 "Method cost, redone at matched cadence" section.
                  Carries the three caveats that section insists on: MD is <1 %
                  of cost in both so an MD-step axis is meaningless; the gcmc
                  failure above mu ~ -2.04 matters more than the factor; 2.2x
                  per-attempt is a LOWER bound because gcmc's cost scales with
                  N and mc/sites' does not.
    Rendered to PNG and read slide by slide; two text-size and two glyph fixes
    made (box-level `size` does not reach bullets in the deck builder -- each
    bullet needs its own; and the angle brackets of <c> do not render in Roboto).
- diffusivity_work: |
    SCOPE EXTENDED to ni-h-diffusivity 2026-08-24T11:00Z (Erik: slides on the
    diffusivities + configurations to check for melting + do we know the
    potentials' Tm). No other session claims that tree.
    MOUNT RECOVERED: the EPERM state of this morning is gone -- Erik reconnected
    the cluster-mounts/cmmg folder. All four project subtrees list and read.
    (1) DECK, 5 slides, in SIMULATIONS/Ni-H-DIFFUSIVITY/:
        20260824_Ni-H-diffusivity_group-update_Pezold-EAM.pptx + _preview.pdf
        + .deck.yaml. Setup / Arrhenius results / the 800-900 K melting /
        the new 500 K finding / the Tm answer.
    (2) THE OPEN 500 K ANOMALY IS RESOLVED -- and the 2026-08-20 framing of it
        was WRONG. It was recorded as "D(500) = D(600), no activated process
        does that", which suspects both points. It is ONE point: 600 K is
        normal, 500 K is anomalously FAST (7.5x its x=0.99 sibling at the same
        T) and is the only anisotropic run in the set (D_z/D_xy = 1.79).
        Cause, three signatures agreeing: sigma_xx = 14.72 GPa against 1.9-2.5
        for every other cell in the ladder AND at the lowest T; Lz 2.7 % short
        of both its 600 K sibling and its x=0.99 counterpart; and g_min = 0.104,
        LESS ordered than the 600 K (0.038) and 700 K (0.053) cells above it,
        which is backwards. Not still relaxing (dlz/dstep -1.4e-11 A/step).
        VERDICT: one badly equilibrated cell -- discard and re-run, do not
        explain. x=0.95 biaxial Arrhenius stays REJECT until redone; its
        600/700/800 K points are sound. Melting result unaffected.
        Full write-up appended to 02_D-H-IN-HYDRIDE/thread.md.
    (3) CONFIGURATIONS MIRRORED at Erik's request: nine final .data files in
        02_D-H-IN-HYDRIDE/final-configs-for-inspection/ (x0.95 biaxial 500-900 K
        ladder, x0.99 biaxial 500 + 800 K, x0.95 stress-free 500 + 900 K).
        New artefacts in analysis/: check-500K-anomaly_hydride-biaxial.py,
        fig3_500K-biaxial-anomaly_is-a-bad-cell_Pezold-EAM.pdf,
        crystallinity-and-stress_hydride-configs_Pezold-EAM.dat.
    (4) MELTING TEMPERATURE: WE DO NOT KNOW IT, for either potential, and it is
        not in the literature entries either (EAM lineage Angelo/Moody/Baskes
        1995 refitted by von Pezold; MEAM Ko/Shim/Lee 2011 -- neither NIST IPR
        entry nor the accessible abstracts quote one; the PRB 65 024209
        EAM-vs-MEAM liquid-Ni comparison is paywalled). Not a threat to the
        existing runs (exp. Ni 1728 K vs our 1000 K ceiling, and the dilute legs
        measure crystalline at every rung) but Tm(pure Ni) is the wrong quantity
        for thread 02 anyway -- what governs it is the hydride's Tm AT ITS
        STRESS STATE, bracketed only to 800-900 K. Method if wanted: solid-liquid
        coexistence; a single-phase heat-until-it-melts run overshoots 10-20 %.
    A CONVENTION CLARIFIED, not an error: thread 02's "~1.3-1.4 GPa coherency
    stress" is the HYDROSTATIC average; sigma_xx is 3/2 of it (pzz = 0 by
    construction), i.e. ~1.9-2.1 GPa. Noted in thread.md so the two numbers are
    not read as a contradiction later.
- designer_scope: |
    PROMOTED to designer 2026-08-24T16:50Z at Erik's explicit request ("please
    check with the designer: we had a couple of clear rules you are violating").
    DESIGNER LOCK TAKEN. Verified: the only other claim is session
    2026-08-20-1145-cluster-status-sweep, which took it on 08-20T12:20Z and is
    4 days stale (last_active 08-20T14:55Z); flagged stale by both the 08-20 and
    the 08-24 sweeps. Session 2026-08-24-1531-nih-at-dislocs-status is pilot
    mode and holds no lock, and its scope (Ni-H-AT-DISLOCS) is disjoint from
    mine. Area: the command hand-off rules only -- I am not reopening anything
    else while holding it.
    WHAT I GOT WRONG, in the thread 01 hand-off, 2026-08-24T16:05Z:
      (1) No machine tag on any command block. preferences.md "Command
          hand-offs" says: "State where the block runs (which machine/shell,
          e.g. 'in your cmmg shell') at the top". Erik set that rule himself on
          2026-07-28/29 with the words "can you tell me exactly what to submit
          how and where (ALWAYS!)". The rule existed and I did not apply it.
      (2) Wrote `cmmg:` as the rsync ssh target. `cmmg` is the project's NAME
          for the cluster and an alias on Erik's Mac; the host is
          <CLUSTER_HOST> and the user is <CLUSTER_USER>. This also violates L21
          ("Always specify SSH user explicitly"), which likewise already existed.
    ROOT CAUSE, and it is the part worth fixing: the public canon/clusters.yaml
    is identity-scrubbed and carries <CLUSTER_USER> / <CLUSTER_HOST>
    PLACEHOLDERS. I read that file, hit the placeholders, and filled them from
    memory instead of resolving them from canon/local/clusters.local.yaml --
    which holds `user: <CLUSTER_USER>`, `host: <CLUSTER_HOST>`, and even carries
    the note "ALWAYS give the user explicitly -- L21". I treated a placeholder
    as a value. The startup ritual's step 0 only asks whether canon/local/
    EXISTS; it never asks the session to READ the cluster identity out of it,
    so nothing in the ritual forced the lookup.
    FIXED IN CANON (this session, under the lock): session-startup step 0 now
    requires resolving and echoing the real user@host; preferences.md's
    hand-off rule gains the placeholder clause and a worked example;
    canon/lessons.md gains L41; canon/templates/lint-handoff.sh is new and
    catches all three failure modes mechanically.
    INSTANCE FIXED SEPARATELY in thread 01's Runs section: four numbered blocks,
    each tagged MAC (M5) or CMMG SHELL, real host everywhere, plus the pull-back
    block which was missing entirely.
- designer_addendum: |
    LOCK RE-TAKEN 17:30Z and RELEASED 17:35Z for one addition, prompted by the
    staging block failing when Erik ran it.
    WHAT FAILED: BLOCK 1 rsync'd into
    /cmmc/ptmp/<CLUSTER_USER>/NI-MELTING-POINT-EAM/01_METHOD-VALIDATION-ANGELO-EAM/,
    whose PARENT did not exist. rsync creates only the LAST component of a
    destination path. Died with "mkdir ... failed: No such file or directory".
    A hand-off defect, not a cluster problem, and in the SAME hand-off I had
    just corrected -- I fixed the ssh target and the machine tags and did not
    re-read the block for what else it assumed.
    WHY IT IS A CLASS, not a typo: a staging block written from the template of
    an EXISTING project inherits the assumption that the tree is already there.
    The first thread of a new project is exactly when it is not, and it is also
    exactly when nobody has run the block before. This will recur at every new
    project unless the rule is written down.
    MERGED: canon/preferences.md, Command hand-offs -- "the first hand-off into
    a NEW tree creates the remote directory itself", with both forms (explicit
    `ssh ... mkdir -p`, or `--rsync-path='mkdir -p ... && rsync'` for one
    authentication instead of two). Records that `--mkpath` is NOT the
    recommended form: it needs rsync >= 3.2.3 on the SENDING side and macOS does
    not reliably provide it.
    thread 01 now carries BLOCK 0 plus the one-authentication alternative, and
    lint-handoff.sh passes on it. The lint CAUGHT a real miss while I was
    editing -- the alternative command initially sat under an untagged
    parenthetical -- which is the first time it has earned its keep.
    NOT ADDED TO THE LINT: "does this rsync's parent exist" is not decidable
    from the hand-off text alone. Left as a written rule.
- designer_work_done: |
    DESIGNER LOCK RELEASED 2026-08-24T17:10Z. Merged under it, all four aimed at
    the same root cause -- a session can pass the environment gate without ever
    resolving the cluster identity, and then meet a <CLUSTER_HOST> placeholder
    with nothing but memory:
      1. canon/session-startup.md -- NEW STEP 0(b2). Existing step (b) only asked
         whether canon/local/ EXISTS. (b2) now requires opening
         canon/local/clusters.local.yaml, reading ssh.user / ssh.host / scratch,
         and ECHOING them in the startup brief, so the identity is in context
         before any hand-off is written. Carries the rule "a cluster name is
         never a host".
      2. canon/preferences.md, Command hand-offs -- two clauses added: the
         machine tag must be the FIRST thing in each block and every
         machine-crossing gets its own numbered block; and never emit or guess a
         <PLACEHOLDER> from the identity-scrubbed public canon.
      3. canon/lessons.md -- L41. Includes the part that matters: BOTH output
         rules already existed (preferences.md's machine tag since 2026-07-28,
         L21's explicit user since thread 01). The gap was upstream, in the step
         that supplies the input. Generalizes to any <PLACEHOLDER> in canon: it
         is a lookup instruction, and filling it from memory is the bug even
         when the memory is right.
      4. canon/templates/lint-handoff.sh -- NEW. Catches unresolved placeholders
         in commands, remote targets without user@, cluster names used as hosts,
         and command blocks with no machine tag. Joins backslash continuations
         first, because the actual 2026-08-24 miss had `cmmg:` two continuation
         lines below the word `rsync` -- a first cut of the lint missed it for
         exactly that reason. Verified against a reconstruction of the original
         bad hand-off (fires on all three rules) and against the corrected
         thread 01 (clean). Header carries a SCOPE note: reference docs that
         contain illustrative commands will fire and must not be "fixed".
    SWEEP of every existing thread.md and RESTART-BRIEF with the new lint. Two
    pre-existing findings, both untagged sbatch blocks, both OUTSIDE this
    session's write scope and therefore REPORTED NOT FIXED:
      * Ni-A0-CIJ-EAM-MEAM/03_LATTICE-CONSTANT-AT-300K/thread.md (~line 266)
      * Ni-H-AT-DISLOCS-EAM-MEAM/RESTART-BRIEF.md (~lines 174, 189) -- that tree
        is actively owned by session 2026-08-24-1531-nih-at-dislocs-status.
    Everything else in SIMULATIONS is clean.
- thread01_written: |
    2026-08-24T16:20Z. Erik took all proposals AND added the large box. Thread 01
    inputs + submit scripts WRITTEN to the Mac; status designed -> ready.
    DECIDED: Baskes protocol primary (whole-sample thermostat, total-energy drift
    bracket) with the Cherne-style interface velocity extracted from the SAME
    trajectories under that same thermostat -- reported as "Cherne-style, our
    thermostat", never as a replication of theirs. BOTH cells. Lateral box =
    full NPT, all three axes independent at zero stress (couple none), chosen
    over clamping y/z at a(T) because a clamped lateral cell is exactly what put
    the ni-h-diffusivity 500 K task at 14.7 GPa, and because clamping would need
    a_solid(T) in advance at every rung.
    FILES in 01_METHOD-VALIDATION-ANGELO-EAM/: prepare-two-phase_Ni-melting.in,
    measure-melting_Ni-melting.in, submit-{prepare,measure}-*.slurm and a .probe
    variant of each. Style walk done: us-ascii clean, L1 runtime-variable grep
    empty, L3 generic-filename grep empty, every ${VAR} the inputs need is passed
    by every submit script (checked programmatically, not by eye).
    TWO BUGS CAUGHT BEFORE WRITING, both worth remembering:
      * pair_coeff element list. First draft passed "Ni Al H" for the Angelo
        setfl. WRONG -- the list maps ATOM TYPES to elements and this system has
        ONE type, so it is a single "Ni". Would have died on line 1.
      * output filenames used ${L}x${L}x${L}, which is right for the cube and
        wrong for the 10x10x40 box. Now ${L}x${L}x${LX} throughout.
    RANKS deliberately NOT baked into the measure submit script, because the two
    cells want different values: cube 2048 atoms -> --ntasks=1 (2048 atoms/core),
    large box 16000 -> --ntasks=8 (2000 atoms/core). Passed on the sbatch line.
    OPEN RISK flagged in thread.md for the probe to settle: the melt stage uses
    `fix nph x` over ALL atoms plus velocity-zero + setforce-zero on the cold
    half. That is the intended reading of Baskes's "keeping the atoms in the
    other half fixed", but it must be confirmed from the prep snapshot that the
    cold half is still crystalline and on its sites.
    PROBE COMMANDS HANDED TO ERIK, nothing submitted (strict-A). Gates: clean
    exit, ALL PHASES COMPLETE, all Performance lines, no ERROR, two-phase data
    written, x-profile showing one high-fcc and one low-fcc half, cold half still
    crystalline in the snapshot. The measure probe is the walltime-calibration
    source. Production commands drafted in thread.md but explicitly not to be
    submitted until the probes pass.
- ref40_read: |
    2026-08-24T16:05Z. Erik put Baskes, Phys. Rev. Lett. 83 (1999) 2592 in the
    project root. READ IN FULL. It closes most of the gap AND exposes a
    discrepancy the anchor paper does not flag.
    BASKES SPECIFIES: a CUBE of fcc, 2048 atoms (8x8x8 conventional cells),
    periodic in 3D -- NOT an elongated slab, which is what this thread had
    proposed. Equilibrate whole crystal at zero P with Nose-Hoover +
    Parrinello-Rahman. Melt the x>0 half by heating it while HOLDING THE x<0 HALF
    FIXED; during that step y and z lengths are held fixed and only x lengthens.
    Confirm the melt by the hot region's g(r). Then thermostat the WHOLE SAMPLE at
    a ladder of temperatures and watch the TOTAL ENERGY: rises => melts through,
    falls => freezes through. Tm is the bracket between them.
    THE DISCREPANCY, now tabulated in thread 01: Cherne et al. DEPART from the
    method they cite. Baskes thermostats the whole sample and uses the sign of the
    energy drift; Cherne use a two-region thermostat ("temperature held fixed away
    from the interface", velocity read against "the temperature of the
    UNCONTROLLED region") and an interface-velocity zero crossing. The 1478 K came
    out of the MODIFIED version, and the modification is the part described in one
    sentence and never specified.
    STILL OPEN AFTER REF 40: Cherne's system size for the Ni runs, their
    two-region thermostat geometry/coupling, their timestep, their ladder spacing
    (+-25 K is consistent with ~50 K steps -- an inference, flagged as one).
    INFERRED, flagged as inference: interface normal is (100), from a conventional
    fcc cube cut at x=0. Baskes never names it.
    DESIGN REVISED AWAY FROM MY EARLIER PROPOSAL, twice:
      * cell 2048-atom CUBE (Baskes's own), not the 16 000-atom slab I proposed;
        plus a 10x10x40 = 16 000 size check, because 8x8x8 is small for a melting
        point and finite-size shifts of tens of K are plausible.
      * ranks: 2048 atoms wants ONE core (2048 atoms/core). Two ranks would sit at
        the canon floor for no gain. The 16 000-atom check takes 8 ranks
        (2000 atoms/core). Both ~150 timesteps/s, ~3 min per 25 ps point.
    BOTH OBSERVABLES to be recorded from the same trajectories (energy drift AND
    interface velocity) -- free, and it tells us which one 1478 K reproduces under.
    QUESTIONS CUT FROM FOUR TO TWO: (1) which protocol counts as "theirs" --
    proposal is Baskes primary (only fully reproducible one) with the Cherne-style
    velocity extracted alongside; (2) lateral box during measurement -- full NPT
    on all three axes, or y/z clamped at the solid's zero-pressure parameter with
    x free. Orientation, run length and per-atom stress are now settled.
- scope_narrowed: |
    2026-08-24T15:40Z, Erik: pure Ni ONLY -- no hydride, no biaxial constraint,
    no MEAM. Two potentials, in order: the original Angelo file first (exact
    replication of the paper's setup and protocol as far as it is specified),
    then the Pezold refit with the identical protocol. Kept as its own project.
    Project RENAMED accordingly: NI-MELTING-POINTS-EAM-MEAM ->
    **NI-MELTING-POINT-EAM**, id ni-melting-point-eam. Threads cut from five to
    two (01 Angelo validation, 02 Pezold). The hydride and biaxial threads are
    GONE, not deferred -- if they come back they need a fresh decision, and the
    caveat that a constrained cell has no thermodynamic Tm still stands.
    ALSO REQUESTED: full structure output for visual inspection, and care with
    atoms per core.
    KEY FINDING FROM RE-READING THE PAPER FOR THIS: **the melting-run setup is
    NOT in it.** Section III B specifies only the method (ref 40 = Baskes PRL 83
    (1999) 2592), two-phase, zero pressure, T held fixed AWAY FROM the interface,
    25 ps at 0.1 ps output, a bracketing ladder, velocity read against the
    temperature of the UNCONTROLLED region, +-25 K. It does NOT give system size,
    geometry, interface orientation, timestep, thermostat details, or
    equilibration. The 1372 and 10976 atom figures belong to the transport and
    structure-factor runs, NOT to the melting runs. So "exactly their setup" can
    match the protocol but must CHOOSE the geometry -- and thread 02 must then
    use the identical one or the comparison is worthless. Written into thread 01
    as an explicit specified/not-specified list.
    SIZING, with the canon atoms/core rule honoured: 10x10x40 fcc = 16 000 atoms
    on 8 ranks = 2000 atoms/core, top of the efficient band, sub-node s.cmmg.
    Not 16 ranks (that is 1000 atoms/core, the floor, for no gain on a job this
    short). Throughput sized from MEASURED data, not a guess: ni-h-diffusivity
    production did 1.26 Matom-step/s on 4 ranks at 7800 atoms = ~0.3
    Matom-step/s per core, same machine and potential family. => ~150
    timesteps/s here, ~3 min per 25 ps point, whole project a few core-hours.
    OUTPUT SET PROPOSED (per Erik's request for full structure): every 0.1 ps,
    id type x y z + per-atom PE + compute cna/atom, so the interface is visible
    in OVITO with no post-processing (~150-200 MB per T point). PLUS a small
    z-resolved order-parameter profile .dat that the velocity fit actually uses
    -- fitting a velocity out of a 200 MB dump when a 200 kB profile will do is
    backwards. Per-atom stress NOT proposed and flagged as such: the standing
    2026-08-20 rule covers phase transformations, but this cell is at zero
    pressure with nothing anisotropic in it and the stress would triple frame
    size at 0.1 ps.
    FOUR QUESTIONS PUT TO ERIK, all blocking the input file: (1) fetch ref 40
    first? (2) replicate their two-region thermostat, or global NPT as a
    documented deviation? (3) interface orientation, unspecified by them? (4)
    measure over both 25 ps and 100-200 ps in one run?
    STILL NOTHING SUBMITTED AND NO LAMMPS INPUT WRITTEN.
- new_project: |
    NEW SIM-PROJECT OPENED 2026-08-24T15:00Z at Erik's request: melting points of
    the potentials we actually run on. id `ni-melting-points-eam-meam`, folder
    SIMULATIONS/NI-MELTING-POINTS-EAM-MEAM/. project.md + thread 01 written;
    threads 02-05 deliberately NOT created (canon opens a thread when work
    starts, and thread 01 may change the protocol they would inherit).
    THE DESIGN, in one line: validate the protocol against a published number
    BEFORE measuring anything new. `/cmmc/ptmp/<CLUSTER_USER>/POTENTIALS/
    NiAlH_jea.eam.alloy` IS the Angelo/Moody/Baskes 1995 potential (its header
    carries the citation), and Cherne/Baskes/Deymier PRB 65 (2001) 024209
    Table II gives Tm = 1478 +- 25 K for it by two-phase coexistence. Thread 01
    runs our protocol on that exact file. Reproduce it -> everything downstream
    is trustworthy; miss it -> protocol bug, and no Tm gets quoted anywhere.
    Threads planned: 02 Pezold EAM (Ni), 03 Ko/Shim/Lee MEAM (Ni), 04 B1 NiH
    stress-free (the one ni-h-diffusivity actually needs), 05 NiH under biaxial
    constraint -- 05 carries an explicit caveat that under a fixed in-plane
    strain the constraint does work on the cell, so a coexistence calculation
    does NOT measure the same quantity as at fixed pressure; the 800-900 K
    figure should stay a stability bracket unless we agree otherwise.
    NOTHING SUBMITTED AND NO LAMMPS INPUT WRITTEN. Four decisions are Erik's and
    are listed in thread 01: the output set and cadence (canon requires this
    before an input exists), cell size, run length per temperature point
    (25 ps reproduces the anchor exactly, 100-200 ps gives a defensible
    velocity), and whether failing to reproduce 1478 K halts the project.
- paper_analysed: |
    Cherne/Baskes/Deymier PRB 65 024209 read in full (Erik put the PDF in
    SIMULATIONS/Ni-H-DIFFUSIVITY/). Table II: A-EAM 1478, CY-EAM 1536, MEAM
    Ni1 1880, Ni2 1825, Ni3 1890, Ni4 1570 K, all +-25; experiment 1726 K.
    Method: two-phase moving interface, zero pressure, 25 ps per point, Tm where
    the interface velocity crosses zero.
    THE TWO READINGS THAT MATTER, both now in the new project's thread 01:
      * Table I shows Ni3 and Ni4 differ ONLY in the screening parameter Cmin
        (2.0 vs 0.8) and their Tm differs by 320 K. So "MEAM melts higher than
        EAM" is about parameter choices, not the formalism -- and says nothing
        directly about our Ko/Shim/Lee file, which is a different 2NN MEAM.
      * All six potentials reproduce the liquid structure factor and the
        zero-pressure liquid density to within 1.8 % while missing Tm by up to
        250 K. Getting the liquid right is NOT evidence of getting Tm right.
    Consequence written into ni-h-diffusivity thread 02: if our refit sits near
    1478 K, our 1000 K ceiling is T/Tm = 0.68 rather than 0.58, so D_H is
    probably biased HIGH toward the top of the ladder (prefactor more than
    slope), and the constrained hydride's 800-900 K collapse is 0.54-0.61 Tm,
    which stops looking anomalous once the denominator is known. Flagged as NOT
    established until Tm is measured on our own file.
- in_flight: |
    FOUND, 2026-08-24T07:53Z: ni-h-phase-diagram thread 03 RUN 14 (EAM 300 K
    size scan) HAS COMPLETED on the cluster. All 12 tasks (L6 x 6 mu, L10 x 6
    mu) wrote `JOB DONE: L=.. mu=.. seed=..` in their .out, all .err are empty,
    and every task carries block averages through step 44000 (the full run).
    Last write 2026-08-20T21:03. UNHARVESTED -- nothing of run 14 is on the Mac.
    Verified by exact-path reads of the 12 slurm-*.out files at
    .../03_EAM-ISOTHERM-AND-ELASTICS/14_EAM-SIZE-SCAN-300K-MCSITES/, not by
    enumeration. The per-mu results dirs list EMPTY (L15 staleness, live again
    this session: results/L6 and results/L10 list their 6 mu dirs, but each
    mu dir returns 0 entries and `find -newermt` returns 0 across all five
    cluster project trees). The ALL PHASES COMPLETE / Performance gates could
    therefore NOT be checked -- JOB DONE + full-length block averages is what
    is established.
- sacct_reconciliation: |
    `sacct -X -S 2026-08-20` PASTED BY ERIK 2026-08-24T08:0xZ. Fifteen jobs,
    ALL COMPLETED 0:0, none failed, none timed out, and NOTHING submitted after
    them -- so no work is in flight anywhere as of now.
      22344607_0-5  NiH-EAM-m+  s.cmmg   8 CPUs  run 14 size scan L6   COMPLETED
      22344608_0-5  NiH-EAM-m+  s.cmmg  16 CPUs  run 14 size scan L10  COMPLETED
      22344727      NiH-HBIND+  p.cmmg 512 CPUs  ni-h-at-dislocs t01 r01 COMPLETED
      22346669      nihcyc-zstd   s.cmmg  64 CPUs  cycle trajectory zstd  COMPLETED
                    Elapsed 00:16:29, WorkDir /cmmc/ptmp/<CLUSTER_USER>/
                    Ni-H-HYDRIDE-CYCLE-EAM/01_CHARGE-DISCHARGE-100-SLAB-300K
      22346875      nihcyc-frames s.cmmg  8 CPUs  cycle frame extraction COMPLETED
                    Elapsed 00:14:37, same WorkDir
    THREE OF THESE WERE UNKNOWN TO THE 08-20 SWEEP, which recorded the Hbind
    job as FAILED-and-undiagnosed and the two cycle jobs as "PREPARED, not run".
    All three ran and succeeded after that entry's last stamp.
    22344727 CONFIRMED AS THE PROBE RE-RUN, 2026-08-24, second sacct paste:
    JobName NiH-HBIND-RELAX-D90-EAM-PROBE, Elapsed 00:03:26 against Timelimit
    01:00:00, step 22344727.0 `lmp` 00:03:15, Start 2026-08-20T14:57:15,
    End 15:00:41, WorkDir /cmmc/ptmp/<CLUSTER_USER>/Ni-H-AT-DISLOCS-EAM-MEAM/
    01_H-BINDING-MAP-D90-0K/01_RELAXED-BINDING-SUBSET. So Erik applied the
    `group HGRP clear` fix and the probe passes. THE 372-SITE PRODUCTION IS
    STILL NOT SUBMITTED.
    WALLTIME BASIS, UPPER BOUND ONLY: 195 s of lmp wall over 20 sites = 9.75 s
    per site at 256 ranks, so 372 sites = 60.5 min, x1.5 buffer = ~91 min.
    THIS IS AN OVERESTIMATE and must not be quoted as the calibration: the
    3:15 includes one-off setup (568606-atom read, neighbour build, and the
    pristine-cell E0 relaxation), which is not per-site. The probe's own .out
    prints the true per-site cost from relax-loop-start/end.txt -- read it
    before sizing production, do not use the number above.
    PRODUCTION IS BLOCKED ON THE PROBE'S OWN GATE, NOT ON WALLTIME: the probe
    exists to answer `restore_meV` (must sit at the numerical floor; a drift
    invalidates the whole loop design), the FIRE iteration counts, and
    E_ins_loose vs E_ins_tight. All three live in
    probe/Ni-disloc-d90-Hbind-relaxed-Pezold-EAM-0K.dat, which is EPERM this
    session. NO PRODUCTION MAY BE PROPOSED UNTIL THAT FILE IS READ.
    THE CYCLE FRAME JOB'S OUTPUT IS ALREADY ON THE MAC: 20 key frames in
    Ni-H-HYDRIDE-CYCLE-EAM/key-frames/ (4 runs x 5 frames), written 2026-08-20
    15:33-15:45Z, i.e. after the sweep entry's 14:55Z stamp. The thread.md line
    "TRAJECTORY HANDLING PREPARED (not yet run)" is therefore STALE and should be
    corrected by whoever takes that scope.
    STILL NEVER RESUBMITTED: NiH-0K-Hsite-anchors-KoShimLee-MEAM (2x TIMEOUT,
    ni-h-phase-diagram thread 01). Absent from this window as from the last.
- mount_state: |
    MOUNT IS PARTIALLY EPERM, 2026-08-24T08:0xZ -- the TCC mode of proposal
    2026-08-20-1150, not L15 staleness, and it is PER-PROJECT-SUBTREE:
      Ni-H-PHASE-DIAGRAM-EAM-MEAM  ... files READABLE (run 14 read in full)
      Ni-H-AT-DISLOCS-EAM-MEAM     ... every file EPERM (dirs resolve)
      Ni-H-HYDRIDE-CYCLE-EAM       ... every file EPERM
      Ni-H-DIFFUSIVITY             ... every file EPERM
    Diagnostic that separates the two modes: directories stat fine and `[ -d ]`
    is true, but `ls -l`/`head -c 1` on a file KNOWN to exist returns
    "Operation not permitted" -- L15 staleness returns empty listings and
    ENOENT instead. 3 retries with backoff on each, no recovery.
    FIX PER 08-20: re-connect the cluster-mounts/cmmg folder in the desktop
    app; no remount needed. Asked of Erik; the three EPERM subtrees are
    unverifiable until then.
- notes: |
    designer lock NOT taken. Cloud Cowork session on M5's connected folders
    (SIMULATIONS, DEVEL, cluster-mounts/cmmg all reachable; environment gate
    passed). Mac side of every project is unchanged since 2026-08-20T15:45,
    i.e. since the 08-20 sweep closed its last write.
    THE THREE STALE `active` ENTRIES flagged on 08-20 are STILL open
    (2026-08-05-1425-mcsites-presentation, 2026-08-03-1401-nih-at-dislocs-design,
    2026-08-02-1647-ingest-eam-dislocs-ni-cu), and 2026-08-20-1145-cluster-
    status-sweep is now stale too (last_active 08-20T14:55). Re-flagged to Erik.
    ANSWERED 2026-08-24 by Erik's sacct paste -- see sacct_reconciliation
    above. Nothing submitted after 2026-08-20; nothing in flight.


---

## recently_closed

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
- closed: 2026-08-29T15:20Z
- summary: |
    Closed by designer session 2026-08-29-1442-designer-inbox-merge at Erik's
    instruction; stale in `active` since 2026-08-03T10:05Z (26 days). The work
    was already COMPLETE at that timestamp and the entry simply never migrated:
    EAM-DISLOCS-Ni-Cu ingested and stamped in place, threads 01-04 created,
    worked and closed, the curated mirror to the Mac done, and the `fix sgcmc`
    group-counter bug confirmed at source level (canon proposal
    2026-08-02-1900, since merged as L38). in_flight was already (none) -- no
    reconciliation needed. Open question 5 (no full-Cu-start runs exist) stands
    in the project file for whoever reopens the project.
- handoff_to: null

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
- closed: 2026-08-29T15:20Z
- summary: |
    Closed by designer session 2026-08-29-1442-designer-inbox-merge at Erik's
    instruction; stale in `active` since 2026-08-04T18:40Z (25 days). Delivered:
    ni-h-at-dislocs-eam-meam founded, thread 01 run 00 complete (0 K H binding
    map, d90 x Pezold-EAM, all 953534 sites, mu_core ~ -2.421 eV as an upper
    bound), and three corrections put on the record -- the `metric volume`
    classifier, the WITHDRAWN slab-bending claim, and symmetric colour scales.
    in_flight RECONCILED AND CLEARED: both staged-not-submitted jobs were
    overtaken by later sessions. The d90 relaxed-binding probe ran as 22344727
    (COMPLETED 2026-08-20T15:00) and its production as 22719302 (COMPLETED
    2026-08-24T22:02); the strain-reference / pre-fill line continued under the
    08-24 and 08-28 at-dislocs sessions, which carried threads 02, 04 and 05
    forward. Nothing from this entry is still waiting on the cluster.
    STALE NOTE CORRECTED: "OWED TO CANON ... None merged" is no longer true --
    all three proposals (2026-08-04-1710 metric volume, -1712 background shape,
    -1730 symmetric colour scales) were merged in the 2026-08-20 designer pass;
    the colour-scale rule now lives in preferences.md "Plot defaults".
- handoff_to: 2026-08-28-1558-nih-at-dislocs-readout

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
- closed: 2026-08-29T15:20Z
- summary: |
    Closed by designer session 2026-08-29-1442-designer-inbox-merge at Erik's
    instruction; stale in `active` since 2026-08-05T15:55Z (24 days). Delivered:
    the presentation spine (7 sections, master deck with swappable depth
    modules) and the run-14 size-scan probes, both of which passed every gate.
    in_flight RECONCILED AND CLEARED: the run-14 production arrays this entry
    handed over were not submitted on 08-05; the same run 14 was resubmitted on
    2026-08-20 as 22344607 (L6) / 22344608 (L10) and all 12 tasks COMPLETED
    cleanly by 2026-08-20T23:03Z (Erik's sacct paste, 2026-08-29). No open loop
    remains. The presentation itself was never resumed -- if it is picked up,
    the deck decisions in `notes` above are the starting point.
- handoff_to: null

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
- closed: 2026-08-29T14:45Z
- summary: |
    Closed by designer session 2026-08-29-1442-designer-inbox-merge at Erik's
    instruction; entry had been stale since 2026-08-20T14:55Z. Work delivered:
    the cross-project status sweep (four projects reconciled against Erik's
    sacct paste) plus TWO designer passes that merged 20 proposals into
    learnings.md, lessons.md (L38-L41), preferences.md, style/lammps.md,
    session-startup.md (0(c), step 1b) and ARCHITECTURE.md (17.4, 17.6).
    in_flight (run 14 arrays 22344607/22344608) was ANSWERED by Erik's
    2026-08-24 sacct paste and reconciled in 2026-08-24-0753-status-readout --
    no open loop carried by this entry. DESIGNER LOCK RELEASED.
- handoff_to: 2026-08-29-1442-designer-inbox-merge

### session_id: 2026-08-28-1558-nih-at-dislocs-readout
- mode: pilot
- scope: |
    Status readout of ni-h-at-dislocs-eam-meam ("H segregation to
    dislocations in Ni"), THEN PROMOTED 16:2xZ to pilot write ownership of
    threads 04 (d0, screw) and 05 (d30): harvest run 00 of both from the
    cluster, run the d90-style analysis, produce OVITO dumps for Erik,
    update both thread.md. See scope_change.
- started: 2026-08-28T15:58Z
- last_active: 2026-08-28T20:05Z   # real UTC; earlier 20:xx/21:xx stamps in this entry were mine and were wrong
- simulation_root: ~/Desktop/SIMULATIONS   # connected folder, matches local.yaml default
- machine: M5   # canon/local/.this-machine
- cluster_identity: |
    cmmg -> <CLUSTER_USER>@<CLUSTER_HOST>, scratch /cmmc/ptmp/<CLUSTER_USER>
    (resolved from canon/local/clusters.local.yaml, echoed in chat only).
    Mount ~/cluster-mounts/cmmg CONNECTED and HEALTHY -- listings and
    exact-path reads both work in every at-dislocs run dir.
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - LLM-LAMMPS-public/canon/proposals-inbox.md (append only, pilot route)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/04_H-BINDING-MAP-D0-0K/**   (added 2026-08-28T16:2xZ)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/05_H-BINDING-MAP-D30-0K/**  (added 2026-08-28T16:2xZ)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/project.md (open question 3 + one frontmatter line ONLY; added 2026-08-28T16:4xZ)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/{fig6,character-comparison,compare-characters}* (project-root comparison artefacts; added 2026-08-28T16:4xZ)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/02_MU-SWEEP-D90-300K/**   (added 2026-08-28T19:0xZ)
- designer_lock: NOT taken -- pilot mode. Lock is FREE.
- scope_change: |
    PROMOTED read-only -> WRITE OWNERSHIP of threads 04 (d0) and 05 (d30),
    2026-08-28T16:2xZ, at Erik's request ("take the write scope and do that
    harvest ... please also do the analysis. get the dump files so I can
    look at them"). Scope: harvest run 00 of both threads from the cluster,
    run the d90-style run-00 analysis on each, produce OVITO-loadable dumps,
    update both thread.md files. NOT thread 01, NOT thread 02, NOT any
    cluster writes beyond reads through the mount.
    TAKEOVER, ON THE RECORD: both thread paths are declared by
    2026-08-25-1725-nih-two-project-status (last_active 2026-08-26T16:05Z,
    conversation ended) and by 2026-08-24-1531-nih-at-dislocs-status
    (last_active 2026-08-24T17:20Z). Both are stale and both were flagged
    stale by the 08-27 portfolio sweep. This session takes threads 04/05
    ONLY; neither entry is closed here, and their other declarations
    (thread 01/02, the hydride-cycle and phase-diagram paths) are NOT taken.
    Erik told, in-turn.
    SCOPE ADDED 2026-08-28T19:0xZ at Erik's request: THREAD 02
    (02_MU-SWEEP-D90-300K), to build the run-01b and run-02 job files after
    he took the four open design decisions -- cylinder MC zone, bracket and
    refine, per-mu box from a(mu) with Vegard interpolation, d90 first.
    TAKEOVER, ON THE RECORD: thread 02 sits under two STALE whole-tree
    declarations -- 2026-08-03-1401-nih-at-dislocs-design (last_active
    2026-08-04, 24 days) and 2026-08-24-1531-nih-at-dislocs-status
    (2026-08-24), both of which declare SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/**.
    Neither is closed here. No LIVE session declares it.
    DISCLOSED, NOT HIDDEN: I edited 02_MU-SWEEP-D90-300K/thread.md at
    ~18:5xZ (the fix ave/atom removal and the a(T,mu) section) BEFORE
    declaring this scope. Erik had approved both edits in-turn; the scope
    registration is what was late, not the consent.
    SCOPE WIDENED BY ME, ON THE RECORD, 2026-08-28T16:4xZ: the character
    comparison is by construction not inside either thread, so it went to
    the project root, and it ANSWERS project open question 3, so project.md
    was edited too. Neither was in the scope I registered at 16:2xZ. Nothing
    else in project.md was touched and RESTART-BRIEF.md was NOT touched --
    it is stale (2026-08-04, still says thread 01 run 01 "not submitted")
    and correcting it belongs to whoever takes the project-wide scope.
- scope_collisions: |
    Threads 04/05 taken over from two stale entries -- see scope_change.
    No LIVE session declares them: 2026-08-27-0915-hydride-cycle-continue
    owns Ni-H-HYDRIDE-CYCLE-EAM only. Designer lock FREE and not needed.
- step_1b: |
    DISCHARGED FROM THE MOUNT for this project, 2026-08-28T15:5xZ; every
    at-dislocs run dir read by exact path.
    * t02 run 00b whole-cell catalogue -- SUBMITTED TWICE (22733000 AND
      22733029, same run dir) and COMPLETE 2026-08-27T02:27. Gates PASS:
      JOB DONE, n_sites 1702230 (> thread 01's 953534), z extent
      -0.0248..247.7088 A reaching both free surfaces, classes
      33.05/65.44/1.51 %. UNHARVESTED on the Mac, and thread 02's status
      table STILL SAYS "HANDED OVER, NOT SUBMITTED" -- stale.
    * t04 run 00 (22728805) and t05 run 00 (22728806) -- COMPLETE since
      2026-08-25/26, .err empty, JOB DONE, rows = n_sites+7. Mac dirs hold
      INPUTS ONLY: unharvested, unanalysed, 3 days.
    * t04/t05 run 01 relaxed-subset PROBES -- staged on the cluster, NO
      slurm output: never submitted.
    * t01 run 00 + run 01 -- complete, harvested, analysed (mu_core
      -2.4061 eV / E_seg_reservoir -131.1 meV). Closed loops.
    SACCT PASTE SUPPLIED by Erik 2026-08-28T16:0xZ (-S 2026-08-25). All
    at-dislocs jobs confirmed COMPLETED, exit 0:0: 22728805 (4:59:52),
    22728806 (4:46:56), 22733000 (1:32) + 22733029 (1:27), and the five
    prefill jobs 22732904/22732911/22732916/22732922/22732927. Nothing of
    this project is queued or running.
    OTHER PROJECTS -- reported, NOT acted on (not this session's scope):
    * 22730595_2 -- CORRECTED 2026-08-28T16:1xZ. sacct printed it RUNNING
      at 2-06:04:10 with End = Unknown; THIS SESSION READ THAT AS GROUND
      TRUTH and wrongly told Erik it was still holding 16 cores on s.cmmg
      and that the hydride-cycle session's "scancel DONE" record was wrong.
      It was not. `scancel -s KILL 22730595_2` returned "Invalid job id
      specified": the controller had not known the job for a day. What
      sacct was showing is an UNCLOSED ACCOUNTING RECORD -- the hung lmp
      step never let the epilog write an end record. The tell was in the
      same paste: elapsed 54 h against a 24 h cap, which Slurm would have
      enforced on a live job. 2026-08-27-0915's record stands as written;
      no cores were wasted beyond 08-27T11:4xZ. Canon proposal
      2026-08-28-1610-sacct-state-can-lie-for-an-unreaped-job.
    * 22732087_[0-6] nimelt-t02-measure PENDING since 2026-08-26, ~2 days
      in queue. Owner 2026-08-25-1536-nimelt-probe-fix. Not a fault.
    * 22733464_[0-1] (t03 rate B) and 22733466_[0-1] (t04 dome) RUNNING,
      28.5 h elapsed (t04 _1 24.7 h, it started later). Projections 80-84 h
      against the 96 h cap -- on track. UNHARVESTED, still producing.
    * 22734031_[0-3] fine 900 K bracket and 22734032_[0-5] L14 size scan
      ALL COMPLETED 2026-08-27 evening, exit 0:0, 3:57-7:46 elapsed.
      TEN TASKS UNHARVESTED -- new open loop, owner is the hydride-cycle
      session, flagged to Erik in-turn.
    * 22730595_9 TIMEOUT at the 24 h cap -- accepted by design (L34), closed.
    * Historic failures already understood and fixed: 22728943_[0-1] /
      22728944_[0-1] (wall/reflect in periodic z), 22728707 (nimelt),
      22731708_1 (notify probe).
- in_flight: |
    NOTHING RUNNING. All five overnight jobs reconciled 2026-08-28T19:5xZ
    from the run dirs on the mount (Erik went home ~19:1xZ and was not
    consulted; nothing was resubmitted).
    * 22736809 d0 run 01 PRODUCTION -- COMPLETE, HARVESTED, ANALYSED.
      362/362, restore 0.0000, 724/724 force tolerance, 23 min.
      mu_core(d0) = -2.3702 eV. LOOP CLOSED.
    * 22736817 d30 run 01 PRODUCTION -- COMPLETE, HARVESTED, ANALYSED.
      365/365, restore 0.0000, 730/730 force tolerance, 1 radial site not
      assured (gap 20.3 meV) and excluded. mu_core(d30) = -2.4071 eV.
      LOOP CLOSED.
    * 22736810 d30 run 01 PROBE -- COMPLETE, all gates passed. LOOP CLOSED.
    * 22736816 thread 02 run 01b -- COMPLETE. All SEVEN cells written, every
      gate PASS. LOOP CLOSED.
    * 22736814 -- superseded by 22736816. LOOP CLOSED.
    * 22736818 run 02 PROBE -- **FAILED at 19:16Z, one minute in, DIAGNOSED
      AND FIXED, NOT RESUBMITTED** (sbatch is strict-A). See below. This is
      the ONLY thing outstanding on the cluster.
    THE RUN-02 PROBE FAILURE, and it is a good one:
      ERROR: Atom IDs must be consecutive for velocity create loop all
      The pre-filled cell has 573807 atoms with a MAXIMUM ID of 576659 --
      the IDs are not consecutive, inherited from the Volterra construction
      upstream in ni-dislocs-eam-meam-ace where atoms were deleted.
      `velocity create` defaults to `loop all`, which requires 1..N.
      FIXED: `loop geom`, which assigns from coordinates, needs no IDs, and
      is decomposition-independent (so it is also the better choice for
      reproducibility across rank counts). Input re-linted.
      ROOT CAUSE WORTH KEEPING: the idiom was copied from
      ni-h-hydride-cycle-eam, where it works because those cells are BUILT
      FRESH with consecutive IDs. An idiom carries its home project's
      preconditions with it -- same class as canon proposal 2026-08-28-1910.
    NEEDS ONE RSYNC + ONE SBATCH FROM ERIK: the corrected run-02 probe.
      The 01b cells it depends on all exist, so it is unblocked.
- work_done: |
    THREADS 04 AND 05 RUN 00 HARVESTED AND ANALYSED, 2026-08-28T16:1x-16:4xZ.
    Gates re-read on the mount first (both .err empty, JOB DONE, rows =
    n_sites+7, classes 33.35/66.06/0.59 d0 and 33.28/66.01/0.70 d30).
    * DEFECT FOUND AND FIXED: export-Hbindmap-for-ovito.py computed the
      radius from the line with NO minimum image. Harmless on d90 (line
      0.3 A off the box centre); WRONG on d0, whose line at x = 144.0 in
      lx = 250.33 makes 7.6 % of all sites closer the other way round.
      This is the SAME defect fixed in select-relaxation-subset.py on
      2026-08-24 -- the instance was fixed, the class was not. New
      derivative script requires --lx and applies the minimum image in the
      radius AND in the far-field background selector. Identical copies in
      both threads, cmp-verified. Canon proposal
      2026-08-28-1650-fix-the-class-not-the-instance-minimum-image.
    * REGRESSION CHECK before any new number was believed: the new code was
      run over d90's own 1-period data and reproduces thread 01's published
      results exactly -- E_seg_min -0.1463 (published -0.1464), tet -0.1330,
      distorted -0.0881, 1.358 % below -50 meV, 989 sites below -100 meV.
    * SCIENCE. d0 (screw) BINDS: E_seg -0.1023 eV at a CORE-DISTORTED site
      on the glide plane, mu_core -2.377 eV, reach 21 A, strain step
      +0.3 meV = zero. Project open question 3 answered in the negative.
      d30: -0.1475 eV at an OCT site on the tension side, mu_core -2.4225,
      reach 49 A, step +10.9 meV -- the same trap depth as d90 (-0.1463)
      to 1.2 meV. What differs is CAPACITY: 80 / 322 / 989 sites below
      -100 meV for d0 / d30 / d90. Strain step scales with b_edge as
      0.00 / 0.48 / 1.00 against sin(0)/sin(30)/sin(90).
    * FIGURES VERIFIED BY EYE before delivery (the d90 set's blank fig3 is
      the precedent): fig1-3 per thread + fig6 comparison, all readable,
      symmetric diverging scales, clipping stated on the figure.
    * Artefacts: 1-period OVITO dumps + .dat for both characters, per-thread
      analysis .txt and fig1-3, the two scripts, and at the project root
      character-comparison_*.txt + fig6_*.{pdf,png} + compare-characters.py.
      thread.md 04 and 05 given full RESULTS sections; project.md open
      question 3 answered.
    * The 55 MB maps and 50 MB catalogue tables STAY ON THE CLUSTER, per the
      d90 precedent. Working copies were pulled to the device shell's own
      scratch ($HOME/scratch-nih, outside mnt/), not into Erik's folders.
    MINIMIZER BAKE-OFF WRITTEN 2026-08-28T16:5x-17:0xZ, thread 04, at Erik's
    request and after a brainstorm in chat. Five arms (fire, fire+abcfire,
    cg, hftn, quickmin) over the same 20 sites, equal budget in
    FORCE EVALUATIONS not iterations, fairness gate on the converged fnorm
    before any cost table, agreement gate on E_ins across arms. Rigid-lattice
    start, no seeding -- Erik's explicit decision; seeding was raised as a
    possibly bigger lever and deliberately rejected for this run because it
    would confound the comparison and could pick the basin for the deep set.
    DOC-CHECK CAUGHT MY OWN CHAT ERROR: I proposed "FIRE with 2.0 parameters"
    as a separate arm; in lammps/250722 the `fire` DEFAULTS ARE the Guenole
    2020 parameters, so arm 0 already is FIRE 2.0 and there is no
    2.0-vs-default comparison to be had. Corrected in the input header and
    in thread.md rather than silently dropped.
    Also doc-checked and acted on: `hftn` ignores min_modify (so `norm` is
    not settable for it -- hence the fairness gate); `fix setforce` with
    value 0.0 is legal during minimize; `$(dt)` was removed in favour of the
    literal timestep rather than reasoning about L33's scope.
    BAKE-OFF RESULT 2026-08-28T18:3xZ, job 22736776: FIRE WINS and MY
    PRE-RUN ARGUMENT WAS WRONG. I reasoned in chat that a near-quadratic
    basin entered close to its minimum should favour cg or hftn over damped
    dynamics. cg needed the SAME iteration count as fire (6855 vs 7957) so
    its line search was pure overhead; hftn converged in 3-4 Newton steps
    per site exactly as advertised and still lost 2.5x on the constant
    (~180 evaluations per outer step in its inner CG loop); quickmin lost
    20x, worst on the core-distorted deep sites. Recommendation: keep FIRE,
    add `min_modify abcfire yes` for a further 8 %. L26 from the winner:
    14.27 ms per force evaluation at 256 ranks -> ~33 min for a 372-site d0
    production, so -t 02:00:00. Canon proposals
    2026-08-28-1830-score-minimizers-in-force-evaluations and
    2026-08-28-1835-lammps-log-echoes-the-command-before-its-output.
    THREAD 02 OUTPUT DESIGN CHANGED 2026-08-28T18:5xZ at Erik's request:
    `fix ave/atom` REMOVED along with its MC-off windows and probe gate;
    per-atom stress now comes from a short fixed-box quickmin quench of the
    A and B write_data files, as a separate small job. Deletes the
    accumulator-vs-mc/sites hazard outright. Convergence criterion for the
    quench recorded (loose ftol + max |dr| against the unquenched snapshot).
    OVERNIGHT HARVEST 2026-08-28T19:4x-19:5xZ (unattended). d0 and d30 run 01
    both COMPLETE and analysed through ONE parameterised script,
    analyse-relaxed-subset_run01.py at the project root -- written
    parameterised rather than copied precisely because two d0->d30 token
    substitutions had already corrupted paths earlier in the session.
    Method is thread 01's d90 script unchanged (relaxed per-(family,z)
    background from the bg ladder, distorted referenced to octahedral,
    mu_core from the reservoir-referenced depth).
    RESULTS: mu_core -2.3702 (d0) / -2.4071 (d30) / -2.4061 (d90). d30 and
    d90 agree to 1.0 meV after relaxation, 1.2 meV rigid; the screw is
    37 meV higher and its trap stays CORE-DISTORTED and moves INWARD
    (r 6.27 -> 2.12 A). Relaxation shallows every character: 7.1 / 15.4 /
    15.3 meV. d90's radial pathology appears in d30 in a much milder form
    (one site, 20.3 meV gap, converged, flagged and excluded) and not at
    all in d0.
    A REAL FIGURE DEFECT CAUGHT, and caught only because staging was down.
    Panel (b) of fig5 plotted E_seg_RIGID (per-family) against
    E_seg_RESERVOIR (octahedral-referenced) with a HARDCODED ylim of 0.05 --
    which put every tetrahedral site at +0.34..+0.42 eV and silently clipped
    ALL 208 OF THEM off the top. That is exactly the defect this session
    criticised in the d90 figure set on 2026-08-27. Fixed twice over: the
    panel now plots rigid vs RELAXED (both per-family, so the diagonal means
    something), and the limits come from the data with an explicit
    count of off-axes points printed as a gate. I could not eyeball the
    figure because device staging began returning session_stale_relogin at
    19:4xZ, so I checked the plotted ranges numerically instead -- which is
    what found it. Visual checking would probably have found it too; the
    numeric check is the more reliable habit.
    RELAXED CHARACTER COMPARISON ADDED 2026-08-28T20:0xZ (second check-in
    fired at 19:56Z; it found the harvest already done, so its remaining
    item -- update the character comparison -- was what it bought):
      compare-characters-relaxed.py
      character-comparison_relaxed-vs-rigid_Pezold-EAM-0K.txt
      fig7_character-comparison_relaxed_Pezold-EAM-0K.{pdf,png}
    It reads TWO column layouts explicitly (d90's per-site file is thread
    01's 2026-08-25 format and carries x,y; d0's and d30's do not) rather
    than assuming one. A pointer was appended to the RIGID comparison .txt
    saying it is superseded in part and where the relaxed numbers are; the
    rigid file's own numbers are unchanged and it stays valid as a rigid
    document.
    THE AXES-COVERAGE GATE EARNED ITS KEEP TWICE MORE. Written into fig5
    after the reservoir/relaxed mix-up, it then caught the SAME class of
    defect in fig7 panel (a): 212 of 362 points off-axis, because
    reservoir-referenced tetrahedral sites sit at +0.27..+0.42 eV (the bulk
    oct/tet splitting, not binding). Panel (a) now shows oct + core-distorted
    only, with the reason written in the code. The 4-6 remaining
    core-distorted outliers per character are CLIPPED AND COUNTED ON THE
    FIGURE, per Erik's 2026-08-04 rule -- clip loudly, never silently --
    rather than squashed into a widened axis.
    Third instance of this defect class in two days (d90 fig3 2026-08-04,
    fig5 tonight, fig7 tonight). The gate stays in both scripts.
    BOX SCALING, Erik's question, answered from data: a(300 K, mu) IS
    already available -- column 4 of isotherm_EAM_300K_merged.dat, the file
    the pre-fill already reads, from stress-free NPT. No new phase-diagram
    run needed at 300 K. It also exposed an inconsistency: the pre-fill
    fills at bulk x(mu) but the box is scaled to PURE-Ni a0(300 K), and
    those contradict. Magnitude: <=0.002 % strain where the core transition
    is predicted (-2.42..-2.38), rising to 0.109 % at mu = -2.28 -- against
    the 0.37 % this project rejected at kickoff. Full table in thread 02.
- notes: |
    FOR ERIK, MORNING OF 2026-08-29 -- WHAT HAPPENED OVERNIGHT.
    Everything you submitted landed and is analysed except one job, which
    failed in a minute and is already fixed.
    LANDED AND ANALYSED: d0 and d30 run 01 (the relaxed single-H binding).
    All three characters now have relaxed numbers, and the rigid story
    survives: mu_core = -2.3702 (d0) / -2.4071 (d30) / -2.4061 (d90). d30
    and d90 agree to 1.0 meV; the screw sits 37 meV higher and its trap
    stays core-distorted. Relaxation makes every character SHALLOWER, never
    deeper. Written into threads 04 and 05 and into project.md's open
    question 3. Thread 02's run 01b also completed -- all seven pre-filled
    cells written, every gate passed.
    ALSO ADDED overnight: the relaxed three-character comparison
    (character-comparison_relaxed-vs-rigid_*.txt and fig7_*) at the project
    root, plus a pointer in the rigid comparison saying it is superseded in
    part.
    NEEDS YOU: the run-02 probe (22736818) died at 19:16Z on
    `velocity create loop all` -- this cell family's atom IDs are not
    consecutive. Fixed to `loop geom`. Re-rsync
    musweep-d90-300K_Ni-disloc-d90-Pezold-EAM.in and resubmit; no
    dependency needed now, the cells exist.
    ALSO WAITING: nothing else. Thread 02 run-02 PRODUCTION is still to be
    written and is blocked on that probe's gates.
    ONE THING I COULD NOT DO: file staging from the device started failing
    at 19:4xZ with `session_stale_relogin` -- the desktop app needs you to
    sign in again. It does not affect the shell or any of the above, but it
    means I could not LOOK at the figures I generated; I verified them
    numerically instead, and that is how I caught a real defect (see
    work_done). Worth re-authenticating before the next session.

    Cloud Cowork session on M5's connected folders (DEVEL, SIMULATIONS,
    cluster-mounts/cmmg). Stale entries still sitting in `active` and NOT
    closed here (read-only session): 2026-08-02-1647-ingest-eam-dislocs-
    ni-cu, 2026-08-03-1401-nih-at-dislocs-design, 2026-08-05-1425-mcsites-
    presentation, 2026-08-20-1145-cluster-status-sweep, 2026-08-24-0753-
    status-readout, 2026-08-24-1531-nih-at-dislocs-status,
    2026-08-25-1725-nih-two-project-status, 2026-08-27-0852-portfolio-status.
- closed: 2026-08-29T14:35Z
- summary: |
    Session ran 2026-08-28, closed 2026-08-29 at Erik's request.
    Status readout of ni-h-at-dislocs-eam-meam that turned into a full day's
    pilot work across four threads. DELIVERED: d0 and d30 run 00 rigid maps
    harvested and analysed (they had sat unharvested for three days); a
    five-arm minimizer bake-off; run 01 relaxed binding COMPLETE for all
    three characters, so mu_core is now measured and not bounded; thread 02
    run 01b (seven pre-filled cells at a(mu)) complete; thread 02 run 02
    written and probed.
    SCIENCE: mu_core = -2.3702 (d0) / -2.4071 (d30) / -2.4061 (d90) eV.
    d30 and d90 agree to 1.0 meV; the screw is 36 meV higher and its trap is
    core-distorted, not octahedral. Relaxation shallows every character.
    Project open question 3 ANSWERED: the screw is not the null case.
    IN_FLIGHT IS NOT EMPTY AT CLOSE -- ONE ITEM: the thread 02 run-02 probe
    (22736818) failed on `velocity create loop all` because this cell
    family's atom IDs are not consecutive. FIXED to `loop geom` and
    re-linted, but NOT resubmitted (sbatch is strict-A and Erik had gone
    home). It needs one rsync of musweep-d90-300K_Ni-disloc-d90-Pezold-EAM.in
    and one sbatch; the seven cells it depends on all exist, so it is
    unblocked and needs no dependency.
    VERIFIED AT CLOSE (2026-08-29T14:3xZ): nothing on the cluster has moved
    since 2026-08-28T19:16Z -- the probe was not resubmitted, and THE
    CLUSTER STILL HOLDS THE PRE-FIX INPUT (14338 bytes, zero occurrences of
    `loop geom`; the Mac copy is 15417 bytes with three). Resubmitting
    without the rsync first will fail in exactly the same way. The rsync is
    not optional bookkeeping; it IS the fix.
    ALSO OUTSTANDING, not a loop: device file staging began returning
    session_stale_relogin at 19:4xZ -- the desktop app needs a fresh sign-in
    before any session can look at images or PDFs again.
    FIVE CANON PROPOSALS FILED, none merged (pilot route).
- handoff_to: null   # no successor registered; the morning summary in `notes` is the hand-over


### session_id: 2026-08-26-1713-all-projects-status
- mode: pilot
- scope: |
    READ-ONLY portfolio status readout across ALL simulation projects in
    ~/Desktop/SIMULATIONS: which projects/threads are active, what is on
    the cluster, what is unharvested, and what can/should be submitted
    next. NO writes to any project.md or thread.md, no cluster writes, no
    sbatch drafting beyond quoting hand-overs already written by the
    owning sessions.
- started: 2026-08-26T17:13Z
- last_active: 2026-08-26T19:40Z
- simulation_root: ~/Desktop/SIMULATIONS   # confirmed: connected folder, matches local.yaml default
- machine: M5   # canon/local/.this-machine
- cluster_identity: |
    cmmg -> <CLUSTER_USER>@<CLUSTER_HOST>, scratch /cmmc/ptmp/<CLUSTER_USER>
    (resolved from canon/local/clusters.local.yaml, echoed in chat only).
    Mount ~/cluster-mounts/cmmg is CONNECTED but lists EMPTY with no
    permission error -> L15 staleness; remount requested from Erik.
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/02_MU-SWEEP-D90-300K/**   (added 2026-08-26T17:47Z)
  - LLM-LAMMPS-public/canon/proposals-inbox.md (append-only; pilots do not edit canon)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/project.md                (added 2026-08-26T17:47Z)
  - cluster-mounts/cmmg/Ni-H-AT-DISLOCS-EAM-MEAM/02_MU-SWEEP-D90-300K/**  (proposed writes, confirmed first)
- scope_change: |
    PROMOTED read-only portfolio readout -> PILOT WRITE OWNERSHIP of
    ni-h-at-dislocs-eam-meam THREAD 02 ONLY, 2026-08-26T17:47Z, Erik's pick
    (AskUserQuestion: "Thread 02 only").
    TAKEOVER, ON THE RECORD, from TWO stale entries, neither closed here:
      * 2026-08-03-1401-nih-at-dislocs-design -- declares the whole tree
        `SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/**` + the cluster mount;
        last_active 2026-08-04T18:40Z = 22 DAYS stale; flagged stale by the
        08-20, 08-24 and 08-25 sweeps. Its design scope is broader than
        thread 02 and is not mine to declare finished.
      * 2026-08-24-1531-nih-at-dislocs-status -- declares the whole tree +
        the mount; last_active 2026-08-24T17:20Z = 2 days stale. Its own
        scope_change says "Scope: thread 01 run 01 production only ... NOT
        thread 02", so thread 02 was never worked by it.
    NOT TAKEN, and deliberately so: threads 04/05 (d0/d30) are held by
    2026-08-25-1725-nih-two-project-status, last_active 2026-08-26T16:05Z
    (LIVE, ~1 h), whose in_flight carries the d0/d30 harvest as its own
    pending item. Erik chose to leave them there. This session will not
    read-modify-write anything under 04/ or 05/.
    Thread 01 is NOT taken either -- read-only on it as the source of the
    mu grid (mu_core = -2.4061 eV, E_seg_min -0.1311 eV relaxed).
    Erik told, in-turn.
- in_flight: |
    RUN 00b, the WHOLE-CELL site catalogue: written and staged on cmmg
    2026-08-26T20:4xZ, handed to Erik (strict-A), NOT SUBMITTED.
      02_MU-SWEEP-D90-300K/00_SCALED-AND-PREFILLED-CELLS/
      submit-catalogue-fullcell_Ni-disloc-d90-Pezold-EAM-0K.slurm
    p.cmmg, 256 ranks, -t 00:20:00, est. 90-120 s. Probe waiver recorded.
    A RESUMING SESSION MUST CHECK THE RUN DIR FIRST (step 1b).
- run_01_history: |
    THREAD 02 RUN 01, the octahedral pre-fill. DRY-RUN GATE PROBE RUN BY
    ERIK 2026-08-26T18:4xZ: all seven gates PASS on the real 953534-site
    catalogue (class fractions 33.31/65.92/0.77 % exactly), 317584
    octahedral sites, python3 3.6.15 -- BUT GATE 4 passed at 3.05 meV
    against a 4 meV tolerance and was investigated rather than accepted.
    THE GATE WAS WRONG, NOT THE BACKGROUND. See work_done. Script fixed
    and RESTAGED (32866 B, Mac<->cluster cmp-identical).
    RE-PROBE (PROBE2) RUN BY ERIK 2026-08-26T19:0xZ: GATE 4 now reads
    -24.330 vs -24.323 meV against a 0.500 meV tolerance -- PASS with a
    factor ~70 of margin. All other gates bit-identical to PROBE1 (the
    per-mu seed is deterministic). Residual statistics checked rather than
    assumed: z = +0.63 +0.68 +2.35 -0.09 +0.69 +0.32 over the six mu with
    enough H to mean anything; the +2.35 at mu = -2.32 is a draw
    (P(max|z|>2.35) ~ 19 % over ten points), and the mu_eff histogram
    agrees with the exact per-site sum to 0.008 %.
    PRODUCTION ATTEMPT 1 (2026-08-26T19:1xZ): SILENTLY TRUNCATED TO ONE MU
    POINT. My hand-over passed the grid as
    `sbatch --export=ALL,MU_LIST=-2.4400,-2.4100,...`; sbatch's --export
    uses COMMAS as its own separator, so MU_LIST became "-2.4400" and the
    rest were read as variable NAMES. The job exited clean with every
    green light. MY BUG, in the hand-off, not the script's.
    Fixed at the source: the 9-point grid is now the DEFAULT MU_LIST in
    the submit script so nothing need be exported; the trap and the
    override form that does work are documented in the script header.

    AND THE TRUNCATION WAS LUCKY -- checking the one cell exposed a REAL
    DEFECT in the pre-fill. Its profile had 99 z-bins against GATE 3's 69
    background layers. Cause: the background was looked up BY BIN with a
    silent fallback to the bottom/top value when a bin held no far-field
    sites. A (111) plane is displaced by the dislocation field, so its
    near-core sites land in a different 0.5 A bin from its far-field ones;
    30 of 99 bins therefore held ONLY near-core sites and had no far-field
    median at all. Measured on the real catalogue: 13.123 % of octahedral
    sites assigned a background wrong by a MEAN of 13.59 meV and up to
    27.22 meV -- as much as the entire strain step the pre-fill exists to
    reproduce -- concentrated on precisely the near-core sites the thread
    is about. All seven gates passed while it happened.
    FIX: the background is now INTERPOLATED linearly in z on nodes at the
    mean z of each layer's far-field sites (not the bin centre -- the
    plane is displaced). New GATE 3b reports node span, max gap and the
    clamped fraction, and fails if the nodes cannot cover the sites.
    Validated on the real thread-01 data: 69 nodes, max gap 2.039 A = the
    (111) interlayer spacing exactly (one node per atomic plane, none
    missing), 1.463 % clamped at the outermost half-plane where the
    profile is flattest (< 0.1 meV error).
      before: 13.1 % of sites wrong by mean 13.6 meV, max 27.2
      after :  1.5 % held at an endpoint, error < 0.1 meV
    Script restaged (35894 B) + submit (6743 B), Mac<->cluster
    cmp-identical.
    PROBE3 RUN BY ERIK 2026-08-26T19:3xZ: all nine mu, GATE 3b present,
    every gate PASS. Fix confirmed by mechanism, not just by green:
    expected below/above ratio moved 1.62 -> 1.70 because the mis-assigned
    bins were mostly BELOW the glide plane and the fallback was handing
    them the TOP (compression) value; mu_eff shifted ~1.3 meV throughout,
    as it must to hold the total. The residual 1.463 % clamp looked
    asymmetric (529 below / 4117 above) and was CHECKED: those sites are
    members of the same outermost (111) plane as the node, |dz| <= 0.0015
    and 0.1186 A, true bg error mean AND max 0.0000 meV -- clamped total
    = exactly one plane's worth. The asymmetry is the top plane's larger
    z-spread, not physics.
    PRODUCTION ATTEMPT 2 RUN BY ERIK 2026-08-26T19:4xZ: COMPLETE. Nine
    cells + nine profiles + summary in
    00_SCALED-AND-PREFILLED-CELLS/prefilled-cells-a300K-Pezold-EAM/ on
    cmmg, ~34 MB per cell.
    VERIFIED, not assumed -- each cell read back and cross-checked on four
    independent conditions: header atoms count == 568606 + N_H; type-2
    atoms actually in the file == N_H; sum of n_H over the profile's 99
    z-bins == N_H; every H inside the map zone. ALL NINE CONSISTENT.
    N_H = 0 / 9 / 10 / 47 / 131 / 491 / 1612 / 2208 / 3272 for
    mu = -2.44 .. -2.28. File sizes rise monotonically with N_H
    (33861508 -> 34051420 B), a free independent check.
    Realised below/above ratio tracks the expected 1.70 at the three
    richest points (1.696/1.680/1.704 vs 1.698/1.696/1.693); below
    N_H ~ 100 the ratio is meaningless, which is why GATE 7 gates on the
    Poisson interval and not on the ratio.
    HARVEST: curated inbound of the summary + nine profiles (.dat, ~4 kB)
    to 00_SCALED-AND-PREFILLED-CELLS/results/prefill/. The .lmps cells
    STAY on the cluster -- structures are ask-first, 34 MB each, and they
    are the run inputs where they sit.
    THREAD 02 RUN 01 IS CLOSED. in_flight cleared.
    A RESUMING SESSION MUST CHECK THE RUN DIR FIRST (step 1b): expect
    prefilled-cells-a300K-Pezold-EAM/ with 9 .lmps (~34 MB each) + 9
    profiles + a summary. The single mu-2.440 cell now sitting there is
    from the TRUNCATED, PRE-FIX run and is superseded.

    PROPOSAL EXTENDED with this as a second instance the same day: a gate
    that quantifies a population must state the DENOMINATOR it covers.
    GATE 3 asked "does every background LAYER have enough far-field sites"
    and truthfully said yes -- for the 69 that HAD any. It could not see
    the 30 that had none, because they never entered the dict to be
    counted. A silent fallback (dict .get with a default, a clamp, an
    except-pass) is invisible to every gate that only inspects the objects
    that took the normal path. What caught it was not a gate at all: two
    numbers describing the same thing disagreed (99 vs 69) and that was
    not allowed to pass.
      /cmmc/ptmp/<CLUSTER_USER>/Ni-H-AT-DISLOCS-EAM-MEAM/
        02_MU-SWEEP-D90-300K/00_SCALED-AND-PREFILLED-CELLS/
        submit-prefill-oct-zprofile_Ni-disloc-d90-a300K-Pezold-EAM.slurm
    s.cmmg, 1 task, --mem=16000, --time 00:30:00, ~7 min estimated.
    A RESUMING SESSION MUST CHECK THE RUN DIR FIRST (step 1b).
    NOTE for whoever checks: the cluster directory LISTING reads empty
    through the bridge (L15 write-side stale view) while exact-path reads
    return correct byte counts -- verified 31069 / 6312 / 2324 B for the
    .py / .slurm / isotherm. Do not read the empty listing as "nothing was
    staged"; ask Erik for an `ls` from his own shell.
- work_done: |
    INHERITED OPEN LOOP CLOSED, 2026-08-26T18:0xZ -- 22 days after it opened.
    thread 02 run 00, the strain reference
    (submit-strainref-Eins_Ni-H-oct-tet-a300K-Pezold-EAM.slurm, staged
    2026-08-04, submit handed the same day, never reconciled by any session):
    RAN AND PASSED. Erik's sacct paste 2026-08-26 --
      21638799  NiH-STRAINREF-A300K-EAM  COMPLETED  00:00:08
                2026-08-04T21:21:47  0:0
    Mount came back online the same turn (Erik remounted); outputs read
    directly. GATES: .err empty, JOB DONE, 6/6 rows, and the physics gate --
    coordination 6.0/6.0 for octahedral and 4.0/16.0 for tetrahedral in all
    three strain states, i.e. exactly the required values, which also
    independently confirms the `coord 4 3.0` classifier of the thread-01
    catalogue at a0(300 K).
    RESULT, E_ins at a0(300 K) = 3.536955 A, "below" = tension side:
      oct rigid   -2.099994 / -2.084526 / -2.068502  step +31.49 meV
      oct relaxed -2.166516 / -2.153235 / -2.139557  step +26.96 meV
      tet rigid   -1.572174 / -1.551486 / -1.529998  step +42.18 meV
      tet relaxed -1.775177 / -1.757999 / -1.740652  step +34.52 meV
    TWO FINDINGS:
      (1) The symmetry assumption the run was written to test HOLDS -- the
          unstrained reference sits 0.28 meV (rigid) / 0.20 meV (relaxed) off
          the midpoint of the two strained states, under 2 % of the half-step.
      (2) The STEP IS 30 % LARGER than the 24.2 meV thread 01 measured from
          the rigid far field at a0(0 K). c_below/c_above goes 2.55 -> 3.38
          (rigid) / 2.84 (relaxed). The pre-fill recipe in thread.md was
          written on 2.55 and must be renormalised. Still sub-kT, still small
          against the 146 meV core trap -- the physics argument is unchanged,
          only the number moves.
      Also recorded: relaxation narrows the oct-tet gap from 0.533 to
      0.395 eV (oct gains -68.7 meV, tet -206.5 meV). "Octahedral only"
      pre-fill still correct -- relaxed tet:oct is exp(-15.3).
    HARVEST: .dat + .out curated-pulled to
    02_MU-SWEEP-D90-300K/00_SCALED-AND-PREFILLED-CELLS/results/, manifest
    alongside; Mac and cluster copies of the .in and .slurm cmp-identical;
    write verified by directory listing.
    thread.md: status designed -> open, run-00 section appended with gates,
    result table and the open design question.
    ERIK'S DECISION 2026-08-26T18:1xZ: the pre-fill uses the RELAXED pair.
    The thread-01 rigid background is rescaled about its mean by
    STEP_RATIO = 26.959/31.492 = 0.8560. Reasoning on the record in
    thread.md run 01: the MC inserts into a fluctuating cell and MD runs
    afterwards, so the equilibrium sampled is the relaxed one; and mu_eff
    is FITTED, so of the ~69 meV rigid->relaxed shift only the 4.5 meV
    change in the STEP survives -- the rest is absorbed exactly. The
    shape-preserving rescale is licensed by the three run-00 points
    (E_ins affine in strain to under 2 % of the half-step), not assumed.
    Made falsifiable rather than final: the pre-fill writes its realised
    N_H(z) and the run's own equilibrated N_H(z) measures the truth.

    RUN 01 WRITTEN 2026-08-26T18:1x-18:3xZ:
      prefill-oct-zprofile_Ni-disloc-d90-a300K-Pezold-EAM.py (31069 B)
      submit-prefill-oct-zprofile_...slurm (6312 B)
      isotherm_EAM_300K_merged.dat copied in from ni-h-phase-diagram t05
    Pure Python 3 stdlib -- no numpy/pandas, no anaconda module, nothing
    installed into cluster $HOME. Mac and cluster copies cmp-identical.
    SEVEN GATES, five global before any file is written; GATE 4 is the one
    a physically wrong run FAILS -- the background is RECOMPUTED from the
    thread-01 map's own documented definition and must reproduce that
    thread's independently measured -24.32 meV rigid strain step.
    GATE 7 note worth keeping: the expected below/above ratio is NOT
    exp(step/kT) = 2.84 (that is a SHARP two-half split); the measured
    profile saturates over lambda = 53 A so the half-cell average is ~1.6.
    Gating on the sharp-step number would fail a CORRECT fill.
    THREE CHOICES FLAGGED, NOT HIDDEN, all written into thread.md:
      (a) pre-fill is MAPZONE-only (the catalogue exists nowhere else; H
          outside could not be moved by fix mc/sites anyway), and N_Ni for
          the target is the octahedral-site count, not the cell's 568606;
      (b) the source cell's ~1e-9 A/ps FIRE-residual Velocities section is
          DROPPED (the production input creates velocities at 300 K);
      (c) mu = -2.45 is deliberately absent -- the isotherm tabulates
          x = 0 at -2.47 and log-interpolation across a zero is undefined;
          the script refuses it rather than inventing a number.
    MU GRID (one MU_LIST line in the submit): -2.44 -2.43 | -2.41 -2.40 |
    -2.38 -2.35 -2.32 -2.29 -2.285 -2.28. -2.41/-2.40 straddle
    mu_core = -2.4061 tightly; -2.44/-2.43 are the core-still-empty
    anchors (~1 and ~3 H in the whole MC region). Cells are cheap, the
    RUNS are not -- which to submit as production is NOT decided here.
    VERIFIED BEFORE HAND-OVER, in the container on a synthetic stand-in
    built to the same shapes (432000 catalogue rows, 143860 oct sites, the
    real isotherm verbatim, the real box): all seven gates exercised and
    passing on all ten mu; both refusal paths confirmed to abort with the
    right message; the written data file parsed back by an INDEPENDENT
    LAMMPS-data reader (ASE) as 4000 Ni + 1478 H with the correct box and
    masses.
    GATE 4 CORRECTED 2026-08-26T18:4x-18:5xZ, before any production submit.
    The dry run's GATE 4 gave -27.37 meV against thread 01's -24.32 meV,
    inside a 4 meV tolerance but not comfortably. Investigation: thread
    01's own background is recoverable EXACTLY as E_ins - E_seg from its
    published per-site map; compared plane by plane the recomputation
    agreed to 0.0005 meV over all 99 octahedral z-planes -- the background
    was right to half a microelectronvolt. The two values thread 01 quoted
    as its far-field pair sit at z = 76.0 / 180.0 A, ~50 and ~53 A from
    the glide plane (about one decay length lambda = 53 A), NOT at the
    +-68.5 A map-zone edges. The gate computed the zone-edge step and
    compared it to a mid-profile pair: two different quantities.
    FIX: evaluate the background at the planes the reference was measured
    at. Agreement is then 0.007 meV and the tolerance drops 4 -> 0.5 meV,
    i.e. a gate that can actually fail. Re-validated on the REAL thread-01
    data (the 1-period map, whose per-plane background is identical)
    before restaging.
    SCIENCE IMPACT: none on the cells -- STEP_RATIO is a ratio of two
    quantities measured the same way on the same run-00 cells, so it is
    amplitude-independent. It DOES correct the reported bias: three steps
    are now on the record and must not be confused -- 31.49/26.96 meV
    (run-00 strain cells, ASYMPTOTIC, ratio 2.84), 27.37/23.43 meV (map
    zone edges, WHAT THE PRE-FILL USES, ratio 2.48), 24.32/20.82 meV
    (thread 01's quoted pair, ratio 2.24). thread.md's "~24 meV realised
    inside the +-70 A zone" is corrected: 24.32 meV is realised inside
    +-~52 A; the zone edge is 27.37 meV.
    OPEN OBSERVATION, recorded not resolved: the two independent estimates
    of the ASYMPTOTIC rigid step disagree by 24 % -- 31.49 meV from the
    run-00 strain cells vs 39.1 meV from thread 01's A(1-exp(-d/lambda))
    fit. Does not propagate into the pre-fill; should be settled before
    any absolute claim about the far-field strain bias.
    PROPOSAL FILED (pilot route, not a canon edit):
    canon/proposals-inbox.md 2026-08-26-1845-gate-reference-must-be-the-
    same-quantity -- a gate's REFERENCE must be the same quantity the gate
    COMPUTES, and a gate that passes at a large fraction of its own
    tolerance is a failure to investigate, not a pass.

    RUN 02 CADENCE -- MY ERROR, CAUGHT BY ERIK, CORRECTED 2026-08-26T20:1xZ.
    Erik asked for ~100x the attempts per block. I answered with a cost
    model built on RUN 14's cadence (fix mc/sites 20 1000) and concluded
    100x = 50 days/mu, that the catalogue must shrink, and that the MC
    region should become a 40 A cylinder. All three wrong, and the project
    record already said so -- Erik sent me back to it.
      (1) THIS thread's cadence is `fix mc/sites 1000 1000`, decided and
          written up in thread 02 with a three-part rationale Erik asked
          for, and repeated in project.md. That is 1 trial per MD STEP,
          not 50. Every number I quoted was 50x too pessimistic.
      (2) Nevery = 20 was EXPLICITLY REJECTED by this thread, with
          evidence (the mc/sites bench records ZERO acceptance at plateau
          mu at Nevery 20). I built a cost argument on the one cadence the
          thread had ruled out.
      (3) The MC region was already decided: project.md, "MC region is
          +-70 A about the glide plane (z = 56.5 to 196.5 A), which also
          excludes the two free (111) surfaces" -- a z-slab, with a second
          justification (free Ni surfaces are strong H traps that would
          swamp the signal) I never mentioned. My cylinder proposal
          reopened a closed decision on incomplete grounds.
    LESSON FOR THE CLASS, not the instance: I had READ project.md at
    takeover and still argued from a sibling thread's parameters instead
    of this thread's own decided ones. Before proposing a change to a
    run parameter, re-read the section of project.md / thread.md that SETS
    it -- a value quoted from a sibling run is not this run's value.
    WHAT SURVIVES: the source finding. fix_mc_sites.cpp calls
    energy_full() per trial (lines 796, 857) = comm->exchange/borders +
    neighbor->build(1) + pair->compute over the whole cell. That
    independently CONFIRMS the "~2.5x an MD step per trial" figure the
    thread had already adopted. The cadence was wrong, not the trial cost.
    CORRECTED ARITHMETIC, on the thread's own model and the parent
    relaxation's measured 14.89 ms/iteration at 128 ranks:
      Ntrials   1000 -> 26 s/block   -> 43 min per 100 blocks
      Ntrials  10000 -> 3.2 min      -> 5.3 h
      Ntrials 100000 -> 31 min       -> 52 h (96 h cap; restart chain at 200)
    So 100x is AFFORDABLE, ~52 h for 100 blocks. The constraint that
    actually bites is the thread's OWN frozen-catalogue crowding bound:
    <= ~430 accepted insertions per block. Where 100x lands depends on the
    acceptance rate, unmeasured for this cell -- 7x over the bound at "a
    few percent", right on it at 0.5 %. The bound is soft in a safe
    direction (violating it biases to UNDER-acceptance, not a wrong
    equilibrium). RECOMMENDATION PUT TO ERIK: probe at 1000 100000 at two
    mu (-2.44 and -2.40), read f_MC[7], set production Ntrials from
    430/acceptance.
    ALSO RE-FLAGGED: project.md says "THE WHOLE CELL is pre-filled". Run 01
    pre-filled MAPZONE ONLY (the catalogue exists nowhere else). Flagged at
    the time against thread.md's wording; project.md is more explicit and
    the deviation should be Erik's call, not mine.

    ERIK'S DECISIONS 2026-08-26T20:2x-20:4xZ:
      * CADENCE `fix mc/sites 1000 10000` -- 10x the designed attempts per
        block, not the 100x first asked for, after the thread's OWN cost
        model was applied: rate = Ntrials/(Nevery + 2.5 Ntrials) can never
        exceed 1.40x however large Ntrials gets (all it removes is the 29 %
        MD share). 10000 captures 1.35x = 96 % of that ceiling at 300
        accepted/block, inside the <=430 frozen-catalogue bound; 100000
        adds 3 % and lands 7x past it. Against run 08's 15/22/64
        attempts-to-equilibrium benchmark at 512 ranks, the real win is the
        DILUTE case dropping from 1.10 to 0.82 of a 96 h job. Probe still
        measures f_MC[7]; if acceptance is well under 3 %, Ntrials can rise
        toward 430/acceptance (43000 at 1 %), which is where 100x would be
        justified.
      * FAR FIELD AT EQUILIBRIUM CONCENTRATION -- closes the run-01
        deviation against project.md's "the whole cell is pre-filled".
      * SURFACE EXCLUSION = 2 r_c, per Erik, CHECKED and it found two
        things: (1) r_c is 5.65 A from the setfl header, NOT the 4.90 A the
        filename suggests, so 2 r_c = 11.30 A not 9.80; (2) the 2D-dynamic
        boundary slab as BUILT is 3 (111) layers = 5.0865 A = 0.90 r_c, not
        2 r_c as Erik recalled. NOT a defect -- the criterion is that the
        outermost MOBILE atom have a complete neighbour shell, and it sits
        6.104 A above the surface against r_c 5.65, a 0.45 A margin. But
        thin, and `nlayers = 3` is hard-coded while only layer_dz tracks
        the potential: a longer-cutoff potential (KoShimLee MEAM, an ACE
        with r_c > 6.10 A) would silently break it. Flagged for the parent
        project.
    RUN 00b WRITTEN + STAGED: catalogue-fullcell_*.in (4330 B) +
    submit-*.slurm (4957 B) + make-site-list.py, Mac<->cluster
    cmp-identical, all style greps (1.1/1.12/1.16) clean, ASCII, no
    reset_timestep. Differs from thread 01's catalogue by exactly one thing:
    no `region MAPZONE`. Answers a free question -- whether `region` filters
    emitted SITES or the ATOMS fed to the tessellation; the latter would mean
    thread 01's catalogue carries edge artefacts at z = 56.5 / 196.5.

    NOT WRITTEN, and next: the mu sweep itself (run 02). It needs the
    output set + cadence agreed with Erik FIRST (preferences.md: ask, do
    not infer; the standing answer for a defect/hydride problem is
    per-atom stresses and energies, but it is still asked).
- notes: |
    Step 1b NOT yet reconcilable: cluster mount stale, sacct paste
    requested from Erik. Open loops carried by other entries as of
    2026-08-26T17:13Z:
      * 22730592 NiH-BIAXMISFIT-EAM (hydride-cycle t02 production)
      * 22730593_[0-1] CYC2-RATEB re-probe (hydride-cycle t03)
      * 22730594_[0-1] CYC2-DOME re-probe (hydride-cycle t04)
      * 22730595_[0-9] EAM-ISOTHERM-900K (phase-diagram t03 run 15)
      * ni-h-at-dislocs t04/t05 run 00 (22728805 / 22728806) COMPLETE
        but UNHARVESTED
      * ni-melting-point t02 prepare+probe blocks handed over, status
        unknown to this session
    Coexisting active sessions with overlapping read scope:
    2026-08-26-1608-convexhull-0k-brainstorm, 2026-08-26-1424-nicu-
    phasediagram-status, 2026-08-26-1410-ace-ni-nb-potprops,
    2026-08-25-1725-nih-two-project-status, 2026-08-25-1536-nimelt-probe-fix.
    This session writes to none of their paths.

    NI-MELTING-POINT-EAM thread 02 -- SCOPE DECLINED, 2026-08-26T17:2xZ.
    Erik asked to check its status; the scope is owned by
    2026-08-25-1536-nimelt-probe-fix and Erik chose to resume THAT
    conversation rather than transfer ownership here (AskUserQuestion).
    This session made NO writes under NI-MELTING-POINT-EAM/.
    Findings handed across so the resuming session need not re-derive them:
      * thread.md (mtime 2026-08-26T15:09Z, LATER than that entry's
        last_active 12:15Z -- its last_active is stale) records probes
        BOTH PASS, L26 = 135 steps/s at 1 rank, and production prepare
        tasks 2-3 BOTH CELLS PASS (cube a0 3.5854 COLD 1088; large box
        a0 3.5824 COLD 8102).
      * THE ONLY OPEN ITEM is the 7-rung measure ladder
        (--array=0-6 --ntasks=8, 1300-1600 K, ~30 min/rung at 8 ranks),
        handed over 2026-08-26 -- SUBMITTED? UNKNOWN to any session.
      * Nothing of thread 02 is harvested to the Mac: the local thread
        dir holds only the 2 .in files, 4 submits and thread.md.
      * Mount ~/cluster-mounts/cmmg STILL lists empty (L15) as of 17:2xZ;
        remount needed before any gate check can be done through it.
- closed: 2026-08-26T19:40Z
- summary: |
    Portfolio status readout across all 9 live projects, then took
    ni-h-at-dislocs-eam-meam THREAD 02 (only) from two stale entries and
    carried it from a 22-day-old open loop to two completed runs plus a
    third handed over.
    DELIVERED: run 00 (strain reference) reconciled and gated; run 01 (the
    octahedral z-profile pre-fill) written, probed three times, and
    COMPLETE -- 9 cells verified against their profiles on four independent
    conditions; run 00b (whole-cell catalogue) written and staged.
    DECISIONS TAKEN BY ERIK AND RECORDED: relaxed normalisation for the
    pre-fill (STEP_RATIO 0.856059); output set + cadence for the mu sweep;
    `fix mc/sites 1000 10000`; far field at equilibrium concentration;
    surface exclusion 2 r_c = 11.30 A.
    *** in_flight IS NON-EMPTY: run 00b is handed over and NOT SUBMITTED.
    A resuming session must CHECK THE RUN DIR FIRST (step 1b). ***
    Three of my own defects were found and fixed before they reached
    production, each by refusing to accept a near-miss: GATE 4 compared two
    different quantities and passed on a loose tolerance; the background
    was looked up by BIN with a silent fallback, mis-assigning 13.1 % of
    octahedral sites by up to 27.2 meV; and an `sbatch --export` comma
    collision silently truncated a production run to one mu -- which is how
    the second one was found. One canon proposal filed covering the class.
    ALSO: I twice argued from a sibling thread's parameters instead of this
    thread's own recorded ones (run 14's cadence; a cylindrical MC region
    that was already decided as a z-slab). Erik caught both. The lesson is
    in thread.md and in the entry above: re-read the section that SETS a
    parameter before proposing to change it.
    NOT DONE, and next: submit run 00b, then run 01b (whole-cell pre-fill,
    needs two new script arguments), then write run 02. Full resume block
    at the end of 02_MU-SWEEP-D90-300K/thread.md under "RESUME HERE".
- handoff_to: null
- open_for_other_sessions: |
    ni-dislocs-eam-meam-ace: the 2D-dynamic boundary slab is `nlayers = 3`
    hard-coded while only `layer_dz` tracks the potential. Sound for
    Pezold-EAM (outermost mobile atom clears the surface by 6.104 A vs
    r_c = 5.65) but only a 0.45 A margin; a potential with r_c > 6.10 A
    (KoShimLee MEAM, an ACE) would silently break it. Not this session's
    scope; flagged for whoever owns that project.
    SESSIONS.md carries a lint-no-identity FAILURE at the ace-ni-nb-potprops
    entry (real cluster user and scratch path in a PUBLIC-repo file). Not
    mine to edit; must be fixed before any push.


### session_id: 2026-08-25-1536-nimelt-probe-fix
- mode: designer+pilot     # promoted from pilot 2026-08-25T16:05Z, Erik's request
- scope: |
    Diagnose and fix the failed ni-melting-point-eam prepare probe
    (job 22719499, FAILED 18 s, flagged as OPEN LOOP by
    2026-08-25-1105-sim-status). Fix the .in files on the Mac, hand
    over staging + re-probe (strict-A). No production submits until
    probes pass.
- started: 2026-08-25T15:36Z
- last_active: 2026-08-26T16:30Z
- simulation_root: ~/Desktop/SIMULATIONS   # confirmed by Erik (AskUserQuestion)
- machine: M5
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry)
  - SIMULATIONS/NI-MELTING-POINT-EAM/**
  - LLM-LAMMPS-public/canon/**   (DESIGNER, lock taken 2026-08-25T16:05Z)
- scope_change: |
    TAKEOVER, ON THE RECORD: 2026-08-24-0753-status-readout owns
    SIMULATIONS/NI-MELTING-POINT-EAM/** but its conversation ended
    2026-08-24T17:35Z and the probe failure it handed over was never
    diagnosed. Erik confirmed the takeover (AskUserQuestion,
    2026-08-25T15:38Z). Only the melting scope is taken; that entry's
    ni-h-phase-diagram and ni-h-diffusivity scopes are NOT taken.
    PROMOTED pilot -> designer+pilot 2026-08-25T16:05Z at Erik's explicit
    request ("switch to designer, and implement everything in your inbox").
    DESIGNER LOCK TAKEN, verified free: 2026-08-25-1105-sim-status released
    it 13:10Z (its scope_change says so); no other active entry claims it.
    Designer area: the three pending inbox items, incl. building the
    mu-scan loader TOOL that 0934 is blocked on (Erik's "everything").
    LOCK RELEASED 2026-08-25T17:15Z after the pass (mode stays
    designer+pilot on the record for the L45 same-turn merge; further
    project work is pilot).
- designer_pass: |
    INBOX CLEARED 2026-08-25T16:05-17:15Z under the lock. All three pending
    items merged; NOTHING left pending.
    (1) 2026-08-25-1410 macfuse53-readdir-root-cause -> learnings.md
        "Cluster discipline", new root-cause bullet cross-referencing the
        two existing 2026-08-25 mount bullets.
    (2) 2026-08-25-1545 no-dollar-substitution-inside-formulas (this
        session's own, from probe attempt 1) -> style/lammps.md 1.16 +
        lessons.md L44 + hard-gate grep in lint-lammps-input.sh (verified
        both directions).
    (3) 2026-07-30-0934 promote-mu-scan-loader-to-tool -> TOOL BUILT after
        three pending reviews, at Erik's explicit "implement everything":
        ~/DEVEL/MU-SCAN-LOADER (library + CLI + 18 tests, git-initialised),
        card canon/tools/mu-scan.card.yaml + catalog row. Schema PINNED as
        a (kind, ncol) registry -- which immediately surfaced a NINE-column
        run-14 trace schema unreadable by nih_loaders.py; L43 guard band
        folded in and verified on the real run-06 mu=-2.02 files;
        isotherm equivalence with nih_loaders.py verified on real data.
        Deploys to ~/bin + GitHub push handed to Erik (strict-A analog).
    ALSO UNDER THE LOCK, mid-pass (probe attempt 2 interrupt): L45 +
    style/lammps.md 1.17 + a second lint gate for the dangling-thermo-
    reference class -- filed directly, no inbox round-trip, per the
    fix-the-class rule. Lint verified: fires on reconstructions of both
    probe bugs, passes both fixed inputs.
- in_flight: (none — all handed-over jobs ran, were harvested and analysed)
- notes: |
    DIAGNOSIS (from mount, read-only, before registration): probe died at
    prepare .in line 61 -- `${L}` inside a `$(...)` immediate formula;
    LAMMPS error "Invalid syntax in variable formula (src/variable.cpp:2619)".
    Stage-1 NPT equilibration itself completed cleanly (13 s pair time,
    2048 atoms, 0 dangerous builds). Same pattern at prepare line 94 and
    measure lines 58 + 61 (never reached). Fix: v_L inside formulas.
    FIX APPLIED to Mac .in copies 15:42Z, doc-verified
    (docs.lammps.org/variable.html); thread.md carries the full write-up.
    Canon proposal filed: 2026-08-25-1545-no-dollar-substitution-inside-
    formulas (style rule + two style-walk greps).
- mode_at_close: pilot                    # designer lock RELEASED 2026-08-25T17:15Z — free
- closed: 2026-08-26T17:10Z
- summary: |
    Ni-melting-point-eam taken from a failed 18-s probe to BOTH THREADS AT
    RESULT: Tm(Angelo) = 1425 +- 25 K, Tm(Pezold refit) = 1475 +- 25 K
    (two-phase coexistence, 16000 atoms, both observables concurring; the
    refit melts 50 K above its parent). Along the way: 4 input/protocol
    bugs found and fixed (L44 ${}-in-$(), L45 dangling thermo ref, nph
    dilate-all strain, guarded-settle confinement freezing), canon gained
    L44+L45+2 lint gates+3 merged proposals, the mu-scan loader tool was
    BUILT (MU-SCAN-LOADER, card live), and deliverables (4-slide MPI deck,
    7 OVITO figures w/ cards+provenance) are committed to the project
    tree. in_flight EMPTY at close. Open, not started: bracket refinement
    rungs, seed replicas, optional pure-Baskes cube ladder, thread 03+
    (MEAM etc.) if ever wanted. One proposal pending in the inbox
    (2026-08-25-1740 barostat-dilate + one from this close, if filed).
- handoff_to: null


### session_id: 2026-08-26-1305-notify-email-body
- mode: designer
- scope: |
    Erik's report: "the emails sent do not contain a body, and their
    subject line is too long to be displayed." Root cause is Slurm's own
    --mail-type mail (subject-only boilerplate, no body). Fix the CLASS in
    canon: a body-carrying notification helper sourced by every submit
    script, Slurm's mail cut back to a backstop. Also: report the state of
    the proposals inbox (Erik asked). NO pilot work, NO project trees, NO
    submissions.
- started: 2026-08-26T13:05Z
- last_active: 2026-08-26T16:00Z
- simulation_root: n/a (designer-only session; not asked, not needed)
- machine: M5   # canon/local/.this-machine
- cluster_identity: |
    cmmg resolves to <user>@<host> per canon/local/clusters.local.yaml,
    scratch /cmmc/ptmp/<user>. READ ONLY for this session -- the cluster
    mount is NOT connected here, which is fine: designer work only.
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - LLM-LAMMPS-public/canon/**   (DESIGNER LOCK taken 2026-08-26T13:05Z,
    RELEASED 2026-08-26T16:00Z at close -- the lock is FREE)
  - LLM-LAMMPS-public/ARCHITECTURE.md
- designer_lock: |
    TAKEN 2026-08-26T13:05Z, verified free: 2026-08-25-1536-nimelt-probe-fix
    released it 08-25T17:15Z (its scope_change says so) and
    2026-08-25-1105-sim-status released it 08-25T13:10Z. No other active
    entry claims it. 2026-08-25-1725-nih-two-project-status is active but
    is pilot-mode and holds no lock.
- designer_pass: |
    NOTIFICATION E-MAIL, fixed as a class, 2026-08-26T13:05-13:15Z.
    Diagnosis: Slurm's --mail-type mail carries a SUBJECT ONLY and an
    empty body; the subject is fixed boilerplate leading with
    "Slurm Job_id=..." so at mail-list width the job name is truncated
    away and status + submission directory never arrive. Slurm has no
    body option -- the only fix is for the job to mail itself.
    (1) NEW canon/templates/slurm-notify.sh -- sourced helper. Subject
        "[LMPS] <STATUS> <job name> <job id>" (status front-loaded);
        body = full job name, ABSOLUTE SLURM_SUBMIT_DIR, working dir,
        status + exit code, cluster/partition/ranks/node, start/end/wall,
        stdout+stderr paths and the last 15 lines of each, plus any
        lmps_notify_context KEY VALUE lines (PROJECT, THREAD at minimum).
        Sends STARTED at arm, then exactly one of COMPLETED / FAILED
        (exit code) / TIMEOUT-OR-CANCELLED (Slurm's SIGTERM). No MTA on
        the node -> writes NOTIFY-<jobid>.txt in the submit dir and prints
        loud NOTIFY FALLBACK lines; never kills the job, never silent.
        VERIFIED on the Mac VM against a stub sendmail: all four statuses,
        the stdout/stderr tails, the SIGTERM path, the single-final-mail
        guard, the no-MTA fallback, and errexit save/restore.
    (2) NEW canon/templates/slurm-notify.probe.slurm -- one-off array 0-1
        (task 1 exits 1 on purpose) that inventories the MTA on a compute
        node and exercises both mail paths. ~1 core-minute.
    (3) canon/style/shell.md: new section 6 (the rule) + the section-3
        submit-script skeleton now sources the helper and pre-flights it
        in the existence-check loop. Never `source ... || true`.
    (4) canon/clusters.yaml: sbatch_defaults mail-type BEGIN,END,FAIL ->
        FAIL,TIME_LIMIT (backstop only; the helper owns BEGIN/END), new
        `notify:` block with helper path, source_of_truth, deploy command
        and mta_on_compute_nodes: unverified.
    (5) canon/learnings.md "Submit-script discipline": new bullet.
    (6) ARCHITECTURE.md clusters.yaml sketch refreshed (it still showed
        get-user-env: L, removed 2026-08-02, and the old mail-type).
    (7) canon/local/clusters.local.yaml + local.example: notify_helper.
    PROBE VERDICT 2026-08-26T14:5xZ: TRANSPORT WORKS, the reported bug is
    FIXED. Erik's mailbox has, for job 22731708 on cmmg002: [LMPS] STARTED
    ..._0 and [LMPS] FAILED ..._1 WITH FULL BODIES (status + exit code,
    absolute SUBMITDIR /cmmc/ptmp/<user>/_NOTIFY-PROBE, WORKDIR, cluster/
    partition/ranks/node, start+now+wall, the PROBE and NODE context lines,
    stdout+stderr paths and the tails of both), [LMPS] COMPLETED ..._0, and
    Slurm's own subject-only "Slurm Array Summary ... Failed, Mixed,
    ExitCode [0-1]" -- so the FAIL backstop survives the mail-type cut to
    FAIL,TIME_LIMIT. Subjects fit and lead with the status.
    MTA INVENTORY, recorded in clusters.yaml: `command -v sendmail` finds
    NOTHING on the compute node while /usr/sbin/sendmail IS executable;
    /usr/bin/mail and /usr/bin/mailx also present. The helper's two-step
    sendmail detection is therefore LOAD-BEARING on this cluster, not
    defensive clutter -- a `command -v`-only probe would have silently
    degraded to `mail`. Style rule 6 gained that as an explicit bullet.
    Also recorded: SLURM_CLUSTER_NAME is `cmmc`, not our key `cmmg`, so
    bodies read "cmmc / s.cmmg" (expected; L42 class).
    GATE FULLY PASSED, CONFIRMED BY ERIK 2026-08-26T15:0xZ: ALL FOUR
    expected [LMPS] mails are in the mailbox WITH BODIES -- 2 x STARTED
    (one per array task), 1 x COMPLETED, 1 x FAILED. The two apparent gaps
    in the first paste (a bodiless COMPLETED, a missing STARTED _1) were
    PASTE ARTEFACTS, not drops: concurrent delivery from a single node in
    the same second is clean. No re-probe needed. mta_on_compute_nodes:
    verified stands on evidence, not on an exit code.
    ORIGINAL SUBMISSION RECORD: job 22731708,
    array 0-1 on s.cmmg. SLURM-LEVEL BEHAVIOUR EXACTLY AS DESIGNED --
    22731708_0 COMPLETED 0:0 and 22731708_1 FAILED 1:0, 00:00:05 each
    (the sleep 5), so both the success and the deliberate-failure paths
    executed. STILL OPEN, and the whole point of the probe: whether the
    MAILS ARRIVED. The cluster mount is not connected to this session, so
    the probe's .out (MTA inventory + any NOTIFY FALLBACK lines) cannot be
    read from here; asked Erik to paste it and to say how many [LMPS]
    mails landed. mta_on_compute_nodes stays `unverified` in clusters.yaml
    until that verdict is in.
    NOT DONE, deliberately: existing submit scripts under SIMULATIONS/ are
    pilot property (2026-08-25-1725 owns those trees) and queued jobs were
    not touched. The rule governs scripts written from here on.
- second_pass: |
    MOUNT CANON + REPO SCRUB, 2026-08-26T15:1x-15:3xZ, same designer lock.
    (1) INBOX ITEM 2026-08-25-2010 (urgent) MERGED. The "Root cause of the
        empty-listing states" bullet in learnings.md keeps its diagnosis but
        its mitigation list is REPLACED by the measured state: the bug is NOT
        macFUSE-5.3-specific (reproduced twice on 5.2.0), a downgrade is NOT
        a fix, `-o dir_cache=no` is a softener not a cure (`ls -la` exits 0
        with zero entries -- the empty answer is generated fresh, not
        cached), and the WORKING fix is sshfs built from libfuse/sshfs
        PR #379, installed as /opt/homebrew/bin/sshfs-pr379 with
        dotfiles/shell/cluster-mounts.zsh preferring it. Plus the auto_cache
        content flavor and the `pgrep -fl sshfs` diagnostic (the mount table
        can never confirm mount options). CONFIRMING EVIDENCE the proposal
        could not have had: Erik reports 2026-08-26 that his cluster mounts
        have run WITHOUT PROBLEMS since the PR #379 build was deployed --
        recorded in the bullet and in clusters.yaml.
    (2) clusters.yaml mount block gained `sshfs_binary_preferred` and
        `dir_cache=no`: the BINARY matters as much as the options, which the
        old option-only block did not say.
    (3) IDENTITY SCRUB of the public repo: 24 real values across 6 tracked
        files (SESSIONS.md 14, preferences.md 3, ARCHITECTURE.md 2,
        lessons.md 2, session-startup.md 2, proposals-inbox.md 1) replaced
        by <CLUSTER_USER> / <CLUSTER_HOST> / <MAC_USER>. New placeholder
        <GITHUB_USER> for the sibling tool repos -- a GitHub handle is NOT
        the cluster user and must not be scrubbed to the same token;
        documented in README's table, real value in canon/local/local.yaml.
        lint-no-identity now CLEAN; lint-canon-paths clean; all YAML parses.
    (4) CLASS FIX, the point of the whole scrub: startup step 0(b2) tells
        every session to ECHO the resolved cluster identity, and sessions
        have been transcribing that echo into their SESSIONS.md entries --
        so the leak REGENERATES every session and a one-time cleanup would
        have been undone by tomorrow. session-startup.md now says the echo
        is CHAT ONLY, the entry records the placeholder form, and run paths
        in in_flight/notes are written in placeholder form too, with
        lint-no-identity named as the pre-push guard.
    PUSHED 2026-08-26T15:1xZ. Public repo: rebased onto the remote's
    1ee2418 (Erik's own PR #1, adds only .github/workflows/repo-vitals.yml
    -- disjoint file sets, no conflicts) and pushed as 1ee2418..b2ca851.
    Locks cleared, `git gc` ran: 0 loose objects, 344 in-pack. Post-merge
    lint CLEAN.
    KEY FINDING, checked commit by commit over the whole published history:
    the real cluster identity was NEVER PUSHED. The only identity token in
    any published commit is the GitHub handle (3 occurrences, in
    github.com/<GITHUB_USER>/... URLs and the action ref), which is public
    by construction. Today's scrub was PREVENTIVE -- the 24 values had
    accumulated in tracked files that had not yet been pushed. No history
    rewrite needed, no force-push anywhere. Recorded because the opposite
    conclusion ("we leaked, rewrite history") was the plausible one and is
    expensive and irreversible.
    LINTER AMENDED: `.github/` joins `canon/local/` in the skip list. The
    incoming workflow carries Erik's author byline and institutional
    address, and deploy/rollout.sh REGENERATES those files, so a hand-scrub
    would be undone at the next rollout. The linter guards CLUSTER identity,
    not authorship of a public repo. Verified by staging the incoming file
    and re-running the lint.
    TWO BLEMISHES, left alone deliberately: (a) two commits carry the same
    message -- 5164207 is the real one, b2ca851 is a 5-line SESSIONS.md
    follow-up written by a LIVE session between Erik's two `git add -A`
    runs, so the message is misleading but the content is right; rewriting
    pushed history to fix a commit message is not worth it. (b) 29
    tmp_obj_* files survive `git gc` because gc.pruneExpire defaults to two
    weeks and they are six days old; `git gc --prune=now` clears them, or
    they expire on 2026-09-03.
    OVERLAY PUSH FAILED, my error: I handed `git push origin master` for
    BOTH repos, but the overlay's branch is `main`. Class fix in README
    (section 3): the two repos do not share a default branch name; check
    `git status -sb` or push `HEAD`, which is branch-name agnostic.
- third_pass: |
    INBOX CLEARED 2026-08-26T15:3x-15:5xZ, same designer lock. NOTHING PENDING.
    (1) 2026-08-25-2015 wall-fixes-need-nonperiodic-dims -> style/lammps.md
        1.13, APPENDED to the existing L41 vacuum-gap paragraph rather than
        given its own number: the wall is what the L41 rule tells you to add,
        and this rule says the boundary change must ride along in the SAME
        edit -- they are one instruction, not two. LINT GATE added to
        lint-lammps-input.sh as the proposal specified (parse the last
        `boundary` line, map each wall face keyword to its axis, fail on p).
        VERIFIED BOTH DIRECTIONS on real files: fires on a reconstruction of
        the killed probe (p p p + wall zhi), passes the two REAL fixed inputs
        (hydride-cycle threads 03/04), a no-wall input, and an x-axis wall on
        a non-periodic x. Regression: the older L1/L44/L45 gates still fire,
        and 12 recent .in files across SIMULATIONS still lint clean.
    (2) 2026-08-25-1740 barostat-dilate-defaults-to-all -> style/lammps.md
        NEW 1.18. Numbered 1.18, placed physically after 1.13 (the file is
        already non-monotonic; stable numbers beat file order because other
        canon cites them). Semantics half = rule body with `dilate HOT` and
        the fix_nh.html substrate example; "quantify held-fixed" half = its
        corollary, sharpened with WHY the obvious check fails (an fcc
        percentage is invariant under uniform strain -- it reports a perfect
        crystal at 8 % misfit). Deliberately NOT linted: `dilate all` is the
        DEFAULT, so there is no token to grep; absence of `dilate` is a smell
        only, and a gate that cries on every npt gets switched off.
    (3) 2026-08-25-2130 desired-sign-is-not-a-passed-gate -> SPLIT.
        Probe-discipline half -> learnings.md Workflow rules, "A gate is a
        mechanism, not a sign and not a marker". Confinement half ->
        style/lammps.md NEW 1.19, "A held rigid crystal template ORDERS the
        liquid beside it" (~1 nm per-wall reach, <2-3 nm film, PBC counts as
        the second face, three escapes). The two cross-reference each other.
        ADDED beyond the proposal: "green mechanical gates say the job RAN,
        not that it measured anything -- every probe needs at least one gate
        a physically wrong run would FAIL." That is the clause that
        generalises past melting points. KEPT: the proposing session's
        first-person framing that the design error was its own; the reusable
        half is the question it did not ask, and it reads better unsanded.
    COMMON THREAD, recorded because it is the reason all three existed: each
    is a PRECONDITION no syntax check catches -- a wall needs a non-periodic
    axis, a barostat needs an explicit dilate group, a probe gate needs a
    mechanism. Style 1.9 already names the class; these are three instances,
    and only the first is machine-checkable.
- inbox_readout: |
    Reported to Erik, NOT merged (he asked what is in the inbox, not for a
    merge pass). FOUR pending: 2026-08-25-1740 barostat-dilate-defaults-to-
    all (style/lammps.md); 2026-08-25-2010 empty-listing-mitigations-
    superseded (learnings.md, priority URGENT); 2026-08-25-2015 wall-fixes-
    need-nonperiodic-dims (style 1.13 + a lintable gate, filed in the bare
    "## <id>" heading form); 2026-08-25-2130 desired-sign-is-not-a-passed-
    gate (learnings.md probe discipline). Everything else is merged.
- in_flight: (none of this session's own)
- notes: |
    Cloud Cowork session on M5's connected folders (DEVEL, SIMULATIONS).
    Cluster mount NOT connected -- designer-only work, environment gate (c)
    satisfied for that mode.
    STEP 1b DISCHARGED 2026-08-26T14:2xZ against Erik's
    `sacct -X -S 2026-08-25` paste. NEITHER live entry needed editing by
    this session:
    * 2026-08-25-1536-nimelt-probe-fix reconciled and HARVESTED its own
      21:55Z hand-over while this session was working (its entry now
      carries Tm(Angelo EAM) = 1425 +- 25 K from the 7-rung large-box
      ladder 22731059_[0-6] and the NGUARD=0 cube probe 22731060, all
      COMPLETED 0:0). Loop closed by its owner, not by me.
    * 2026-08-25-1725 self-reconciled at 13:2xZ. The paste moves it on two
      points, recorded here for its owner rather than written into a live
      entry: 22730595_1 has since COMPLETED (03:18:44), so mu-bracket is
      2 of 10 done with _2.._9 RUNNING at 04:25 -- already ~1.6x task 0's
      02:43:55, cheapest-first as designed and comfortable against the
      24 h cap; and 22730592 (biaxial-misfit production) plus both
      re-probe arrays 22730593/22730594 are STILL PENDING after ~20 h.
    NOTE for whoever owns the nimelt scope: tasks _0 and _1 of array
    22731636 (nimelt-t02-prepare) do not appear in that sacct listing at
    all, while _2 shows COMPLETED and _3 RUNNING. An array whose first two
    tasks are simply absent is not a normal state and is worth one look.
    TIMESTAMP NOTE: last_index_updated read 2026-08-26T15:45Z when this
    session started, which is CEST written as Z (real UTC then was 13:4xZ).
    Flagged, not rewritten -- it belongs to a live session's entry.
    lint-no-identity FAILS on the repo as found, in files this session did
    NOT touch (SESSIONS.md, lessons.md, preferences.md, proposals-inbox.md,
    session-startup.md). Zero leaks in anything written here. Flagged to
    Erik: the public repo cannot be pushed until those are scrubbed.
- closed: 2026-08-26T16:00Z
- designer_lock_released: 2026-08-26T16:00Z   # verified free for the next session
- summary: |
    Fixed the job-notification class: Slurm's own mail is subject-only with an
    empty body, so every submit script now sources canon/templates/
    slurm-notify.sh and mails itself -- short status-first subject, body with
    the full job name, the ABSOLUTE submission directory, exit code, resources,
    wall time and the stdout/stderr tails. Verified end to end on cmmg
    (job 22731708): all four mails delivered with bodies, plus Slurm's
    subject-only FAIL backstop. Corrected the mount canon (the merged
    mitigations were measured false the same evening; the PR #379 sshfs build
    is the fix, and Erik confirms trouble-free mounts since). Scrubbed 24
    identity values out of the public repo and CLOSED THE SOURCE -- startup
    step 0(b2)'s identity echo is now chat-only, so the leak cannot
    regenerate; established, commit by commit, that the real cluster identity
    was NEVER pushed, so no history rewrite was needed. Cleared the proposals
    inbox: nothing pending, for the first time this session.
    in_flight: EMPTY -- no jobs owned, nothing handed over.
    ONE OPEN ITEM, not a loop: six files of canon changes from the third
    (inbox) pass are COMMITTED NOWHERE. Erik has the commands; a successor
    should check `git status` in the repo before assuming this canon is
    published.
- handoff_to: null


### session_id: 2026-08-25-1105-sim-status
- mode: pilot
- scope: |
    Started as a READ-ONLY status readout ("status about our last
    simulations"). PROMOTED at Erik's choice (AskUserQuestion) to harvest +
    analysis of ni-h-at-dislocs-eam-meam thread 01 run 01 (d90 relaxed
    binding). No cluster writes, no submissions.
- started: 2026-08-25T11:05Z
- last_active: 2026-08-25T13:10Z
- simulation_root: ~/Desktop/SIMULATIONS   # connected mid-session by Erik,
    together with cluster-mounts/cmmg (mount healthy throughout; one L15
    stale-view episode ~12:59-13:14 CEST diagnosed live, remount + folder
    re-connect fixed it)
- machine: M5
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry, plus the step-1b
    reconciliation of 2026-08-24-1531's in_flight, per session-startup 1b)
  - SIMULATIONS/Ni-H-AT-DISLOCS-EAM-MEAM/01_H-BINDING-MAP-D90-0K/**  (added 11:20Z)
- scope_change: |
    PROMOTED read-only -> write ownership of thread 01 harvest+analysis,
    2026-08-25T11:20Z, Erik's pick from the offered next steps.
    PROMOTED pilot -> designer+pilot 2026-08-25T~12:30Z at Erik's explicit
    request ("as designer, can you implement the items in your inbox?").
    DESIGNER LOCK TAKEN, verified free: 2026-08-24-0753 released it
    17:35Z on 08-24 (its designer_addendum says so explicitly); the only
    other claim, 2026-08-20-1145-cluster-status-sweep, is 5 days stale and
    was already overridden by the 08-24 pass. Designer area: merge the
    proposals inbox + the duplicate-L41 repair found while merging; nothing
    else touched under the lock. LOCK RELEASED 2026-08-25T~13:10Z after the
    pass (mode back to pilot for any further project work).
    TAKEOVER, ON THE RECORD: 2026-08-24-1531-nih-at-dislocs-status (1 day
    old, conversation ended, in_flight discharged by this session's
    reconciliation) declares the same tree. Only its thread-01 scope is
    taken; threads 04/05 (d0/d30) are NOT taken. Erik told, in-turn.
- work_done: |
    RUN 01 HARVESTED AND ANALYSED, 2026-08-25T11:10-11:35Z.
    Harvest (curated, via device_bash cp mount -> SIMULATIONS, verified by
    byte counts): production .dat, subset table, .in, selector, site feeds
    into 01_RELAXED-BINDING-SUBSET/relaxedbind-d90-Pezold-EAM-0K/. Logs and
    slurm .out/.err stay on the cluster; gate numbers recorded in thread.md.
    ANALYSIS (script + per-site table + fig5 in 01_RELAXED-BINDING-SUBSET/):
      * Gate 1 restore 0.0000 meV on all 372; rigid cross-check to run-00
        map <= 1e-6 eV; 360/372 sites convergence-assured (all 12
        non-assured are radial-ladder sites whose H migrated or hit the
        20000-iteration cap; worst gap 439.8 meV = site 373499 falling into
        373550's basin).
      * HEADLINE: relaxation makes the deepest oct trap SHALLOWER,
        -0.1464 -> -0.1311 eV, so mu_core = -2.4061 eV (was "-2.421 upper
        bound" -- premise measured false). Enrichment 159x at 300 K.
        Thread-02 grid -2.471..-2.291 CONFIRMED, no re-centring.
      * FUNNELING: 43 deep sites -> ~8 relaxed basins (14 oct sites share
        E_ins_tight -2.242994 eV to 1 ueV across r = 3.8-12.3 A).
      * Tet per-family trap deepens -0.1331 -> -0.1500 eV but sits +0.256 eV
        above bulk oct: no dilute-limit tet trapping. Relaxed bulk tet-oct
        splitting +0.4066 eV (rigid +0.5487).
      * Reach: |E_seg| < 10 meV beyond r = 60 A; +-70 A MC zone vindicated.
    thread.md updated: run-01 RESULTS section, run status COMPLETE, the
    section-5 "upper bound" claim superseded on the record.
- designer_pass: |
    INBOX MERGED 2026-08-25T~12:30-12:50Z under the lock (see scope_change).
    (1) proposal 2026-08-25-1200-mount-background-traversers -> MERGED into
        learnings.md "Cluster discipline" as "Background traversers on the
        mount", updated with the post-filing evidence (VM identity confirmed,
        ls-vs-exact-path-stat discriminant verified live, GUI route for the
        Time Machine exclusion).
    (2) proposal 2026-07-30-0934-promote-mu-scan-loader-to-tool -> still
        PENDING, third consecutive review, same verdict: the tool must exist
        (own repo, deployed) before a card can land; needs its own dev
        session starting from nih_loaders.py with the ave/trace schema
        pinned. Erik told in-turn; now the only pending inbox item.
    (3) SEVEN NON-SCHEMA ENTRIES FOUND AND MERGED. The pass first
        (wrongly) declared the two 08-24 filings missing: a `^proposal_id:`
        grep misses entries filed as bare `## <id>` headings, and SEVEN such
        entries existed -- five from 2026-08-05 (skipped by two designer
        passes) and the two from 08-24. Corrected in the same pass; a new
        inbox Conventions bullet mandates sweeping both patterns and
        reconciling SESSIONS.md "filed as proposal" claims before declaring
        the inbox empty. Merged: 2026-08-24-0930 + 2026-08-05-1105 folded
        into ONE learnings.md Process rule ("Scalar-reducing helpers name
        their convergence filter; a verdict for question A is not a quality
        label for question B" -- the naive always-filter-on-converged form
        would have repeated the 08-05 ln-x mistake); 2026-08-24-1558 ->
        learnings.md Process ("A fix applied on one side lands on both
        sides", incl. the resume-after-failure cmp clause);
        2026-08-05-1100 -> lessons.md L43 (guard-band the block averages);
        2026-08-05-1110 -> preferences.md Plot defaults (widest interval
        with no converged state); 2026-08-05-1115 -> style/lammps.md 1.15
        (derived diagnostic columns must earn their place);
        2026-08-05-1210 -> learnings.md Cluster discipline (health-check a
        mount at the depth the work needs). mu_at_half CODE not changed --
        that fix belongs to the next ni-h-phase-diagram scope holder.
    (4) HOUSEKEEPING under the lock: lessons.md carried TWO lessons numbered
        L41 (vacuum-gap, 08-20; hostname/placeholders, 08-24). The 08-24
        hostname lesson is renumbered L42 with a renumbering note; its
        citations in preferences.md and session-startup.md updated. Older
        SESSIONS.md entries that say "gains L41" are left as historical
        record.
- in_flight: (none)
- notes: |
    designer lock NOT taken. Mount cluster-mounts/cmmg is HEALTHY this
    session: listings and exact-path reads both work (d90 run-01 output
    dir listed, .out staged and read). Session also debugged the Mac
    sshfs mount infrastructure earlier in the conversation (outside
    framework scope; symptom: mounts silently disappear during work).
    STEP-1b RECONCILIATION done from run outputs on the mount, then
    CLOSED by Erik's sacct paste (2026-08-25T~11:10Z,
    `sacct -X -S 2026-08-24`):
      22719302 NiH-HBIND-RELAX-D90-EAM      COMPLETED 01:16:47  0:0
        -> the d90 thread-01 run-01 production; job ID now recorded.
      22719558 NiH-BINDMAP-D0-EAM-PROBE     COMPLETED 00:09:55  0:0
      22719559 NiH-BINDMAP-D30-EAM-PROBE    COMPLETED 00:09:01  0:0
        -> Erik submitted the d0/d30 run-00 probes 2026-08-24 evening.
        GATES NOT YET CHECKED (PROBE DONE / ALL PHASES COMPLETE /
        site counts / L26 cost). NEXT ACTION for whoever takes threads
        04/05: check gates, then hand over the two production sbatches.
      22719499 nimelt-t01-prepare-probe     FAILED 00:00:18  1:0
        -> OPEN LOOP, ni-melting-point-eam (scope of 2026-08-24-0753):
        probe died in 18 s, undiagnosed. Flagged to Erik in-turn; not
        this session's scope.
    STALE active entries, re-flagged: 2026-08-05-1425-mcsites-
    presentation, 2026-08-03-1401-nih-at-dislocs-design,
    2026-08-02-1647-ingest-eam-dislocs-ni-cu, 2026-08-20-1145-cluster-
    status-sweep.
- closed: 2026-08-26T15:20Z
- summary: |
    Two-day session, three arcs, all closed. (1) PILOT: d90 thread-01 run-01
    production (job 22719302) reconciled, harvested, analysed -- relaxed
    trap SHALLOWER than rigid, mu_core = -2.4061 eV, thread-02 grid
    confirmed, funneling + 12 non-assured radial sites documented;
    thread.md + artefacts in 01_RELAXED-BINDING-SUBSET/. (2) DESIGNER
    (lock taken and released same day): inbox merged incl. seven
    non-schema entries two passes had missed; duplicate L41 -> L42.
    (3) MOUNT INFRASTRUCTURE (with Erik hands-on): empty-listing bug
    root-caused to sshfs exhausted-handle readdir (NOT macFUSE-5.3-only,
    NOT fixed by downgrade or dir_cache=no); fixed by locally built
    sshfs-pr379 (snapshot readdir) + auto_cache; two-master ssh config
    (per-cluster sshfs ControlMaster, FQDN aliases); Time Machine
    exclusion; all in dotfiles git. Erik confirms mount stable 2026-08-26.
    OPEN FOR A DESIGNER: urgent inbox proposal 2026-08-25-2010 (supersede
    the morning-state empty-listing mitigations in learnings) still
    pending -- until merged, canon misleads sessions into remount/downgrade
    advice. in_flight empty at close.
- handoff_to: null


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
