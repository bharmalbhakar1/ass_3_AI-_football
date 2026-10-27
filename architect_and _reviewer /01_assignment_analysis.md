01 — Assignment Analysis

Project: COL333 Assignment 3 — AI Football
Repository: https://github.com/bharmalbhakar1/ass_3_AI-_football
Document purpose: Requirements analysis and problem understanding
Status: Initial analysis; algorithm selection and final architecture intentionally deferred.

⸻

1. Objective

The objective of this assignment is to develop an AI agent that controls a football team in a simulated environment.

The agent must make decisions during open play, selecting actions for its controlled players based on the current game situation. It must attempt to score goals, prevent the opponent from scoring, and account for the rules and uncertainties of the simulator.

The assignment focuses on game-playing AI, adversarial decision-making, search, and efficient action selection.

The assignment encourages students to formalize the state and action spaces and use search-based approaches that consider possible actions by both teams. The implementation should build on algorithms and mathematical ideas taught in the course, or reasonable variations of them.

This document establishes the requirements and identifies the questions that must be resolved before selecting an algorithm or designing the final architecture.

Evidence labels

* Verified: Explicitly stated in the supplied assignment statement or established from the inspected starter archive.
* Interpretation: A reasonable implication of the assignment, not an additional requirement.
* Unresolved: Requires further inspection of the starter code or clarification from the instructor.

⸻

2. Source and Repository Access

2.1 Assignment statement

The supplied ass_problem(1).txt was accessible locally and reviewed.

It is the primary source for the requirements documented here.

2.2 Starter-code archive

The uploaded archive COL333-Assignment-3-py2d-fork.zip was inspected to identify relevant files and directories.

The following components were identified:

Path	Purpose	Inspection status
README.md	Setup and usage instructions	Identified; detailed review pending
a3.py	Match runner and evaluation/debugging commands	Identified; command behaviour requires verification
src/student/agent.py	Agent implementation	Identified; implementation requires review
src/student/interface.py	Agent API and action definitions	Identified; exact signatures and semantics require review
src/student/framework/world.py	World and state implementation	Identified; transition mechanics require review
src/student/framework/trace.py	Trace and replay support	Identified; available diagnostics require review
src/student/framework/policy.py	Framework policy support	Identified; role requires review
src/student/framework/runtime.py	Runtime support	Identified; timing and execution behaviour require review
src/formations/	Formation-related components	Identified; helper behaviour requires review
requirements.txt	Environment dependencies	Identified; permitted dependencies require verification
check_requirements.py	Environment checking	Identified

The archive also contains simulator and support components, including idl/, scripts, and server.py.

The list above is not an exhaustive archive inventory.

Access limitation: The remote GitHub repository could not be independently verified during the initial review. The analysis therefore relies on the supplied assignment statement and uploaded starter archive. It is not established that the current remote repository is identical to the uploaded archive.

No files have been modified, and no match has been executed as part of this analysis.

2.3 Available course notes

The following lecture materials are available:

Lecture file	Topic
L01-Course-Overview(1).pdf	Course overview
L02-Introduction(1).pdf	Introduction to AI
L03-Problem-Solving-As-Search-Uninformed(1).pdf	Uninformed search
L04-Constraint-Satisfaction(1).pdf	Constraint satisfaction
L05-Local-Search-Algorithms(1).pdf	Local search
L06-Goal-Informed-Search(1).pdf	Goal-informed search
L07-Modeling-Uncertainty(1).pdf	Modeling uncertainty
L07-Modeling-Uncertainty_restore(1).pdf	Second version of modeling-uncertainty notes
L08-Markov-Decision-Processes(1).pdf	Markov decision processes
L10-Adversarial-Search(1).pdf	Adversarial search
L11-Bayesian-Networks(1).pdf	Bayesian networks

The detailed contents of these lectures have not yet been fully analyzed. Their relevance to the assignment is assessed preliminarily in Section 8.

Whether the two modeling-uncertainty PDFs differ remains unverified.

⸻

3. Problem Definition

3.1 Teams and controlled players

The assignment specifies the following environment:

* Two teams compete against each other.
* Each team has five players.
* Four outfield players per team are controlled by the corresponding AI agent.
* One goalkeeper per team is controlled automatically by the simulator.
* Each agent runs in a separate process.
* Agents cannot communicate with each other.
* The agent makes decisions during open play.
* Set-piece situations, including kick-offs, free kicks, and corners, are handled automatically.

The exact scheduling of player decisions and the handling of overlapping or simultaneous actions must be verified in the starter code.

3.2 State space

The assignment describes the state space primarily in terms of:

* Positions of our players.
* Positions of opposing players.
* Position of the ball.

Player positions are represented as points in continuous two-dimensional space.

This description establishes the high-level problem representation but does not prove that these are the only fields available in the runtime observation.

The complete observation schema must be established by inspecting interface.py and the relevant framework code.

3.3 Action space

The assignment identifies seven basic action types:

Action	Description
Kick	Kick the ball using a direction and power
Dribble	Move with the ball in a specified direction
Hold	Hold the ball
Clear	Clear the ball away
Go to a point	Move towards a specified position
Intercept	Attempt to intercept the ball
Tackle	Attempt a tackle in a specified direction

The agent interface includes two decision functions:

* on_ball: Determines what to do when the player has the ball.
* off_ball: Determines what to do when the player does not have the ball.

The precise signatures, input fields, return formats, legal parameter ranges, and action semantics remain to be verified from the implementation.

3.4 Formation helpers

The assignment describes two helper functions:

Function	Purpose
home_position	Returns the location a player should occupy according to the formation
points_around	Samples points around a central point at a specified distance

Their exact implementations, coordinate conventions, and edge-case behaviour must be inspected before relying on them.

⸻

4. Core Challenges

4.1 Large action space

Player positions are continuous, and some actions accept continuous parameters.

Consequently, there are many possible actions and candidate positions.

The assignment explicitly highlights the trade-off between sampling more candidate positions, increasing the branching factor, improving decision quality, and maintaining acceptable decision latency.

The actual candidate-generation and search strategy must be determined only after understanding the API and simulator mechanics.

4.2 Adversarial decision-making

The opponent actively attempts to prevent our team from scoring and to score itself.

The assignment encourages search-based approaches that consider possible actions by both teams.

This makes adversarial search a high-priority topic for further investigation. The appropriate search method cannot yet be determined because the available transition model and decision scheduling have not been verified.

4.3 Uncertain action outcomes

Football actions do not always produce their intended results. The assignment discusses uncertainty, including the possibility that a pass may not succeed.

Before selecting an uncertainty-handling method, it is necessary to determine whether the simulator exposes action-success probabilities, a usable transition model, or only observations and action interfaces.

4.4 Defensive and offensive decisions

The assignment recommends progressing beyond basic execution to improve:

* Ball progression and scoring.
* Interception.
* Defensive decisions.
* Use of opponent positions.

The agent must account for the state of the game rather than rely on a small set of hard-coded situations that may fail to generalize.

4.5 Computational efficiency

The agent must make decisions under strict time limits.

Search depth, the number of candidate actions, the representation of states, and the computational cost of evaluating actions will therefore be important design considerations.

No final optimization strategy has been selected.

⸻

5. Football Rules and Match Events

The simulator includes several football rules and events that can affect match outcomes.

The assignment identifies:

* Offside.
* Corner kicks.
* Free kicks and indirect free kicks.
* Kick-offs.
* Throw-ins.
* Goal kicks.
* Fouls.
* Yellow cards.
* Player dismissals.
* Back-passes.

Important rule details stated in the assignment include:

1. Offside: Passing to a teammate beyond the last defender may result in offside, except when the teammate is still in their own half. An offside results in a free kick for the opposing team.
2. Back-passes: A defender’s back-pass to the goalkeeper can concede an indirect free kick.
3. Yellow cards: Two yellow cards can result in a player being sent off, leaving the team with four players against five.
4. Fouls inside the box: There are no penalties in this setup. A foul inside the box concedes an indirect free kick and may also result in a card.
5. Set pieces: The agent is responsible for open-play decisions; set-piece situations are handled automatically.
6. Match resolution: There is no extra time or penalty shootout to resolve draws.

These rules establish relevant environmental constraints. Their precise implementation details have not yet been verified against the simulator source code.

⸻

6. Requirements and Restrictions

6.1 Implementation requirements

Requirement	Specification
Language	Python
Starter implementation file	src/student/agent.py
Final submission	A single file named agent.py
Required final class	Agent1
Additional deliverable	report.txt
API	Use the exposed API, including on_ball and off_ball
Input/output format	Preserve the exact expected syntax
File modifications	Do not modify files beyond those permitted by the assignment

Other classes may be defined in the same submitted file, but Agent1 must contain the final implementation evaluated by the assignment.

Anything outside the exposed API may be rejected at runtime or during manual review.

6.2 Language and dependency restrictions

* Use standard Python and libraries permitted by the starter environment.
* SciPy is explicitly prohibited, even if installed.
* Do not assume that the grading environment has additional packages.
* Do not rely on hard-coded machine-specific constants to satisfy runtime constraints.

6.3 Algorithmic scope

The assignment is closely tied to game-playing AI covered in the course.

It encourages students to:

* Formalize the state and action spaces.
* Use search-based approaches that consider actions by both teams.
* Build on algorithms and mathematical ideas taught in class, or reasonable variations of them.
* Account for the trade-off between candidate-action granularity, branching factor, decision quality, and latency.

Hard-coded solutions that do not generalize to varied tests are discouraged.

Methods based on fundamentally different mathematical principles outside the course scope are not expected and may be excluded from evaluation.

The exact set of appropriate algorithms must be determined by reviewing the lecture material and the actual API.

6.4 Report requirements

The final submission must include report.txt.

The report must:

* Succinctly describe the core ideas of the algorithms.
* Be no longer than two pages.
* Use standard 11-point Arial font.
* Begin with the submitting student’s name and entry number.

6.5 Submission and collaboration rules

The assignment specifies that:

* Submission is through Gradescope.
* Students may work individually or in pairs.
* Partners must belong to the same class group.
* Students must submit work developed from their own efforts.
* Students must not consult or copy code written by other students or use past submissions.
* Submissions will be checked for plagiarism.

The instructor also cautions against cognitive offloading to generative AI and notes that learning may be assessed elsewhere.

All assistance and implementation work must comply with the course’s academic-integrity requirements.

⸻

7. Runtime Constraints

The assignment establishes the following computational limits:

Constraint	Requirement
Match length	1,500 simulator steps
Potential decisions	Up to 1,500 per match
CPU budget	75 seconds per agent per match
Wall-clock time per decision	Maximum of 1 second
Repeated time-limit violations	Five violations for a player can result in that player being sent off for the rest of the match
Process isolation	Agents run in separate processes
Inter-agent communication	Not permitted

The assignment also describes a disconnection risk involving a blocking call lasting more than 1.5 seconds in the specified circumstances if it is not stopped by the one-second cap.

The code exposes remaining-time information, and the assignment directs students to inspect the starter code to understand it.

The exact mechanism for reading remaining time and enforcing these limits must be verified in the runtime implementation.

Design implication: The agent must remain within both the per-decision and per-match computational budgets. Better decisions are not useful if they repeatedly exceed the execution limits.

⸻

8. Relevant AI Course Concepts

The following mapping identifies topics that may be relevant. It is preliminary and does not prescribe the use of every technique.

Course topic	Lecture	Potential relevance	Status
Adversarial search	L10	Reasoning about our actions and the opponent’s responses	High priority
Goal-informed search	L06	Using heuristic estimates to prioritize promising choices	High priority
Modeling uncertainty	L07	Reasoning about uncertain action outcomes	High priority
Uninformed search	L03	State/action representations and search-cost foundations	Foundational
Markov decision processes	L08	Sequential decision-making under uncertainty	Applicability to be investigated
Local search	L05	Exploring and improving candidate decisions	Applicability to be investigated
Constraint satisfaction	L04	Understanding restrictions on feasible choices	Secondary candidate
Bayesian networks	L11	Representing probabilistic dependencies	Applicability unverified
Course overview and introduction	L01, L02	Background and course framing	Context

8.1 Adversarial search

The assignment explicitly encourages consideration of possible actions by both teams.

Adversarial search is therefore a high-priority topic for the next review stage.

Before choosing a method, the analysis must establish:

* The exact state representation.
* The available action space.
* How actions change the game state.
* Whether hypothetical transitions can be evaluated.
* How the opponent’s decisions are represented.
* The computational cost of exploring possible action sequences.

8.2 Goal-informed search

The continuous action space and branching-factor trade-off make heuristic estimates a relevant topic for investigation.

The specific heuristic functions and search methods, if any, must be selected only after reviewing the lecture notes and simulator mechanics.

8.3 Modeling uncertainty

Uncertain outcomes may affect the value of passing, tackling, and other actions.

The API must be inspected to determine whether success probabilities or transition information are available. The analysis must distinguish known simulator behaviour from estimated outcomes.

8.4 Other course methods

MDPs, local search, constraint satisfaction, and Bayesian networks are candidates for investigation, not established requirements.

Their inclusion in the course does not imply that they should all be implemented. Their relevance must be justified by the assignment and the verified simulator interface.

⸻

9. Evaluation Criteria

9.1 Fixed evaluation

The fixed component contributes approximately 70% of the assignment grade.

It consists of num_agents × k head-to-head matches against teaching-assistant agents. One of the TA agents is provided in the starter code.

The exact values of num_agents and k are not specified in the supplied statement.

9.2 Tournament evaluation

The tournament component contributes approximately 30% of the assignment grade.

The tournament includes a group stage followed by a knockout stage, with 32 agents progressing to the knockout stage.

The group stage uses a one-off round-robin format within each group. Group size will be announced after the number of submissions is known.

Group-stage points are assigned as follows:

Result	Points
Win	3
Draw	1
Loss	0

Head-to-head and knockout matches are best-of-three.

9.3 Tie-breaking rules

The assignment specifies the following tie-breaking order:

1. Higher goal difference: goals scored minus goals conceded.
2. Higher number of goals scored.
3. Fewer yellow cards, with a direct red card counting as 2.5 yellow cards.
4. Fewer fouls committed.
5. Random chance if the tie remains unresolved.

The same criteria are used to resolve equal points in group-stage rankings.

9.4 Evaluation implications

Interpretation: Goals scored alone are insufficient to characterize performance.

Goals conceded, goal difference, cards, and fouls may affect match results and tournament rankings.

Performance evaluation should therefore consider the assignment’s actual scoring and tie-breaking rules.

⸻

10. Deadlines and Logistics

The supplied assignment statement specifies the following:

Item	Requirement
Phase I deadline	5:00 p.m., 29 October 2026
Silent period begins	28 October 2026
Phase I late submission	Accepted up to two days after the deadline
Late penalty	10% per day
Phase II late days	Not available
Phase II	Tournament evaluation; date to be announced
Assignment weight	Approximately 9% of the overall course grade, subject to possible confirmation

The silent period runs from Wednesday, 28 October 2026, through the Phase I deadline. The statement says that basic queries, such as questions about starter code or basic assumptions, will not be clarified during this period.

The assignment directs students to use the A3 thread on Piazza for assignment queries, not email or Teams, and to track Piazza updates.

These details are recorded from the supplied statement. Subsequent announcements have not been independently verified.

⸻

11. Recommended Development Progression

The assignment recommends the following general progression:

1. Become familiar with the starter interface and available actions.
2. Run the starter agents and inspect their behaviour.
3. Build and test a basic implementation against the provided simple Agent2.
4. Verify that the agent can progress the ball and/or score past the goalkeeper.
5. Improve interception and defensive behaviour, including the use of opponent positions.
6. Improve efficiency while remaining within the decision and match time limits.

This is the assignment’s recommended progression, not a guarantee that any particular implementation will succeed.

No baseline agent has been executed during this review, and no match outcomes or performance measurements are claimed.

⸻

12. Unresolved Questions

The following questions must be answered before selecting an algorithm or finalizing an architecture.

12.1 Starter-code and API questions

1. What are the exact signatures and return types of on_ball and off_ball?
2. What fields are included in the observation passed to the agent?
3. How is the acting player identified, and how are team and player indices represented?
4. What are the legal parameter ranges and precise semantics of kicks, dribbles, tackles, and movement?
5. How do home_position and points_around behave, including coordinate conventions and edge cases?
6. Does the framework expose a usable transition model, or only the current observation and action API?
7. How is pass-success uncertainty represented or estimated, if at all?
8. How does the runtime expose remaining time and enforce decision limits?
9. Which a3.py commands are available for match execution, diagnostics, and replay inspection?
10. What metrics can be extracted reliably from match traces and results?
11. Does the uploaded starter archive match the current remote repository?

12.2 Simulator-mechanics questions

1. How are player decisions scheduled?
2. How are simultaneous or overlapping actions resolved?
3. Can hypothetical action outcomes be simulated, or must they be estimated?
4. What information is available about the opponent’s current action or behaviour?
5. How are action failures and uncertain outcomes handled?

Some of these questions may be answered by the same source files. They are separated here to make the evidence requirements explicit.

12.3 Course-method questions

1. Which adversarial-search methods are covered in the relevant lecture?
2. Which heuristic-search methods and principles are available in the course notes?
3. Which uncertainty-modeling techniques are taught, and what assumptions do they require?
4. Are MDPs, local search, or other course methods justified by the actual environment?
5. Which methods would remain within the assignment’s mathematical-scope restrictions?

12.4 Instructor clarification questions

These questions should be raised only if the supplied materials and starter code cannot resolve them, and subject to the assignment’s timing rules.

1. Are there Piazza clarifications that modify any requirement in the supplied statement?
2. Are there additional restrictions on generative-AI assistance beyond those stated in the assignment?
3. What are the final values of num_agents, k, and tournament group size?
4. Are there additional phase-specific submission details beyond the stated agent.py and report.txt requirements?

⸻

13. Requirements to Verify Before Solution Design

The following checklist summarizes the required evidence.

* Verify the exact observation schema.
* Verify the complete action API and legal parameters.
* Verify the semantics and execution of each action.
* Verify whether hypothetical state transitions can be evaluated.
* Verify how the simulator schedules and resolves player decisions.
* Verify the runtime API and remaining-time information.
* Inspect the provided Agent2 and available grading baseline.
* Identify commands for match execution, trace inspection, and replay.
* Review the relevant adversarial-search lecture.
* Review goal-informed search and modeling uncertainty.
* Assess other course methods against the actual problem.
* Establish reproducible metrics for goals, fouls, cards, match results, and runtime.
* Verify whether the remote repository or Piazza announcements contain updates.

⸻

14. Preliminary Assessment

The evidence currently establishes that this is a sequential decision-making problem in a multi-agent, adversarial environment.

The state space includes continuous player positions and the ball’s position. The action space contains several action types, some with continuous parameters. The agent must consider opponent behaviour, uncertain outcomes, football rules, and strict computational limits.

The assignment encourages search-based approaches grounded in the course’s AI methods.

However, the available evidence does not yet establish:

* Which specific algorithm will perform best.
* Whether the simulator exposes a usable transition model.
* Whether the agent can simulate hypothetical actions.
* How the runtime schedules and resolves decisions.
* Which additional course methods are necessary.
* How the final system architecture should be organized.

These conclusions must wait until the API, simulator implementation, and relevant course notes have been examined in detail.

Analysis recommendations

The following are recommendations for the next stage, not additional assignment requirements:

1. Verify before designing. Use the starter code to establish the exact interface and simulator mechanics.
2. Separate facts from estimates. Do not treat uncertain outcomes as guaranteed.
3. Respect course scope. Select methods supported by the lectures and permitted by the assignment.
4. Control computational cost. Evaluate candidate-action granularity and search cost against the runtime limits.
5. Measure performance. Use reproducible match results and runtime measurements rather than assuming a more complicated method will perform better.
6. Avoid premature architectural decisions. Defer final algorithm selection and architecture until the unresolved questions have been addressed.

⸻

15. Current Review Status

Area	Status
Assignment statement	Reviewed
Main requirements and restrictions	Extracted
Starter archive inventory	Inspected
Remote repository consistency	Unverified
API implementation	Detailed review pending
Simulator transition mechanics	Detailed review pending
Runtime implementation	Detailed review pending
Detailed lecture analysis	Pending
Baseline match execution	Not performed
Performance benchmarks	Not available
Final algorithm selection	Deferred
Final architecture	Deferred
Code modifications	None

Next step: Inspect src/student/agent.py, src/student/interface.py, src/student/framework/world.py, and src/student/framework/runtime.py. Use those files to resolve the API, state-transition, scheduling, and timing questions before proceeding to algorithm selection.