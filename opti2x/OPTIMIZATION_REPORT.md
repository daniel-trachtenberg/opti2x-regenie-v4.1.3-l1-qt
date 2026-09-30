# Opti2x Wave 1 report — regenie v4.1.3 F-L1-QT

## Terminal status: **QUALIFIED**

**Contract:** `regenie-v4.1.3-serial-eigen-step1-l1-qt-n2000-m10842-b50-v1`  
**Fixture revision:** 1  
**Candidate:** `experiments/l1_qt/stack_winners/` (H-L1-1 + H-L1-2)  
**Pushable worktree:** `/workspace/opti2x/runs/regenie-v4.1.3/experiments/l1_qt/stack_winners/worktree/`  
**When packaged (PT):** 2026-09-29 ~4:50 PM PT  

Fixture-scoped claim only. See `CLAIM_SCOPE.md`.  
**Do NOT cite H1 L0 3.14×.** Clean-pin baseline only.

---

## Speedup + CI (N=30 paired vs clean baseline)

Evidence: `benchmarks/certification_l1_qt/{stats,certification_l1_qt}.json`  
Harness: `experiments/_helpers/cert_paired_l1_qt.sh` (warmup + NLWP + 30 randomized pairs, seed 202609291).

| Metric | Value |
|--------|------:|
| Parent (baseline) median wall | 3.970 s |
| Child (stack) median wall | 1.735 s |
| **Median speedup (medians)** | **2.288×** |
| Median paired speedup | 2.293× |
| Child faster | **30/30** |
| **One-sided 95% Student-t lower bound** | **2.271×** |

Gates: median ≥ 2.0 ✓, lower_95 ≥ 2.0 ✓, oracle PASS ✓, NLWP=1 ✓, adversarial ACCEPT ✓.

Formal frozen baseline median (prior N=30 solo): **4.015 s**. Paired parent median 3.970 s is consistent.

---

## Composition (credited)

| Layer | Hypothesis | Role |
|-------|------------|------|
| Wave1 | **H-L1-1** LLT per τ | Replace fold eigendecomp with Cholesky of `(X1+τI)` |
| Wave1 | **H-L1-2** rankUpdate Gram | Half-work selfadjoint fold `WᵀW` |

**Not credited:** H-L1-3 noalias (no win); L0-H1; `-march`/MKL/threads.

**Build:** Eigen-only `make` with `HAS_BOOST_IOSTREAM=1`, locked BGEN v1.1.7. Same CXXFLAGS as baseline.

---

## Adversarial review

Path: `research/ADVERSARIAL_L1_QT.md` — **ACCEPT for credit**.

- Algebraically equivalent ridge for τ>0; equivalent Gram via rankUpdate.
- Stress: F-L1-QT oracle ×3 PASS; example QT byte-identical; alt-seed byte-identical.
- No LOCO skip, τ-grid/fold/bsize change, thread bump, MKL, or ISA flag.

---

## Rejected / marginal (Wave1 ledger)

| ID | Decision | Why |
|----|----------|-----|
| H-L1-1 alone | ACCEPT but <2× wall | confirm 1.91× vs formal |
| H-L1-2 alone | marginal ACCEPT | ~1.07×; needed in stack |
| H-L1-3 | REJECT | ~1.00× vs session |

Full notes: `research/HYPOTHESES_l1_qt.md`, `experiments/l1_qt/SUMMARY.md`.

---

## Residual / F-S2-QT

Post-stack wall ~1.74 s; L1 chrono ~1.05 s remains (Gram + 5×LLT + OOS + lowmem read).  
Step2 smoke from L1 pred was ~0.39 s — **not** certifiable; grow M_test / BT+Firth for F-S2-QT next. L1 QUALIFIED does not block or substitute Step2.

---

## Limitations (explicit)

- Fixture-scoped (N=2000, M=10842, bsize=50).  
- QT K-fold `ridge_level_1` only; LOOCV/BT not certified here.  
- Byte-identical LOCO is empirical on exercised inputs, not a universal FP theorem.  
- No public push performed.
