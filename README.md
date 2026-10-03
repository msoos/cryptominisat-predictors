# CryptoMiniSat predictor models

The xgboost model the `FINAL_PREDICTOR=ON` build of CryptoMiniSat embeds
as its default: `predictor_<tier>.json` for each tier in cmake's
`PRED_TIERS` (default `disc`). Clone this into `src/predict/` of the
solver; the build reads them from there.

The models must match the solver's feature list,
`scripts/crystal/best_features.txt`: same features, same order. Retrain
and commit here whenever that list changes. How they are made is in
`scripts/crystal/CLAUDE.md` of the solver.

Each model carries its provenance as xgboost attributes (`train_frame`,
`train_rows`, `train_date`, `gathered_by` = the solver commit that
gathered the data) and the 1st/99th percentile of every feature in its
training data (`feature_lo`/`feature_hi`); the solver prints the former
at load and counts values outside the latter. `python3 -c 'import
json,sys; print(json.load(open(sys.argv[1]))["learner"]["attributes"])'
predictor_disc.json` shows them.

Current model: `predictor_disc.json`, trained 2026-10-03 on 14 UNSAT SAT
Competition 2020 instances of 14 families, 20000 rows per use stratum
per instance (`FIXED=20000`), the stats build of solver commit fb16b3a4c
(it dumps the cost and activity columns, which the model does not use).
Label: every future use of the clause discounted by its distance
(halving every 30k conflicts), learnt as the clause's rank among the
clauses of its reduce (`TIERS=disc TARGET=rel`), 40 trees of depth 5,
feature list `best_features.txt` as of solver commit b5d6c352c: the
24 scale-free features (nothing that grows with the run length or the
instance size, see the solver's `scripts/crystal/CLAUDE.md`). The
three-horizon models (`predictor_{short,long,forever}.json`) are in this
repo's history if `--predtiers short,long,forever` is wanted.
