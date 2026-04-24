# Placement Rules (Reference)

## `main.dart`

- Put: `main()`, `runApp()`, lightweight setup calls (Riverpod/Hive init), app root widget.
- Don’t put: feature logic, service internals, large UI trees (beyond root app widget).

## `config/`

- `app_config.dart`: base URLs, feature flags, env config, version constants.
- `theme.dart`: `ThemeData`, color/typography tokens, component themes.
- `routes.dart`: named routes, navigation setup, route guards, deep linking.

## `screens/`

- Feature folders: `screens/<feature>/`
- Main page widget: `screens/<feature>/<feature>_screen.dart`
- Feature-only widgets: `screens/<feature>/widgets/`
- Don’t put: reusable cross-feature widgets (go to `widgets/`), data fetching logic, business logic.

## `widgets/`

- Reusable components used by multiple screens.
- Group by category (buttons, dialogs, input, cards, etc.).
- Prefer: one widget per file.

## `models/`

- Data structures, DTOs, response wrappers, enums, exceptions.
- Ok: `fromJson()` / `toJson()`.
- Keep data-focused; avoid embedding app logic.
- If using Hive annotations (`@HiveType()`), keep them here; adapters/registration live elsewhere.

## `services/`

Low-level, integration-oriented code.

- `services/api/`: Dio setup, interceptors, endpoint wrappers.
- `services/storage/`: Hive CRUD service, secure storage wrapper, preferences (optional).
- `services/auth/`: auth workflows, token handling/refresh.

## `providers/`

- All Riverpod providers grouped by domain/feature.
- Providers orchestrate state and call repositories/services (depending on complexity).

## `repositories/`

- Domain-level data access methods (fetch + cache + mapping + retry/error translation).
- Coordinate API + local cache decisions.
- Called by providers.

## `utils/`

- Pure helpers: constants, validators, extensions, formatters, logger, small helpers.
- Avoid: side effects and business workflows.
