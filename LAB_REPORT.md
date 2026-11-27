# AI53 Phase 2 lab report

At each milestone, copy the observed results under **Checkpoint**, then answer the matching **Question** from the Lab Guide.

## Milestone 1 · Inspect the model handoff

### Checkpoint 1 · Evidence

- GitHub repository URL:
- Expected model version: phase1-reference-v1
- Three class labels: `angular_leaf_spot`, `bean_rust`, `healthy`
- First pushed commit SHA: 27c5fd5be6e343ca4768a3052711318c90366b74

### Question 1 · Explanation

Answer: The model version is used for version tracking. The Git SHA identifies the individual and unique commit made in the repository.

## Milestone 2 · Serve predictions with FastAPI

### Checkpoint 2 · Evidence

- Final API test count after TODO 1: `7 passed in 0.33s`
- Browser prediction JSON for `data/current/leaf-01.jpg` (`UNAVAILABLE` and the error shown if the page gives no prediction):
  `{
  "label": "angular_leaf_spot",
  "confidence": 0.5016347169876099,
  "model_version": "phase1-reference-v1"
}`

### Question 2 · Explanation

Answer: `/health` confirms that the FastAPI server process is running while `/ready` verifies the model that actually loaded into memory and reports the version of the model. The model version help us to know which model was used for the prediction.

## Milestone 3 · Run tests and build in GitHub Actions

### Checkpoint 3 · Evidence

- Local branch: main
- Local commit SHA: d6f6aff74279daf584bceb1610f0df07dcecf6f8
- Local API test count: `7 passed in 0.33s`
- Actions run source (`main`, `PULL REQUEST` or `UNAVAILABLE`): `main`
- Actions run URL: https://github.com/GreenMom7/AI54-Phase-2/actions/runs/46007344671
- `tests` job status: `PASS`
- `docker-build` job status:`PASS`
- Container smoke result or first failed CI step: `PASS`

For the two job statuses, use `PASS`, `FAIL`, `PENDING`, `SKIPPED`, `CANCELLED` or `UNAVAILABLE`. Use `SKIPPED` when Actions shows that a job was skipped. Use `UNAVAILABLE` for missing CI evidence.

### Question 3 · Explanation

Answer: The `tests` runs `uv sync --locked` and `pytest -q tests/test_api.py` on pushes and pull requests to `main`. The check run will ensure that the dependency issues, Docker build failures or wrong model artifacts are caught automatically before the code is merged or deployed to staging phase.

## Milestone 4 · Verify a staging release

### Checkpoint 4 · Evidence

- Mode (`RENDER` or `LOCAL`): `RENDER`
- Target URL: https://ai54-phase-2.onrender.com
- Tested commit SHA: d6f6aff74279daf584bceb1610f0df07dcecf6f8
- Expected model version from Checkpoint 1: `phase1-reference-v1`
- Observed model version from `/ready` (`UNAVAILABLE` if it returned none): `phase1-reference-v1`
- Smoke check result (`PASS` or `FAIL`): `PASS`
- First error, if any: No error occured
- Earlier Render attempt, if switching to local (URL if available and reason for switching):

### Question 4 · Explanation

Answer: I would not approve the deployment as it was not the intended version of the model. I need to check the model artifact in `/model`

## Milestone 5 · Compare input distributions

### Checkpoint 5 · Evidence

Fill each row from its own `summary.json`. Use the file from the CURRENT report for the first row and the file from the SHIFTED report for the second. In both files, `current_mean` is the mean brightness of the batch you checked. Use the exact `score` (p-value) and `threshold` (cutoff). Enter `DRIFT` when `drift_detected` is true and `NO DRIFT` when it is false.

| Batch     | Reference mean (`reference_mean`) | Batch mean (`current_mean`) | Method      | Score                  | Threshold | Result (`DRIFT` or `NO DRIFT`) |
| --------- | --------------------------------- | --------------------------- | ----------- | ---------------------- | --------- | ------------------------------ |
| `CURRENT` | 0.4724440528104574                | 0.47574189751633994         | K-S p_value | 0.8079631540901643     | 0.05      | `NO DRIFT`                     |
| `SHIFTED` | 0.4724440528104574                | 0.16466240836601304         | K-S p_value | 1.6911233892144742e-17 | 0.05      | `DRIFT`                        |

- Final full test count: `9 passed, 10 warnings in 2.90s`

### Question 5 · Explanation

Answer: We are using the K-S test on `mean-brightness` with a threshold of `0.05`

- For `CURRENT` : `p=0.808 > 0.05` giving us `NO DRIFT` because the brightness of the images matches the reference data.
- For `SHIFTED` : `p=1.69e-17 < 0.05` giving us `DRIFT` as result because the images are significantly darker than the reference batch.

The shift might affect the model's accuracy. From the labels, we should investigate why incoming photos became darker and then evaluate model accuracy on the shifted data
