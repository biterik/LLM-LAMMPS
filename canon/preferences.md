# LLM-LMPS — Erik's scientific preferences

Pilot-maintained. Pilot proposes new entries; Erik confirms.

## Units & conventions

- LAMMPS default: `units metal` (Å, eV, ps, bar) for all metallic systems.
- Boundary: `p p p` unless the geometry demands otherwise (free surfaces,
  dislocations, slabs).
- `atom_style atomic` for pure metals; `charge` only when needed.

## Concentration convention (Ni-H and interstitial systems)

- **x = N_H/N_Ni is the headline concentration.** For Ni-H (and any
  interstitial system), concentration in outputs, analyses, plots and
  prose is x = N_species / N_host (e.g. H per Ni). The site-catalogue
  occupancy c = N/M of `fix mc/sites` is tool-dependent (the dynamic
  catalogue's M changes with the clearance window and lattice
  expansion — 1/3 on the NiH shelf when tet sites are counted) and
  appears only as a diagnostic column, never as the reported
  concentration. (Set 2026-07-29, Erik's words: "concentration please
  as number of H / number of Ni ... keep this convention in the
  future".)

## Stress in a cell containing vacuum (slabs, films, surfaces, wires)

- **Correct every stress to the MATERIAL volume.** LAMMPS reports the virial
  divided by the WHOLE CELL volume, so any cell with vacuum in it reports a
  diluted stress. Quote the corrected value, state the correction factor
  f = V_cell / V_material where it is used, and keep the uncorrected number
  alongside it in the summary file so the two can never be confused. Same class
  of error as a concentration with the wrong denominator, above.
- **The check is nearly free and it validates itself.** For a cubic metal the
  corrected biaxial modulus must match C11 + C12 - 2 C12^2/C11, so getting the
  correction right also confirms the slab thickness, the strain definition and
  the sign convention in one comparison. A thread that skips it has no such
  check. (2026-08-27, ni-h-hydride-cycle-eam thread 02: a 120 a0 slab in a
  477.48 A box, f = lz_cell/lz_slab = 1.1250. Uncorrected the biaxial modulus
  reads 168.2 GPa and misses the elastic-constant combination ~202 GPa by 17 %,
  which looks like a physics problem; corrected it is 196.4 GPa. Every stress in
  the thread was understated by 11 %.)
- **Yield is the stress maximum, not a tolerance** -- see L47.

## Potentials — go-to picks

(stubs; fill as we observe Erik's defaults across projects)

- **Ni**: EAM Tehranchi (`ni_h_rcut4.90_rcut2.eam.alloy`) and MEAM Ko
  (`NiH_KoShimLee.meam` + `NiH_KoShimLee_library.meam`) — both with H
  capability. (Set 2026-05-29 from project `ni-static-EAM-MEAM`.)

## Cell sizing

- **Bigger boxes for stress-sensitive work.** Default for elastic-
  constant and other small-strain stress measurements: cell side ≥
  **5× max pair cutoff**, not just the bare PBC minimum of 2× cutoff.
  Linear-doubling above the minimum is cheap and gives clean stress
  signals. See lesson L22. (Set 2026-05-29.)

## Computation defaults

- Minimization: `min_style cg`, energy tol `0.0`, force tol `1e-3`,
  iteration cap `800000`. Tighten when energetics demands it.
- Box relaxation: `fix box_relax all iso 0.0` for isotropic; switch to
  `aniso` / `tri` when the question demands it.
- `thermo_style custom step temp etotal fnorm fmax` as a sensible default;
  extend with system-specific columns when needed.

## Convergence and force-norm reporting

- **`fnorm` is extensive; never report it bare.** LAMMPS `fnorm` is the 2-norm
  (length) of the GLOBAL force vector, a vector of size 3N for N atoms:
  `fnorm = sqrt(sum_i sum_a f_ia^2)`, and `ftol` is compared directly against
  it in force units (docs.lammps.org/minimize.html). A bare fnorm cannot be
  compared between cells of different size: the same physical convergence gives
  `fnorm = 1e-3` at 5.7e5 atoms and `fnorm = 8e-5` at 4000 atoms. See L9.
- **Always report both normalizations, and name the definition.** Every LAMMPS
  input carries a header block defining fnorm and both normalizations; every
  `thermo_style` in a minimization carries `v_fnorm_per_atom` (= fnorm/N) and
  `v_fnorm_per_dof` (= fnorm/sqrt(3N)) alongside the raw `fnorm`; every
  thread.md, run.yaml and chat statement about convergence quotes both. Erik
  (2026-08-03): *"ALWAYS state forcenorm/natoms and state that you are using
  this definition."* The phrase "per-atom RMS" is banned as ambiguous -- it was
  read three different ways in one sentence, spanning a factor 1300.
- **Default 0 K stopping criterion: `fnorm/sqrt(3N) < 1e-8 eV/A`.** This is the
  RMS force per degree of freedom and the only one of the three that is
  size-independent. `fnorm/N` is NOT: it falls as 1/sqrt(N), so a fixed
  `fnorm/N` target silently lets the per-atom force grow as sqrt(N) -- 4.4e-6
  eV/A in a 5.7e5-atom dislocation cell against 3.7e-7 eV/A in a 4000-atom bulk
  cell, from the same number. Erik's choice, 2026-08-03. Write it into the
  input computed from the atom count, never hard-coded per cell:

      variable n_atoms      equal count(all)
      variable sqrt_3N      equal sqrt(3.0*v_n_atoms)
      variable FTOL_PER_DOF equal 1.0e-8
      variable ftol_global  equal v_FTOL_PER_DOF*v_sqrt_3N
      minimize 0.0 ${ftol_global} <maxiter> <maxeval>

  Tighten when the science demands it; loosen only on the record.
- **Cap `maxiter` at ~15-20x the expected iteration count**, not at 800000. A
  minimization that runs into the walltime cap never reaches its `write_data`
  and loses everything; one that hits `maxiter` writes the structure and says
  so in the `Stopping criterion` line. (This overrides the 800000 default in
  "Computation defaults" above for any run long enough to approach its wall.)

## Directory and file naming

- **CAPITALS for directories, preserve chemical element typography.**
  Project root, thread dirs, run dirs all use CAPS
  (`Ni-A0-CIJ-EAM-MEAM/`, `01_LATTICE-CONSTANT-AT-0K/`,
  `01_MIN-EAM-PEZOLD/`). Element symbols keep their canonical case
  (Ni not NI, Al not AL). The `id` field in frontmatter stays
  lowercase-kebab; the directory name uses CAPS. See ARCHITECTURE.md
  §4 folder naming. (Set 2026-05-29.)
- **Extensions: `.dump` for dumps, `.lmps` for write_data.** Anything written
  by a `dump` command ends `.dump`; anything written by `write_data`, or by an
  external tool whose product is a LAMMPS data file, ends `.lmps`. Not `.data`,
  not `.lammps`, not `.fcc`. Set 2026-08-03 (Erik: *"please have dumps called
  .dump and writeouts *.lmps"*), matching his own EAM-DISLOCS-Ni-Cu files
  (`d90_Ni-Mishin04_x-101_y1-21_z111_25x10x25.lmps`). Pre-existing files keep
  their names -- the dcreator-built `.fcc` cells are LAMMPS data files with a
  misleading extension and are NOT renamed; the rule governs what we write.
  Note this supersedes the `.data` spelling used in ni-a0-cij-eam-meam and in
  the "Output style" section's parenthetical below.

## Structure building

- **External build (LEGO) preferred** over in-LAMMPS `lattice` +
  `create_atoms` for anything beyond trivial cubic boxes. Keeps the
  structure as an inspectable artifact, separate from the input
  script. The 2026-05-29 Ni-baseline used in-LAMMPS build as a
  documented exception (small project, single-element fcc) — not the
  default. (Set 2026-05-29.)

## Shared resources

- **Potentials stay in `/cmmc/ptmp/<CLUSTER_USER>/POTENTIALS/`** — the
  canonical cluster location. Do NOT copy potential files into each
  project. Reproducibility is provided by the YAML provenance block
  (citation, fingerprint, KIM ID where applicable) in the run.yaml,
  not by duplicating the file. Otherwise a parameter study with N
  runs produces N copies of the same potential file. (Set 2026-05-29.)

## Communication style

- **Spell out abbreviations at first use.** Every acronym, symbol or shorthand
  gets its expansion the first time it appears in a response -- "mean-squared
  displacement (MSD)", "the diffusion coefficient of hydrogen as a function of
  temperature, D_H(T)". After that the short form is fine within the same
  response. Applies to chat, project files, figure captions and data-file
  headers alike. Set 2026-08-06 (Erik: *"Please always explain your
  abbreviations: no clue what : D_H(T) MSD means?"*). Treat as a standing
  preference, not a one-off -- it is not the first jargon-density feedback.

- **Name the aim, not just the thread and run number.** A thread or run is never
  referred to by its number alone. Every first mention -- in a response, a status
  readout, a table row, a figure caption, a hand-off, a `scope:` field -- carries
  in the same breath the question the run exists to answer:

  > not "thread 05 runs 01 and 02 -- all ten tasks complete"
  > but "thread 05 runs 01+02, the fine 900 K mu bracket and its L14 size check
  >     (is the transition still first order, and is the gap physical or
  >     finite-size?) -- all ten tasks complete"

  The directory name is not the aim: `05_CHARGE-DISCHARGE-RATEB-900K` says what
  the run IS, never what it is FOR, and the aim is the half Erik needs in order
  to judge whether a result matters. Later mentions in the same response may use
  the bare number once the aim is on the record. Where the aim is genuinely
  unknown to the session, say so ("thread 04, aim not stated in thread.md")
  rather than quoting the folder name as if it were a purpose -- that is also
  the signal that the thread.md needs a one-line aim at its top.
  Set 2026-08-29 (Erik: *"always mention the aim of a thread and not just the
  thread and run number"*), after a status readout gave the full x(mu) table for
  two runs and drew a conclusion from them without ever saying what either run
  was for. The cost is asymmetric, which is why this is a rule: a reader who
  does not know the aim cannot tell a decisive result from a routine one, so
  every unlabelled number is read at the same weight -- precisely the failure a
  status readout exists to prevent.

## Output style

- **Ask what to write out, and how often, before writing the input.** The
  output set is Erik's to specify, not the pilot's to infer. At design time,
  ask explicitly: which PER-ATOM fields (positions, type, stresses, energies,
  coordination, velocities), which GLOBAL/thermo fields, and the cadence of
  each -- then record the answer in thread.md alongside the run design. Do not
  copy an output set from a template or a sibling run without re-asking; the
  sibling answered a different question.
  **Standing answer (2026-08-20): per-atom stresses and per-atom energies are
  wanted** whenever the science involves defect nucleation, phase
  transformation, or coherency stress. Erik: *"please always ask which
  properties should be written out and in what frequency. Here we would need
  the atomic stresses and Energies."*
  Decide content and cadence TOGETHER -- per-atom stress plus energy is roughly
  2.5x the frame size, so the sampling interval usually has to move with it.
  (Where it bit: ni-h-hydride-cycle-eam thread 01 dumped `id type x y z` only,
  75 GB across four production runs at 4 nodes and 14-65 h each, leaving the
  hydride-induced stress field -- the mechanism the project exists to study --
  unmeasurable without re-running.)
- **Minimalism.** Each input writes only the outputs that answer the
  current question. Don't dump the full LAMMPS menu (per-atom stress,
  virial, every thermo column, etc.) by default. Erik's own words
  (transcript, 2026-05-29):

  > "I like to have not the full menu of every thing outputted, but
  > just what makes sense in that context. If i need the pressures
  > later, I will make a 1 step calculation to calculate them."

  See lesson L18.
- **Space-separated, not CSV.** Output data files for time-series and
  scalar results use space-separated columns (gnuplot-friendly), not
  comma-separated. Erik's own words: *"please no csv output, I like
  gnuplot, just space separated."* Header lines start with `#` and
  explain columns + units explicitly. (Set 2026-05-29.)
- **Headers on every data file** that's not pure LAMMPS-format
  (`.data`, `.dump`). Erik's own words: *"all data files should have
  a header that explains the columns!"* Header includes: what the
  file is, when generated, key context (potential, structure,
  thread), and column names + units. Pure LAMMPS-format outputs
  (`.data`, `.dump`) get the default LAMMPS header line; provenance
  lives in run.yaml, not in the file.
- **Minimalism applies AFTER the output set is agreed.** The two bullets are
  ordered deliberately: minimalism governs what to leave out of the set Erik
  asked for, never what to omit from it. A session that reads L18 as a general
  bias toward fewer columns produces a dataset that cannot answer the question
  it was run for.

## File authorship

- **Erik Bitzek is the author** of all input/submit scripts and
  project files in this project. Pilot transcribes/writes; Erik is
  named in the header. Tool credit: "created with LLM-LMPS". Erik's
  own words (transcript, 2026-05-29):

  > "please have as author me (Erik Bitzek) and created with LLM-LPMS"

  Header convention for LAMMPS input scripts:

  ```
  # ---------------------------------------------------------------
  # <filename>
  # <one-line description of what this script does>
  # Project: <id>  Thread: <thread-dir>  Run: <run-dir>
  # Author: Erik Bitzek  (created with LLM-LMPS)
  # Date: <YYYY-MM-DD>
  # ---------------------------------------------------------------
  ```

## Output precision

Output files (per-step thermo dumps, block-averaged streams, per-atom
dumps, snapshot files) should store each quantity at a precision **just
below the relevant physical noise floor**, not at the float64 noise
floor. Two reasons:

1. File size: per-step streams over 100k+ steps balloon when every
   float is 20 digits.
2. Readability: gnuplot/awk grep'ing through 20-digit numbers is
   needlessly painful.

The rules below come from three considerations that apply in different
combinations to different quantities:

- **Aggregate-cell observables** (T, P, total energy, box dimensions):
  pick precision ~1000x below typical instantaneous fluctuation. The
  fluctuation IS the noise floor; storing finer is meaningless.
- **Per-atom observables** (per-atom PE, per-atom stress): pick
  precision such that `N_atoms * per_atom_precision` is well below
  the smallest meaningful aggregate. Rule of thumb: assume future
  N_atoms up to ~1e8, target ~meV/atom resolution on the aggregate.
- **Geometric quantities** (atomic positions, displacements): the
  *use case* of the dump dictates precision, not the raw float64
  noise.
  - **OVITO visualization + standard defect-analysis methods**
    (CNA, ACNA, PTM, centrosymmetry, atomic strain, DXA, Wigner-
    Seitz, surface-mesh, coordination): all of these are tolerant
    to position noise of ~mA or larger (PTM has the tightest
    floor, ~mA RMSD; CNA/coordination are tolerant to ~10% of
    nearest-neighbor distance ~0.2 A; DXA and W-S build their
    construction at ~a/2 scale). `%.4f` A (100 uA) sits 1000x
    below the most sensitive method and is way below screen-pixel
    resolution for any reasonable rendering. Use this as the
    default for trajectory dumps.
  - **Between-configuration displacement diff** (e.g., subtract
    one CG-relaxed structure from another to extract defect-induced
    relaxation patterns): need precision below the smallest
    meaningful displacement, typically `%.6f` A (uA).
  - **Research-grade convergence diff**: only when the science
    requires sub-fm precision (ftol-implied residual at minimization
    convergence is ~ftol/k_local; for Ni EAM at ftol=1e-8 eV/A and
    k ~ 50 eV/A^2 per atom, that's ~2e-10 A). `%.8f` A.
  - **`write_data` output for a future `read_data`**: keep LAMMPS
    default (`%-1.16g`). Data files must round-trip exactly across
    LAMMPS sessions; never override precision.

  At MD temperatures, per-fs thermal step is ~1e-5 A — the `%.4f`
  default still resolves below that. The CG case is special: CG
  doesn't have a fixed "max step" the way damped dynamics does
  (Erik's note); use the ftol-implied residual instead of the
  damped-dynamics displacement.

### Per-quantity defaults (LAMMPS `metal` units)

| Quantity                                              | Unit    | fix print / dump_modify fmt | fix ave/time fmt | Rationale                                  |
|-------------------------------------------------------|---------|-----------------------------|------------------|--------------------------------------------|
| step                                                  | -       | `%d`                        | `%d`             | integer counter                            |
| Temperature                                           | K       | `%.3f`                      | `%.3f`           | sigma_T ~ several K at our N               |
| Pressure, stress components                           | bar     | `%.3f`                      | `%.3f`           | sigma_P ~ 100-1000 bar instantaneous       |
| Box dimensions lx, ly, lz                             | A       | `%.6f`                      | `%.6f`           | NPT thermal noise ~ mA; uA = 1000x         |
| Tilt factors xy, xz, yz                               | A       | `%.6f`                      | `%.6f`           | same as box                                |
| Volume                                                | A^3     | `%.4f`                      | `%.4f`           | derived from L^3                           |
| Total potential / kinetic energy                      | eV      | `%.6f`                      | `%.6f`           | uEV total = sub-neV/atom; fine             |
| Per-atom potential energy                             | eV/atom | `%.8f`                      | n/a              | 10 neV/atom; supports 1e8-atom diffs       |
| Per-atom stress component                             | bar     | `%.3f`                      | n/a              | same noise as cell-level                   |
| Forces                                                | eV/A    | `%.6e`                      | `%.6e`           | scientific; spans ftol=1e-8 to ~10         |
| **Atomic positions in dump for OVITO viz / standard defect analysis (CNA, PTM, DXA, centrosymmetry, atomic strain, Wigner-Seitz)** | A | `%.4f` | n/a | 100 uA = below all OVITO method floors; CNA/PTM ~mA RMSD threshold, atomic strain ~mA |
| Atomic positions in dump for between-config displacement diff (CG-relaxed) | A | `%.6f` | n/a | uA; resolves fine relaxation differences between near-degenerate minima |
| Atomic positions in research-grade dump (ftol-implied residual at convergence) | A | `%.8f` | n/a | sub-fm; only when configurations will be subtracted to extract small displacements |
| `write_data` (data file for read_data on next run)    | A       | LAMMPS default (`%-1.16g`)  | n/a              | data files must round-trip exactly; do NOT override |

### How to apply in LAMMPS inputs

- **fix print**: use the `$(quantity:fmt)` form in the message string,
  e.g.:
  ```
  fix STEP_LOG all print 1 "$(step:%d) $(temp:%.3f) $(lx:%.6f) ..." title "..." file FNAME screen no
  ```
- **fix ave/time**: defaults are `%g` (~6 sig figs). **Per-column
  format override is NOT available on `fix ave/time` (see L28).**
  `fix_modify FIX_ID format ...` only works on `fix ave/atom`,
  `fix ave/chunk`, `fix ave/grid`, `fix ave/histo`,
  `fix ave/correlate`. For `fix ave/time` output precision, accept
  the default `%g`; if finer control is needed, post-process the
  .dat file with Python/awk after the run. The default `%g`
  is fine for typical magnitudes (box ~ 35.3776 A, pressure ~
  -1187.57 bar, temp ~ 299.5 K — all ~6 sig figs, well below
  noise floor of the respective quantity).
- **dump custom (positions, per-atom quantities)**: use
  `dump_modify DUMP_ID format line "..."` or `format col N "..."`
  per column. The dump_modify form lets you mix `%d`, `%.6f`,
  `%.8f`, `%.6e` per channel.

(Set 2026-05-31, Thread 03 of ni-a0-cij-eam-meam. Surfaced when
Erik noticed the per-step file held 19-digit-precision floats far
below float64 noise. Extended same day with the OVITO-trajectory
rules after Erik flagged that `%.6f` for OVITO viz dumps is also
overkill — OVITO defect-analysis methods all sit at ~mA sensitivity.)

### Design-time disclosure rule

**Every output proposal during a design conversation must include
the per-quantity precision in the same proposal.** When the pilot
suggests:
- a `fix print` time-series (which columns, every N steps)
- a `fix ave/time` block-averaged stream (which channels, window
  size)
- a `dump custom` trajectory or snapshot (which per-atom columns)
- a `write_data` archive

...the precision per column is part of the proposal, not something
the user has to elicit. This is parallel to the runtime-estimate
rule in `canon/learnings.md` ("Workflow rules"): metadata that
downstream consumers need belongs with
the design, not in a follow-up. Surfaced 2026-05-31 Thread 03.

## Command hand-offs

- **Absolute paths, copy-paste ready, always.** Any command Erik is
  asked to run (sbatch, ssh, rsync, cd, analysis invocations) appears
  as a complete copy-pasteable block with full absolute paths — every
  `cd` targets an absolute directory (included whenever the command is
  cwd-sensitive, e.g. sbatch scripts resolving `SLURM_SUBMIT_DIR`),
  and every file argument not resolved by the script itself is
  absolute. No relative `cd ../..` chains, no "from the same directory
  as before", no "from the respective run dirs" prose. State where the
  block runs (which machine/shell, e.g. "in your cmmg shell") at the
  top; one block per logical action, in execution order. A hand-off
  must be paste-able from any starting directory.
  (Set 2026-07-28/29, Ni-H sessions. Erik's words: "can you tell me
  exactly what to submit how and where (ALWAYS!)" and "please (always,
  remember that) give the correct path and submit command so I can
  easily copy paste". Promotes the identical instruction from the
  2026-07-28 SCIENCE-KICKOFF handoff to canon.)

- **Every block carries a machine tag, and the tag is the first thing in
  it.** Not a footnote, not implied by context, not "then submit it":
  `**on the MAC (M5), in a local shell**` or `**in your CMMG SHELL**`.
  When a sequence crosses machines -- stage from the Mac, ssh, submit on
  the cluster, pull back to the Mac -- each crossing is its OWN numbered
  block with its own tag. A reader who pastes block 3 into the wrong
  shell is the failure this prevents, and it is silent: an `rsync` run
  on the cluster instead of the Mac succeeds and copies the wrong way.

- **Never emit a `<PLACEHOLDER>` from `canon/clusters.yaml`, and never
  substitute a guess for one.** That file is identity-scrubbed;
  `<CLUSTER_USER>` and `<CLUSTER_HOST>` are pointers to
  `canon/local/clusters.local.yaml`. Resolve them there, or say you
  cannot. **A cluster's project name is not a hostname** -- `cmmg` is a
  key in `clusters.yaml` and an alias on Erik's Mac; the host is
  `<CLUSTER_HOST>` and the user is `<CLUSTER_USER>`, and both belong in
  every ssh / scp / rsync / sshfs target (L21).
  Run `canon/templates/lint-handoff.sh` over any hand-off before sending
  it: it catches unresolved placeholders, bare-host targets, and blocks
  with no machine tag. (Set 2026-08-24 after a hand-off named `cmmg:`
  as an rsync target and tagged no block with a machine. L42.)

- **The first hand-off into a NEW tree creates the remote directory
  itself.** `rsync` creates only the LAST component of a destination
  path; every parent must already exist. The first thread of a new
  project is exactly when nothing above it does, so a staging block
  written from the template of an existing project fails on its first
  use. Either lead with an explicit

  ```
  ssh <user>@<host> 'mkdir -p <absolute remote dir>'
  ```

  or fold it into the transfer with
  `--rsync-path='mkdir -p <dir> && rsync'`, which costs one
  authentication instead of two and does not depend on the local rsync
  version (`--mkpath` needs rsync >= 3.2.3 on the SENDING side, which
  macOS does not reliably give you). (Set 2026-08-24: the
  ni-melting-point-eam thread 01 staging block assumed
  `/cmmc/ptmp/<CLUSTER_USER>/NI-MELTING-POINT-EAM/` existed. It did not, and the
  transfer died with `mkdir ... failed: No such file or directory`.)

- **Write commands for the shell that will run them, and prefer forms with no
  shell-dependent behaviour left in them.** A hand-off is composed by a session
  that never executes it. **Erik's login shell is zsh**, where a glob matching
  nothing is a hard error (`no matches found`) rather than a pass-through as in
  bash -- so a remote glob in an `rsync`/`scp` source cannot be written bare.
  Quoting the source to protect it from the local shell does not rescue it
  either: quoting also stops the REMOTE shell expanding it, and collapses
  several space-separated paths into one literal filename.

  So: **no globs in hand-off commands.** Use explicit absolute paths (which the
  first rule in this section already requires), or, when the task is "pull this
  subtree from several places", `--include` / `--exclude` filter rules against a
  single source directory:

  ```
  rsync -avh --progress -m \
    --include='*/' --include='<subdir>/***' --include='*.<ext>' --exclude='*' \
    <user>@<host>:<absolute remote root>/ <absolute local root>/
  ```

  Filter rules need no shell quoting on either side and rebuild the directory
  layout themselves. Test the form against a mock tree of the same shape before
  offering it. (Set 2026-08-29: a two-path quoted rsync source died with
  `link_stat "…0[34]_*…for-ovito /…*.frameindex" failed: No such file or
  directory` -- one argument, no expansion on either side, the space read as
  part of the filename. Same shape as L42: something true in the writing
  session's head and false in the shell that had to run it.)

## Plot defaults

- **gnuplot-friendly format.** Space-separated columns; `#`-prefixed
  comment lines for headers and units. This is the format expected by
  Erik's downstream plotting.
- **Plots of fits include the fitted equation with values.** When a
  plot shows a polynomial / curve fit overlaid on data, annotate the
  plot with the explicit fitted form and the extracted coefficients
  in physical units. Example for a cubic stress-strain fit:
  `σ(ε) = a0 + a1·ε + a2·ε² + a3·ε³`
  `a0 = 1.4e-9 GPa, a1 = 254.5 GPa (= C₁₁), a2 = ..., a3 = ...`
  Reader can immediately see the curve being plotted, the extracted
  target coefficient, and the higher-order verifiers (per L24). Set
  2026-05-31 (Thread 02 closure feedback). Applies to matplotlib
  outputs from `analyze-Cij.py` and any future fit-plot scripts.
- **One series per physical condition; quality goes in the marker.**
  Merge all scans of the same condition (coarse + fine mu windows, an
  original plus a longer rerun of one point) into a single curve,
  finer or longer runs superseding coarser ones on shared abscissa
  values, and keep provenance in a `src` column so any point can be
  traced to its run. Encode data quality with marker style — **filled
  = converged/assured, open = not assured** — never by drawing a
  second series. Erik's words (2026-07-30): "separating the data into
  fine and the first (coarse) run does not provide any new results,
  please plot them together. use however open symbols if the
  convergence is not assured."
  Corollary: **report the sampling density of any feature you
  compare.** When a claim rests on the *shape* of a transition (width,
  sharpness), print the number of grid points that actually fall
  inside it for each curve. A curve through two points that jump from
  x ~ 0 to x ~ 1 is evidence about the grid, not about the physics.
  (The 2026-07-30 merge exposed exactly this: the EAM had ZERO points
  inside its own 300 K two-phase window while the MEAM had four.)
  Extension for first-order transitions (2026-08-25, from inbox
  2026-08-05-1110): additionally report the **widest interval in the order
  parameter containing no CONVERGED state**. A finite cell inside a
  two-phase region cannot converge to an intermediate value however long it
  is sampled, so the emptiness is physics rather than sampling, and the
  interval's endpoints ARE the coexisting compositions -- the diagnostic and
  the binodal are the same measurement. An empty interval of order the grid
  spacing is the honest way to say "no transition resolvable at this
  resolution". Counting converged points inside a naive window is not
  sufficient: they cluster on the phase branches (Pezold-EAM 600 K had five
  points inside 0.05 < x < 0.95, all sub-stoichiometric beta).
- **Give the region of interest its own axis.** If part of the data
  runs away in the plotted quantity (e.g. tetrahedral overcharge,
  x → 2.5, against a transition at x < 1), add a companion figure
  restricted to the regime the question is about, using a named cut
  constant (e.g. `X_TET = 1.05`) so the same threshold is applied
  everywhere and is visible in the code. A plateau-aligned zoom
  (abscissa minus each curve's own plateau) is the version that
  compares shapes across curves whose absolute reference differs.
  Where the runaway branch IS shown, mark it distinctly (separate
  colour) so it cannot be misread as belonging to the main regime, and
  never drop it without saying so in the caption. Erik's words
  (2026-07-30): "Please provide additional plots that exlude mu /
  concentraiton values where the tetrahedral sites get filled, so that
  one can actually see the transition regime better."
- **Symmetric colour scales for signed quantities, always.** A map, heat map
  or particle colouring of anything that can be positive or negative --
  segregation and binding energies, stresses, displacements, differences of any
  kind -- uses a **diverging** colormap (`RdBu_r`, `coolwarm`, `bwr`) on a
  scale **symmetric about zero** (`vmin = -v`, `vmax = +v`). Set 2026-08-04
  (Erik: *"I always teach my students that if they show such images, make sure
  to use symetric color scales! please do that and try to remember it."*). A
  sequential colormap or auto-ranged limits puts zero at an arbitrary place on
  the bar, and a dipole field then reads as a monopole -- an edge dislocation
  binds H in the tension lobe below the glide plane and repels it in the
  compression lobe above, two nearly equal and opposite lobes, and an
  asymmetric scale renders them in unrelated colours. The physics that matters
  most in the figure is exactly what the wrong colour scale destroys.
- **Sequential colormaps are for quantities with a physical zero at one end**
  (concentration, density, count, magnitude). There, zero at the bottom of the
  bar is the honest choice.
- **Clip loudly, never silently.** When a few outliers would flatten the
  structure, clip at a stated percentile and put both the clip and the true
  extreme in the title. Do not let outliers set the limits, and do not hide
  that you cut them. (2026-08-04: a handful of squeezed core sites reached
  +0.66 eV while everything of interest lived inside +-0.15 eV.)
- **Print the range for external viewers.** Any script that writes a file for
  OVITO or ParaView prints the symmetric colour range to enter by hand --
  "Adjust range" in those tools returns `[min, max]` (here `[-0.146, +0.662]`)
  and will reintroduce exactly this problem downstream.
