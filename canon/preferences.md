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

## Directory naming

- **CAPITALS for directories, preserve chemical element typography.**
  Project root, thread dirs, run dirs all use CAPS
  (`Ni-A0-CIJ-EAM-MEAM/`, `01_LATTICE-CONSTANT-AT-0K/`,
  `01_MIN-EAM-PEZOLD/`). Element symbols keep their canonical case
  (Ni not NI, Al not AL). The `id` field in frontmatter stays
  lowercase-kebab; the directory name uses CAPS. See ARCHITECTURE.md
  §4 folder naming. (Set 2026-05-29.)

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

## Output style

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
