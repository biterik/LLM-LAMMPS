# LLM-LMPS — Style guide for shell commands

Pilot consults before any shell command Write. Rules here are the
materialization of relevant entries in `../lessons.md` plus standing
conventions.

---

## 1. SSH / SCP / SSHFS — always explicit user (L21)

**Every** ssh, scp, rsync, sshfs command in this project includes the
username explicitly:

```bash
ssh <CLUSTER_USER>@<CLUSTER_HOST> "<remote command>"
scp <CLUSTER_USER>@<CLUSTER_HOST>:<remote path> <local path>
sshfs <CLUSTER_USER>@<CLUSTER_HOST>:/cmmc/ptmp/<CLUSTER_USER> ~/cluster-mounts/cmmg
```

Never the bare-host form (`ssh <CLUSTER_HOST>…`). Erik's local SSH config may
resolve it, but every other context (this session, a different agent, a
different machine, a script copy-pasted into a notebook for sharing)
does not have that config. Explicit-user is portable.

For ssh that runs a long-lived command (mount, tunneling), include
`-o ServerAliveInterval=30 -o ServerAliveCountMax=3` to detect dead
connections promptly.

## 2. Compression — zstd default; `-19` for mile-pebble curation (L17)

`zstd` over `gzip` for everything in this project. Faster compress,
smaller ratio, much faster decompress, on the file types we care about
(LAMMPS `.data`, `.dump`, `.log`).

Defaults:

```bash
# In-flight / scratch — fast
zstd <file>             # level 3 default

# Mile-pebble curation (cluster → Mac archival) — max ratio
zstd -19 -c <file> > <file>.zst

# Streaming compress over ssh (for mile-pebble pulls)
ssh <CLUSTER_USER>@<CLUSTER_HOST> "zstd -19 -c <remote-path>" > <local-path>.zst
```

Decompress: `zstd -d <file>.zst` (or `unzstd <file>.zst`).

`.zst` extension always. Do not double-suffix (`.data.zst`, not
`.data.zst.zstd`).

## 3. Bash strictness — every submit script

Every submit script (and any non-trivial standalone shell script in
this project) starts with:

```bash
#!/bin/bash
set -euo pipefail
```

For submit scripts specifically (`learnings.md` Submit-script discipline):

```bash
module purge
module load <lammps/version>
cd "$SLURM_SUBMIT_DIR"

# Existence check for every referenced file
for f in <input.in> <potential files...> <data files...>; do
  [[ -r "$f" ]] || { echo "Missing: $f" >&2; exit 1; }
done

LMP_BIN="${LMP_BIN:-lmp}"
srun "$LMP_BIN" -in <input.in> -screen none
```

`--job-name` is descriptive (matches the run-dir slug, no
`job1`/`test`). `--reservation=Erik` only when Erik says "urgent".

## 4. Rules from lessons.md

- **L11** — `module purge` before any `module load` (deterministic env).
- **L16** — `set -euo pipefail` + file-existence pre-flight in every submit script.
- **L17** — zstd default; `-19` for mile-pebble curation (§2).
- **L21** — always explicit SSH user (§1).

(No remaining placeholder slots — L11 and L16 reconstructed from
transcript on 2026-05-30, see lessons.md numbering notes.)

## 5. Standing conventions (not lessons)

### Path quoting

Quote variables in paths: `cd "$SLURM_SUBMIT_DIR"`, not `cd
$SLURM_SUBMIT_DIR`. Spaces in user-folder paths (e.g.,
`~/Library/Application Support/…`) break unquoted expansion.

### Heredoc for multi-line remote commands

```bash
ssh <CLUSTER_USER>@<CLUSTER_HOST> bash <<'EOF'
  cd /cmmc/ptmp/<CLUSTER_USER>/Ni-A0-CIJ-EAM-MEAM
  ls -la
EOF
```

Single-quoted `'EOF'` prevents local shell from expanding `$variables`
before sending.

### No bare `rm -rf` on cluster paths

Cluster cleanup is a separate, explicit operation. Pilot proposes; Erik
runs. Never embedded inside a chained `&&` sequence.
