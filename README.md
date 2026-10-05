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

Current model: `predictor_disc.json`, trained 2026-10-05 on 21 UNSAT
instances of 19 families (SAT Competition 2020 and SAT Race 2019), 20000
rows per sampling cell per instance (`FIXED=20000`), no row weights
(`XGB_WEIGHTS=none`), the stats build of solver commit aca2c1959 (every
tracked clause locked, auto dump ratio). Label: every future use of the
clause in the trimmed proof, halving every 4 reduces, learnt as the
clause's rank among the clauses of its reduce (`TIERS=disc
TARGET=rel`), 40 trees of depth 5, feature list `best_features.txt`:
the 24 scale-free features. On 9 held-out instances, 3 seeds, `--xor
0`: 110% of the normal build's conflicts and 118% of its time.
