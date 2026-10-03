# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| grounded_diagnosis | The candidate plan's stated cause read against the repro-evidence block and control runs | The stated cause directly explains the failure and does NOT contradict or ignore any facts, steps, or control outputs in the repro evidence. | required |
| bounded_scope | The candidate plan's scope statement and proposed changes read against the issue context | The plan proposes only the minimal, bounded changes necessary to fix the reported issue, without unasked-for redesigns, drive-by refactors, dependency migrations, or feature additions. | required |
| executability_and_testability | The candidate plan's approach, file targets, and test plan read against the issue and repro evidence | The plan specifies concrete files/locations and approach so a stranger can execute it immediately, AND includes a decisive test plan with specific, observable pass/fail outcomes for the fix itself. | required |
| thread_and_repo_conventions | The candidate plan comment read against the issue thread highlights and repo-facts block (contributing policy / AI disclosure requirements) | The plan comment directly engages with any explicit maintainer direction in the thread AND complies with all repo policies, including mandatory AI-use disclosures if required by the repo-facts policy. | required |

## Verdict rule

Accept if every required check passes; preferred checks never change the verdict; unclear counts as fail.
