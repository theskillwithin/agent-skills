---
name: ui-hill-climb
description: Improve or review an existing UI through iterative screenshot comparison and independent Council reviews. Use for substantial UI polish, redesigns, or requests to benchmark visual quality against peer products.
---

# UI hill climb

Use the running product as the review surface. Translate the user's feedback into outcomes someone can observe, such as being able to scan thread titles or distinguish a primary action from a status label. Capture the initial state before editing. Agree on what counts as good enough from the brief, and set a bounded round or time budget. For a focused polish pass with no stated budget, start with up to three edit-and-review rounds and keep the strongest verified version.

## Set up a fair comparison

- Choose screens and states that represent the user's main workflows. Include affected themes, viewport sizes, and dense or edge states when they could change the judgment.
- Capture the baseline and each candidate under matched conditions. Keep the route, data, state, theme, viewport, and zoom consistent. Label screenshots with the state and revision. Use the user's design references first. For peer benchmarks, compare equivalent workflows and hold the reference images fixed across rounds.
- Set a short rubric based on the brief. Look for visible issues in hierarchy, typography, spacing and density, consistency, and meaningful use of color and contrast. Judge each screen in terms of the task it supports. Keep working flows, legibility, keyboard behavior, and other testable requirements as gates.

## Run the Council review loop

1. Invoke `$council` for the initial benchmark and the final candidate. Follow that skill for reviewer selection and availability. Give every delegate the same rubric and screenshots of equivalent states across the baseline, current candidate if one exists, and references. Keep reviewers independent and read-only. Hold back previous verdicts and the target score.
2. Ask each delegate to rank the designs and score the relevant criteria out of 10. For each important finding, ask them to name the screen, what is visible, and how it affects the task. Resolve disagreements by checking the cited evidence against the brief.
3. Fix the highest-impact verified issues in a coherent pass. Preserve wins from the strongest prior version. Recapture the changed screens and exercise the affected interactions. To conserve usage, use one reviewer for intermediate passes when the user has not requested a full Council review every round. Bring the full Council back for a fresh review of the same final revision. If that review exposes material issues, fix them and repeat within the agreed budget.
4. Record each revision, its concrete improvements and regressions, and each delegate's scores by round. Mark skipped or unavailable reviews and disclose if the full Council could not review the final candidate. Scores can move even for unchanged reference images. Treat small swings as noise unless the visible evidence supports the change. If the rubric or reference set changes, rerun the comparison against the baseline.

Stop when the candidate meets the agreed criteria and passes the functional checks, when another pass yields no material improvement, or when the budget is reached. If the user set a numerical threshold, report whether each required reviewer met it on the same final revision. Show matched before-and-after views where available and call out any unverified states or remaining tradeoffs.

## When an outcome can be measured directly

Turn representative failures into repeatable checks and run them on the baseline. For a secondary efficiency target, first reach the required correctness level and retain that version as a checkpoint. Then optimize the cost, latency, or size and rerun the same checks after each change. For example, before shortening tool descriptions, check that the model still chooses the expected tool on the same set of past failure cases.

Adapted from Theo's T3 Code redesign and evaluation experiment. [Agents Sucked At UI (Until Today)](https://www.youtube.com/watch?v=xq0eB8vxeqk)
