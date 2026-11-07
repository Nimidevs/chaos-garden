
# Chaos Garden — Repository Structure Steering

This document explains where code and project documentation belong. Keep the structure understandable. Do not create folders just to satisfy a diagram.

## 1. Intended repository layout

```text
chaos-garden/
├── .kiro/
│   ├── steering/
│   │   ├── product.md
│   │   ├── tech.md
│   │   └── structure.md
│   ├── specs/
│   │   └── <feature-name>/
│   │       ├── requirements.md
│   │       ├── design.md
│   │       └── tasks.md
│   ├── hooks/
│   └── agents/
│
├── apps/
│   └── web/
│       └── src/
│           ├── app/
│           ├── features/
│           │   ├── architecture/
│           │   ├── simulation/
│           │   ├── incidents/
│           │   └── challenges/
│           ├── components/
│           └── styles/
│
├── packages/
│   └── simulation/
│       └── src/
│           ├── domain/
│           ├── engine/
│           ├── components/
│           ├── events/
│           ├── metrics/
│           └── scenarios/
│
├── docs/
│   ├── product-spec.md
│   ├── architecture.md
│   └── hackathon/
│       ├── submission-checklist.md
│       └── kiro-usage-log.md
│
├── .env.example
├── .gitignore
├── LICENSE
├── README.md
├── package.json
└── pnpm-workspace.yaml
```

This is a target structure, not a demand to create every directory immediately. Create a folder when its first real file is needed.

## 2. Ownership and responsibilities

### `.kiro/steering/`
Persistent project guidance. These documents should be relatively stable and apply across features.

### `.kiro/specs/`
Feature-specific planning documents. Each spec folder describes a meaningful feature or subsystem.

Kiro can create and update these files through its spec workflow. Developers review and maintain them too.

### `.kiro/hooks/`
Kiro hook configuration for automating useful development actions. Add hooks only when their behavior is understood and useful. Do not add untested automation merely to make the project look advanced.

### `.kiro/agents/`
Optional custom Kiro agents and their configuration. Use them for a genuinely distinct review or workflow role, not as decoration.

### `apps/web/`
The browser application: screens, components, styles, interactions, and UI state.

### `packages/simulation/`
The simulation domain and engine. It must remain independent of React and browser-specific code.

### `docs/`
Human-facing project documentation, architecture explanations, hackathon records, and submission preparation.

## 3. Dependency direction

Allowed:

```text
apps/web  ───────>  packages/simulation
apps/web  ───────>  optional API/adapters
```

Not allowed:

```text
packages/simulation  ───────>  apps/web
packages/simulation  ───────>  React or browser UI
```

The simulation package should be reusable and testable without the web application.

## 4. Naming conventions

- Use descriptive, lowercase kebab-case for feature spec folders, e.g. `simulation-engine`.
- Use names that reflect the domain, not temporary implementation details.
- Keep public simulation types and functions deliberately small.
- Prefer feature-based organization in the web app over one enormous global components folder.
- Avoid duplicate definitions of simulation types in the UI.

## 5. Repository hygiene

- Keep `.kiro/` at the repository root.
- Commit relevant `.kiro/` files to Git; do not ignore the folder.
- Keep generated build output, dependency folders, local environment files, and secrets out of Git.
- Keep `.env.example` safe to publish.
- Keep the README useful to someone discovering the project for the first time.
- Ensure the repository includes an appropriate open-source `LICENSE`.
- Avoid committing personal notes, private data, or unreviewed agent output.

## 6. Documentation quality

A developer or judge should be able to understand:
- What Chaos Garden is.
- How to run it locally.
- How the simulation works at a high level.
- Where the engine and UI live.
- How Kiro contributed to development.
- How to access the deployed application and demo.

Prefer short explanations and diagrams over unexplained jargon.
