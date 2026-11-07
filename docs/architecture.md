
# Chaos Garden — Architecture

**Status:** Initial architecture baseline, version 0.2  
**Purpose:** Explain the system in language a developer can understand before diving into implementation.

## 1. Overview

Chaos Garden has two main parts:

1. **Web application:** what the user sees and interacts with.
2. **Simulation engine:** the system that decides what happens during an experiment.

The web application displays the simulation. It must not independently invent simulation results.

```text
┌─────────────────────────────────────────────────────┐
│                 React Web App                      │
│                                                   │
│  Architecture Canvas · Controls · Metrics         │
│  Event History · Challenges · Haunted UI          │
└───────────────────────┬───────────────────────────┘
                        │ commands / snapshots
                        ▼
┌─────────────────────────────────────────────────────┐
│             Simulation Controller                 │
│       Start · Pause · Reset · Replay              │
└───────────────────────┬───────────────────────────┘
                        ▼
┌─────────────────────────────────────────────────────┐
│              Simulation Package                   │
│                                                   │
│  Scenario Validation                             │
│  Virtual Clock + Event Scheduler                 │
│  Component Models                                │
│  Job / Request Lifecycle                         │
│  Seeded Randomness                               │
│  Event History                                   │
│  Metrics                                         │
└─────────────────────────────────────────────────────┘
```

Optional services such as an AI investigator or saved-run API sit outside the engine and communicate through explicit adapters.

## 2. Main boundary: engine versus interface

### Simulation engine
Responsible for:
- Advancing virtual time.
- Processing scheduled events.
- Moving jobs through the system.
- Enforcing component capacities and states.
- Applying failures and recovery.
- Recording what happened.
- Producing state and metric data.
- Supporting deterministic replay.

### Web application
Responsible for:
- Drawing components and connections.
- Showing component states and job movement.
- Rendering metrics and event history.
- Providing controls for users.
- Displaying explanations and challenge instructions.
- Applying the haunted visual identity.

The interface may animate a job moving between components, but the animation is only a visual representation. It cannot decide whether the job completed.

## 3. Suggested package layout

```text
packages/simulation/src/
├── domain/
│   ├── types.ts
│   ├── scenario.ts
│   └── configuration.ts
├── engine/
│   ├── engine.ts
│   ├── scheduler.ts
│   ├── clock.ts
│   └── random.ts
├── components/
│   ├── service.ts
│   ├── queue.ts
│   ├── worker.ts
│   └── storage.ts
├── events/
│   ├── event-types.ts
│   └── event-log.ts
├── metrics/
│   ├── metrics.ts
│   └── definitions.ts
├── scenarios/
│   └── starter-scenario.ts
└── index.ts
```

This is a suggested organization. Adjust it if the implementation stays simpler without some files.

## 4. Virtual time and event scheduling

The engine uses **virtual time**, not the computer's wall clock.

For example, the simulation might process:
- At virtual time 0 ms: a job arrives.
- At virtual time 20 ms: a worker starts processing it.
- At virtual time 120 ms: processing completes.

The application can run this experiment faster or slower than real time. The outcome should not depend on the user's laptop speed or browser frame rate.

A scheduler stores future events and processes them in order. Events with the same timestamp need a deterministic tie-breaking rule. The simulation-engine spec must define that rule before implementation.

## 5. Reproducibility

Each experiment has explicit inputs:
- Scenario.
- Workload configuration.
- Failure/recovery schedule or user commands.
- Random seed, if randomness is used.

The engine must use a seeded random generator rather than ambient randomness. Given the same inputs and seed, the same event ordering and outcome should be produced.

The UI may render differently at different frame rates, but the simulation result must remain the same.

## 6. Component model

The first scenario contains a small set of component types:

| Component | Responsibility |
|---|---|
| Service/API | Receives incoming jobs. |
| Queue | Holds jobs that are waiting. |
| Worker pool | Processes jobs up to its configured capacity. |
| Storage/completion destination | Represents successful completion or persistence. |

The engine spec will define how jobs move between these components, how capacity works, what happens when a component fails, and what the metrics mean.

## 7. Job lifecycle

The engine should represent a job's lifecycle explicitly. A conceptual lifecycle is:

```text
Incoming
   │
   ▼
Accepted ──► Waiting in queue ──► Processing ──► Completed
   │                 │                 │
   └── Rejected      └── Failed       └── Failed
```

This is conceptual, not yet a complete state machine. The engine spec must define valid transitions and what happens to jobs during worker failure, queue overflow, reset, and recovery.

## 8. Events and metrics

Events are the record of what happened. Metrics summarize those events and the current state.

Examples of events:
- Job arrived.
- Job accepted or rejected.
- Job started processing.
- Job completed.
- Worker became unavailable.
- Worker recovered.
- Queue reached capacity.

Examples of metrics:
- Jobs received/completed/rejected.
- Current queue depth.
- Throughput over a defined interval.
- Waiting time or processing time.

Every metric must have a clear definition and unit. The engine spec must determine whether a metric is cumulative, instantaneous, or measured over a time window.

Avoid calculating the same metric independently in the UI and engine.

## 9. UI-to-engine interface

The web app should interact with the engine through a small controller or adapter.

Conceptually:

```text
User clicks "Start"
        │
        ▼
Controller sends command to engine
        │
        ▼
Engine processes scheduled events
        │
        ▼
Controller exposes updated state/events
        │
        ▼
UI renders the new state
```

Commands and snapshots should be typed. UI state such as selected panel or open tooltip should not be stored in simulation state.

## 10. Failure injection and recovery

Failures are explicit engine commands or scheduled events. They change component availability or behavior according to documented rules.

The UI should show:
- What failed.
- When it failed.
- Which component was affected.
- What consequences followed.
- When recovery happened.
- Whether the system returned to healthy behavior.

A visual effect alone is not a failure. Every failure shown in the UI must correspond to engine state or an engine event.

## 11. Optional AI investigator

The investigator receives a bounded evidence package, such as:
- Relevant event records.
- Metric changes.
- Component states.
- Simulation time range.

It returns a summary, possible causes, and suggestions. It does not become part of the engine's execution path.

If the AI service is unavailable, users can still inspect the event history and complete the core experience.

## 12. Testing strategy

### Engine tests
- Event ordering and virtual time.
- Queue capacity and overflow.
- Worker availability and capacity.
- Job lifecycle transitions.
- Failure and recovery.
- Metrics.
- Deterministic replay.
- Invalid configurations.

### Integration tests
- A job moves through the starter architecture.
- A failure produces the expected consequences.
- Recovery changes the outcome as expected.
- Metrics agree with event history.

### UI tests and manual checks
- Essential controls work.
- Component states are visible.
- Metrics and event history are readable.
- The haunted theme does not obscure meaning.
- The demo scenario works from a clean start.

## 13. Decisions still to be made in the engine spec

The following must be explicitly settled before implementation:
- How simulation time advances.
- Event ordering when timestamps are equal.
- Workload arrival model.
- Queue discipline and capacity behavior.
- Worker concurrency and service duration.
- Job behavior when workers fail.
- Recovery behavior.
- Metric definitions and time windows.
- Reset and replay semantics.
- Supported component configuration and validation.

Do not treat these open decisions as already implemented behavior.
