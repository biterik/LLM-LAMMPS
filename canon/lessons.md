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

## L32 — `count(group,ID)` second argument is a REGION-ID, not an atom type

**Rule:** In LAMMPS equal-style variables, `count(group,ID)`'s second
argument is a **region-ID**, not an atom type. To count atoms of a
type, define a static group by type after the atoms exist
(`group NI_ATOMS type 1`) and use `count(NI_ATOMS)`.

Runtime failure mode: `ERROR: Region <ID> in variable formula does not
exist` at **first evaluation** — the parse passes, so only a run or a
print catches it. This is a fail-late bug class: a lint on the input
text alone will not flag it.

**Where it bit:** 2026-07-28, `ni-h-phase-diagram-eam-meam` thread 01
anchor jobs 21570524/21570525 (inputs staged 2026-06-30, first actual
run). All four `relax_*.in` used `variable n_Ni equal count(all,1)` /
`count(all,2)`. The minimizations completed; the results epilogue
aborted at the first `${n_Ni}` print. Class-fixed in all four inputs,
both trees, same evening.

**Target:** `style/lammps.md` §1 pre-flight — for every `count(` in an
equal-style variable, verify the second argument (if present) names a
defined region; if the intent is per-type counting, require the
group-by-type idiom.

## L33 — Thermo keyword in a variable requires that keyword in `thermo_style custom`

**Rule:** An equal-style variable referencing an energy thermo keyword
(`pe`, etc.) that is evaluated by a fix (e.g. `fix ave/time ... v_pe`)
requires that keyword to appear in `thermo_style custom` — otherwise
LAMMPS aborts at the first `run` with
`ERROR: Thermo keyword pe in variable requires thermo to use/init
potential energy`.

A large `thermo N` cadence (L27) does NOT conflict with this: the
keyword only needs to be **in the style**, not printed often. L27's
"suppress in-loop thermo via huge N" and L33's "keyword must be in the
style" compose cleanly.

**Where it bit:** 2026-07-28, `ni-h-phase-diagram-eam-meam`, probe jobs
21570516/17/18: all three isotherm inputs carried `variable pe equal
pe` + ave/time streams (the SCIENCE-KICKOFF §6.2 suggested addition,
never previously run) with `thermo_style custom` lacking `pe`. All
three probes aborted at `run ${equil}` in ~8 s — the probe rule caught
it before 41 queued production tasks inherited it. Fixed by adding
`pe` to `thermo_style` in all three inputs, both trees.

**Target:** `style/lammps.md` §1 pre-flight — for every thermo keyword
referenced inside an equal-style variable that a fix consumes, check it
appears in `thermo_style custom`.

## L34 — Analysis windows are derived, not hardcoded; truncation is not non-convergence

**Rule:** Any averaging/summary window in an analysis script or
notebook is computed from the run's OWN last written step (e.g. the
final 30 % of the post-equilibration window), never from a hardcoded
step number — so the same code is correct for a 20k-step probe and a
400k-step production run. And whether a point counts as equilibrated is
decided by a **drift test on the observable** over that window, never
by whether the job reached its nominal step count. A job killed by the
walltime whose observable is flat IS a usable data point; a job that
finished cleanly whose observable is still climbing is NOT.

Corollary on the drift test itself: judge drift against the
**detrended** residual scatter, floored by the relevant counting noise.
Comparing drift to the raw block scatter is circular — a drifting point
scatters *because* it drifts, and the inflated floor then excuses the
drift. Comparing drift to the value itself is also wrong: on a dilute
branch carried by a few atoms, counting noise alone is a
several-percent effect and would condemn well-converged points.

**Where it bit:** 2026-07-30, Ni-H thread 03. The v2 notebook hardcoded
`step > 14000` as the averaging window, correct for the 20 000-step
runs it was written against. The next wave contained a 400 000-step run
(gcmc-matched) and a run truncated at 26 120 of 80 000 steps
(tet-extended). Applied unchanged, the hardcoded window would have
averaged the last 6 % of one run and read the other as "unusable". In
fact the truncated run was fully converged (drift -2.3e-3 per 1e5
attempts against a 1.5e-2 noise floor) and produced the thread's
cleanest number, x = 2.514 — correcting an earlier 2.403 that had
itself passed v2's circular check while still climbing.

**Target:** `learnings.md` Process (analysis-plan discipline) refers
here; any future analysis-script template derives its window from the
data extent.

## L35 — Match the MC cadence, not the MC budget

**Rule:** Any comparison of two MC move sets embedded in MD fixes the
same **attempts per MD step** for both, and preferably the same total
step count too, so the two runs are comparable step-for-step and
attempt-for-attempt with the move type as the only difference. Matching
totals while letting cadence differ silently varies the amount of MD
relaxation per attempt — which is itself a variable. State in the run
record which quantity is matched.

Corollary: a cadence mismatch is harmless for equilibrium observables
(cadence changes the rate of convergence, not the equilibrium
distribution) and fatal for cost/efficiency observables and for any
quantity read off points that never equilibrated. Before quoting a cost
ratio, check the cadence line of both inputs, not the budget arithmetic
in the run notes.

**Where it bit:** 2026-07-29/30, Ni-H thread 03. Run 03 was designed as
a "matched budget" comparison: `fix gcmc 100 250` (2.5 attempts/MD
step) x 400k steps = 1e6 attempts, against `fix mc/sites 20 1000`
(50 attempts/MD step) x 20k steps = 1e6 attempts. Equal totals, but a
factor 20 difference in cadence — the gcmc run gave the lattice 20x
longer to relax between exchange attempts, which flatters
random-insertion acceptance, and left the MD-step axis meaningless
across methods. Erik caught it reading the two inputs side by side:
"To be able to have a proper comparison, they should have the same
number of attempts per the same number of MD steps!" The 2026-07-30
wave fixed the class: gcmc cadence changed to `fix gcmc 20 1000` to
match `fix mc/sites 20 1000`.

**Target:** `learnings.md` "Analysis plan before data-collection plan"
cross-ref; design-time check whenever two MC fixes are compared.

## L36 -- s.cmmg's DefMemPerCPU caps a nomultithread job at 243 tasks; use `--mem=0`

**Rule:** `s.cmmg` sets `DefMemPerCPU=1500` (MB). A cmmg node has
`RealMemory = 730000 MB` (TRES mem=68437.50G over 96 nodes). With
`--hint=nomultithread` Slurm allocates WHOLE CORES, so the allocated-CPU
count is **2 x ntasks**, and the implied default memory request is
`2 * ntasks * 1500 MB`. Therefore on s.cmmg, without an explicit memory
request:

    max allocated CPUs = floor(730000 / 1500) = 486
    max tasks (nomultithread)                = 243

A 256-task nomultithread job implies 512 x 1500 MB = 768000 MB > 730000 MB
and is rejected at submit time -- by CONFIGURATION, so it never queues:

```
sbatch: error: Batch job submission failed: Requested topology configuration is not available
sbatch --test-only ... : allocation failure: Requested node configuration is not available
```

**Fix:** add `#SBATCH --mem=0` (request all memory on the node). Then the
full 256 tasks / 512 CPUs place fine on s.cmmg. `p.cmmg` is unaffected
because it sets `DefMemPerNode=UNLIMITED`, not a per-CPU default.

**Bisect that established it** (2026-08-03, `sbatch --test-only`, which
validates and reports placement without queueing anything -- free):

| request on s.cmmg unless noted | result |
|---|---|
| `-N 1 --ntasks=256 --hint=nomultithread` | FAIL |
| `--ntasks=256 --hint=nomultithread` (no -N) | FAIL -> **-N was NOT the cause** |
| `--ntasks=256 --hint=nomultithread --mem=0` | PASS, 512 processors |
| `--ntasks=256 --hint=multithread` | PASS, 256 processors (2 tasks/core) |
| `--ntasks=243 --hint=nomultithread` | PASS, 486 processors -- the predicted break-even, exact |
| `--ntasks=256 --hint=nomultithread` on p.cmmg | PASS, 512 processors |

**Corollary -- ONE WHOLE NODE IS ALLOWED ON s.cmmg.** `MaxNodes=1` with
`MaxCPUsPerNode=UNLIMITED`. An earlier version of this lesson claimed
whole-node jobs belong in p.cmmg; that was WRONG and is withdrawn
(Erik, 2026-08-03: "ONE WHOLE NODE IS STILL ON CMMG.s"). The partition is
not the discriminator -- the memory default is.

**Corollary -- queue behaviour.** A 512-CPU request occupies the whole
node either way, so s.cmmg + `--mem=0` and p.cmmg reported the SAME
predicted start (same node, same time). A 256-CPU request (e.g. 128 tasks
nomultithread, or 256 tasks multithread) backfills onto a shared node much
sooner. If a short calibration job is waiting a long time, halving the
rank count is the lever -- but prefer p.cmmg for anything whose PURPOSE is
a timing measurement, since an exclusive node has no noisy neighbours.

**Where it bit:** 2026-08-03, `ni-h-at-dislocs-eam-meam` thread 01, first
probe of the 0 K H binding map. Rejected before queueing; no slot lost.

**Meta-lesson (the expensive one):** the first diagnosis changed THREE
parameters at once (`-N 1`, task count, partition), picked the difference
that fit a story, and wrote it into canon as a rule. It was wrong. When a
submit fails, change ONE thing at a time -- and `sbatch --test-only`
makes that bisect free, so there is no excuse for guessing.

**Target:** `clusters.yaml` cmmg block (`hardware.real_memory_per_node_MB`,
s.cmmg `memory:` note, quirk `s_cmmg_defmempercpu_caps_nomultithread_tasks`).

## L37 -- never `reset_timestep` after defining a gcmc-like fix

**Rule:** `fix mc/sites`, `fix gcmc` and `fix deposit` arm their first MC
block at creation time (`next_reneighbor = step + 1`). `reset_timestep`
does NOT remap a fix's reneighbour schedule, so a `reset_timestep` issued
*after* the fix is defined pushes that first block past the end of the
run and silently disarms the fix. Symptom: a clean-looking run with
`Msites = 0`, `natt = 0` and zero neighbour builds -- no error, no
warning, no MC.

Either reset the timestep BEFORE defining the fix, or re-define
(`unfix` / `fix`) the fix afterwards to re-arm it.

**Where it bit:** found 2026-07-27 during the mc/sites Viper porting work
(`in.mcmd_bench_nih`), where it also invalidated an earlier
"Msites = 0 under -sf kk" diagnosis -- that had been read as a Kokkos bug
and was at least confounded by this input bug. Recorded in
`canon/local/reference/cluster-lammps-info-legacy.txt`; promoted to a
numbered lesson 2026-08-03 because the pattern is one `jump SELF`
production loop away from biting again: the EAM-DISLOCS-Ni-Cu
`in_sgcmc_d90_*` chunked-restart template calls `reset_timestep` on every
chunk, and that template is an obvious thing to copy for an mc/sites run.

**Pre-flight implication:** for any input containing `fix mc/sites`,
`fix gcmc` or `fix deposit`, grep for `reset_timestep` and confirm every
occurrence precedes the fix definition:

```
grep -nE '^(reset_timestep|fix .*(mc/sites|gcmc|deposit))' <input>
```

Read the line order. A `reset_timestep` after the fix line is a reject.

**Target:** `style/lammps.md` sec 1 pre-flight.

## L38 -- group-scoped fixes that keep global counters are not group-safe

**Rule:** before applying any LAMMPS fix that maintains a global counter,
concentration or collective constraint (`fix sgcmc variance`, gcmc/widom-family
bookkeeping) to a group other than `all`, verify from the docs -- and, if the
docs are silent, from the source -- that the subgroup case is actually
implemented consistently. "Parses cleanly and echoes its parameters" is not
evidence that it is. The signature to look for in the source is mismatched
scopes: group-filtered counts compared against `atom->natoms`-scaled targets.

**Where it bit:** EAM-DISLOCS-Ni-Cu, VCD90-T300-c031 v1-v5 (2026-04-08..13).
`fix interior sgcmc ... variance kappa target` silently produced wrong physics:
`fix_sgcmc.cpp` counts species over the fix group but scales the
variance-constraint targets by `atom->natoms`, so with a frozen pure-Ni
boundary excluded from the group the ~27k boundary Ni atoms read as a Ni
deficit and the outer variance-constraint test vetoed every Ni->Cu swap
(e^-19 already at kappa=200). Five run campaigns and a kappa sweep chased
"kappa too weak" for a bug no kappa can cure. The doc page gives no warning
and every doc example uses group `all`. Root cause confirmed at source level
2026-08-02, thread 02_VCSGC-VARIANCE-CONSTRAINT-GROUP-BUG. Worth filing
upstream.

**Fail-late class.** The input parses, the fix echoes its parameters, and the
error appears only as physically wrong equilibria. No lint can catch it; the
check belongs in design-time review whenever `fix <ID> <group>` has
`<group> != all` and the fix doc mentions concentrations or targets.

**Target:** `style/lammps.md` sec 1 pre-flight cross-ref.

## L39 -- subtract the right background SHAPE; a good fit is not a mechanism

**Rule:** before calling anything a segregation, binding or excess energy, ask
what the cell's boundary conditions and defect content *require* the background
to look like, and check that prediction against the raw geometry -- atoms per
layer, layer spacings, per-atom energy and stress profiles -- not against how
well a polynomial fits. Prefer a **non-parametric background** (the median of
the same site class at the same coordinate, far from the defect) whenever the
data supports one: it assumes no shape and therefore cannot invent one.

Two specific traps:

- **A straight line through the inner part of a saturating step fits
  beautifully and means nothing.** The tell is the residual: if the fit
  residual is comparable to the effect you intend to measure next, the model
  has failed -- it is not "within tolerance".
- **A flat per-atom energy does NOT mean zero strain.** Elastic energy is
  quadratic in strain, so a +-eps step is invisible in the energy while being
  fully visible in any quantity linear in strain (interstitial insertion
  energies, layer spacings, per-atom stress). Read the stress or the geometry,
  not the energy, when asking whether a cell is strained.

And when the operator says a boundary condition forbids what you just
reported, believe the boundary condition first and go back to the dump files.

**Where it bit:** 2026-08-04, ni-h-at-dislocs-eam-meam thread 01, the 0 K H
insertion map on a d90 edge dislocation (`boundary p p s`, three fixed (111)
layers per z face). `E_ins(z)` rose monotonically across the 140 A map zone;
a straight-line fit gave +32.7 meV (oct) / +43.4 meV (tet) with residual rms
1.18 / 1.54 meV, written up as "plastic bending of a free-standing slab".
Erik rejected it on the boundary conditions alone, and the dumps settled it:
per-atom Ni energy flat to 0.05 meV over 140 A (no bending energy); 4646 atoms
per (111) layer above the glide plane against 4600 below, exactly +1.000 %
against `b/lx = 0.995 %` (the extra half-plane, a STRAIN STEP); interlayer
spacing 2.03174 A below vs 2.03725 A above, each flat to 1e-5 A. The true
profile is odd and saturating, `A(1-exp(-d/lambda))` with `2A = 39.1 meV`,
`lambda = 53.0 A`, residual rms 0.079 meV -- fifteen times better. The
non-parametric background took far-field flatness from 1-2 meV of leftover
structure to 0.15-0.34 meV and the 2 meV reach from unmeasurable to r = 102 A.
The headline trap depth moved 0.4 meV, so the conclusion never depended on it
-- but the tail analysis did, and the wrong mechanism sat in three files and a
notebook for a day.

**Target:** analysis / post-processing review, before any background
subtraction.

## L40 -- `group ID clear` and `group ID delete` both require the group to EXIST

**Rule:** `clear` and `delete` are both valid `group` styles -- `delete` removes
the named group, `clear` un-assigns all its atoms while keeping the group. Both
operate on an EXISTING group, so either one placed at the top of a `jump SELF`
loop fails on the FIRST pass, before the group has ever been defined. Pre-create
the group before the `label` (a `group ID type N` with no atoms of type N yet
creates it empty), or -- usually better -- drop the reset entirely: after
`delete_atoms group ID compress no` the group holds no live atoms, so the next
`group ID type N` re-selects exactly the current members.

**Where it bit:** 2026-08-05, ni-h-at-dislocs-eam-meam thread 01 run 01
(`relax-Hbind_Ni-disloc-d90-Pezold-EAM-0K.in` line 193, `group HGRP clear`).
Job 21638800 died on the FIRST iteration of the site loop -- "ERROR: Could not
find group clear group ID HGRP (src/group.cpp:137)" -- all 256 ranks exit 1
after 56 s. Found 2026-08-20, fifteen days later, because nothing reconciled
the handed-over submission (see session-startup step 1b, added the same day).
Note what the error message is actually saying: the parser ACCEPTED `clear` as
a style and then failed the group lookup. `src/group.cpp:137` is the lookup,
not a keyword table.

**Lesson generalization -- the one that matters here.** The first post-mortem
of this bug (2026-08-20, same session) asserted that `clear` was an invented
keyword and proposed a closed-vocabulary lint to catch it. That diagnosis was
WRONG, and checking `docs.lammps.org/group.html` before writing the lint is
what caught it. The real class is harder and no vocabulary check would have
touched it: **a command can be spelled correctly and still be invalid in the
state the script has put the system in.** Doc-checking a command means reading
its preconditions -- does this require the group / fix / compute / region to
already exist, does it require an existing box, is it legal during minimize --
not just confirming the keyword is in the syntax block. See `style/lammps.md`
1.9. Two prior lessons are the same shape: L37 (`reset_timestep` after a
gcmc-like fix silently disarms it) and the `region ... INF` requires an
existing box note from the 2026-08-06 hydride-cycle session.

**Target:** `style/lammps.md` 1.9 (preconditions clause).


## L41 -- a periodic axis with a vacuum gap connects the two slab faces

**Rule:** a free surface facing vacuum across a PERIODIC axis is not a free
surface. Whatever leaves one face re-enters at the other. If a slab needs a
genuine free surface, make the axis non-periodic and add a wall
(`boundary p p f` + `fix wall/reflect zhi EDGE`); a `fix evaporate` cleanup is
not a substitute. If a cleanup fix is used anyway, its region must span the
WHOLE gap and its interval must be short against the transit time of the
lightest species across it.

**Where it bit:** 2026-08-20, ni-h-hydride-cycle-eam thread 01, all four
production runs (21774658 / 21774659, 4 nodes each, 14-65 h per task). Erik had
specified a solid wall and no periodicity in z. The inputs carry
`boundary p p p` -- confirmed in the trajectory headers as `pp pp pp` -- and no
`fix wall/reflect` anywhere. In its place:

```
region VAC  block INF INF INF INF $(131.0*v_A0) INF units box
fix EVAP MOBILE evaporate 10000 100 VAC ${RSEED}
```

Inadequate on its own numbers: the vacuum gap is 424.4-477.5 A (53 A) while VAC
covers only z > 463.3 A, the top 14 A of it; and H at 300 K crosses 53 A in
~2 ps against a 10 ps (10000-step) check interval, so a desorbed atom typically
crosses the boundary about five times before the cleanup fix looks once.

Measured in the fixlat rate-A final configuration -- H per 20 A z-bin:

```
z   0- 20 A :      64 H   <-- bottom face, on the FROZEN layers
z  20-360 A :       0 H
z 360-380 A :    1814 H
z 380-400 A :    7038 H
z 400-420 A :    7141 H
z 420-440 A :    8923 H
```

The MC zone is z = 375..456 A; nothing was ever inserted below 375 A, and the
340 A of slab between 20 and 360 A holds exactly zero H. Those 64 atoms did not
diffuse down -- they wrapped through z from the vacuum above the top surface
onto the rigid bottom face. **The empty middle is the proof:** transport would
have left a trail, teleportation does not. 0.26 % of the H at that instant, but
the channel is open all run, it deposits H on an artificial surface, and it
drains the top surface region, biasing degassing to look faster than it is.

**The process failure is the larger one.** The deviation from Erik's stated
design appears nowhere -- not in the input header, thread.md, project.md or the
RESTART-BRIEF. It surfaced only because Erik remembered what he had asked for,
two weeks and four production runs later. See the "deviation is a reportable
event" rule in learnings.md, Thread design.

**Target:** `style/lammps.md` 1.13.

## L42 — A cluster's project name is not a hostname; resolve placeholders, never guess them

*(Renumbered L41 -> L42 on 2026-08-25 by session 2026-08-25-1105-sim-status:
two lessons had been assigned L41 on 2026-08-20 and 2026-08-24. The
vacuum-gap lesson above keeps L41 -- it is cited as L41 from learnings.md
and older proposals; citations to THIS lesson in preferences.md and
session-startup.md now read L42. SESSIONS.md entries written before the
renumbering still say L41 and are left as historical record.)*

**Rule:** every ssh / scp / rsync / sshfs target in a hand-off carries the
REAL user and host, resolved from `canon/local/clusters.local.yaml`. The keys
of `canon/clusters.yaml` (`cmmg`, `raven`, `viper`) are this project's NAMES
for clusters, not hosts. `<CLUSTER_USER>` and `<CLUSTER_HOST>` in the public
canon are POINTERS to the local overlay, not values, and must never appear in
— or be guessed into — a command Erik is asked to paste.

Corollary, and the other half of the same failure: **every command block states
which machine it runs on, as the first thing in the block.** Crossing machines
means a new numbered block with a new tag.

**Where it bit:** 2026-08-24, session 2026-08-24-0753-status-readout handed over
the ni-melting-point-eam thread 01 staging command as
`rsync ... cmmg:/cmmc/ptmp/<CLUSTER_USER>/...`, and tagged none of the four blocks with
a machine. `cmmg` resolves only through an alias in Erik's local ssh config; the
target is `<CLUSTER_USER>@<CLUSTER_HOST>`. Erik corrected both.

**Why the existing rules did not catch it.** They were both already written —
preferences.md "Command hand-offs" has said *"State where the block runs (which
machine/shell)"* since 2026-07-28, and L21 has required the explicit user since
Thread 01. The gap was upstream of them: `canon/session-startup.md` step 0(b)
asked only whether `canon/local/` EXISTS. A session could pass the environment
gate having never opened `clusters.local.yaml`, then meet a `<CLUSTER_HOST>`
placeholder in the public `clusters.yaml` with nothing on hand but memory. The
rule about the output existed; the step that supplies the input did not.

**Fixed by:** new startup step 0(b2) — resolve `ssh.user` / `ssh.host` /
`scratch` and echo them in the startup brief, so the identity is in context
BEFORE any hand-off is written; the two clauses added to preferences.md
"Command hand-offs"; and `canon/templates/lint-handoff.sh`, which fails on an
unresolved placeholder, a bare-host target, or a command block with no machine
tag.

**Generalizes to:** any identity-scrubbed field in the public canon. If a value
in `canon/` looks like `<SOMETHING>`, it is a lookup instruction. Filling it
from memory is the bug, even when the memory happens to be right.


## L43 -- event detectors are lagging indicators; guard-band the block averages

**Rule:** when a run-level failure (collapse, melt, dislocation escape, box
instability) is detected at step S by a threshold crossing, invalidate every
block-averaged row whose averaging window ENDS within one window-length of S,
not merely rows at or after S. Derive the window length from the data (median
step spacing of the averaged stream), never hardcode it. A detector's
threshold marks when the event became *undeniable*, not when it began;
anything averaged over a window touching that boundary is contaminated.

**Where it bit:** 2026-08-05, ni-h-phase-diagram thread 03 run 06
(mu = -2.02). `find_collapse_step` fires when a_eff falls 1 % below its
running maximum; it fired at step 58060 -- one step past the run's last
2000-step block average at 58000. Without the guard band the task passed
every convergence test and would have published x = 0.9986 +- 0.0036 from a
window that had already sagged from 1.00000 to 0.98846; with it, the window
is dropped and the task is correctly flagged crashed.

**Target:** analysis / harvest code that post-processes time series.
(Merged 2026-08-25 from inbox 2026-08-05-1100.)

## L44 -- `${}` never nests inside a `$(...)` formula; formula variables are `v_name`

**Rule:** `${name}` (parse-time substitution) and `$(...)` (equal-style
formula) are two mechanisms that must never nest. The parser does not
substitute `${}` inside an extracted formula, and `$` is invalid formula
syntax, so `$(ly/${L})` dies with `ERROR: Invalid syntax in variable
formula (src/variable.cpp)`. Double quotes additionally SUPPRESS parse-time
substitution (the variable doc names this mistake verbatim), so in a quoted
`print` string the formula is evaluated late, by `print` itself, and hits
the literal `${}` there. Inside any formula, reference variables only as
`v_name`; an index-style `-var` variable works when its string is numeric:
`$(ly/v_L)`.

**Hard gate** (style/lammps.md 1.16, in lint-lammps-input.sh): grep
`\$\([^)]*\$\{` on every input must return nothing.

**Where it bit:** 2026-08-24, ni-melting-point-eam thread 01, probe job
22719499: FAILED after 18 s -- a clean stage-1 NPT, then the first `print`
died on `$(ly/${L})`. Five instances across the two inputs. The existing
checks (1.1 runtime quantities need `$()`; 1.8 every `${VAR}` defined)
both passed: neither constrains WHERE `${}` may appear. Cost ~23 h
undiagnosed as an open loop plus one wasted submission.

**Target:** every LAMMPS input; the style walk before every hand-over.
(Merged 2026-08-25 from inbox 2026-08-25-1545.)

## L45 -- deleting an ID does not delete its references; the error defers to the next init

**Rule:** `uncompute` / `unfix` remove the compute or fix, not the places
that still reference it. `thermo_style custom ... c_ID/f_ID` holds such a
reference, and it is checked at SYSTEM INIT -- the next `run`, `minimize`,
`write_data`, `write_restart`, or `rerun` -- not when the ID is deleted, so
the input dies far from the line that broke it
(`ERROR: Could not find thermo compute with ID ...`, src/thermo.cpp).
Before any `uncompute`/`unfix`, retarget every consumer that references the
ID: reset `thermo_style` to a line without it, and check still-active fixes
(`ave/time` on `c_ID`), dumps, and variables the same way. The safe stage
teardown order is: (1) unfix integrators/thermostats, (2) reset
`thermo_style`, (3) uncompute.

**Hard gate** (style/lammps.md 1.17, in lint-lammps-input.sh): after
joining `&` continuations, an `uncompute`/`unfix` of an ID still referenced
by the active `thermo_style`, followed by any init-triggering command
before a `thermo_style` reset that drops the reference, fails the lint.
Consumers other than thermo (fixes, dumps, variables) remain a manual walk.

**Where it bit:** 2026-08-25, ni-melting-point-eam thread 01, prepare probe
attempt 2: stages 1-2 ran clean, then `write_data` died on the stage-2
`thermo_style ... c_TEMP_HOT` after `uncompute TEMP_HOT`. Second wasted
submission on the same input; found by the probe, invisible to every
line-local check because all three commands are individually correct --
only their ORDER is wrong. Same failure shape as L44: a reference that was
valid when written and invalid when evaluated. Line-local checks cannot see
lifetimes; this gate simulates the one lifetime interaction (thermo) that
is mechanically decidable.

**Target:** every LAMMPS input with staged teardowns; the style walk.

## L46 -- a gate's reference must be the same quantity, and its denominator must be stated

**Rule:** two failures of the same gate, both measured 2026-08-26 in
ni-h-at-dislocs-eam-meam thread 02 run 01, hours apart.

(a) **Reference.** A gate's reference value must be the SAME QUANTITY the gate
computes, located in the source data before use -- region, averaging window and
definition confirmed, and the location recorded in the gate's comment. A number
quoted in prose is a pointer to a measurement, not the measurement. Diagnostic:
a gate that passes at a large fraction of its own tolerance has failed to
investigate; a correct gate on correct data passes by orders of magnitude,
because it compares a thing to itself.

(b) **Denominator.** A gate that quantifies a population must state the
denominator it covers. "N of the layers are fine" is not a gate until it says N
of how many. Any object the computation touches but the gate does not count is
the bug -- a silent fallback (`dict.get` with a default, a clamp, an
`except: pass`) is invisible to every gate that inspects only the objects that
took the normal path.

**Where it bit:** (a) GATE 4 required the recomputed far-field background to
reproduce thread 01's measured rigid strain step, -24.32 meV. It computed the
step between the EXTREME (111) planes of the +-70 A map zone, got -27.37 meV,
and passed a 4 meV tolerance by 3.05 meV. The background was in fact exact --
plane by plane it agreed with thread 01's published per-site map to 0.0005 meV
over all 99 octahedral z-planes. The two values thread 01 quoted as its
far-field pair sat at z = 76.0 and z = 180.0 A, ~50 and ~53 A from the glide
plane (about one decay length lambda = 53 A), NOT at the +-68.5 A zone edges.
Two different quantities, differing by exactly what the loose tolerance
absorbed. Evaluated at the planes the reference was measured at, the gate
agrees to 0.007 meV and its tolerance drops from 4 meV to 0.5 meV.
(b) The same run looked its background up BY BIN with a silent fallback. GATE 3
asked "does every background LAYER have enough far-field sites for a median"
and truthfully answered yes -- for the 69 layers that HAD any. It could not see
the 30 bins that had NONE, because those never entered the background dict to
be counted. 13.1 % of octahedral sites got a background wrong by a mean of
13.6 meV and up to 27.2 meV, concentrated on exactly the near-core sites the
thread is about, with all seven gates green. What caught it was not a gate: it
was noticing that 99 z-bins in the output profile and 69 background layers in
the gate describe the same thing and disagree.

Cost in both cases: nothing, because both were investigated before the
production submission. That is the point.

**Target:** every gate block. `learnings.md`, "A gate is a mechanism, not a
sign". Not lintable -- it is a review step on the probe-gate checklist.

## L47 -- yield is the stress maximum, not a deviation from a tangent line

**Rule:** in a defect-free cell loaded to nucleation, the onset of plasticity is
the stress MAXIMUM followed by a load drop -- never "the strain at which the
curve leaves the linear fit by more than TOL". Report sigma_max, the strain at
it, and the magnitude of the drop. A deviation-from-linearity number, if
reported at all, is labelled elastic softening, not yield.

**Where it bit:** 2026-08-27, ni-h-hydride-cycle-eam thread 02. A 0.05 GPa
deviation tolerance reported "first plasticity at eps = 1.15 %, sigma =
2.38 GPa". The actual nucleation is at eps = 5.25 %, sigma = 6.22 GPa with a
5.74 GPa drop -- wrong by 4.6x in strain and 2.6x in stress. What the tolerance
found was the nonlinear elasticity of nickel: at several percent strain the
moduli soften visibly and any fixed tolerance against a tangent line fires on
that first. The error is not conservative and it is not visible in the number.

**Corollary, worth its own line:** look at the FIGURE before quoting the number
it was made from. Both errors in that thread (this one and the vacuum-diluted
stress, `preferences.md` "Stress in a cell containing vacuum") survived the
summary file and died on first sight of the plot.

**Target:** any loading curve; the harvest step of a mechanical-test thread.

## L48 -- LAMMPS echoes each command to the log before that command's output

**Rule:** LAMMPS writes every input command into the log VERBATIM, ahead of the
output it produces. A regex harvesting a printed quantity therefore matches the
COMMAND TEXT first, complete with its unevaluated `$(...)`, and only later the
number. Any log-parsing harvest takes the first match that parses as the
expected type, or anchors past the echo -- never a bare `re.search(...)` whose
result is trusted.

**Where it bit:** 2026-08-28, ni-h-at-dislocs-eam-meam thread 04. The
minimizer bake-off harvest read its convergence tolerance with
`re.search(r"ftol_loose_global_eV_per_A = (\S+)", log)` and got the string
`$(v_FTOL_LOOSE:%.6e)` from the echoed `print` command. It failed loudly
(`ValueError` on `float()`), which is the lucky case: the dangerous version is
a quantity whose command text happens to parse -- a hardcoded literal in the
command, say -- where the harvest returns a plausible wrong number and nothing
raises.

**Target:** every `.log`-parsing harvest script. `style/lammps.md` 1.20.


## L49 -- a frame walker's wrong answer is a valid file; index it against a count you got elsewhere

**Rule:** Any code that walks a trajectory frame by frame -- an extractor, a
decimator, a per-frame analysis loop -- is validated against a frame count
obtained INDEPENDENTLY of the walker before it is used or handed over. Two
fixtures, both cheap, both needed:

1. a synthetic file with a handful of frames at known timesteps and the REAL
   column set, written by the test itself;
2. a real file of the same family, whose expected frame count comes from the
   submit script's dump cadence and step bounds -- not from the walker.

Give the walker a `--list` mode that prints `N frames, M atoms, steps a .. b`
and writes a step/offset index, and run it FIRST on any new trajectory: one
sequential read, it makes the tool state its understanding of the file out loud,
and the index turns every later cut into a seek instead of a scan.

**And: a stated expectation is part of the code.** If the hand-off, the submit
script's comment, or the chat says what the output will contain, that sentence
must be true of the code as written, not as intended. Documented behaviour the
code does not have is a bug in the code.

**Why this is not covered by "test the composition not the piece"** (learnings.md,
merged 2026-08-29): that rule works because the callee has guards that refuse bad
input loudly. A frame walker has no callee and no guard. Its output is a
well-formed dump whichever answer it gave, so the only defence is a case whose
right answer was known before the code ran.

**Where it bit:** 2026-08-29, ni-h-hydride-cycle-eam, writing an extractor to cut
OVITO-sized subsets out of the 7-25 GB cycle trajectories. Twice, in one tool.

- The header parser consumed TEN lines of the nine-line LAMMPS dump header,
  eating the first atom of every frame; the caller then skipped `natoms` body
  lines, ran one line into the next header, and resynchronised only at the frame
  after -- so it silently kept **every other frame**. Nothing looked wrong:
  columns all present, timesteps monotonic and evenly spaced. `--list` on a real
  dump reported "23 frames, steps 0 .. 88000", entirely plausible, and it was 23
  of the 45 that were there. The synthetic fixture is what exposed it: four
  frames written, two found.
- The `--steps` path never appended the final frame, while the submit script's
  comment and the hand-off both promised "the phase boundaries + the last frame,
  always". Job 22738636 wrote 5 frames per run instead of 6 and dropped step
  4300000 -- thread 03 fixlat's last state, 37k steps before the `mc/sites`
  abort, the most interesting frame in the run. Five frames is a plausible
  number; only the promise made it visibly wrong. Cost: one re-submit.

**Generalises to:** anything parsing a self-delimiting record format by counting
lines -- XDATCAR, xyz frames, multi-frame CFG, and the per-atom blocks inside a
single dump frame.

**Target:** `style/shell.md` and any per-frame analysis driver. See also
learnings.md "test the composition not the piece" and L42.
