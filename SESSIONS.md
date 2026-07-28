# LLM-LMPS - Active sessions dashboard

Single source of truth for "who is working on what right now." Each
LLM-LMPS Cowork session self-registers on startup, updates
`last_active` on major actions, and migrates from `active` to
`recently_closed` on wrap-up.

See ARCHITECTURE.md §17 (Concurrency model) for the rules and
`canon/session-startup.md` for the startup ritual.

last_index_updated: 2026-07-28T00:00Z

---

## active

_None. No session holds the designer lock; no pilot scope is owned._

---

## recently_closed

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
