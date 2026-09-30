# Adversarial review — F-L1-QT stack (H-L1-1 + H-L1-2)

**Date:** 2026-09-29 PT  
**Candidate:** `experiments/l1_qt/stack_winners/`  
**Binary sha256:** `c070782674ac0cee63205073939588ec328cab346d9fd3b37db405d52c20b873`  
**Baseline sha256:** `4bd69197dead7415860b4e066cffb0d7a974fa3f655076fd130921131383d3d7` (clean pin, **NO H1 L0**)  
**Pin:** `5ff08954c5ada4c8fd037a8d38cbb57acbf0dd01` (v4.1.3)  
**Patch:** single file `src/Step1_Models.cpp` inside `ridge_level_1` only  
**Verdict: ACCEPT for credit**

**Do NOT cite H1 L0 3.14×.** This is a separate Level-1 path / fixture.

---

## 1. Mathematical claim

### Baseline (per fold \(i\), `use_simple_ridge`)

\[
X1 = \sum_{k\neq i} W_k^\top W_k,\quad
X2 = \sum_{k\neq i} W_k^\top y_k
\]

One eigendecomposition \(X1 = V D V^\top\), then for each ridge \(\tau_j\):

\[
\hat\beta_j = V (D + \tau_j I)^{-1} V^\top X2
\]

Fold Grams formed as full `WᵢᵀWᵢ` GEMM.

### Stack

1. **H-L1-1:** \(\hat\beta_j = (X1 + \tau_j I)^{-1} X2\) via Eigen `LLT.solve` (in-place diag mutate).  
2. **H-L1-2:** fold Gram via `selfadjointView<Lower>().rankUpdate(Wᵀ)` + mirror upper (same SPD Gram, half triangle work).  
3. Optional `p1.noalias() = W_fold * beta` (numerically identical intent; not a separate credit).

### Equivalence

**Exact arithmetic:** \(V(D+\tau I)^{-1}V^\top = (X1+\tau I)^{-1}\) for symmetric \(X1\) and invertible \(D+\tau I\). RankUpdate forms the same Gram as `WᵀW` for real matrices.

**Floating point:** Different roundoff paths in general; **observed** byte-identical `fit_1.loco` on F-L1-QT, example QT, and alt-seed fixture.

**SPD:** Default τ grid remapped from fractions with strictly positive τ; \(X1+\tau I\) SPD for τ>0 → LLT valid.

---

## 2. Cheat / regression checklist

| Check | Result |
|-------|--------|
| Skipped LOCO / changed write path | **No** — only `ridge_level_1` solve + Gram formation |
| Changed τ / λ grid | **No** |
| Changed CV folds / LOOCV / bsize | **No** — fixture still `--bsize 50`, 5-fold |
| Float mode / `-march` | **No** — same CXXFLAGS as clean baseline |
| Thread / OpenMP / MKL | **No** — serial envelope; NLWP=1 |
| Oracle edits | **No** |
| Crediting L0-H1 | **No** — clean pin baseline; L1-only patch |
| Scope | Single file `Step1_Models.cpp` |

---

## 3. Stress evidence

Artifacts: `research/adversarial_l1_qt/` (`run_stress.sh`, `stress.log`, `stress_summary.txt`).

| Stress | Result |
|--------|--------|
| F-L1-QT vs frozen oracle ×3 | **PASS** — loco sha `fdcb019d…390a`; walls ≈1.75–1.90 s |
| `example/` QT: cand vs baseline | **PASS** — loco **byte-identical** |
| Alt-seed (N=400,M=736): cand vs baseline | **PASS** — loco **byte-identical** |
| Patch files | Only `src/Step1_Models.cpp` |

---

## 4. What this does *not* claim

- Not H1 L0 speedup; not package-wide 2× beyond F-L1-QT.  
- Not LOOCV / BT / Cox L1 paths.  
- Not that LLT equals eig bit-for-bit for all user τ configs — only estimator equivalence + observed primary outputs on exercised QT step1 inputs.  
- Not Step2 (F-S2-QT) qualification.

---

## 5. Verdict

**ACCEPT for credit** as the primary algorithmic stack for portable Eigen-only F-L1-QT QUALIFIED claim.

Rationale: algebraically equivalent ridge + equivalent Gram; no forbidden lever; oracle byte-identical on primary fixture ×3, example, alt-seed; NLWP=1; formal N=30 lower_95 ≈ 2.271× ≥ 2.
