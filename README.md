# CryptoMiniSat predictor models

The xgboost model the `FINAL_PREDICTOR=ON` build of CryptoMiniSat embeds
as its default: `predictor_disc.json`. Clone this into `src/predict/`
of the solver; the build reads it from there.

The model must match the solver's feature list,
`scripts/crystal/best_features.txt`: same features, same order. Retrain
and commit here whenever that list changes. How it is made is in
`scripts/crystal/CLAUDE.md` of the solver.

The model carries its provenance as xgboost attributes (`train_frame`,
`train_rows`, `train_date`, `gathered_by` = the solver commit that
gathered the data) and the 1st/99th percentile of every feature in its
training data (`feature_lo`/`feature_hi`); the solver prints the former
at load and counts values outside the latter. `python3 -c 'import
json,sys; print(json.load(open(sys.argv[1]))["learner"]["attributes"])'
predictor_disc.json` shows them.

Current model: `predictor_disc.json`, trained 2026-10-07 on 21 UNSAT
instances of 19 families (SAT Competition 2020 and SAT Race 2019),
gathered twice: once with glue driving the reduce (stats build of solver
commit aca2c1959) and once with the model of that first round driving
it (19 of the 21, commit 2cbb20943). 20000 rows per sampling cell per
instance (`FIXED=20000`). A ranker (`rank:ndcg`, `XGB_OBJ=rank`): the
order of the clauses within a reduce, learnt from whether a clause is
used in the trimmed proof in the next 8 reduces. 40 trees of depth 5,
feature list `best_features.txt`: the 24 scale-free features. It orders
the reduce candidates only. On 9 held-out instances, 3 seeds, `--xor
0`: 101.6% [92.5, 111.3] of the normal build's conflicts.
