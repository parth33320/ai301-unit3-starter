# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding
- Where it lives: The candidate plan's diagnosis/cause section read against the repro-evidence block (and any control runs or timing matrices in steps 1–4).
- What good looks like: The stated cause directly accounts for the observed repro steps. If control runs rule out a hypothesis (e.g., issue happens without a pager, pyarrow table missing values before cast, or error prints in same build), a diagnosis blaming that ruled-out component is UNGROUNDED and FAILS.

## Scope
- Where it lives: The candidate plan's scope statement, non-goals, and proposed file changes read against the issue context.
- What good looks like: The plan stays strictly within the boundaries of the reported issue. Plans that bundle printer rewrites, dependency migrations, settings UI, containerd upgrades, or unrelated refactors FAIL for scope creep.

## Executability
- Where it lives: The candidate plan's proposed steps, targeted files/modules, and overall strategy.
- What good looks like: The plan identifies specific files or architectural locations and a clear technical strategy. Plans that say "poke around", "profile and optimize", "investigate somewhere", or lack file/approach details FAIL for unbuildability.

## Test plan
- Where it lives: The candidate plan's test plan section read against the repro evidence.
- What good looks like: The test plan specifies a concrete, observable pass/fail outcome for the fix (e.g., exit code 0, expected stdout/color, specific test assertion). Vague test plans like "should feel fast", "nothing else broken", or "run full test suite" FAIL.

## Honesty
- Where it lives: Stated unknowns, risks, and trade-offs in the plan.
- What good looks like: The plan explicitly acknowledges genuine trade-offs or deferred non-critical work rather than feigning certainty or hiding omissions.

## Comms
- Where it lives: The candidate plan comment read against the thread highlights and repo-facts block.
- What good looks like: The comment addresses any explicit maintainer requests or direction in the thread. Furthermore, if the repo-facts block states an AI-use disclosure policy, the comment MUST include the required AI-use disclosure statement (e.g., stating AI assistance).
