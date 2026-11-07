
# Chaos Garden — Product Steering

This document is persistent product guidance for Kiro and human contributors. It explains what we are building, who it is for, and the principles that should guide product decisions.

## 1. Product identity

**Name:** Chaos Garden  
**Category:** Kiroween 2026 — Costume Contest  
**Short description:** A haunted playground for building, breaking, and understanding distributed systems.  
**Product promise:** Build a system. Unleash chaos. Find out what survives.

Chaos Garden is an interactive visual simulation of a small software system under stress. Players construct or load a system made of components such as an API, queue, workers, and storage. They send work through the system, introduce failures, observe the consequences, and experiment with recovery.

The haunted theme is part of the product's identity and experience, not decoration added at the end. The system is a strange, living network. Failures are hauntings. The player is an investigator trying to understand and restore order.

## 2. The problem

Distributed systems are difficult to understand through definitions and diagrams alone. Concepts such as queue saturation, worker capacity, bottlenecks, cascading failure, and recovery become easier to understand when people can see cause and effect.

Chaos Garden makes those concepts approachable through experimentation:

1. Build a simple system.
2. Give it work to process.
3. Observe normal behavior.
4. Introduce a failure.
5. Follow the consequences.
6. Change something and try again.

The product should be useful to someone who has never operated a distributed system while remaining technically credible to a developer who knows the concepts.

## 3. Audience

Primary audience:
- Computer science students and learners.
- Junior developers and early-career engineers.
- Developers curious about backend systems and reliability.

Secondary audience:
- Experienced engineers who want a quick visual experiment.
- Hackathon judges and viewers who may not know distributed systems.

Do not assume every user understands terms such as throughput, latency, concurrency, or backpressure. Explain unfamiliar concepts in plain language where they appear.

## 4. Core experience

A user should be able to:

1. Open Chaos Garden and understand the premise without reading a long tutorial.
2. Start with a ready-made haunted system or create a simple one.
3. Run a workload and see jobs move through the system.
4. Observe useful metrics and a readable event history.
5. Trigger a failure, such as taking workers offline or overwhelming a queue.
6. See what changed and identify the likely bottleneck.
7. Make a change, replay the experiment, and compare outcomes.

The central loop is:

**Build → Run → Haunt → Investigate → Repair → Replay**

## 5. Product principles

### 5.1 The simulation is the source of truth
The engine determines what happens. The interface visualizes it. AI may explain evidence, but it must never invent simulation outcomes or secretly alter the engine.

### 5.2 Show cause and effect
When a failure happens, users should be able to trace what it affected. Avoid unexplained metric jumps or decorative animations that imply events the engine did not produce.

### 5.3 Make the first experiment fast
A new user should reach a meaningful experiment quickly. Provide a working starter scenario and sensible defaults. Do not require a complex architecture before the first run.

### 5.4 Teach without lecturing
Prefer a visible consequence, a short explanation, and an optional deeper explanation over long walls of technical text.

### 5.5 Be spooky, not confusing
Use haunted language, atmosphere, and visual storytelling, but preserve the real technical meaning. A ghost worker must still clearly be a worker.

### 5.6 Keep the simulation reproducible
The same scenario, configuration, seed, and event ordering should produce the same result. Users should be able to replay an experiment and compare changes fairly.

### 5.7 Build a complete small experience
A reliable, understandable, polished experiment is better than many half-working features.

## 6. Haunted identity and visual direction

The setting is a haunted digital estate: a garden of interconnected rooms, corridors, machines, and crypts representing software components.

The mood should be:
- Dark, atmospheric, and eerie.
- Restrained rather than noisy.
- Readable and accessible.
- More like a polished dark-fantasy investigation than a collection of Halloween stickers.

Possible visual ingredients:
- Deep charcoal, midnight, and muted purple backgrounds.
- Controlled accents such as spectral green, candlelight amber, or ghostly cyan.
- Fog, subtle glow, restrained particles, and carefully used motion.
- Gothic or display typography for titles only.
- Readable sans-serif typography for technical content.
- Distinct states for healthy, degraded, failed, and recovering components.

Do not make the interface depend on color alone. Use labels, icons, and shapes to distinguish states. Respect reduced-motion preferences and avoid flashing effects.

### Theme vocabulary

Use themed language in headings, story text, challenges, and event descriptions. Keep standard terminology close by when needed.

Examples:
- Haunting — a failure or injected disturbance.
- Ghost workers — workers that process jobs.
- Crypt — persistent storage.
- Paranormal activity — unusual events or incidents.
- Vital signs — system metrics.
- Exorcise or restore order — recovery action.

These are presentation labels, not mandatory replacements for technical terminology. Never rename a concept so much that users cannot understand it.

## 7. MVP scope

The first complete version should support one understandable system:

- An API/service that receives work.
- A bounded FIFO queue.
- A configurable worker pool.
- A storage component or simplified completion destination.
- A configurable workload.
- A deterministic simulation engine.
- Start, pause, resume, and reset controls.
- Worker failure and recovery.
- Queue saturation or a traffic spike.
- A live architecture view.
- A small set of meaningful metrics.
- An event history that explains what happened.
- At least three guided challenges or experiments.
- A clear, polished haunted interface and demo path.

The simulation-engine spec must define exact component behavior before implementation. The UI must not invent rules that the engine does not implement.

## 8. Scope boundaries

Not part of the initial MVP:
- Real cloud infrastructure or actual distributed deployment.
- Kubernetes, container orchestration, or packet-level network simulation.
- Multiplayer or collaborative editing.
- A full production observability platform.
- Complex authentication or user accounts.
- A general-purpose workflow editor.
- An AI agent that independently changes the architecture.
- A large collection of components before the basic system works.

## 9. AI investigator (optional and bounded)

An investigator may summarize an incident and explain likely causes using recorded simulation evidence.

Rules:
- The engine remains authoritative.
- The investigator receives structured facts, not an unrestricted opportunity to invent metrics.
- Separate observed facts from hypotheses and recommendations.
- Point to relevant events or metric changes where practical.
- It must not mutate simulation state or trigger actions without explicit user action.
- The core experience must work when the AI service is unavailable.
- Never expose API keys in the browser or repository.

Treat this as a stretch feature. Do not sacrifice the engine, replay, challenges, or polish to add it.

## 10. UX and accessibility

- Every major control has a clear label and understandable effect.
- Explain unfamiliar metrics near where they appear.
- Provide useful empty, loading, error, and paused states.
- Make the current simulation state obvious.
- Do not rely only on animation to communicate a change.
- Keep text contrast readable against dark backgrounds.
- Support keyboard interaction for essential controls.
- Respect reduced-motion settings.
- Avoid excessive sound; sound must be optional and unobtrusive by default.

## 11. Success criteria

Chaos Garden is successful when:
- A first-time user can explain the basic premise after a short interaction.
- A user can run a system, cause a failure, and observe its consequences.
- Displayed metrics agree with recorded events and engine state.
- Replay with unchanged inputs produces the same result.
- A user can identify at least one bottleneck or failure cause through evidence.
- The app remains usable without an AI provider.
- The haunted theme is clear in the experience and presentation.
- The project is small enough to finish, test, deploy, and demonstrate reliably.

## 12. Decision rule

When choosing between features, prioritize:

1. Correct simulation behavior.
2. A complete and understandable experiment.
3. Clear visual feedback and evidence.
4. Theme and presentation quality.
5. Optional advanced features.

If a feature makes the system harder to understand, less deterministic, or less likely to be finished, simplify or postpone it.
