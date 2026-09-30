# Claim scope — F-L1-QT QUALIFIED

**Contract id:** `regenie-v4.1.3-serial-eigen-step1-l1-qt-n2000-m10842-b50-v1`  
**Fixture:** `benchmarks/dev_fixture_l1_qt` (N=2000, M=10842, `--bsize 50` → 1085 L1 cols)  
**Resource envelope:** `--threads 1` + `OMP/MKL/OPENBLAS/GOTO_NUM_THREADS=1` + `taskset -c 0`  
**Build:** Eigen-only Makefile, `HAS_BOOST_IOSTREAM=1`, locked BGEN; **same CXXFLAGS as baseline** (no `-march=native`, no MKL/OpenBLAS).  
**Baseline binary:** clean pin sha `4bd69197…d3d7` (**NO H1 L0 patch**).  
**Candidate:** H-L1-1 (LLT per τ) + H-L1-2 (rankUpdate Gram) in `ridge_level_1` only.

QUALIFIED means ≥2× on **this Level-1 fixture only** under the frozen serial Eigen-only envelope — **not** the prior L0 H1 fixture, **not** package-wide, **not** Step2.

**Forbidden credits:** H1 L0 3.14×; thread relaxation; MKL/`-march` as primary win.
