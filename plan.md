# Plan: Implement an Offline Eval Runner for Benchmark Review Quality

**Target Repository:** `codepath/pathreview-ai301-fa26-s3`
**Target Issue:** Issue #14 ("Implement an offline eval runner that measures review quality across a benchmark portfolio set")

---

## 1. Context & Problem Statement
Currently, evaluating plan review quality across benchmark portfolio sets requires manually running standalone evaluation scripts or inspecting output files individually. This lacks a reproducible, offline evaluation runner that can programmatically run benchmark evaluations, aggregate performance scores across portfolio test packages, and export structured evaluation results to `eval_results.json`.

---

## 2. Proposed Architectural Changes & Execution Plan

### `scripts/run_evals.py`
We will implement/update `scripts/run_evals.py` to provide a clean CLI entry point for offline evaluation.

1. **Module & Class Instantiation:**
   - Import the `EvalSuite` class (or instantiate the evaluation harness pipeline).
   - Configure the runner to load offline benchmark portfolio packages (from `eval/packages/` or target package directories).

2. **Benchmark Execution Loop:**
   - Iterate through portfolio packages (e.g., `pkg-01` through `pkg-20` and calibration packages).
   - Evaluate model predictions/rubric verdicts against ground-truth labels.
   - Aggregate precision, recall, category-level accuracy, and agreement scores.

3. **Structured Output Generation:**
   - Serialize the aggregated metrics and package-level verdicts into `eval_results.json` at the workspace root or designated output path.
   - Output fields must include:
     - `timestamp`: UTC ISO 8601 timestamp.
     - `total_packages`: Total packages evaluated.
     - `agreement_score`: Fractional and percentage agreement.
     - `category_metrics`: Breakdown of accuracy per category (`clear-accept`, `scope-creep`, `wrong-cause`, `unbuildable`, `thread-convention`).
     - `results`: Detailed list of package IDs, predicted verdicts, gold labels, and failed check details.

---

## 3. File Boundaries

- **Files to create/modify:**
  - `scripts/run_evals.py`: Implement the offline evaluation runner script and `EvalSuite` integration.
  - `eval_results.json`: Generated output artifact containing structured evaluation metrics.

- **Files to keep untouched:**
  - `eval/gold-labels.json`: Read-only ground-truth label definitions.
  - `eval/packages/*`: Read-only benchmark evaluation packages.
  - `skill/scope.md`, `skill/rubric.md`, `skill/procedure.md`, `skill/references/evidence-guide.md`: Core skill definition files (evaluated by the runner).

---

## 4. Scope Boundaries & Non-Goals

- **Non-Goal 1:** Modifying gold labels or altering benchmark package definitions.
- **Non-Goal 2:** Making live external API calls during offline benchmark evaluation mode.
- **Non-Goal 3:** Introducing UI or web-based dashboards for evaluation visualization.
- **Non-Goal 4:** Refactoring or modifying core skill grading heuristics outside the specified evaluation runner script.

---

## 5. Test Plan & Verification Strategy

### Verification Steps
1. **Runner Execution:**
   - Run command: `python3 scripts/run_evals.py`
   - Verify process exits with exit code `0`.

2. **Output Schema & Content Verification:**
   - Confirm `eval_results.json` is generated.
   - Verify `eval_results.json` is valid JSON and contains required keys (`total_packages`, `agreement_score`, `category_metrics`, `results`).
   - Validate that agreement score meets or exceeds the required threshold (18/20 or 100% agreement on offline benchmarks).

3. **Regression Test:**
   - Re-run `python3 eval/run_eval.py --rubric skill/rubric.md --evidence skill/references/evidence-guide.md --procedure skill/procedure.md` to verify zero regression across existing harness evaluations.
