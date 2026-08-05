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
public interfaces, seams, and dependency direction—not by file count or layer
symmetry. `presentation`, `application`, `domain`, and `data` normally remain
cohesive internal roles or folders inside the feature package unless an
independent package seam is justified.

Prefer deep modules: a small interface should hide substantial policy,
transformation, lifecycle, or error handling. A shallow module exposes nearly
as much knowledge as its implementation and usually adds indirection without
leverage. Before splitting a role into a module, apply the deletion test: if
deleting it only relocates the same complexity into callers, keep the code
together. Internal seams are useful for implementation structure and tests;
they do not have to become public interfaces or packages.

Create a separate module or package when it has a concrete seam and a reason
that survives the deletion test, such as independent ownership or release, a
genuinely reusable capability, a meaningful compile-time dependency constraint,
a distinct lifecycle, or multiple concrete adapters that actually vary. One
adapter is a hypothetical seam; two adapters are evidence of a real one. Do
not create interfaces for every concrete class, one-method pass-through use
case, repository wrapper, mapper, or view-model command solely to imitate a
layered diagram.

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

Place an abstraction with its innermost consumer—the role or module that needs
to depend on the capability rather than its implementation. The abstraction's
location should make the seam useful to callers and tests, not merely reproduce
the names of Clean Architecture layers.

| Need | Placement |
| --- | --- |
| A business capability used by application code | Contract in `domain`; implementation in `data` |
| A collaborator required only by a deep application module | Contract beside that module; implementation outside its seam |
| A platform/API adapter used only by data code | Contract and implementation in `data/services` |
| A reusable external integration with independent consumers or lifecycle | `packages/platform/*` |

Use an interface only when it preserves a real seam: an inner module needs the
contract, multiple adapters genuinely coexist, or a deterministic test
replacement is useful. Do not create interfaces merely to mirror concrete
classes. Keep feature-specific adapters in `data`; extract them to
`packages/platform/*` only for a genuine reuse, ownership, lifecycle, stable
API, or dependency boundary.

Repositories are sources of truth and own caching, refresh, retry, and error translation. Services wrap external data sources and hold no application state. A domain/use-case layer is conditional, not mandatory.

## Feature implementation defaults

Use `ChangeNotifier`/`Listenable` in examples as the SDK baseline without
prescribing a state-management package. Keep get_it responsible for
construction and lifetime; keep reactive UI state in the view model and view.

For a feature, start with the smallest cohesive deep module and expose only the
interface its callers need. Define immutable contracts at real seams, add
stateless adapters only when an external source needs adaptation, and keep
layer roles internal when splitting them would reduce locality. Test through
the module interface; add focused internal tests when an internal seam makes
complex behaviour easier to verify, but do not promote that seam to a public
package boundary without an independent reason. Test package interfaces
separately from user-visible app flows.

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
