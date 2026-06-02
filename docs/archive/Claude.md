# RADON Creative Swarm

Valtech RADON internal system for AI-native campaign ideation.
This repo builds a **stateful multi-agent creative engine**, not a generic chatbot.
Primary goal: generate **original, strategically strong, brand-aware campaign concepts** through layered agent orchestration.

Core stack:
- Python
- LangGraph
- FastAPI
- Pydantic
- Vector DB (Chroma or Qdrant)
- MCP tools
- React + React Flow for visual cockpit

Primary architecture:
- Backend/core is the source of truth
- Frontend/cockpit visualizes and controls the system
- Canvas must never become the execution engine
- Agent outputs must be traceable through lineage, scoring, and ranking

## Non-negotiables

- Treat `LangGraph` as the orchestration core for agents, loops, state, and checkpoints.
- Treat the React Flow app as a **cockpit**, not as workflow truth.
- Keep business logic out of UI components and HTTP route handlers.
- Every important transformation must be explicit in code: generation, critique, mutation, ranking, selection.
- Prefer deterministic state transitions and inspectable outputs over “magic” hidden prompting.
- Optimize for originality, brand alignment, and explainability — not just fluent text.

## Hard Rules

- ALWAYS define or update types when changing agent state or node contracts.
- ALWAYS model agent interactions through explicit graph nodes, edges, reducers, or orchestration helpers.
- ALWAYS preserve idea lineage: parent/child relations, generation count, mutation history, evaluator scores.
- ALWAYS separate core logic from transport layers: keep scoring, mutation, Elo, and lineage logic outside API/UI code.
- ALWAYS include error handling for model calls, MCP tools, vector retrieval, and event streaming.
- ALWAYS make new behavior observable through logs, traces, or state snapshots.
- NEVER put secrets, tokens, or credentials in code, docs, prompts, or examples.
- NEVER move core orchestration logic into frontend code.
- NEVER introduce a new framework, orchestration layer, or dependency without approval.
- NEVER replace explicit state with opaque prompt-only behavior when state is required.
- NEVER refactor unrelated files while solving a local task unless approved.

## Preferences

- Prefer small, composable nodes over giant pipelines.
- Prefer typed Pydantic models or clearly typed dict contracts at system boundaries.
- Prefer pure functions for scoring, filtering, ranking, mutation, and normalization.
- Prefer boring, debuggable infrastructure over clever abstractions.
- Prefer additive changes that preserve observability.
- Prefer concise naming and predictable folder structure.

## Anti-Patterns

- Generic chatbot UX pretending to be a multi-agent system
- Hidden prompt chains without state visibility
- UI-driven architecture where canvas structure defines runtime behavior
- Monolithic “super agents” doing generation, critique, and ranking in one step
- Scores without provenance
- Mutation loops without termination conditions
- Ranking without reproducible logic
- Brand retrieval injected as giant unfiltered context dumps
- Overwriting existing ideas instead of preserving lineage

## Success Criteria

Good implementations:
- are easy to inspect and debug
- make graph behavior legible
- preserve idea lineage across generations
- show why an idea won or lost
- support human review at key checkpoints
- improve output quality without hiding system behavior
- make the cockpit more useful over time without changing core truth

## Core Workflow

Expected system flow:
1. Ingest brief
2. Retrieve brand DNA via RAG
3. Fetch external signals via MCP tools
4. Run divergent creative swarm
5. Run adversarial critique layer
6. Mutate or prune weak ideas
7. Rank surviving ideas with explicit selection logic
8. Expose finalists, lineage, scores, and traces to the cockpit

## Commands

Use the real project commands if they exist. If missing, ask before inventing workflow commands.

Typical commands:
- backend dev: `uvicorn app.main:app --reload`
- frontend dev: `pnpm dev`
- backend tests: `pytest`
- frontend lint: `pnpm lint`
- typecheck: `pnpm tsc --noEmit`

Before suggesting “done”, run or reference the relevant test/lint/typecheck commands when available.

## Workflow Rules

- When asked to change architecture, confirm scope first.
- When debugging, inspect the actual failing path before proposing a rewrite.
- When adding a node or layer, state:
  - what inputs it reads
  - what state it writes
  - how it affects lineage, scores, or control flow
- When suggesting UI work, explain what comes from backend truth vs frontend presentation.
- Flag complexity, technical debt, weak observability, or unclear ownership immediately.
- If a rule should really be enforced automatically, recommend CI/test enforcement instead of prose.

## Human Approval Required

Ask before:
- adding major dependencies
- changing repo structure significantly
- changing graph architecture across multiple layers
- modifying persistence, auth, deployment, or secrets handling
- deleting files
- introducing external paid services

## Context Routing

For backend-specific rules, also read:
- `backend/CLAUDE.md`

For frontend/cockpit-specific rules, also read:
- `frontend/CLAUDE.md`

For graph/orchestration-specific rules, also read:
- `docs/architecture/graph-system.md`

For design principles of the cockpit, also read:
- `docs/product/cockpit-principles.md`