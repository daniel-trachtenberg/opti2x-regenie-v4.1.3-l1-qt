# Reproduce F-L1-QT QUALIFIED candidate

```bash
ROOT=/workspace/opti2x/runs/regenie-v4.1.3
PIN=5ff08954c5ada4c8fd037a8d38cbb57acbf0dd01

# Worktree from clean pin
cd $ROOT/source
git worktree add --detach /tmp/l1qt_wt $PIN
$ROOT/experiments/_helpers/seed_objs.sh /tmp/l1qt_wt
rm -f /tmp/l1qt_wt/src/Step1_Models.o
patch -p1 -d /tmp/l1qt_wt < $ROOT/deliverable-l1-qt/optimized.patch
$ROOT/experiments/_helpers/build.sh /tmp/l1qt_wt

# Serial timing + oracle (N=8 smoke)
$ROOT/experiments/_helpers/bench_l1.sh smoke /tmp/l1qt_wt/regenie /tmp/l1qt_bench 8 4.015

# Formal N=30 paired (uses stack_winners path by default — point CAND_BIN if needed)
$ROOT/experiments/_helpers/cert_paired_l1_qt.sh 30 202609291
```

Envelope always: `taskset -c 0` + `--threads 1` + OMP/MKL/OPENBLAS/GOTO=1.
