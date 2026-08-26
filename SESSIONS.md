# LLM-LMPS - Active sessions dashboard

Single source of truth for "who is working on what right now." Each
LLM-LMPS Cowork session self-registers on startup, updates
`last_active` on major actions, and migrates from `active` to
`recently_closed` on wrap-up.

See ARCHITECTURE.md §17 (Concurrency model) for the rules and
`canon/session-startup.md` for the startup ritual.

last_index_updated: 2026-08-26T14:40Z   # real UTC

---

## active

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
- last_active: 2026-08-26T14:40Z
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
  - SIMULATIONS/ACE-Ni-Nb_POTPROPS/**   (new tree, created by this session)
- designer_lock: |
    NOT taken and not needed -- pilot mode. The lock is held by
    2026-08-26-1305-notify-email-body (taken 13:05Z, active today).
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
- last_active: 2026-08-26T15:30Z
- simulation_root: n/a (designer-only session; not asked, not needed)
- machine: M5   # canon/local/.this-machine
- cluster_identity: |
    cmmg resolves to <user>@<host> per canon/local/clusters.local.yaml,
    scratch /cmmc/ptmp/<user>. READ ONLY for this session -- the cluster
    mount is NOT connected here, which is fine: designer work only.
- owns_writes_to:
  - LLM-LAMMPS-public/SESSIONS.md (this entry only)
  - LLM-LAMMPS-public/canon/**   (DESIGNER LOCK taken 2026-08-26T13:05Z)
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
    NOT COMMITTED, deliberately -- see notes.
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

### session_id: 2026-08-25-1725-nih-two-project-status
- mode: pilot
- scope: |
    Status readout of ni-h-at-dislocs-eam-meam and ni-h-hydride-cycle-eam
    at Erik's request. Read-only on both trees for the readout; Erik has
    said he wants to CONTINUE WORK afterwards -- a write scope will be
    added to this entry once he picks the thread. No submissions yet.
- started: 2026-08-25T17:25Z
- last_active: 2026-08-26T15:45Z
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

### session_id: 2026-08-25-1536-nimelt-probe-fix
- mode: designer+pilot     # promoted from pilot 2026-08-25T16:05Z, Erik's request
- scope: |
    Diagnose and fix the failed ni-melting-point-eam prepare probe
    (job 22719499, FAILED 18 s, flagged as OPEN LOOP by
    2026-08-25-1105-sim-status). Fix the .in files on the Mac, hand
    over staging + re-probe (strict-A). No production submits until
    probes pass.
- started: 2026-08-25T15:36Z
- last_active: 2026-08-26T12:15Z
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
- in_flight: |
    PROBE ATTEMPT 2 (Erik's resubmit after the v_L fix) FAILED at stage-3
    write_data: dangling c_TEMP_HOT in thermo_style after uncompute (L45,
    new lesson). Fixed on the Mac 16:40Z; canon got L45 + style 1.17 + a
    lint gate in the same turn (lock held). RE-STAGE + RESUBMIT handed to
    ATTEMPT 3 PASSED ALL GATES incl. the quantitative layer-spacing check
    (1.80 vs 1.794 A; interior fcc 80.5%; hot half 0.0%). Prepare input
    VALIDATED (job IDs from Erik's sacct paste 18:3xZ: 22728707 FAILED
    thermo / 22728717 strained / 22728719 PASS). MEASURE PROBE handed
    ~18:05Z but NEVER SUBMITTED per that same sacct + empty squeue --
    loop closed and SUBMITTED; measure probe ran 2026-08-25 evening:
    mechanical gates PASS, physics gate exposed a PROTOCOL FLAW (3500 K
    liquid's stored heat -> 1682 K transient -> thin slab melts at every
    rung). Erik decided (brainstormed in-turn): THOT -> 2200 K + guarded
    stage 0 in measure (liquid settles at each rung's TMEAS against the
    held crystal, then release). Implemented + linted; RE-PROBE PAIR
    handed 19:20Z, run by Erik: BOTH PASS -- guard confines the
    overshoot to the liquid (+125 K, settled by 0.4 ps), slab SURVIVES,
    cell FREEZES at 1450 K (etotal falling, fcc growing into the liquid)
    = consistent with Tm > 1450. Protocol validated at probe scale.
    PRODUCTION PREPARE run + GATE-CHECKED 20:30Z: both Angelo cells VALID
    (interiors at a0/2 within 0.4%, hot halves molten, COLD 1086/8106).
    CUBE LADDER RAN CLEAN AND WAS HARVESTED+ANALYSED 21:0x-21:35Z:
    VERDICT INVALID -- the guarded settle's rigid template epitaxially
    froze the cube's 15 A liquid film at EVERY rung (validated
    transverse-order metric; no melting even at 1600 K). Own design
    error, on the record; proposal 2026-08-25-2130 filed. Options put to
    Erik: BOTH ARMS chosen (AskUserQuestion). NGUARD made explicit in
    both measure submits; invalid cube harvest quarantined on the Mac.
    BOTH ARMS RAN CLEAN (2026-08-26). LARGE-BOX LADDER VALID:
    **Tm(Angelo EAM) = 1425 +- 25 K** (1400 freezes / 1450 melts; both
    observables concur at all 7 rungs; finals confirm via transverse
    order). ~28-103 K below the Cherne anchor 1478+-25 -- intervals
    nearly touch; candidate causes recorded in thread.md. Cube no-guard
    probe: slab survives -- pure Baskes viable, cube ladder now optional.
    Harvested (13 rung dirs on Mac, 6 files each, verified by count).
    OVITO panels (large-box start / 1400 K frozen / 1500 K molten) rendered
    via ovzm, and the one-slide MPI-SusMat deck built and DELIVERED --
    committed to the thread dir with deck spec, viz cards and provenance.
    Thread status: RESULT. Open next steps (Erik's call): split the
    1400-1450 bracket, seed replicas, optional cube ladder.
    THREAD 02 OPENED at Erik's request ("let's start with Pezold"),
    2026-08-26T10:45Z: 02_MELTING-POINT-PEZOLD-EAM, inputs byte-identical
    copies of thread 01's validated pair, submits re-labeled t02 +
    Pezold potential in the probes. thread.md written (lint-handoff
    clean, both .in lint clean). PROBE PAIR handed over (strict-A,
    afterok-chained, measure probe NGUARD=0); production waits on probe
    gates + L26 recalibration. PROBES BOTH PASSED (2026-08-26 ~11:30Z):
    a0 = 3.5784, layer spacing clean, slab survives, 135 steps/s (cutoff
    costs ~nothing). PRODUCTION PREPARE handed over (--array=2-3);
    CELLS VALIDATED 12:15Z (layer spacing 1.789 vs a0/2 within 0.2%,
    halves correct; one stale-sshfs EPERM incident bridged via Erik's
    shell + fixed by remount). LARGE-BOX LADDER handed over
    (--array=0-6, Pezold, NGUARD=2000, ~30 min/rung).
    sbatch submit-measure-melting_Ni-melting.probe.slurm (T=1450 K, 2 ps,
    consumes the prepare probe's cell; also the L26 walltime source for
    measure production). History: attempt 2 passed mechanical gates but the
    quantitative snapshot check found the cold half at 8% uniaxial strain
    (nph remap defaults to dilate all). Fixed: fix MELT HOT nph ... dilate
    HOT; FREEZE removed; proposal 2026-08-25-1740 filed. RE-STAGE +
    RESUBMIT handed to Erik ~17:45Z (strict-A): rsync prepare .in, then
    `sbatch submit-prepare-two-phase_Ni-melting.probe.slurm`. A resuming
    session must check probe/prepare-Angelo-EAM-8x8x8/ gates (clean exit,
    ALL PHASES COMPLETE, Performance lines, cold half crystalline,
    two-phase data written) before the measure probe or any production.
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
