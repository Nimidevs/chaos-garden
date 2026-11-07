
# Chaos Garden — Technical Steering

Persistent engineering rules for Kiro and human contributors. These rules apply to implementation, tests, reviews, and technical decisions.

## 1. Technology baseline

Use the following stack unless a deliberate, documented decision changes it:

- TypeScript for application and simulation code.
- npm workspaces for the monorepo.
- React and Vite for the web application.
- React Flow for the architecture canvas where it genuinely helps.
- Tailwind CSS for styling.
- Vitest for unit and integration tests.
- fast-check for property-based tests.
- Zod for validating external or user-provided data at boundaries.
- Zustand only if shared UI state becomes difficult to manage with simpler React state.

Do not add a library just because it is popular. Prefer the smallest dependency that solves a real problem.

## 2. Architecture rules

### 2.1 Simulation is independent of the UI

The simulation package must not import React, browser APIs, UI components, or an AI provider.

It should be possible to run and test the engine in Node without opening a browser.

### 2.2 The engine is authoritative

The engine owns:
- Virtual simulation time.
- Event scheduling and ordering.
- Component state.
- Job/request lifecycle.
- Failure and recovery behavior.
- Randomness.
- Metric-producing facts.
- Event history and replay inputs.

The web application owns:
- Rendering.
- User interaction.
- UI-only state.
- Controls that translate user actions into engine commands.
- Visual presentation of engine state and events.

The UI must not calculate a competing version of simulation truth.

### 2.3 Keep boundaries explicit

Use typed interfaces between the UI, simulation engine, and optional external services. Do not let provider-specific code leak into the simulation domain.

## 3. Simulation correctness

- Use virtual time; do not use real wall-clock time to determine simulation outcomes.
- Use a seeded pseudo-random number generator for randomness.
- Do not use ambient `Math.random()` in simulation logic.
- Use explicit event types and typed payloads.
- Define deterministic event ordering, including how equal-time events are resolved.
- Define units explicitly and use them consistently.
- Model state transitions explicitly; avoid undocumented status strings.
- Keep simulation advancement separate from rendering frame rate.
- Avoid hidden side effects in metric calculations.
- Ensure reset creates a clean run and replay uses the original inputs and seed.
- If the engine cannot process an event or configuration, fail clearly rather than silently corrupting state.

The simulation-engine spec must define exact semantics for arrivals, queue capacity, worker capacity, processing, failures, recovery, overflow, and metrics before implementation.

## 4. Testing rules

- Test the simulation package without a browser.
- Unit-test event scheduling, state transitions, queue behavior, worker behavior, and metrics.
- Test important interactions between components.
- Test deterministic replay.
- Use property-based tests for invariants where useful.
- Include edge cases such as zero workers, full queues, simultaneous events, reset, and invalid configuration.
- Do not treat passing tests as proof that the user experience is correct; manually test the actual app too.

Useful invariant examples, refined against actual engine rules:
- Queue length never exceeds configured capacity.
- A job is never processed twice.
- Completed, rejected, failed, and pending jobs are not accidentally counted as the same outcome.
- Simulated time never moves backward.
- Replaying the same inputs and seed yields the same event sequence and result.
- Metrics are consistent with the events and state they summarize.

## 5. Security and secrets

- Never commit API keys, passwords, access tokens, private credentials, or production secrets.
- Never put secrets in steering files, specs, saved prompts, source code, screenshots, or demo recordings.
- Use environment variables for runtime secrets.
- Maintain `.env.example` with variable names and safe placeholder values only.
- Ignore `.env`, `.env.local`, and other secret-bearing files in Git.
- Validate required environment variables at startup.
- Never expose server-side secrets in client-side bundles.
- Do not log secrets or sensitive request headers.
- If a secret is accidentally committed, revoke or rotate it immediately. Removing it from Git history alone does not make it safe.
- Keep the initial product free of secrets where possible. The client-side simulation itself should not require credentials.
- Any optional AI integration must use a server-side adapter or serverless function. Never call a paid/private model directly from the browser using a secret key.

## 6. Data and external input

- Validate scenario files, configuration, and external payloads at boundaries.
- Prefer explicit, versioned scenario formats if scenarios are persisted or imported.
- Do not trust imported data to contain valid component IDs, connections, capacities, or event parameters.
- Do not silently accept unsupported scenario versions.
- Keep error messages useful to humans; avoid dumping raw stack traces into the UI.

## 7. Performance and scope

- Optimize only after measuring a real bottleneck.
- Keep the engine simple enough to reason about.
- Avoid simulating every visual frame as an engine event.
- Use the event scheduler for simulation behavior; use animation only for presentation.
- Keep rendering updates separate from the engine's event-processing loop.
- Do not build a generalized simulation framework beyond the product's needs.

## 8. Code quality

- Use clear names that explain intent.
- Prefer small functions with one responsibility.
- Keep domain logic out of React components.
- Avoid unnecessary abstractions and speculative extensibility.
- Make state transitions and error paths visible.
- Comment on non-obvious decisions, not every line.
- Do not introduce `any` without a clear reason and narrow scope.
- Do not suppress TypeScript errors to make a task pass.
- Do not leave placeholder implementations presented as complete functionality.

## 9. Kiro collaboration rules

- Read relevant steering documents before proposing implementation.
- For major features, work through requirements, design, and tasks.
- Ask for clarification when a requirement changes engine semantics or product scope.
- Implement tasks incrementally.
- Run relevant tests and report what was actually run.
- Do not claim tests pass unless they were executed and passed.
- Surface assumptions, trade-offs, and unresolved questions.
- Update specs when implementation decisions materially change them.
- Preserve human-readable documentation; avoid unexplained AI-generated jargon.

## 10. Definition of done

A task is not done merely because code was generated.

It is done when:
- Its acceptance criteria are met.
- Relevant tests exist and pass.
- Type checking and linting pass where configured.
- The behavior has been manually checked when it affects the UI.
- Errors and edge cases are handled appropriately.
- The spec/task status reflects reality.
- No secrets or accidental generated files were committed.
