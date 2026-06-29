# LLM-LMPS - Active sessions dashboard

Single source of truth for "who is working on what right now." Each
LLM-LMPS Cowork session self-registers on startup, updates
`last_active` on major actions, and migrates from `active` to
`recently_closed` on wrap-up.

See ARCHITECTURE.md §17 (Concurrency model) for the rules and
`canon/session-startup.md` for the startup ritual.

last_index_updated: 2026-06-09T18:41Z

---

## active

### session_id: 2026-06-09-1841-ingest-dislocs-ni-cu
- mode: designer+pilot
- scope: |
    PILOT: ingest the existing (not-yet-ingested) sim-project
    ~/Desktop/SIMULATIONS/EAM-DISLOCS-Ni-Cu (Ni-Cu dislocation/segregation
    study; Fischer-2018 EAM + Mishin04 Ni; oriented dislocation cells D0/D30/
    D60/D90, SGC + VC-SGC Monte Carlo, ISLANDGRAIN Sigma9 GBs, REFERENCE).
    Stamp project.md in place per ARCHITECTURE ingestion rule (nothing moves,
    nothing renames). DESIGNER: took the (free) lock to formalize a reusable
    ingestion procedure in canon if the existing ARCHITECTURE-only rule proves
    thin.
- started: 2026-06-09T18:41Z
- last_active: 2026-06-09T18:41Z
- owns_writes_to:
  - ~/Desktop/SIMULATIONS/EAM-DISLOCS-Ni-Cu/** (Mac archive — project.md stamp + thread stubs)
  - ~/cluster-mounts/cmmg/DISLOCS-Ni-Cu/** (cluster, via mount)
  - canon/** (designer lock held — ingestion-procedure canon, only if needed)
- in_flight: <empty>
- notes: |
    Designer lock was FREE at startup (released by 2026-06-01-1525 at its
    16:05Z demotion). No pilot-scope collision: the lone other active session
    owns the Ni-A0-CIJ Thread-03 paths, disjoint from this project.
    Confirmed not-yet-ingested: no project.md at project root. howto_dislocs-
    Ni-Cu.txt present (Erik's own notes) — primary ingestion source.

### session_id: 2026-06-01-1525-Ni-thread03
- mode: pilot                             # DEMOTED from designer+pilot at 2026-06-01T16:05Z; designer lock RELEASED (framework backlog empty, only Thread-03 job-wait remains). Lock now free.
- scope: |
    Ni-A0-CIJ-EAM-MEAM / 03_LATTICE-CONSTANT-AT-300K (pilot: move the runs
    forward) + LLM-LMPS framework canon (designer: the two Thread-03 TODOs).
- started: 2026-06-01T15:25Z
- last_active: 2026-06-01T16:05Z
- owns_writes_to:
  - ~/Desktop/SIMULATIONS/Ni-A0-CIJ-EAM-MEAM/03_LATTICE-CONSTANT-AT-300K/** (Mac archive)
  - /cmmc/ptmp/<CLUSTER_USER>/Ni-A0-CIJ-EAM-MEAM/03_LATTICE-CONSTANT-AT-300K/** (cluster, via mount)
  # framework-canon line (.lmps/*, ARCHITECTURE.md, auto-memory) PRUNED
  # 2026-06-01T15:57Z by session 2026-06-01-1557-ARCHITECTURE-designer:
  # stale after this session's 16:05Z demotion to pilot + lock release.
  # Canon now owned by the designer session.
- in_flight: |
    PRODUCTION SUBMITTED ~15:40Z: 20317577 (01 EAM-NPT, ~34min/1:30),
    20317578 (02 MEAM-NPT, ~3.6h/6:00), 20317579 (04 MEAM-scan array 0-4,
    ~2.6h/task / 4:00). Long pole 02 ~4h.
    (Probes PASSED first: 20317574/75/76; $(step) fix confirmed; walltime
    rescaled per L26 for the probe-ntasks=2 vs prod-ntasks=1 mismatch.)
    NEXT when done: curated mirror cluster->Mac, then 4 analysis scripts
    + FFT check, then close thread.
    DESIGNER edits this session (Erik-requested): (1) §17.5 + concurrency
    memory tightened — role tag on EVERY response, no exceptions; (2) new
    "fix the class, not the instance" rule -> learnings.md Process +
    feedback_fix_the_class_not_instance.md + MEMORY.md, lint already in
    style/lammps.md §1.12; (3) created .lmps/templates/probe-input.in.skel
    from the verified probes (closes that designer TODO).
    DESIGNER TODO BACKLOG NOW EMPTY (both prior pending items done). Only
    remaining work tied to this session is the Thread-03 pilot tail:
    await jobs -> curated mirror -> 4 analysis scripts + FFT -> close.
- notes: |
    Designer lock taken (active was (none) at startup). UNBLOCKED:
    got cluster ground truth via Erik's real cmmg shell — all run dirs +
    staged probes exist, run 03 data real (sacct 20285982 all COMPLETED).
    Mac mount re-verified healthy post-reboot.
    Found + fixed a 4th, previously-unrecorded failed probe attempt (jobs
    20286009/10/11): root cause `$(step:%d)` in the fix-print line (%d
    invalid in $(...), values are doubles). Fixed `$(step:%d)`->`$(step)`
    in the six 01/02/04 .in + .probe.in files. Codified as L31 (corrects
    L30). thread.md updated.
    NEXT: Erik resubmits the 3 probes -> verify clean -> rescale prod
    --time per L26 -> submit production.
    Designer TODOs still pending: probe-input.in.skel (build from a
    VERIFIED-clean probe, so after probes pass); L30+L31 lint into
    style/lammps.md §1.

---

## recently_closed

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
