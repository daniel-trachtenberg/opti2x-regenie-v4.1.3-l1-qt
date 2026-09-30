# Wave 1 SUMMARY — F-L1-QT (Step1 Level-1 QT)

**Date:** 2026-09-29 PT  
**Pin:** `5ff08954c5ada4c8fd037a8d38cbb57acbf0dd01` (clean; **NO H1 L0 patch**)  
**Contract:** `regenie-v4.1.3-serial-eigen-step1-l1-qt-n2000-m10842-b50-v1`  
**Formal baseline median (N=30):** 4.015 s (`benchmarks/dev_fixture_l1_qt/baseline.json`)  
**Session remasure (N=8):** 3.915 s  
**Envelope:** `--threads 1`, `OMP/MKL/OPENBLAS/GOTO_NUM_THREADS=1`, `taskset -c 0`  
**Oracle:** `fit_1.loco` byte-identical; `fit_pred.list` basename-normalized  
**Do NOT cite H1 L0 3.14× as L1 evidence.**

---

## Results table

| ID | Status | Median (N) | vs formal 4.015 | vs session 3.915 | Oracle | NLWP | Patch |
|----|--------|------------|----------------:|-----------------:|--------|------|-------|
| Session baseline | remasure | 3.915 s (8) | 1.03× | 1.00× | PASS | 1 | clean pin |
| **H-L1-1** LLT/τ | **ACCEPT** | **2.100 s (8)** confirm | **1.91×** | **1.86×** | **PASS** | 1 | `HL1_1_llt_per_tau/` |
| **H-L1-2** rankUpdate Gram | **ACCEPT** (marginal) | 3.755 s (8) | 1.07× | 1.04× | PASS | 1 | `HL1_2_fuse_gram/` |
| **H-L1-3** noalias GEMV | **REJECT** | 3.915 s (8) | 1.03× | 1.00× | PASS | 1 | `HL1_3_fuse_gemv/` |
| **Stack H-L1-1+2** | **ACCEPT** | **1.715 s (8)** confirm | **2.34×** | **2.28×** | **PASS** | 1 | `stack_winners/` |

Binary SHAs:

| build | sha256 |
|-------|--------|
| baseline (clean) | `4bd69197dead7415860b4e066cffb0d7a974fa3f655076fd130921131383d3d7` |
| H-L1-1 | `556ab9347b0ff7111f1faae0a8a9ab892ad9615586cb6ea05260697ac30f6c73` |
| H-L1-2 | `ecfd51a480c8290233348d43fcd42bee0d6428a79a630c2a05d4ea95f39c02d2` |
| H-L1-3 | `019cfacf990f3c3849dea5dcac94e3aaab6a181ebe022eae4e170c791c4e4939` |
| stack H-L1-1+2 | `c070782674ac0cee63205073939588ec328cab346d9fd3b37db405d52c20b873` |

---

## Per-hypothesis notes

### H-L1-1 — LLT per τ — ACCEPT (primary algorithmic win)
- Replace one `SelfAdjointEigenSolver(X1)` + diagonal `(D+τ)⁻¹` with 5× `LLT.solve` of `(X1+τI)`.
- In-place diagonal mutate avoids P×P copies between τ.
- L1 internal chrono: ~3271 ms → ~1287 ms (~2.5× on L1 cluster).
- Confirm median **2.10 s → 1.91×** vs formal (alone **below** 2× wall).
- Oracle **byte-identical** on F-L1-QT (same surprise as L0 path but **re-measured**, not transferred).

### H-L1-2 — selfadjoint rankUpdate Gram — ACCEPT (stack contributor)
- Fold `WᵀW` via `selfadjointView<Lower>().rankUpdate(Wᵀ)` + mirror upper.
- Alone ~1.07× vs formal / ~1.04× vs session (noise-gate marginal).
- Stacks cleanly with H-L1-1 (GEMM residual after eig kill).

### H-L1-3 — noalias on eig τ-apply / OOS GEMV — REJECT
- `noalias` on `VtX2`, `beta_l1`, `p1` — no measurable wall win (~1.00× vs session).
- Micro-opt retained optionally in stack `p1.noalias()` (negligible).

### Stack H-L1-1 + H-L1-2 — ACCEPT → formal cert
- Confirm median **1.715 s → 2.34×** vs formal 4.015.
- L1 chrono median ~1056 ms.
- Light `p1.noalias()` included; not credited as a separate win.

---

## Formal N=30 paired certification

Harness: `experiments/_helpers/cert_paired_l1_qt.sh` (seed 202609291).  
Evidence: `benchmarks/certification_l1_qt/{stats,certification_l1_qt}.json`.

| Metric | Value |
|--------|------:|
| Parent (baseline) median | 3.970 s |
| Child (stack) median | 1.735 s |
| **Median speedup (medians)** | **2.288×** |
| Median paired speedup | 2.293× |
| Min paired speedup | 2.169× |
| Child faster | **30/30** |
| **One-sided 95% Student-t lower bound** | **2.271×** |
| Oracle all pairs | PASS |
| NLWP max | 1 |

Gates: median ≥2 ✓, lower_95 ≥2 ✓, oracle ✓, NLWP=1 ✓, adversarial ACCEPT ✓.

---

## Terminal status: **QUALIFIED**

Fixture-scoped only (F-L1-QT). Deliverable: `deliverable-l1-qt/`.  
**No public git push.** Do not credit H1 L0.

---

## Residual / next

Post-stack residual wall ~1.7 s; L1 still ~1.05 s (Gram + τ-LLT + OOS + read_l0). Further L1 micro-opts unlikely to move package claim.  
**F-S2-QT:** prior smoke ~0.39 s ≪ 1 s — proceed to grow Step2 fixture separately; L1 claim is independent.
