# LAYERSWEEP-01 — screen stage analysis

Scope: the `screen` stage only (24 combos × seed 42). **The confirm stage was deliberately not
started.** No `sweep.json`, no `configs/` file, and no other study artifact was modified.

| | |
| --- | --- |
| Executed commit | `cd3171f64f6b82a562c0c0eb088860abbcb4eeae` (verified equal on controller and pod) |
| Stage | `screen`, seed 42, 24 combos, 24/24 `succeeded`, 0 `failed` |
| Machine | RunPod Pod `f0fthdemq21l6m`, 1× NVIDIA L4 (SECURE, US-MO-2), $0.49/hr |
| Wall clock | 8,525 s (2 h 22 m) for the stage, 13:38 → 15:59 UTC 2026-10-06 |
| Per-run wall | min 1,869 s · median 2,080 s · max 2,367 s · sum 49,846 s (≈5.85× effective concurrency) |
| Supervisor | exactly one, detached (`setsid nohup`); never a second |
| Ranking metric | peak `val_acc_all` on **validation**; test columns are context only |
| Evidence | pod + controller `sweep_state/` (`runs.jsonl`, `logs/`, `identity/`, `SWEEP.json`), `outputs/<combo>/{checkpoints,results}`, MLflow experiment `taskrelation-mtrl-layersweep` (24 runs) |

## 1. Screen table (ranked by validation)

| rank | combo | group | k | layers kept | val_acc_all | test_opt_acc_all | test_opt_er (leaky) |
|---:|---|---|---:|---|---:|---:|---:|
| 1 | `oddalt-k2` | odd alternate dropping | 2 | 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,22,24 | 0.9744 | 0.9758 | 0.7884 |
| 2 | `bottom-k6` | bottom layer dropping | 6 | 6-24 | 0.9740 | 0.9763 | 0.7794 |
| 3 | `sym-k2` | symmetric dropping | 2 | 0,1,2,3,4,5,6,7,8,9,10,11,14,15,16,17,18,19,20,21,22,23,24 | 0.9736 | 0.9721 | 0.7866 |
| 4 | `oddalt-k8` | odd alternate dropping | 8 | 0,1,2,3,4,5,6,7,8,10,12,14,16,18,20,22,24 | 0.9736 | 0.9730 | 0.7631 |
| 5 | `oddalt-k6` | odd alternate dropping | 6 | 0,1,2,3,4,5,6,7,8,9,10,11,12,14,16,18,20,22,24 | 0.9735 | 0.9739 | 0.7667 |
| 6 | `evenalt-k4` | even alternate dropping | 4 | 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,19,21,23 | 0.9734 | 0.9751 | 0.7848 |
| 7 | `contrib-k8` | contribution based dropping | 8 | 0,1,2,3,4,5,6,7,8,9,10,12,13,14,22,23,24 | 0.9733 | 0.9743 | 0.7613 |
| 8 | `top-k2` | top layer dropping | 2 | 0-22 | 0.9732 | 0.9751 | 0.7830 |
| 9 | `bottom-k8` | bottom layer dropping | 8 | 8-24 | 0.9731 | 0.9735 | 0.7613 |
| 10 | `contrib-k6` | contribution based dropping | 6 | 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,21,22,23,24 | 0.9727 | 0.9738 | 0.7740 |
| 11 | `contrib-k4` | contribution based dropping | 4 | 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,20,21,22,23,24 | 0.9725 | 0.9755 | 0.7957 |
| 12 | `evenalt-k8` | even alternate dropping | 8 | 0,1,2,3,4,5,6,7,8,9,11,13,15,17,19,21,23 | 0.9725 | 0.9710 | 0.7740 |
| 13 | `oddalt-k4` | odd alternate dropping | 4 | 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,18,20,22,24 | 0.9724 | 0.9744 | 0.7957 |
| 14 | `evenalt-k2` | even alternate dropping | 2 | 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,23 | 0.9724 | 0.9738 | 0.7794 |
| 15 | `contrib-k2` | contribution based dropping | 2 | 0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,19,20,21,22,23,24 | 0.9724 | 0.9741 | 0.7902 |
| 16 | `top-k8` | top layer dropping | 8 | 0-16 | 0.9723 | 0.9723 | 0.7812 |
| 17 | `evenalt-k6` | even alternate dropping | 6 | 0,1,2,3,4,5,6,7,8,9,10,11,12,13,15,17,19,21,23 | 0.9722 | 0.9728 | 0.7776 |
| 18 | `bottom-k2` | bottom layer dropping | 2 | 2-24 | 0.9720 | 0.9740 | 0.7703 |
| 19 | `top-k6` | top layer dropping | 6 | 0-18 | 0.9717 | 0.9724 | 0.7685 |
| 20 | `top-k4` | top layer dropping | 4 | 0-20 | 0.9717 | 0.9733 | 0.7902 |
| 21 | `bottom-k4` | bottom layer dropping | 4 | 4-24 | 0.9715 | 0.9746 | 0.7613 |
| 22 | `sym-k4` | symmetric dropping | 4 | 0,1,2,3,4,5,6,7,8,9,10,15,16,17,18,19,20,21,22,23,24 | 0.9711 | 0.9693 | 0.7902 |
| 23 | `sym-k6` | symmetric dropping | 6 | 0,1,2,3,4,5,6,7,8,9,16,17,18,19,20,21,22,23,24 | 0.9704 | 0.9710 | 0.7902 |
| 24 | `sym-k8` | symmetric dropping | 8 | 0,1,2,3,4,5,6,7,8,17,18,19,20,21,22,23,24 | 0.9682 | 0.9685 | 0.7794 |

`test_opt_er_acc` is the speaker-leaky single-split column and is **never** a result (PLAN §6); it is
shown only to keep the context visible. `test_opt_acc_all` is likewise context, not selection.

## 2. Does the pattern favour layer identity or layer count?

Pre-registered rule (PLAN §6): *"Compare spread within a `k` across groups against spread within a
group across `k`. If the former is comparable to the latter, H2 is supported and H1 is not."*

| comparison | spread (max − min of `val_acc_all`) |
| --- | --- |
| within `k`=2 across the 6 groups | 0.00239 |
| within `k`=4 across the 6 groups | 0.00231 |
| within `k`=6 across the 6 groups | 0.00365 |
| within `k`=8 across the 6 groups | 0.00540 |
| within *top* across `k` | 0.00147 |
| within *evenalt* across `k` | 0.00119 |
| within *oddalt* across `k` | 0.00196 |
| within *symmetric* across `k` | 0.00540 |
| within *bottom* across `k` | 0.00253 |
| within *contribution* across `k` | 0.00098 |
| overall (all 24) | 0.00624 |

**Read: H2 (count-only) is supported, H1 is not.** Equal-`k` combos do cluster — the spread across
groups at a fixed `k` (0.0023–0.0054, mean ≈0.0034) is the same order as the spread across `k`
within a group (0.0010–0.0054, mean ≈0.0023); the *group* label buys no separation that the count
does not already explain. The ordering is not explained by `k` alone either: there is no monotone
`k` trend in the pooled data, and the top four ranks are `k`=2, 6, 2, 8.

Two structured exceptions worth carrying into the confirm stage rather than into a claim:

* *symmetric* is the only group that degrades monotonically with `k` (0.9736 → 0.9711 → 0.9704 →
  0.9682) and supplies the single worst combo; its within-group spread (0.0054) is the largest of
  any group.
* The two `oddalt` extremes (`k`=2 best, `k`=4 rank 13) show that a within-group ordering at
  `k`=2 vs `k`=4 of 0.0020 is itself the size of the whole effect being chased.

**No subset separates from the all-25 reference.** The all-25 arm of `DG-0007` is not on this
tracking server (searched: no `dg0007` experiment exists), and the parent `PLAN.md` records only a
qualitative *"versus 0.97 without it"*, so the reference value must come from the confirm stage's
matched re-run rather than from this screen. Everything measured here sits at 0.968–0.974 — i.e. in
the same region as the recorded reference, which keeps the third pre-declared outcome (the arm is
insensitive to this axis at this resolution) live alongside H2.

Single seed caveat, stated rather than buried: one seed cannot separate 0.002 spreads from seed
noise (`F1`), so the above is the screen's *read*, and only the confirm stage can support a
comparison. Nothing here is a result.

## 3. Proposed confirm-stage combos

Per PLAN §5 the confirm set is "top 2 by validation"; the spec's current entries (`top-k2`,
`sym-k2`) are explicitly placeholders and are **not** the screen's top 2 (ranks 8 and 3), so the
selection to write into `sweep.json` before launching confirm is:

1. **`oddalt-k2`** — rank 1, `val_acc_all` 0.9744 (seed 42); odd alternate dropping, 23 layers kept.
2. **`bottom-k6`** — rank 2, `val_acc_all` 0.9740; bottom layer dropping, 19 layers kept.

Reason: they are the two highest validation peaks, and they span the axis that matters for the
study — a low-drop candidate (`k`=2, 23 layers kept) and a mid-drop candidate (`k`=6, 19 kept) —
so a five-seed confirm tests both `k` regimes against the matched all-25 reference instead of
spending five seeds on one corner of the space.

Caveats for the researcher, not decisions taken here:

* Ranks 2–5 span only 0.0009 (`bottom-k6` 0.9740 → `oddalt-k8` 0.9736), i.e. the choice of rank 2
  is inside single-seed noise; `sym-k2` (rank 3) is an equally defensible second slot.
* If a **matched-`k`** comparison is preferred (to attack H1 vs H2 directly rather than to hunt the
  best number), the natural pair is `oddalt-k2` + `sym-k2` — both `k`=2, same seed set, opposite
  drop schemes, spread 0.0008. That choice would test identity at fixed count; the mechanical
  top-2 above would not.

## 4. Execution record, deviations, and defects found (recorded, not hidden)

* **One supervisor, one stage.** The supervisor was started detached (`setsid nohup … supervisor
  --stage screen`) with `--env-file /root/.sweep-env`; the controller-side `start` command was
  killed by a local 600 s tool limit after launch, and the pod-side supervisor was verified to have
  survived. No second supervisor was ever started. `DRAIN` was never raised; the stage ended by
  exhaustion (queue empty, no children) with a summary written to `SWEEP.json`
  (`elapsed_seconds: 8525.1`, `counts: {succeeded: 24}`).
* **Concurrency.** Admission ran between 6 and 7 concurrent runs. It was bounded by
  `policy.vram_per_run_gb` (2.5) against free VRAM rather than by the device: measured usage was
  ≈1.07 GB/run (6 runs ≈ 6.4 GB of 23 GB). `policy.ceiling: 8` was never reached. The supervisor
  also correctly refused admissions while the device was ≥95 % utilised
  (`admission: hold (GPU already 99% utilised (limit 95%) …)`), and GPU utilisation sampled between
  0 % and 99 % across epochs because per-epoch feature loading is disk/CPU-bound.
  A corrected `vram_per_run_gb` (~1.2) would admit ~8; that is a science-input change and was **not**
  made — it would need a recorded protocol deviation and a commit, and it cannot affect an
  already-loaded policy anyway.
* **Defect 1 — completion annotation fails for every run.** `improvements/sweep/supervisor.py:233`
  passes `identity["run_id"]` (the *checkpoint directory* name, e.g. `2026_10_06_13_38_10`) to
  `annotate_run`, which calls `MlflowClient.set_tag(run_id, …)` (`supervisor.py:86`). MLflow has no
  run with that id, so all **24/24** annotations recorded
  `{"annotated": false, "reason": "MlflowException: Run '<timestamp>' not found"}`. It is
  deliberately non-fatal (exit 0, identity and hashed outputs still recorded).
* **Defect 2 — the report filter had no producer.** `improvements/sweep/report.py:65` filters
  `tags.sweep_id = '<sweep_id>'`, but `mlflow_utils.set_grouping_tags` publishes `study_id`,
  `stage`, `layers`, `seed`, … and never `sweep_id`, `combo`, `group` or `k`; the supervisor's
  `annotation_tags` adds only `sweep_group`/`sweep_k`/`sweep_combo`/`sweep_layers` and is broken by
  Defect 1. Measured before repair: `report filter hits: 0` while `tags.stage='screen'` matched 12.
* **Repair applied (metadata only).** `sweep_id`, `combo`, `sweep_group`, `sweep_k`, `sweep_combo`
  and `sweep_layers` were set on the 24 runs from `sweep.json` as the authoritative source
  (`patched 24 run(s); skipped []`), after which `sweep report` matched 24/24. No metric, param,
  artifact, config or spec was altered, and the repaired report reproduces exactly the ranking
  computed independently from run params (`val_acc_all` peak per combo).
* **Provenance per run** is in the ledger, not in the tags: each `succeeded` record carries
  `identity` (`git_commit`, `model`, `method`, `representation`, `seed`, `stage`, `study_id`,
  `task_type`, checkpoint/results dirs) and `outputs[]` with per-file `sha256` + `size_bytes` for
  the checkpoint, which is this package's substitute for the backend's evidence validator.
* **Cost.** Pod at $0.49/hr; the stage consumed 2 h 22 m of L4 time (≈$1.16), plus earlier setup on
  the same Pod. The 80 GB network volume bills storage independently (≈$0.16/24 h observed).

## 5. Not done, deliberately

* No `confirm` stage run, no second supervisor, no `DRAIN`.
* No edit to `sweep.json` or `configs/`.
* No `--allow-dirty` anywhere; every remote verb verified the pod's `HEAD` against the controller's
  `cd3171f64f6b82a562c0c0eb088860abbcb4eeae` before touching the pod.

**Before the confirm stage can launch, this file must be committed**: `analysis.md` is inside the
study directory and is not covered by its `.gitignore`, so the sweep's launch check (which refuses
an untracked file in the study directory) will reject the confirm run until it is tracked.
