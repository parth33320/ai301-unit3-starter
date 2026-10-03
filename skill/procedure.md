# Procedure: how this skill grades a plan package

## Read order
1. Issue context and thread highlights: Read first to understand the reported problem, maintainer directions, and repo policies.
2. Repo-facts block: Read to identify contributing guidelines, required templates, and mandatory AI-use disclosure rules.
3. Repro-evidence block: Read to establish the empirical ground truth, including steps to reproduce, control runs, and verified facts.
4. Candidate plan and plan comment: Read last to evaluate the proposed cause, scope, execution steps, test plan, and comment compliance.

## Evidence gathering
- For `grounded_diagnosis`: Compare the plan's diagnosis with all repro evidence facts. Check whether control runs or evidence steps contradict the plan's cause.
- For `bounded_scope`: Compare proposed changes with the issue description. Identify any extra refactorings, migrations, or unrequested features.
- For `executability_and_testability`: Check for specific targeted files/modules, clear action steps, and an observable test outcome.
- For `thread_and_repo_conventions`: Check if thread maintainer directions are addressed, and verify if required AI disclosures are included in the plan comment when repo-facts mandate it.

## Check execution
1. Execute `grounded_diagnosis`: Fail if diagnosis contradicts repro evidence or control runs.
2. Execute `bounded_scope`: Fail if scope creep or unnecessary redesign/migration is present.
3. Execute `executability_and_testability`: Fail if files/approach are missing/vague or test plan lacks observable criteria.
4. Execute `thread_and_repo_conventions`: Fail if thread maintainer direction is ignored or required AI-use disclosure is missing.

## Verdict assembly
1. Evaluate all check grades.
2. If all required checks grade `pass`, emit verdict `accept`.
3. If any required check grades `fail` or `unclear`, emit verdict `reject`.
4. List evidence and failed check names clearly in the output.
