---
name: flutter-apply-architecture-best-practices
description: Apply boundary-first architecture to Flutter/Dart monorepos using Melos, Dart Pub Workspaces, and get_it. Use when changing package boundaries, feature layers, dependency injection, runtime scopes, or workspace validation.
---

# Architecting Flutter Monorepos

Use a **boundary-first** process: preserve repository facts, make ownership and
dependencies explicit, keep composition in the app, and verify the workspace
before declaring the change complete.

Read [REFERENCE.md](REFERENCE.md) for the canonical topology, dependency and
abstraction rules, get_it lifetimes and scopes, Melos/Pub Workspace details,
examples, verification checklist, and source links. Treat it as the single
source of truth for those reference facts.

## Process

### 1. Inventory the workspace

Inspect before proposing structure or editing code:

- root `pubspec.yaml`, SDK constraint, workspace configuration, and lockfile
- root Melos configuration, scripts, filters, and pinned version
- every app, package, public entrypoint, and dependency edge
- composition roots, `GetIt` instances, registration helpers, scopes, and code generation
- tests, analysis configuration, CI commands, and publish settings

Separate discovered constraints from recommendations. Completion criterion:
every workspace member, relevant dependency edge, DI entrypoint, and validation
command is accounted for.

### 2. Choose the smallest useful boundary

Apply the mixed topology in [REFERENCE.md](REFERENCE.md). Choose boundaries by
ownership, public API, lifecycle, independent consumers, release needs, and
dependency direction—not by mirroring `presentation`, `application`, `domain`,
and `data` as separate packages.

Keep product composition in `apps/*`; keep capability packages, shared core
contracts/UI, and reusable platform integrations in their appropriate package
groups. Name packages for their capability, not their directory category.

Completion criterion: every new package has an explicit boundary reason, and no
package exists solely to reproduce a layer or make the tree symmetrical.

### 3. Model dependencies and public APIs

Draw the one-way compile-time graph from the canonical dependency model in
[REFERENCE.md](REFERENCE.md) before changing imports. Keep compile-time
dependencies distinct from runtime interaction and data flow.

Apply the abstraction placement, public API, repository, service, and use-case
rules in the reference. Keep pure Dart contracts and business logic free of
Flutter dependencies where practical.

Completion criterion: every changed import follows the graph, no cycle or app
reverse edge exists, every abstraction has a consumer-owned reason, and the
public API exposes no accidental implementation detail.

### 4. Make composition explicit

Follow the app-owned locator, registration, constructor-injection, lifetime,
readiness, replacement, disposal, and lookup rules in the reference.

Completion criterion: the app composition root visibly defines registration
order, lifetime, async readiness, replacement, disposal, and all collaborators
needed by business/application code.

### 5. Own runtime scopes at the app lifecycle

Apply the scope ownership, lifecycle, initialization, cleanup, and disposal
rules in the reference. Completion criterion: every pushed scope has a named
owner, a reachable exit path, awaited cleanup, and tests for parent restoration
or replacement.

### 6. Implement the feature in its owning package

For each new or changed feature:

- confirm the owning package and dependency graph
- define immutable contracts and the public API
- add stateless services only when an external source needs adaptation
- add repositories for transformation, caching, retry, and error policy
- add a use case only when Step 3 requires one
- implement a view model with immutable UI state and commands
- keep rendering, layout, animation, and simple routing in lean views/widgets
- add an explicit registration helper and call it from the app composition root
- add package unit tests and app integration coverage for user-visible flows

Use the feature implementation defaults in the reference. Completion
criterion: each checklist item is implemented or marked unnecessary with a
reason, and the feature is reachable through its owning package's public API.

### 7. Verify the workspace and release posture

Run the repository-approved commands for dependency resolution, workspace
listing, formatting, analysis, generation, and tests. Use Melos filters for
focused feedback, then run the app checks that consume changed packages. Apply
`melos bootstrap` only when the repository's hooks or synchronization policy
requires it.

For every independently deployable package, validate declared dependencies
outside the workspace and review public API, version, changelog, and migration
impact.

Completion criterion: workspace resolution, package and app checks, generated
output, dependency-cycle checks, and publishable-package standalone resolution
all pass or have a recorded intentional exception.

## Decision guardrails

Treat Flutter architecture guidance, get_it, and Melos as repository-serving
defaults. Preserve repository policy when it exists. When a materially missing
choice affects publishing, state management, ownership, or lifecycle, surface
the ambiguity with a recommended option before editing; record the decision in
the resulting structure or documentation.
