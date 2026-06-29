# LAMMPS capabilities — cmmg, lammps/250722

Captured 2026-05-31 from `lmp -h` (run via
`I_MPI_HYDRA_BOOTSTRAP=fork mpirun -n 1 lmp -h` on login node — see
quirk below; for routine diagnostics, use sbatch).

**Use this file as the cache** when checking whether a specific
package or style is available. Refresh when the cluster's lammps/250722
module is rebuilt, or when adding a new module version.

---

## Identity

- **Binary**: `/mpcdf/soft/SLE_15/packages/skylake/lammps/intel_2025.2-2025.2.0-impi_2021.16-2021.16.0/22Jul2025/bin/lmp`
- **Version banner**: `Large-scale Atomic/Molecular Massively Parallel Simulator - 22 Jul 2025 - Update 3`
- **Compiler**: Intel LLVM C++ 202502.0 / Intel oneAPI DPC++/C++ 2025.2.0 (OpenMP 5.1, C++17)
- **MPI**: Intel MPI 2021.16 (MPI v4.1)
- **Companions in `bin/`**: `lmp`, `msi2lmp`. **No `lmp_serial`** — only the MPI-built binary.

## Build flags

```
-DLAMMPS_GZIP
-DLAMMPS_SMALLBIG
sizeof(smallint): 32-bit
sizeof(imageint): 32-bit
sizeof(tagint):   32-bit
sizeof(bigint):   64-bit
```

## Accelerator + FFT

- OPENMP package: API OpenMP, double precision, OpenMP 5.1
- INTEL package: API OpenMP, single/mixed/double precision, SIMD enabled
- FFT: double precision, mpiFFT engine, MKL with threads

## Compression formats supported

`.gz` (gzip), `.bz2` (bzip2), `.zst` (zstd), `.xz` (xz), `.lzma` (xz).

## Installed packages

```
AMOEBA ASPHERE BOCS BODY BPM BROWNIAN CG-DNA CG-SPICA CLASS2 COLLOID COLVARS
COMPRESS CORESHELL DIELECTRIC DIFFRACTION DIPOLE DPD-BASIC DPD-MESO DPD-REACT
DPD-SMOOTH DRUDE EFF ELECTRODE EXTRA-COMMAND EXTRA-COMPUTE EXTRA-DUMP
EXTRA-FIX EXTRA-MOLECULE EXTRA-PAIR FEP GRANULAR INTEL INTERLAYER KIM KSPACE
LEPTON MACHDYN MANYBODY MC MEAM MESONT MISC ML-IAP ML-PACE ML-POD ML-RANN
ML-SNAP ML-UF3 MOFFF MOLECULE OPENMP OPT ORIENT PERI PHONON PLUGIN PLUMED
POEMS QEQ REACTION REAXFF REPLICA RHEO RIGID SHOCK SPH SPIN SRD TALLY UEF
VORONOI YAFF
```

**Highlights for the current project (Ni a₀ / Cij with EAM + MEAM):**

- `MANYBODY` — provides `pair_style eam`, `eam/alloy`, `eam/fs`, etc.
- `MEAM` — provides `pair_style meam` (note: bare name, no `/c` suffix; the legacy `meam/c` alias is also listed in the styles).
- `EXTRA-COMPUTE` — provides `compute born/matrix` (the option-(b) candidate for Cij; confirmed built).
- `KIM` — OpenKIM interface available (relevant for the Ko-Shim-Lee MEAM, which has an OpenKIM short-ID).
- `KSPACE` — long-range solvers (not needed for these short-cutoff metals, but available).
- `PHONON` — phonon analysis tools (potential follow-on direction).
- `PLUMED`, `COLVARS` — bias-collective-variable engines (out of scope here, but archived).
- `INTEL`, `OPENMP`, `OPT` — accelerator suffixes available (e.g., `eam/alloy/intel`, `eam/alloy/omp`, `eam/alloy/opt`).

## Style availability — captured snippets

Full enumeration was truncated mid-listing on the original capture; the
sections below are partial. Refresh by re-running an `lmp -h`-capture
sbatch and replacing this section in full if a future question depends
on a style not listed.

### Atom styles (full)

```
amoeba angle atomic body bond bpm/sphere charge dielectric dipole dpd edpd
electron ellipsoid full hybrid line mdpd molecular oxdna peri rheo rheo/thermal
smd sph sphere spin tdpd template tri
```

### Integrate styles (full)

```
respa respa/omp verlet verlet/lrt/intel verlet/split
```

### Minimize styles (full)

```
cg fire/old fire hftn quickmin sd spin spin/cg spin/lbfgs
```

Per **L10** + **L12** (style/lammps.md rules cross-referenced): use `cg`
(or `hftn` / `sd`) with `fix box/relax`; `fire`/`quickmin` are
incompatible with box/relax.

### Pair styles — relevant subset (full capture truncated)

Confirmed present in the partial listing:

- EAM family: `eam`, `eam/alloy`, `eam/alloy/intel`, `eam/alloy/omp`,
  `eam/alloy/opt`, `eam/cd`, `eam/cd/old`, `eam/fs`, `eam/fs/intel`,
  `eam/fs/omp`, `eam/fs/opt`, `eam/he`, `eam/intel`, `eam/omp`, `eam/opt`.
- MEAM family: capture truncated before reaching `meam` proper; the
  package is installed (see above), so `pair_style meam` is available.
  The legacy alias `meam/c` is visible at the top of the truncated
  block (LAMMPS still ships it as a backward-compat name).
- Born / Buckingham / Coul variants: extensive (full block captured).

If a specific pair style needs verification before use, re-run the
`lmp -h` capture sbatch and grep.

### Computes — not yet captured

`compute born/matrix` is available (EXTRA-COMPUTE built). For other
computes, re-run an `lmp -h` capture and grep.

---

## Cluster quirk: lmp on login node

This LAMMPS build calls `MPI_Init()` before processing `-h`. On the
cmmg login node there is no PMI server, so a bare `lmp -h` aborts
with `PMI2_Job_GetId returned 14`.

Workarounds, in order of preference:

1. **sbatch (preferred — Erik's standing rule, 2026-05-31).** Run
   any LAMMPS invocation, including diagnostics, inside a slurm job.
2. `I_MPI_HYDRA_BOOTSTRAP=fork mpirun -n 1 lmp -h` — works on the
   login node but should be used only for one-off, time-pressed cases.
   Do not normalize.

Same rule applies to all LAMMPS module versions on cmmg unless a
serial binary is added.

## When to refresh this file

- After a cluster module rebuild that changes lammps/250722.
- When introducing a new module version (e.g., lammps/241119 capture).
- When a pair / compute / fix / dump style needs verification and isn't
  in the captured snippets above — then re-run the capture sbatch in
  full, replace this file's style sections.
