# CryptoMiniSat predictor models

The xgboost models the `FINAL_PREDICTOR=ON` build of CryptoMiniSat embeds
as its defaults, one per horizon (10k / 30k / 120k conflicts). Clone this
into `src/predict/` of the solver; the build reads them from there.

The models must match the solver's feature list,
`scripts/crystal/best_features.txt`: same features, same order. Retrain
and commit here whenever that list changes. How they are made is in
`scripts/crystal/CLAUDE.md` of the solver.

Current models: trained 2026-09-30 on 14 UNSAT SAT Competition 2020
instances of 14 families, `TARGET=rel` (the clause's rank among the
clauses of its reduce), 40 trees of depth 5, feature list
`best_features.txt` as of solver commit cf378dcef.
