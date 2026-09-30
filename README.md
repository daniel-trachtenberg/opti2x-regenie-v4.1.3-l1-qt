# Opti2x optimized regenie v4.1.3 — Level-1 QT

This repository contains an Opti2x-optimized regenie **v4.1.3** source tree for the **Level-1 quantitative-trait (L1 QT)** fixture.

**Status: QUALIFIED.** The H-L1-1 + H-L1-2 stack in `ridge_level_1` measured a **2.288× median speedup** with a **2.271× one-sided 95% lower bound** over **N=30** paired runs.

## Claim scope

The claim is **fixture-scoped**, not package-wide: a serial, Eigen-only Step 1 Level-1 QT fixture with **N=2000**, **M=10842**, and **`--bsize 50`** (→ 1085 L1 columns), under one-thread controls. The credited changes are **H-L1-1** (LLT per τ) and **H-L1-2** (rankUpdate Gram) in `ridge_level_1` only.

Baseline is the **CLEAN upstream pin** (no H1 Level-0 patch). This package does **not** re-claim the separate L0 H1 ~3.14× result.

This is **not** a package-wide regenie claim, **not** a Step2 claim, **not** a `-march=native` claim, and **not** an MKL/OpenBLAS claim.

## Upstream pin

Upstream: <https://github.com/rgcgithub/regenie>  
Pin: `5ff08954c5ada4c8fd037a8d38cbb57acbf0dd01` (`v4.1.3`)

Related (separate claim): [opti2x-regenie-v4.1.3](https://github.com/daniel-trachtenberg/opti2x-regenie-v4.1.3) — L0 H1 QUALIFIED (~3.14×), fixture-scoped; **not** credited here.

## Build

Eigen-only build (no MKL/OpenBLAS and no `-march=native`):

```bash
make BGEN_PATH=/path/to/bgen/v1.1.7 HAS_BOOST_IOSTREAM=1 -j"$(nproc)"
```

The packaged source intentionally excludes compiled binaries and object/archive files. See `opti2x/REPRODUCE.md` for the pinned patch, fixture contract, correctness oracle, and timing procedure.

## Evidence

- `opti2x/OPTIMIZATION_REPORT.md` — qualification report and limitations
- `opti2x/CLAIM_SCOPE.md` — explicit claim boundaries
- `opti2x/REPRODUCE.md` — build and certification procedure
- `opti2x/certification.json`, `opti2x/attestation.json`, `opti2x/stats.json` — certification evidence
- `opti2x/ADVERSARIAL.md` — adversarial review (ACCEPT)
- `opti2x/WAVE1_SUMMARY.md` — Wave 1 slate summary
- `opti2x/optimized.patch` — H-L1-1 + H-L1-2 combined patch
