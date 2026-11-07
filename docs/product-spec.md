
# Chaos Garden — Product Specification

**Status:** Product baseline, version 0.2  
**Hackathon category:** Costume Contest  
**Audience:** Builders, reviewers, and anyone evaluating the product

## 1. Summary

Chaos Garden is a haunted, interactive playground for learning how distributed systems behave under pressure.

Users build or load a small software system, send work through it, introduce failures, observe the consequences, and experiment with recovery. The application turns abstract backend concepts into visible cause and effect.

**One-line pitch:** Build a system. Unleash chaos. Find out what survives.

## 2. Problem

Distributed systems are often taught through diagrams, terminology, and production stories. Those are useful, but it can be difficult to develop intuition about what happens when a queue fills up, workers disappear, or traffic exceeds capacity.

Chaos Garden gives users a safe, repeatable place to experiment. It makes behavior visible and lets users compare what happens before and after a change.

## 3. Audience and user goals

### Primary users
- Students learning backend or distributed systems.
- Junior developers learning reliability concepts.
- Developers curious about queues, worker pools, capacity, and failures.

### User goals
A user wants to:
- Understand a system's basic flow.
- See what normal operation looks like.
- Cause a failure without deploying real infrastructure.
- Understand why the failure happened.
- Try a fix and compare the result.
- Learn the technical concept without being overwhelmed.

## 4. Product experience

### Main loop
**Build → Run → Haunt → Investigate → Repair → Replay**

1. Start from a guided scenario or a simple architecture.
2. Configure a workload and run the simulation.
3. Observe components, jobs, and system metrics.
4. Trigger a failure or disturbance.
5. Inspect the event history and consequences.
6. Change a configuration or recover a component.
7. Reset or replay to compare outcomes.

### First-session expectation
A first-time user should be able to start a working experiment quickly. A starter scenario must be available; the user should not have to build everything before seeing the product work.

## 5. Core system model

The initial model is deliberately small:

- **API/service:** receives incoming work.
- **Queue:** holds work waiting to be processed; has a defined capacity and ordering.
- **Worker pool:** processes queued work; has configurable capacity.
- **Storage/completion destination:** represents successful persistence or completion.

The simulation-engine spec must define the exact behavior of these components. The UI must not invent behavior that the engine does not implement.

## 6. Functional requirements

### F-01: Starter architecture
The application provides a working starter scenario that a user can run immediately.

Acceptance:
- The starter scenario is valid and deterministic.
- Its components and connections are visible.
- The user can start an experiment without first editing the architecture.

### F-02: Architecture visualization
The system is represented as connected components on a canvas.

Acceptance:
- Component type and state are understandable.
- Connections show the direction of work.
- Healthy, degraded, failed, and recovering states are visually distinct.
- Technical labels remain visible even when themed names are used.

### F-03: Workload controls
The user can configure the amount or rate of incoming work within supported limits.

Acceptance:
- Configuration is validated.
- The UI explains what the workload setting means.
- The engine receives explicit configuration rather than reading UI state directly.

### F-04: Simulation controls
The user can start, pause, resume, reset, and replay a run.

Acceptance:
- Controls have clear and consistent behavior.
- Reset returns the engine to a clean initial state.
- Replay uses the same inputs and seed unless the user intentionally changes them.

### F-05: Failure injection
The user can introduce at least these disturbances:
- Worker unavailable.
- Traffic spike or queue saturation.

Acceptance:
- Each disturbance produces a recorded event.
- The user can see which component or behavior it affects.
- Recovery is explicit and observable.
- Failure behavior is consistent with engine rules.

### F-06: Metrics and system state
The UI displays a small, useful set of metrics, such as:
- Jobs received.
- Jobs completed.
- Jobs rejected or failed.
- Queue depth.
- Processing throughput.
- Waiting or processing time, where defined.

Acceptance:
- Metric definitions are documented in plain language.
- Values agree with engine events and state.
- Units and time windows are clear.
- Do not display misleading precision or unsupported metrics.

### F-07: Incident/event history
The user can inspect a readable sequence of important events.

Acceptance:
- Events have simulation timestamps and understandable descriptions.
- Important events identify affected components where possible.
- The history distinguishes facts from explanations.
- Users can trace a visible consequence to an event.

### F-08: Guided challenges
Provide at least three short experiments that teach a concept.

Potential examples:
- **The Endless Corridor:** observe a queue filling faster than workers can drain it.
- **The Vanishing Crew:** observe the effect of losing worker capacity.
- **The Midnight Surge:** handle a sudden increase in incoming work.

Acceptance:
- Each challenge has a goal and clear starting state.
- It can be completed or explored without external setup.
- The expected learning is explained after the experiment.
- Challenge success is based on engine state, not presentation alone.

### F-09: Haunted presentation
The product consistently expresses its haunted digital-estate identity.

Acceptance:
- The theme is visible in the first screen and main experiment.
- The architecture canvas, component states, incidents, and challenges use a coherent visual language.
- Atmosphere does not obscure technical meaning or controls.
- The UI remains readable and usable.
- Motion and sound are restrained and optional where appropriate.
- The app feels like a designed experience, not a generic dashboard with Halloween emojis.

### F-10: Deterministic experiment
The same scenario, configuration, and seed produce the same simulation outcome.

Acceptance:
- Replay is repeatable.
- The seed and relevant inputs can be inspected or retained.
- Engine tests verify determinism.
- UI animation does not alter simulation outcomes.

### F-11: Optional investigator
An optional investigator can summarize a run and suggest possible causes.

Acceptance:
- It uses actual recorded evidence.
- It distinguishes facts from hypotheses.
- It cannot change the simulation.
- The application remains functional if the AI service is absent.
- No secret key is exposed to the browser.

## 7. Non-functional requirements

### Reliability
- Invalid scenarios and configurations fail clearly.
- Simulation reset and replay are dependable.
- Core experience does not depend on an external AI service.

### Performance
- The first scenario should run smoothly on an ordinary laptop browser.
- Engine work and visual rendering are separated.
- Performance optimizations should respond to measured problems.

### Accessibility
- Readable contrast and typography.
- Essential controls are keyboard accessible.
- Status is not communicated by color alone.
- Reduced-motion preferences are respected.

### Maintainability
- Simulation code can be tested independently.
- Product rules are documented.
- Features have clear boundaries.
- Avoid speculative frameworks and unnecessary dependencies.

## 8. Visual and narrative direction

### Setting
A haunted digital estate: connected rooms, corridors, machinery, and crypts representing a software system.

### Visual mood
Dark, atmospheric, restrained, readable, and polished. Prefer a coherent dark-fantasy investigation aesthetic over generic neon cyberpunk or a cluttered Halloween theme.

### Narrative language
Use haunting, ghosts, crypts, paranormal activity, vital signs, and restoration as contextual language. Keep familiar technical terms beside themed labels where needed.

### Interaction tone
The application should feel curious and slightly eerie, not hostile, childish, or excessively theatrical. The user is an investigator experimenting with a system, not a victim of random effects.

## 9. Priorities

### P0 — Must exist for a credible submission
- Working starter architecture.
- Correct simulation engine.
- Workload and simulation controls.
- Worker failure and queue saturation.
- Useful metrics and event history.
- Deterministic reset/replay.
- Clear haunted UI and one complete experiment.
- Public deployment and repository.
- Submission documentation and demo.

### P1 — Strongly desirable
- Three guided challenges.
- Better architecture editing.
- Comparison of runs.
- Clear incident explanations.
- More polished animations and transitions.

### P2 — Stretch
- AI investigator.
- Additional components and failure types.
- Advanced scenario customization.
- Sound design or extra narrative details.

## 10. Demo story

A reliable demo should tell one clear story:

1. Show a healthy haunted system processing work.
2. Point out its workers and queue.
3. Trigger a haunting that removes worker capacity or increases traffic.
4. Show the queue growing and the system's vital signs changing.
5. Inspect the event history to understand what happened.
6. Restore capacity or change the workload.
7. Replay and show the difference.

The demo must use real working application behavior. Do not fake metrics, simulate UI-only outcomes, or promise features that are not implemented.

## 11. Risks and responses

| Risk | Response |
|---|---|
| Engine becomes too ambitious | Keep the first model small and define exact rules early. |
| Theme becomes confusing | Pair themed language with standard technical labels. |
| UI looks like a generic dashboard | Make the haunted setting part of the canvas, states, incidents, and narrative. |
| AI feature consumes time | Treat it as optional and keep the app useful without AI. |
| Metrics disagree with events | Derive metrics from authoritative engine state/events and test invariants. |
| Demo fails | Use a tested starter scenario and rehearse the exact sequence. |
| Submission requirements are forgotten | Maintain the separate submission checklist and review it before submission. |

## 12. Product acceptance

The product baseline is satisfied when a user can run a starter system, observe normal operation, cause a failure, inspect the consequences, recover or modify the system, and replay the experiment—with correct simulation behavior and a coherent haunted presentation.
