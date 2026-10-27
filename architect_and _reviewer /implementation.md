Implementation Plan — AI Football

1. Architecture

Implement everything in one self-contained agent.py file. Keep the existing API and Agent1 interface unchanged. Use small helper functions within the same file.

2. Required Functions and Responsibilities

Function	Responsibility
on_ball(...)	Choose an action when the player has the ball.
off_ball(...)	Choose an action when the player does not have the ball.
generate_actions(...)	Generate a limited set of candidate actions appropriate to the current state.
evaluate_state(...)	Evaluate attacking potential, defensive safety, possession, and risk.
choose_best_action(...)	Compare candidate actions and select the most promising one.
search(...)	Perform depth-limited adversarial search over our actions and plausible opponent responses.
get_home_position(...)	Apply the provided home_position helper for sensible off-ball positioning.
generate_pass_targets(...)	Use points_around to sample passing locations near teammates.
time_remaining(...)	Check the available decision and match budgets using the actual runtime API.

Important: These are proposed internal helper names, not required API names. Verify the exact on_ball and off_ball signatures before implementation.

3. Main Decision-Making Logic

When the player has the ball

1. Generate candidate passes, dribbles, shots/kicks, holds, and clearances where appropriate.
2. Use points_around to sample potential passing locations near teammates.
3. Reject or penalize unsafe passes, likely offside situations, and actions that expose possession unnecessarily.
4. Evaluate candidates using the state evaluator and a limited search of opponent responses.
5. Return the best valid action found within the time budget.

When the player does not have the ball

1. Determine whether interception or tackling is appropriate.
2. Otherwise, use home_position to maintain useful formation and defensive coverage.
3. Account for teammate positions to avoid unnecessary duplication of roles.
4. Return a valid movement, interception, or tackling action.

Search strategy

* Use depth-limited minimax/H-Minimax to consider our decisions and opponent responses.
* Use iterative deepening to increase search depth while time permits.
* Represent uncertain outcomes using expected-value reasoning where useful and supported by the available state/action model.
* If time runs short, return the best valid action found so far.

Do not assume that the simulator supports exact hypothetical state transitions; verify this before relying on deeper search.

4. Implementation Order

1. Inspect the API: Verify observations, function signatures, action formats, and runtime-budget fields.
2. Implement a valid baseline: Make on_ball and off_ball return legal actions in every supported situation.
3. Add candidate generation: Implement generate_actions and passing-target sampling.
4. Add positioning and defense: Integrate home_position, interception, tackling, and teammate-aware positioning.
5. Implement evaluation: Build evaluate_state using attacking, defensive, possession, and risk factors.
6. Add search: Implement bounded adversarial search and iterative deepening.
7. Add time safeguards: Enforce per-decision and total match budgets.
8. Tune and validate: Test behavior, correctness, and performance before finalizing the single file.

5. Important Edge Cases

* No safe passing target exists.
* The player is near the opponent’s goal or under immediate pressure.
* A pass risks interception or offside.
* Multiple teammates chase the same ball or leave defensive positions uncovered.
* A tackle or interception is inappropriate or unavailable.
* The ball is out of play or the simulator is handling a set piece.
* The search has insufficient time to complete another iteration.
* Candidate generation produces no usable action.
* A returned action has invalid parameters or violates the API.

Always retain a legal fallback action and ensure every decision returns within the deadline.

6. Testing Plan

1. API tests: Verify all action formats and function return values against the starter interface.
2. Baseline match: Run against the provided Agent2; confirm the game runs without exceptions.
3. Scenario tests: Test attacking, defending, passing, interception, tackling, offside risk, and possession under pressure.
4. Search tests: Confirm that search improves decisions without exceeding the time budget.
5. Full-match tests: Run multiple matches and record goals scored, goals conceded, goal difference, fouls, cards, and runtime.
6. Regression tests: After each change, rerun earlier tests to ensure existing behavior remains correct.
7. Final packaging: Verify that the complete implementation is in one agent.py file, uses only permitted dependencies, and works with the grading runner.

Priority: Correct API behavior → reliable baseline → good candidate actions and evaluation → bounded search → empirical tuning.