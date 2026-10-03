# CryptoMiniSat predictor models

The xgboost model the `FINAL_PREDICTOR=ON` build of CryptoMiniSat embeds
as its default: `predictor_<tier>.json` for each tier in cmake's
`PRED_TIERS` (default `disc`). Clone this into `src/predict/` of the
solver; the build reads them from there.

The models must match the solver's feature list,
`scripts/crystal/best_features.txt`: same features, same order. Retrain
and commit here whenever that list changes. How they are made is in
`scripts/crystal/CLAUDE.md` of the solver.

Current model: `predictor_disc.json`, trained 2026-10-03 on 14 UNSAT SAT
Competition 2020 instances of 14 families, 20000 rows per use stratum
per instance (`FIXED=20000`), the stats build of solver commit 0616917e9
(it dumps the cost and activity columns, which the model does not use).
Label: every future use of the clause discounted by its distance
(halving every 30k conflicts), learnt as the clause's rank among the
clauses of its reduce (`TIERS=disc TARGET=rel`), 40 trees of depth 5,
feature list `best_features.txt` as of solver commit b5d6c352c: the
24 scale-free features (nothing that grows with the run length or the
instance size, see the solver's `scripts/crystal/CLAUDE.md`). The
three-horizon models (`predictor_{short,long,forever}.json`) are in this
repo's history if `--predtiers short,long,forever` is wanted.
