# Monorepo reference

## Canonical layout

```text
/
├── apps/
│   ├── mobile_app/
│   └── admin_app/
├── packages/
│   ├── features/
│   │   ├── auth/
│   │   └── profile/
│   ├── core/
│   │   ├── app_core/
│   │   └── design_system/
│   └── platform/
│       ├── networking/
│       └── storage/
├── pubspec.yaml
└── pubspec.lock
```

Directories group packages; they do not create Dart namespaces. `packages/features/auth` can declare `name: auth` and is imported as `package:auth/...`.

For a workspace with one product app, use its product identifier consistently
for the app directory and Dart package. Use role-based names such as
`mobile_app` only when several distinct applications make the role meaningful.
The root workspace may use a distinct package name to avoid a collision.

The repository uses the mixed release model: feature packages are internal by default; packages with real independent consumers and stable APIs may become independently deployable. Do not generalize an app-only feature merely to make the tree symmetrical.

This topology is a practical monorepo packaging strategy, not a fixed Clean
Architecture directory standard. Clean Architecture is enforced by ownership,
public API boundaries, and dependency direction; `presentation`,
`application`, `domain`, and `data` normally remain roles or co-located folders
inside the feature package unless an independent package boundary is justified.

## Module depth and cohesion

Use the smallest number of deep modules that give the codebase useful seams.
A deep module has a small interface and substantial implementation complexity;
callers can use its capability without knowing its internal sequence, state, or
policy. A shallow module exposes nearly as much structure as it hides and adds
navigation cost without buying locality or leverage.

Treat a Dart file or directory as a module only when it owns a coherent
concept, change, lifecycle, or dependency seam. A class-per-file layout is not
an architecture rule. Co-locate presentation, application, domain, and data
roles when they collaborate on one cohesive capability and callers do not need
to distinguish them. Separate them when the separation protects a real
dependency direction, independent change, public API, lifecycle, or consumer.

Use these tests before introducing an internal folder or file:

- **Deletion test:** would removing it concentrate meaningful complexity, or
  only move the same complexity into neighbouring files?
- **Interface test:** is the interface materially smaller than the behaviour it
  conceals and a useful test surface for callers?
- **Locality test:** can a change to one concept be understood and tested by
  staying mostly within the module?
- **Seam test:** is there a real ownership or dependency seam? One adapter is a
  hypothetical seam; two genuinely different adapters are evidence of a real
  seam.

Prefer deepening an existing module—co-locating related roles, narrowing its
public surface, and preserving constructor injection—when these tests do not
support a split. Extract a pure function or helper for a meaningful interface,
independent change, or stable test surface; extraction performed only to make
one line independently testable usually creates a shallow module and reduces
locality.

The target is not the fewest files. It is a small, legible set of deep modules:
each boundary should hide complexity, keep related decisions local, and give
tests leverage through a stable public surface.

## Dependency and interaction model

Prefer capability boundaries and one-way compile-time dependency edges:

```text
app → feature → core contracts
app → feature → platform adapter
```

Inside a feature, distinguish three questions: who imports whom, who calls
whom, and where an abstraction belongs.

### Compile-time dependencies

The following arrows mean imports or implementation relationships, not request
or data flow:

```text
presentation → application → domain contracts
data repository implementation → domain repository contract
data repository implementation → data service → platform API
```

The domain never imports a data implementation, service, or platform API.

### Runtime interaction

At runtime, an application object calls a domain repository contract. Its data
repository implementation satisfies that contract and calls a stateless data
service, which adapts the external platform or data source:

```text
presentation → application → domain repository contract
data repository implementation → data service → platform API
```

### Abstraction placement

Place an abstraction with its innermost consumer—the layer that needs to depend
on the capability rather than its implementation.

| Need                                                                    | Placement                                            |
| ----------------------------------------------------------------------- | ---------------------------------------------------- |
| A business capability used by application code                          | Contract in `domain`; implementation in `data`       |
| A collaborator required only by a use case                              | Contract in `application`; implementation outside it |
| A platform/API adapter used only by data code                           | Contract and implementation in `data/services`       |
| A reusable external integration with independent consumers or lifecycle | `packages/platform/*`                                |

Use an interface only when it preserves a real boundary: an inner layer needs
the contract, multiple implementations genuinely coexist, or a deterministic
test replacement is useful. Do not create interfaces merely to mirror concrete
classes. Keep feature-specific services in `data`; extract them to
`packages/platform/*` only for a genuine reuse, ownership, lifecycle, stable
API, or dependency boundary.

Repositories are sources of truth and own caching, refresh, retry, and error translation. Services wrap external data sources and hold no application state. A domain/use-case layer is conditional, not mandatory.

## Feature implementation defaults

Use `ChangeNotifier`/`Listenable` in examples as the SDK baseline without
prescribing a state-management package. Keep get_it responsible for
construction and lifetime; keep reactive UI state in the view model and view.

For a feature, identify its cohesive deep modules before arranging files.
Define immutable contracts and public APIs at meaningful seams, add stateless
services only when an external source needs adaptation, and test package
boundaries separately from user-visible app flows. Keep trivial role wrappers
co-located rather than creating one shallow module per Clean Architecture
label.

## get_it policy

```dart
void registerAuthDependencies(GetIt getIt) {
  if (!getIt.isRegistered<AuthRepository>()) {
    getIt.registerLazySingleton<AuthRepository>(
      () => AuthRepositoryImpl(apiClient: getIt<ApiClient>()),
    );
  }

  if (!getIt.isRegistered<AuthViewModel>()) {
    getIt.registerFactory<AuthViewModel>(
      () => AuthViewModel(repository: getIt<AuthRepository>()),
    );
  }
}
```

- eager singleton: required immediately and cheap to construct
- lazy singleton: one shared instance created on first access
- factory: new short-lived instance per lookup
- async singleton: startup resource; express dependencies and await readiness
- disposal callback: every resource that owns a stream, controller, subscription, or connection
- named registration: only when multiple implementations of the same type genuinely coexist

The app owns the locator and lifecycle. Package registration helpers receive a locator explicitly. Direct `getIt<T>()` lookups are restricted to the app composition root, registration helpers, and test setup/overrides. Domain, application, data, and view-model classes use constructor injection and do not resolve their own dependencies from `GetIt`. Tests may use a fresh `GetIt` instance or a pushed scope; scope cleanup is awaited.

Use the base scope for process-wide infrastructure. Use named scopes only for authentication/session, tenant, and test lifecycles. A scope is a runtime lifecycle boundary, not a package boundary or a replacement for widget state.

## Pub Workspace and Melos checks

The root workspace `pubspec.yaml` lists members and the root owns the lockfile. Members use `resolution: workspace` and a compatible SDK constraint. Use explicit paths for SDK versions without workspace glob support; use globs only when the repository's Dart floor supports them.

Pin Melos in the root `dev_dependencies` and configure it through the repository's root configuration. Prefer root scripts for consistent analysis, generation, and tests. Use package filters for focused feedback and root-wide runs for CI. Validate independently deployable packages outside the workspace as well as inside it.

Typical project scripts include:

```yaml
scripts:
  analyze:
    run: melos exec -- "dart analyze ."
  test:
    run: melos exec --fail-fast -- "dart test"
```

These are templates, not commands to copy blindly. Inspect existing scripts and generated-code requirements first. `melos bootstrap` is useful when bootstrap hooks or shared-dependency synchronization are configured; it is not automatically required solely to link Pub Workspace members.

## Verification checklist

- root `dart pub get` succeeds and produces the expected root lockfile/config
- `dart pub workspace list` contains every intended member
- no stray member lockfiles or package configs shadow the workspace resolution
- Melos filters select the intended packages
- every package passes format, analysis, and its tests
- generated files are current and policy-compliant
- app tests cover composition-root wiring and user-visible feature flows
- disposable registrations and runtime scopes have cleanup tests
- publishable packages pass standalone dependency resolution and API review

## Sources

- [Dart Pub Workspaces](https://dart.dev/tools/pub/workspaces)
- [Melos Getting Started](https://github.com/invertase/melos/blob/main/docs/getting-started.mdx)
- [Melos Bootstrap](https://melos.invertase.dev/commands/bootstrap)
- [Flutter Guide to app architecture](https://docs.flutter.dev/app-architecture/guide)
- [Flutter architecture recommendations](https://docs.flutter.dev/app-architecture/recommendations)
- [get_it README](https://github.com/flutter-it/get_it/blob/main/README.md)
- [get_it object registration](https://flutter-it.dev/documentation/get_it/object_registration)
- [get_it scopes](https://flutter-it.dev/documentation/get_it/scopes)
