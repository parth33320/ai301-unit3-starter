# Plan and Implement Deliverable: Plan-Check Skill & Eval Suite

---

## 1. Run History Breakdown

To build a robust, deterministic plan-check evaluation skill, we iterated through calibration and evaluation runs against the 20 scored packages and 4 calibration packages in `eval/packages/`.

### Run 0: Initial Baseline (Template / Empty Components)
- **Status:** Failed / Refused to grade.
- **Finding:** The harness correctly refused to grade when `rubric.md`, `evidence-guide.md`, or `procedure.md` contained placeholder templates without concrete checks or verdict rules.

### Run 1: Core Check Formulation & Initial Calibration
- **Changes:**
  - Formulated 4 core checks in `skill/rubric.md`: `grounded_diagnosis`, `bounded_scope`, `executability_and_testability`, and `thread_and_repo_conventions`.
  - Defined explicit evidence locations in `skill/references/evidence-guide.md` mapping each check to sections of the package bundle (repro-evidence block, issue context, plan, and repo-facts block).
  - Defined a 4-step execution procedure in `skill/procedure.md`.
- **Results:**
  - `pkg-01`, `pkg-07`, `pkg-11`, `pkg-16`: Correctly rejected for `wrong-cause` (ungrounded diagnoses contradicting control runs).
  - `pkg-06`, `pkg-12`, `pkg-15`, `pkg-19`: Correctly rejected for `scope-creep` (unrequested migrations and redesigns).
  - `pkg-10`, `pkg-17`, `pkg-18`: Correctly rejected for `unbuildable` (vague steps / unobservable test plans).

### Run 2: Edge-Case Refinement (Thread & Repo Conventions)
- **Changes:**
  - Refined `thread_and_repo_conventions` check to explicitly check for:
    1. Direct engagement with maintainer instructions in thread highlights (e.g., testing patched binaries requested by owners in `pkg-04`).
    2. Mandatory AI-use disclosure compliance when repo-facts block mandates AI assistance statements (e.g., `pkg-20` on Ghostty repo policy).
- **Results:**
  - `pkg-04`: Correctly rejected (`thread-convention`) for ignoring maintainer request for binary feedback.
  - `pkg-20`: Correctly rejected (`thread-convention`) for missing mandatory AI disclosure.

### Run 3: Final Confirming Run (20/20 PASS)
- **Command:**
  ```bash
  python3 eval/run_eval.py --rubric skill/rubric.md --evidence skill/references/evidence-guide.md --procedure skill/procedure.md --save-run eval/eval-run.txt
  ```
- **Output Metrics:**
  - **Agreement:** 20 / 20 scored items (100% agreement, exceeding the 18/20 bar).
  - **Category Floor:**
    - `clear-accept`: 7 / 7
    - `scope-creep`: 4 / 4
    - `thread-convention`: 2 / 2
    - `unbuildable`: 3 / 3
    - `wrong-cause`: 4 / 4
  - **Verdict:** PASS

---

## 2. Deep Dive Package Analysis: `pkg-07`

- **Source Issue:** `processing/p5.js#8930`
- **Category:** `wrong-cause`
- **Gold Label Verdict:** `reject`

### Issue & Plan Context
In `pkg-07`, the issue reports an error handling failure in p5.js Friendly Error System (FES). The candidate plan claims that the root cause of the error is that the FES module was tree-shaken out of the 2.x production bundle during build optimization.

### Repro Evidence Reality
In the package's repro-evidence block, Step 4 includes a control run testing error output in the exact same build environment. The control run demonstrates that instance-method friendly error messages print successfully in the same build, proving that FES is active and present in the bundle.

### Analysis & Rejection Rationale
Because the control run in Step 4 proves that FES is loaded and functional in the build, the candidate plan's diagnosis (that FES was tree-shaken out) is empirically false and contradicted by the package's own control evidence.

### Check Mechanism
Our `grounded_diagnosis` check evaluates whether the candidate plan's stated cause directly explains the failure without contradicting any control runs or steps in the repro evidence block. When executed against `pkg-07`:
1. The evidence gatherer compares the plan's diagnosis ("FES tree-shaken out") against Step 4 of the repro evidence ("FES prints friendly errors for instance methods in same build").
2. The check identifies the explicit contradiction.
3. `grounded_diagnosis` evaluates to `fail`.
4. The verdict rule (`accept if every required check passes`) triggers a `reject` verdict.

---

## 3. Check Rationale & Trade-Offs

### 1. `grounded_diagnosis` (Weight: `required`)
- **Rationale:** A plan built on a false or ungrounded diagnosis will fail during implementation or fix the wrong problem.
- **Trade-off:** Requires rigorous reading of control steps in the repro evidence block, but prevents wasting engineering effort on non-existent root causes.

### 2. `bounded_scope` (Weight: `required`)
- **Rationale:** Scope creep introduces unnecessary risk, makes PRs hard to review, and often breaks unrelated components.
- **Trade-off:** May reject well-intentioned refactorings or infrastructure upgrades bundled into a bug fix, but enforces lean, reviewable, single-purpose changes.

### 3. `executability_and_testability` (Weight: `required`)
- **Rationale:** A plan must name concrete file targets and provide an observable, decisive test outcome so an engineer or AI agent can build and verify it immediately.
- **Trade-off:** Expects plans to be concrete rather than exploratory, but guarantees actionable execution boundaries.

### 4. `thread_and_repo_conventions` (Weight: `required`)
- **Rationale:** Ignores maintainer feedback or violating repository compliance policies (such as AI disclosure rules) leads to rejected PRs and poor community engagement.
- **Trade-off:** Rejects technically sound plans if communication or policy standards are missed, preserving open-source standards and project compliance.

### Category Floor Coverage
Together, these four checks provide 100% coverage across all 5 evaluation categories:
- `clear-accept`: Plans where all 4 checks pass.
- `wrong-cause`: Caught by `grounded_diagnosis`.
- `scope-creep`: Caught by `bounded_scope`.
- `unbuildable`: Caught by `executability_and_testability`.
- `thread-convention`: Caught by `thread_and_repo_conventions`.
