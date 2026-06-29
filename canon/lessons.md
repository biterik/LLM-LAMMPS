# LLM-LMPS — Lessons queue (canonical)

Numbered, append-only record of concrete lessons surfaced during work.
Style files (`style/lammps.md`, `style/shell.md`, etc.) reference back to
lesson numbers here. Cluster-specific quirks land in `clusters.yaml`;
process-level meta-lessons land in `learnings.md`.

**Origin:** lessons 1–21 surfaced during the 2026-05-29 end-to-end walk on
project `Ni-A0-CIJ-EAM-MEAM` (Thread 01, lattice constant at 0K).
Reconstructed 2026-05-30 in four stages:

1. CHECKPOINT.md fragments (~11 of 21) — concrete bug content + numbered
   tags.
2. Erik's manual paste of the yesterday transcript — adding L7–L10,
   L12, L13, L18, L19 with full content.
3. Two further entries (L22 box-size, L23 diff-potential) recovered
   from a later transcript paste; these were walk-queue items #3 and #5
   in the live numbering, renumbered to the tail when their target
   files (lessons.md vs preferences/learnings) were sorted out.
4. The original conversation transcript from Claude desktop's local
   session storage — used to verify L7/L8 ordering (matches) and to
   reconstruct L11 and L16 from technical content discussed during
   the walk (module purge discipline + submit-script strictness).
   L11/L16 carry numbering notes flagging that their original queue
   text wasn't surfaced as visible chat — they're best-fit
   reconstructions, not verbatim quotes.

All 21 numbered slots now have content. L22 and L23 are appended at
the end with notes on their original-queue position.

The reason this reconstruction was needed at all: the prior pilot parked
the 21 lessons in a Cowork task list (session-scoped, not persistent)
instead of writing them straight to this file. See `learnings.md`
Process section and ARCHITECTURE.md §2 rule 5 corollary for the
no-task-list-as-storage rule that came out of it.

---

## L1 — `${var}` vs `$(expr)` in LAMMPS inputs

**Rule:** use `$(...)` (equal-style runtime evaluation) for any quantity
that depends on simulation state — `$(lx)`, `$(pxx)`, `$(fnorm)`,
`$(etotal)`, `$(step)`, etc. `${var}` is **immediate substitution at
parse time** and expands to the *string* `${lx}`-as-defined-variable,
which is almost never what's wanted for runtime quantities.

**Where it bit:** `fix print` line in 0K minimization input — wrote
`${lx}` expecting the box length at minimum; got the literal variable
reference instead.

**Target:** `style/lammps.md`. Layer 1 lint: `grep -E
'\$\{(lx|ly|lz|press|p[xyz]{2}|fnorm|fmax|etotal|step|temp|pe|ke)\}'
<input>` should return nothing.

## L2 — ASCII-only in LAMMPS input files

**Rule:** every character in a `.in` file must be ASCII. No em-dash
(U+2014), no en-dash, no curly quotes, no non-breaking space, no
greek letters in comments.

**Where it bit:** em-dash in a comment line — LAMMPS parser threw at
start-up before any physics.

**Target:** `style/lammps.md`. Layer 1 lint: `file -i <input>` should
report `charset=us-ascii`.

## L3 — Descriptive output filenames (no LAMMPS-tutorial defaults)

**Rule:** every output filename in a LAMMPS input must be descriptive.
The defaults from the LAMMPS quickstart tutorial — `log.lammps`,
`dump.out`, `restart.data`, plus the slightly-better-but-still-generic
`a0-result.txt`, `relaxation-log.dat`, `final-snapshot.dump` — all
violate the project's "descriptive file names always" rule (architecture
§2 rule 2). Substitute structure + observable + potential + thread, e.g.
`Ni-fcc-10x10x10-a0-min-Pezold-EAM.data`.

**Where it bit:** every input written before the walk taught us to lint
for this — generic defaults won by inertia.

**Target:** `style/lammps.md`. See also L20.

## L4 — MEAM library element list must match parameter file's indices

**Rule:** in `pair_style meam`, the `pair_coeff` line has two element
lists separated by the parameter file path:

```
pair_coeff * * <library-file> <elem-list-1> <param-file> <elem-list-2>
```

- `<elem-list-1>` (before param-file) = elements to **extract from the
  library**. Indices in this list are what the parameter file's
  internal references (`zbl(i,j)`, `rho0(i)`, etc.) resolve against.
- `<elem-list-2>` (after param-file) = mapping from LAMMPS atom types
  to extracted-element names. Length = number of atom types in the
  simulation.

The two lists are independent. `<elem-list-1>` must include every
element the parameter file references by index, even if you have no
atoms of that species. `<elem-list-2>` is just your atom-type map.

**Concrete example — Ni-only sim with the Ko-Shim-Lee Ni-H MEAM:**

```
pair_coeff * * ${POTDIR}/${POTENTIAL_LIB} Ni H ${POTDIR}/${POTENTIAL_PAR} Ni
```

`Ni H` extracts both elements (so the parameter file's `zbl(2,2)`,
`rho0(2)` entries resolve to H = index 2). The trailing `Ni` maps the
sole atom type to extracted-element "Ni". Zero H atoms in the box;
LAMMPS still parses cleanly.

**Where it bit:** initial MEAM input for pure Ni used `pair_coeff * *
lib.meam Ni param.meam Ni`. Library extraction had only one element →
index 2 was out of range → LAMMPS errored on the first parameter-file
line referencing index 2 (a `zbl(2,2)` term, line 16 of the param file).

**Layer 1 check:** before write, open the parameter file and grep for
any `(i,j)` or `(i)` index reference; max(i,j) determines the minimum
length of `<elem-list-1>`. Lint script does not automate this — manual
review of the parameter file is required. (See `style/lammps.md` §1.4.)

**Target:** `style/lammps.md` §1.4.

## L5 — `fix print` does NOT fire during `minimize`

**Rule:** `fix print` is not on the list of fixes invoked at minimization
iterations. `thermo` and `dump` do fire during minimize; `fix print`
does not. If you want a per-iteration printout during minimize, use a
custom `thermo_style` column (an equal-style variable referenced in
`thermo_style custom`), not `fix print`.

**Where it bit:** `fix print` written to log box dimensions every N
minimize steps — silently emitted nothing.

**Target:** `style/lammps.md`. General check: for any `fix` used during
minimize, consult its doc page for the "supported during minimize?"
note; many fixes silently do nothing.

## L6 — slurm `-o %x.out` and LAMMPS `log.lammps` are redundant + collide

**Rule:** by default, slurm captures stdout/stderr (`%x.out`) and LAMMPS
writes its own `log.lammps`. The two contain different but overlapping
content, and `log.lammps` is generic (violates L3). Convention: in the
LAMMPS input, set `log <descriptive>.log` at the very top (before
anything else); in the submit script, add `-screen none` to the srun so
LAMMPS doesn't double-print to the slurm `.out`. The slurm `.out` then
captures only the submit-script's own echo lines + any LAMMPS error
that prevented log file open.

**Where it bit:** initial submission had both `log.lammps` (generic) and
slurm `.out` capturing duplicate streams; cleanup was painful and the
`log.lammps` filename violated L3 silently.

**Target:** `style/lammps.md`, `style/shell.md`.

## L7 — Reference potentials via `${POTDIR}` variable at top of input

**Rule:** declare `variable POTDIR string "/cmmc/ptmp/<CLUSTER_USER>/POTENTIALS"`
near the top of every LAMMPS input that uses a shared potential; refer
to the file as `${POTDIR}/<filename>` in `pair_coeff`. One place to
edit if the path changes; no out-of-band sed substitution for paths.

**Why over alternatives:**
- Relative paths (`../../../POTENTIALS/...`) are fragile to run-dir depth
  and break if the run dir moves.
- Sed-substituted absolute paths require out-of-input tooling and hide
  the value from someone reading the input alone.
- A `variable` at top is inspectable, modifiable in one place, and
  self-documenting.

**Where it bit:** before this rule, the natural instinct from existing
LAMMPS examples was relative paths. Got rejected by Erik because run
folders live deeper in the project hierarchy than the tutorial examples
assume.

**Target:** `style/lammps.md`.

## L8 — `pair_style meam` (no /c suffix) for lammps/250722

**Rule:** on cmmg's `lammps/250722` module, the MEAM pair style is
named `meam` — no `/c` suffix. The Fortran-implemented `meam` was
removed in Dec 2018; the C++ implementation took over the plain
`meam` name. Older LAMMPS docs/examples sometimes use `pair_style
meam/c`; that's wrong for current versions.

**Where it bit:** initial draft of the MEAM input was about to write
`pair_style meam/c` based on stale doc-memory. Caught by following the
"look up docs of the loaded version before writing syntax" rule.

**Lesson generalization:** always verify command names and syntax
against the docs of the LAMMPS version actually loaded on the target
cluster (recorded in `clusters.yaml` per cluster); names change
between versions.

**Target:** `style/lammps.md` (current versions) + `clusters.yaml`
(version pinning).

## L9 — `ftol` is the global force 2-norm; scales as √(3N)

**Rule:** in `minimize etol ftol maxiter maxeval`, `ftol` is the
2-norm of the global force vector (length 3N — every Cartesian
component, every atom). It scales as √(3N). A "tight" `ftol` that
looks reasonable for 100 atoms can be unreachable below
double-precision floor for 10⁵ atoms.

**Workflow:** pick a per-atom RMS-force target first (e.g., 1e-6 eV/Å
for a Cij reference state). Convert: `ftol = (per-atom-RMS) ×
√(3N)`. For N=4000: `ftol = 1e-6 × √12000 ≈ 1.1e-4 eV/Å`.

For `etol`: relative energy change between iterations, unitless. Bottoms
out around 1e-12 in double precision for moderate-system total energies;
tighter values just trigger maxiter without converging.

**Where it bit:** initial proposal of `ftol 1e-8` for N=4000 was below
double-precision floor → would have iterated to maxiter without ever
converging. Erik flagged the per-atom-vs-global ambiguity in my
explanation.

**Target:** `style/lammps.md`.

## L10 — `min_modify line quadratic` recommended with `fix box/relax`

**Rule:** when minimizing with `fix box/relax`, set `min_modify line
quadratic` before `min_style`/`minimize`. The fix_box_relax docs
explicitly recommend it for the box-relaxation outer-iteration / line-
search pattern.

**Target:** `style/lammps.md`.

## L11 — `module purge` before any `module load`

**Rule:** every submit script (and any cluster-side shell that loads
modules) begins with `module purge` before any `module load`. The
purge clears any modules inherited from the login shell, ensuring the
job environment is deterministic and reproducible — not contaminated
by whatever Erik happened to have loaded interactively when he
sbatched.

**Example (from Thread 01 submit script):**

```
module purge
module load lammps/250722
```

**Where it bit:** part of submit-script discipline during the walk.
Already encoded as discipline in `learnings.md` Submit-script section
("set -euo pipefail, module purge before any module load, …") and now
made explicit as a numbered lesson.

**Target:** `style/shell.md`.

**Numbering note:** content reconstructed 2026-05-30 from transcript
mining. The original L11 walk-queue entry was a shell rule (per
CHECKPOINT.md's L7-10/12/13/18/19/20→lammps.md, 11/16→shell.md
tagging) but its exact text was never surfaced as visible chat
content in the transcript — only as items in the pilot's internal
accumulating list. This is the best-fit shell rule that surfaced
during the walk and is not otherwise numbered.

## L12 — FIRE / quickmin minimizers cannot be paired with `fix box/relax`

**Rule:** `fix box/relax` augments the minimization objective with a
Parrinello-Rahman strain term and requires an outer-iteration-plus-
line-search pattern. `cg`, `hftn`, and `sd` provide this; `fire` and
`quickmin` are damped-dynamics integrators with no line search and
cannot relax the box DOF consistently. For box-relaxing minimizations,
use `cg` (default), `hftn`, or `sd` — not FIRE.

**Where it bit:** initial draft listed FIRE as one of three real
choices for static bulk minimization with box/relax. Erik called it
out: "I don't think FIRE could actually do box relaxation — read the
docu before suggesting something that might not work!"

**Meta-lesson:** read the docs before listing options. Don't enumerate
choices from priors and hope they all apply to the constraint set in
play. See `learnings.md` meta-lessons section.

**Target:** `style/lammps.md`.

## L13 — Cubic-axis-aligned cells for Cij extraction

**Rule:** for elastic-constant work on cubic crystals, build the cell
with sides aligned to the crystal axes (`x ∥ [100]`, `y ∥ [010]`,
`z ∥ [001]`). Strains then map cleanly to C₁₁, C₁₂, C₄₄ without any
rotation matrix. Same cell is fine for the prior a₀ minimization
(cubic isotropy means orientation doesn't change a₀).

**Where it bit:** orientation was a decision sub-point during cell
setup; cubic-axis-aligned was the obviously correct call once the
downstream Cij use was named.

**Target:** `style/lammps.md`.

## L14 — sshfs mount: cluster-created files cannot be deleted via mount

**Rule:** files created by cluster-side processes (slurm job outputs,
LAMMPS dumps, etc.) appear on the sshfs mount with permissions/ownership
that prevent client-side `rm`. The mount is a read window for these
files; cleanup goes through Erik's terminal (`ssh <CLUSTER_USER>@host rm …` or
an interactive shell on the cluster).

**Where it bit:** trying to clean up a failed run dir from the Mac side
via the mount — silent failure (or permission-denied) on the
job-produced files; the directory couldn't be fully removed without
ssh-ing in.

**Target:** `clusters.yaml` quirks (cmmg block) and `learnings.md`.

## L15 — sshfs mount options that fix stale-view problem

**Rule:** the cmmg mount must use these options to avoid the
sandbox/Mac seeing stale cached views of files that have changed
cluster-side:

```
-o auto_cache,attr_timeout=2,entry_timeout=2,negative_timeout=2,reconnect,ServerAliveInterval=30
```

**Important:** the first draft of this rule included `kernel_cache=no`,
which is invalid syntax — `kernel_cache` is a FUSE boolean flag (presence
= on, absence = off), not key=value. Default already excludes kernel
cache; just omit it. The actual cache-busting option is `auto_cache`,
which invalidates cached data when mtime/size changes.

**Where it bit:** during the walk, files updated by re-running jobs on
the cluster were still showing old contents on the Mac side until the
mount was remounted. On 2026-05-30 first attempt with `kernel_cache=no`
errored out with `fuse: unknown option`; corrected form mounts cleanly.

**Target:** `clusters.yaml` (cmmg mount options).

## L16 — Submit-script strictness + file-existence pre-flight

**Rule:** every submit script begins with `set -euo pipefail` and
includes existence checks for every referenced input/data/potential
file BEFORE the actual `srun`. Fail fast and explicitly — never let
the job run for half a second only to die on a missing file (which
costs a queue slot).

**Pattern (from Thread 01 submit script):**

```bash
set -euo pipefail

# ... module load / cd ...

infile=relax_Ni-fcc_Pezold-EAM.in
potfile=/cmmc/ptmp/<CLUSTER_USER>/POTENTIALS/ni_h_rcut4.90_rcut2.eam.alloy

[[ -f "$infile"  ]] || { echo "Missing input: $infile";  exit 1; }
[[ -f "$potfile" ]] || { echo "Missing potential: $potfile"; exit 1; }

srun -n "$SLURM_NTASKS" "$LMP_BIN" -in "$infile" -screen none
```

**Why both:**
- `set -euo pipefail` catches uninitialized vars, failed pipelines,
  and any unexpected non-zero exit before they cascade.
- Pre-flight existence checks catch the most common cause of wasted
  queue time: a typoed filename or a forgotten file copy.

**Where it bit:** part of submit-script discipline during the walk;
no specific failure quoted, but the pattern was applied consistently
across both Thread 01 runs.

**Target:** `style/shell.md`.

**Numbering note:** content reconstructed 2026-05-30 from transcript
mining. Same caveat as L11 — the original L16 walk-queue entry was a
shell rule but its exact text was never surfaced as visible chat
content. This is the best-fit shell discipline pattern that surfaced
during the walk.

## L17 — zstd is the default compression; `-19` for mile-pebble curation

**Rule:** for any compression of LAMMPS data, dump, or log files in this
project — use `zstd`, not `gzip`. Faster *and* smaller in the regimes
that matter for us. For mile-pebble curation (cluster → Mac archival
pull), use `-19` (max ratio, slow compress, fast decompress). For
in-flight transfers and scratch, default `-3` is fine.

**Where it bit:** chose zstd over gzip during Thread 01 close; ratios
were ~20–30% better than gzip-9 on the `.data` files at comparable
compress time.

**Target:** `style/shell.md`.

## L18 — Output only what makes sense for the current purpose

**Rule:** don't dump "the full menu" of LAMMPS output (per-atom stress,
virial, every thermo column, etc.) by default. Each input writes only
the outputs that answer the current question. Need a quantity later?
Run a one-step calculation on the saved structure to extract it. The
.data mile-pebble + the pair_coeff in the next thread reconstruct full
state cheaply.

**Examples from the walk:**
- a₀ minimization at 0K: no `temp` column (identically zero), no
  per-atom stress (not needed). Output is energy, box, total pressure,
  fnorm — that's it.
- Per-atom snapshot (`dump custom`): only `id type x y z c_PE_ATOM` for
  a relaxation — not the full virial menu.

**Why:** smaller files, faster I/O, less noise to filter when reading,
fewer columns to misinterpret on re-entry.

**Where it bit:** initial output proposal included `pxx pyy pzz` per
atom and a fuller thermo style; Erik called it out as over-broad.

**Target:** `style/lammps.md`.

## L19 — `fix print` format string: `$(...)` for runtime, `${...}` for variables

**Rule:** inside `fix print` (and any LAMMPS `print` command):
- `$(expr)` — evaluated as an immediate expression *at print time*
  (each fire of `fix print`). Use for thermo keywords (`$(step)`,
  `$(lx)`, `$(etotal)`, `$(press)`, …) and on-the-fly arithmetic.
- `${var}` — substitution of a named LAMMPS variable, *at the time
  the print command is parsed*. Used for static labels and pre-set
  values.

For per-iteration output during a `fix print` block, the format
string must use `$(...)` for runtime quantities — otherwise the value
freezes at fix-definition time (parse time).

**Where it bit:** the post-minimize `print` line at Thread 01 used
`${lx}` for the final box length → ERROR "Substitution for illegal
variable lx", because there is no LAMMPS variable named `lx` (it's a
thermo keyword). Inside `fix print` itself we (correctly) used
`$(lx)`; the bug was a one-place inconsistency in the trailing `print`
lines.

**Relationship to L1:** L1 is the general rule; L19 is the specific
`fix print` syntax application + the parse-time-vs-print-time
distinction.

**Target:** `style/lammps.md`.

## L20 — Descriptive-names rule applies to ALL LAMMPS outputs

**Rule:** L3 generalized. The "descriptive file names always" rule
(architecture §2 rule 2) applies not just to `.data` files but to every
output: `log` file, `dump` files, `write_data` outputs, `write_restart`
outputs, screen redirects, any `fix print` file. Pilot must grep the
input for every output-producing command and check each filename
explicitly against the rule before Write.

**Where it bit:** initial inputs used descriptive `.data` names but
left `log.lammps`, `a0-result.txt`, etc. — partial compliance failed
the spirit of the rule.

**Target:** `style/lammps.md`. Generalizes L3.

## L21 — Always specify SSH user explicitly: `ssh <CLUSTER_USER>@host`

**Rule:** every ssh / sshfs / scp command in this project must include
the username explicitly: `ssh <CLUSTER_USER>@<CLUSTER_HOST>`, not
`ssh <CLUSTER_HOST>`. Erik's local SSH config may resolve the
bare-host form, but copy-paste portability across sessions / machines /
agents requires the explicit form. Same convention for `scp`, `rsync`,
`sshfs`.

**Where it bit:** one of the mile-pebble pull commands at end-of-Thread-01
was provided without `<CLUSTER_USER>@`; Erik corrected.

**Target:** `style/shell.md`, `clusters.yaml` (ssh.user already explicit
there).

---

## L22 — Cell-size lower bound for small-strain Cij work

**Rule:** for elastic-constant work via small finite-strain
deformations, the per-side cell size should be **≥ 5× the largest pair
cutoff in play** (not just the bare PBC minimum of 2× cutoff). Stress
is computed as a sum of per-atom virials; per-atom stress noise
averages as 1/√N. A cell at the bare PBC minimum gives tiny absolute
volume changes at strain ε ~ 1e-3 → noisy stress signal → noisy Cij.
Linear-doubling beyond the bare minimum costs ~8× atoms but typically
runs in seconds on 1 core for clean cubic systems, and gives clean
stress-strain curves.

**Worked example (Ni-baseline walk, 2026-05-29):**
- EAM Pezold cutoff: 5.65 Å → bare minimum cell ≥ 11.3 Å per side
  (3×3×3 conventional fcc Ni ≈ 10.56 Å, right at the edge — unsafe).
- 5× cutoff target → ≥ 28.25 Å per side → 8×8×8 conventional Ni (28.16 Å)
  is the next-larger jump that hits the target.
- Walk chose 10×10×10 (35.2 Å, 7× MEAM cutoff, 6.2× EAM cutoff) as a
  cheap-enough margin-of-comfort step beyond 8×8×8.

**Where it bit:** initial proposal of 4×4×4 (14.08 Å, ~2.5× EAM cutoff)
was rejected by Erik specifically on stress-numerics grounds — the
4×4×4 box was technically PBC-safe but its volume change under ε=1e-3
strain is ~3 Å³ which is right at the per-atom-stress noise floor.

**Target:** `style/lammps.md` (cell-sizing decisions for Cij work),
`preferences.md` (Erik's "I like my boxes a bit bigger" default).

**Numbering note:** assigned L22 during 2026-05-30 recovery; original
walk-queue number unknown (likely in the L7–L13 range based on
chronology).

## L23 — Diff local potential files against claimed OpenKIM entries

**Rule:** when writing a `potential` provenance block in run.yaml
that claims a specific OpenKIM ID (or other potential repository
entry), byte-diff the local file against the archived version before
pinning the citation. Filename-based assumption ("the filename looks
like the Tehranchi-Curtin paper, so it must be that KIM model") is
not enough — files with the same lineage may diverge as local refits,
private parametrizations, or pre-archive versions.

**Workflow:**
1. Download the claimed KIM model: `curl -LO https://openkim.org/download/<extended-id>.txz`
2. Extract: `tar -xf <archive>.txz`
3. `diff <(find <extracted-dir> -name '*.eam.alloy' -exec cat {} \;) <local-file>`
4. If identical → pin the extended ID in `potential.openkim_extended_id`.
5. If different → set `openkim_id: null`, fill `source_provenance:`
   with whatever non-KIM origin you can identify (paper, lab page,
   email thread, "shared by colleague X").

**Where it bit:** the EAM file `ni_h_rcut4.90_rcut2.eam.alloy` was
initially assumed to be the Tehranchi-Curtin OpenKIM entry
MO_535504325462 based on filename + lineage. Diff against the two
extended-ID versions (_000 and _004) showed both KIM versions were
byte-identical to each other but *both diverged* from the local file
in the same places. → local Pezold refit, not in KIM. Cited as von
Pezold et al. (2011) doi:10.1016/j.actamat.2011.01.037 with
`openkim_id: null`. Without the diff, a wrong KIM ID would have
propagated into every future thread / archive record / publication
metadata using this potential.

**Target:** `learnings.md` Process section + `style/lammps.md`
(provenance workflow for new potentials).

**Numbering note:** assigned L23 during 2026-05-30 recovery; original
walk-queue number unknown.

## L24 — Fit one order higher than the target coefficient

**Rule:** when extracting a polynomial coefficient of order N from a
measured curve (e.g., the linear slope of σ vs ε, the quadratic
curvature of E vs ε), fit a polynomial of order **at least N+1**, not
just N. The smallness of the higher-order coefficient(s) is what
*verifies* that truncating at order N was valid. Without that fit,
ignoring higher orders is an unverified assumption.

**Where it surfaced:** Thread 02 design discussion (2026-05-31).
Initial proposal was straight-line fit for σ(ε) → Cij. Erik pushed
back: how do we know the linear approximation is good enough? Answer:
include the next order in the fit and report it. If the cubic
coefficient times ε_max² is small compared to the linear-coefficient
times ε_max, the linear extraction is justified by construction.

**Practical pattern:**

- Stress vs strain (target = linear C₁₁/C₁₂/C₄₄):
  fit σ = a₀ + a₁ε + a₂ε² + a₃ε³.
  Extract a₁ = Cij. Verify: a₀ ≈ 0 (residual stress), |a₂|·ε_max << |a₁|,
  |a₃|·ε_max² << |a₁|.

- Energy vs strain (target = quadratic, Cij = 2c/V):
  fit E = a + bε + cε² + dε³ + eε⁴.
  Extract c. Verify: b ≈ 0 (equilibrium), |d|·ε_max << |c|, |e|·ε_max² << |c|.

- Reported residuals also include the rms of the fit (cross-check
  against expected per-point noise from minimization tolerance).

**Consequence for data collection:** going one order higher in the fit
requires more points than the polynomial order would naively demand,
so the strain sweep needs enough magnitudes to over-determine the fit
(at least 2× the number of free parameters, ideally more).

**Target:** `learnings.md` Process section (when proposing analysis
plans for any extracted-coefficient measurement); `preferences.md`
(Erik's scientific-rigor defaults).

## L25 — LAMMPS `if` boolean expressions: string operands cannot contain operator characters

**Rule:** strings used as operands in a LAMMPS `if "expr"` Boolean
expression must not contain characters the math tokenizer interprets
as operators (`-`, `+`, `*`, `/`, `^`, etc.) or as expression
delimiters. The tokenizer splits on those before deciding
"numeric vs. string", so a string like `axial-xx` becomes the
expression `axial − xx`, which fails parsing because `axial`/`xx`
aren't numeric. Per LAMMPS docs: "If the operands have other
characters like a dash or comma, the parser will interpret them as
math operations." Quoting the operands inside the Boolean does NOT
fix it — the docs explicitly say "operands not enclosed in quotes
inside the Boolean expression."

**Where it bit:** Thread 02 strain-template input (2026-05-31).
Mode names `axial-xx`, `shear-yz` failed at
`if "${MODE} == axial-xx" then "jump SELF apply_axial_xx"` with
`ERROR: Invalid Boolean syntax in if command`. Whole sweep aborted at
the first lmp call.

**Fix:** use underscores (or pure alphanumerics) instead of hyphens
for any string that will appear as an `if` operand. Mode names became
`axial_xx`, `shear_yz`, `orthorhombic`.

**Pre-flight implication:** if an input uses `if "..." then ...` with
string operands, the pilot grep-checks the operand strings for
operator characters before Write. Lint rule candidate (not yet
mechanized): for each `if "..."` line in a LAMMPS input, extract the
operands and verify they're `[A-Za-z0-9_]+`.

**Target:** `style/lammps.md` §1 pre-flight (add a checklist item for
`if`-operand sanity).

## Convention going forward

- New lessons are appended with the next number. No renumbering.
- Each lesson states: rule (imperative), where it bit (incident),
  target file(s). Optional: lint pattern.
- Style files import the rule by reference (e.g., "see L4 in
  `lessons.md`"); they don't duplicate the prose. This keeps the queue
  as the single source of truth.
- Numbered placeholders (`[content lost between sessions]`) stay until
  either (a) the content is reconstructed or (b) we explicitly decide
  the slot can be retired (then we replace with `[retired]` rather than
  deleting, to keep numbering stable).

## L26 — Walltime estimation must come from observed steps/sec, not extrapolated wall-clock

**Rule:** Before sizing `--time` for any LAMMPS sbatch, the pilot must
have a *number for this kind of work on this cluster on this potential*.
Either:

  (a) the `Performance: N timesteps/s` line at the bottom of an
      apples-to-apples LAMMPS log (same potential family, same
      atom count, same fix stack, same cluster/partition), OR

  (b) a calibration sbatch: a short ~5000-step MD probe with the
      production fix stack but a token output cadence, ending at the
      Performance line. Then production walltime = total_steps /
      observed_rate, times a 1.5x safety buffer (or larger for the
      first time a potential is used in MD on this cluster).

Extrapolating from *minimization* wall-clock to *MD* wall-clock is not
estimation — minimization step rate has no relationship to MD step rate
(different work per iteration, different ratio of force evaluations to
integration, different cache pressure).

Padding `--time` is essentially free on cmmg's s.cmmg partition (no
node-hours billing for unused walltime). Under-budgeting costs a full
queue cycle + a re-mirror of stale output.

**Where it bit:** Thread 03 of ni-a0-cij-eam-meam (2026-05-31).
The pilot budgeted 45 min for the first MEAM MD run on cmmg (job
20285981, 142000 steps), having "estimated" by extrapolating MEAM
*minimization* wall-clock from Thread 02 (~50 sec total) and applying
a guess factor "MEAM ~3-5x slower than EAM". The job timed out at
step 22000 (end of NVT phase, before any NPT production). MEAM's
actual MD rate on cmmg was 12 timesteps/sec, so 142000 steps needed
~3.3 hours — 4x the budgeted 45 min. The NVT-MEAM-scan array (job
20285987, 5 tasks at 102000 steps each) inherited the same flawed
budget and all 5 tasks also timed out.

The EAM run with 30 min walltime (job 20285980) also timed out, but
just barely — at step ~127000 of 142000 (89% done, 7 of 10 production
blocks captured). Observed EAM rate was 70 timesteps/sec, so 142000
steps actually needed 34 min — 30 min was 12% short.

**Fix:** the first thing a pilot does when writing the slurm script
for any LAMMPS run that doesn't have a direct precedent is decide
whether (a) or (b) applies. If neither, the budget is "unknown — submit
a calibration probe first" and the production submit happens after the
probe returns.

**Pre-flight implication:** for every new sbatch, the pilot states *in
the response that proposes the submit command* either the Performance
line they're scaling from (with the prior job id), or "no precedent —
calibration sbatch first" as a separate action. No more "~10-20 min
estimated" without backing.

**Target:** add to `style/shell.md` (submit-script discipline section)
once that file gets a dedicated walltime/calibration block. Refer here
from `learnings.md` Submit-script discipline.

## L27 — LAMMPS log should not duplicate the dump-file thermo stream

**Rule:** When an input has a dedicated `fix print` (or any other
per-step / fine-grained thermo output stream) writing to a named
descriptive file, the LAMMPS log file (`log <descriptive>.log`) must
NOT carry a parallel thermo cadence inside the run loop. The log
keeps the LAMMPS-emitted run-start and run-end thermo blocks
(unsuppressable) plus the Performance / Loop time summary; everything
plotted with gnuplot/python/etc. comes from the dedicated dump file.

**Why:** the dedicated file is the canonical record. Duplicating it
into the log (a) bloats the log, (b) creates two sources of truth, and
(c) tempts downstream consumers to parse the log instead of the proper
file (which has a clean column header and uniform formatting).

**Mechanism:** LAMMPS does not provide a "suppress in-loop thermo"
switch. The closest is `thermo N` with N greater than the largest
`run` block in the input — that triggers thermo at run-start and
run-end only (those always fire). Pick N = total_step_count + 1, or
any safely-large number like `thermo 1000000` if no single `run` is
that long.

The `thermo_style custom step temp ...` line can stay; it controls
what columns appear in the run-start / run-end blocks (useful as a
visual sanity-check on what each phase entered and exited).

**Where it bit:** Thread 03 NPT inputs (2026-05-31). The four .in
files had `thermo 1000` which wrote 142 thermo rows into the log per
run — duplicating data already in `*-thermo-per-step.dat` and
`*-thermo-block-avg.dat`. Erik flagged it as a violation of a
previously-agreed design ("output goes to named file, not doubled in
log").

**Pre-flight implication:** add to `style/lammps.md` §1: if `fix print`
or `fix ave/time` writes per-step or per-block thermo to a dedicated
file, the `thermo` cadence must be N > total_steps. Grep:

```
grep -E '^thermo +[0-9]+' <input>
```

Read each match — N must be larger than the sum of `run` block sizes
(or, equivalently, larger than the highest reasonable single-phase
step count). Reject if any `thermo N` with N < 1e5 in an MD input
that also has `fix print` writing to a file.

**Target:** `style/lammps.md` §1 — add as §1.11.

## L28 — `fix_modify ... format` is unsupported on `fix ave/time`

**Rule:** The `format` keyword of `fix_modify` works on these fix styles
only:

- `fix ave/atom`
- `fix ave/chunk`
- `fix ave/grid`
- `fix ave/histo`
- `fix ave/correlate`

It does NOT work on `fix ave/time`. Attempting it produces
`ERROR: Fix <ID> ave/time does not support fix_modify format command`
and aborts at parse time.

For `fix ave/time` output precision, accept the LAMMPS default `%g`
format (~6 sig figs; adequate for most aggregate-cell observables).
If finer control is needed, post-process the output file in Python /
awk after the run.

**Where it bit:** Thread 03 of `ni-a0-cij-eam-meam`, jobs
20285992/20285993/20285994 (2026-05-31). The pilot asserted
`fix_modify BLOCK_AVG format 2 "%.6f" format 3 "%.6f" ...` based on
the general fix_modify pattern; all three production jobs aborted at
parse time. Per-step file precision (via `$(quantity:%.Nf)` in
`fix print`) is unaffected; that syntax IS verified-working.

**Target:** mention in `style/lammps.md` precision-control section
when one is added; refer here.

## L29 — `fix ave/time` title1/title2 are literal strings (no substitution)

**Rule:** The `title1` and `title2` keyword arguments of
`fix ave/time` are written as-is to the output file with no variable
substitution. `${var}` and `$(...)` appear LITERALLY in the .dat
header.

To include dynamic context in the header, EITHER:

- hardcode the values in the title string (one .in file per
  potential, so the label is known at write time), OR
- include the context in the output FILE NAME via a
  `variable BLOCK_AVG_FILE string ...` that itself contains
  `${VAR}` text — the `file` keyword's argument DOES expand.

**Where it bit:** Thread 03 of `ni-a0-cij-eam-meam`, attempt-2
EAM-scan output (2026-05-31). Pilot's `title1` had
`${POTENTIAL_LABEL}` and `${EPS_SCAN}` which appeared literally in
the .dat header. From LAMMPS docs: *"These strings are written as-is
to the file with no substitution."*

**Target:** style/lammps.md note when output-section is added.

## L30 — Standalone `print` does not support `$(quantity:format)`

> **Correction (2026-06-01, see L31):** the rule below is partly
> mis-diagnosed. The discriminator is NOT "standalone `print` vs
> `fix print`" — it is the *conversion specifier*. `$(quantity:%d)`
> fails in BOTH `print` and `fix print` because LAMMPS evaluates the
> expression as a double and `%d` is an integer conversion. The
> float-format cases (`$(temp:%.3f)` etc.) work fine in `fix print`.
> See L31 for the corrected rule; keep L30 for the incident record.

**Rule:** The `$(quantity:format)` runtime-evaluation-with-format
syntax is supported by `fix print` but NOT by the standalone `print`
command. Using it produces:
`ERROR: Incorrect conversion in format string (src/input.cpp:649)`
and aborts at parse time.

In `print`, use:

- `$(quantity)` — bare, LAMMPS chooses default format, OR
- `${var}` — parse-time substitution for known-at-parse-time
  string/equal variables, OR
- a fully static string with no `$(...)` and no `%` characters.

The `%` character in a `print` argument is treated as the start of
a printf-style format conversion; if there's no matching argument,
parse fails. This is a separate failure mode from the
`$(quantity:format)` one and bites even on otherwise-static strings
that happen to contain `%`.

**Where it bit:** Thread 03 of `ni-a0-cij-eam-meam`, attempt-3 probe
jobs 20286003/20286004/20286005 (2026-05-31). Phase-boundary marker
prints in the probe .in used
`print "PROBE marker: end of phase 1 (NVE) at step $(step:%d)"`
and all three probes aborted at the first such line. The probe
pattern (proposed by Erik) caught it at ~30 sec cluster cost —
exactly its purpose. Bug source: same "felt natural, wasn't
verified" failure mode as L28.

**Pre-flight implication:** when writing `print` statements in a
LAMMPS input, grep the argument for `%` characters. If any present,
read the LAMMPS `print` docs to confirm the intent works without
arguments to match. Safest: keep `print` arguments fully static
strings unless dynamic content is essential.

**Target:** add to `style/lammps.md` §1 as a pre-flight check on
`print` argument strings.

## L31 — In `$(...)` evaluation, integer/`%d` conversions are invalid; values are doubles

**Rule:** LAMMPS immediate/deferred expression evaluation, `$(expr)` or
`$(expr:format)`, evaluates `expr` as a **double-precision float**. The
optional `:format` must therefore be a floating-point conversion
(`%g`, `%e`, `%f` and width/precision variants). An **integer
conversion (`%d`, also `%i`, `%u`, `%x`) is rejected** with:
`ERROR: Incorrect conversion in format string (src/input.cpp:649)`.

This applies to BOTH `print` and `fix print` — it is a property of the
`$(...)` evaluator, not of the surrounding command. (This corrects L30,
which attributed the failure to "standalone `print`": the actual
discriminator is the conversion specifier.)

For an integer-valued quantity like `step`, use one of:

- `$(step)` — bare; default format is `%.20g`, which prints integral
  doubles exactly (`0`, `10`, `120000` — no decimal point, no precision
  loss for step counts in the normal range), OR
- `$(step:%.0f)` — explicit fixed-point with no fractional digits.

Float quantities are unaffected: `$(temp:%.3f)`, `$(lx:%.6f)` etc. are
correct and were never the problem.

**Where it bit:** Thread 03 of `ni-a0-cij-eam-meam`, 4th failed probe
attempt — jobs 20286009 (01 NPT-EAM), 20286010 (02 NPT-MEAM),
20286011 (04 NVT-scan-MEAM), 2026-05-31 21:37 cluster time. After L30's
fix made the standalone phase-marker `print`s static, the
`fix STEP_LOG all print 1 "$(step:%d) $(temp:%.3f) ..."` line still
carried `$(step:%d)`. All three probes loaded modules, read data,
pre-strained, created velocities, built the neighbor list, then aborted
at the first integration step (`run ${NVE_STEPS}`) when `fix print`
first evaluated the string. The error points at the `run` line because
`$(...)` inside a `fix print` quoted string is evaluated per-invocation
during the run, not at parse time. This 4th attempt was not recorded in
thread.md at the time; discovered 2026-06-01 by reading the probe logs.

**Fix applied:** `$(step:%d)` → `$(step)` in the six Thread-03 inputs
that get re-run (01/02/04, each `.in` + `.probe.in`). Run 03's `.in`
retains the latent bug but is out of scope (its data was collected
2026-05-31 16:46 from a pre-bug version; not being re-run).

**Pre-flight implication:** grep every LAMMPS input for `%d`/`%i`/`%x`
inside any `$(...)` (in `print` OR `fix print`); replace with `$(q)` or
a float format. Combined lint with L30: for each `print`/`fix print`,
scan the argument for (a) `$(...:%[diuxX])` → reject, (b) bare `%` not
inside a `$(...)` → reject (L30).

**Target:** `style/lammps.md` §1 pre-flight — fold into a single
`$(...)`/`%`-in-print check alongside L30.
