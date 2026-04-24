---
name: flutter-structure-guide
description: Enforces a detailed Flutter app folder structure (config/screens/widgets/models/services/providers/repositories/utils/hive/l10n) with Riverpod, Hive, Dio, and flutter_secure_store. Use when scaffolding or refactoring a Flutter project, adding new features/files, or when the user asks where a file should go or how to organize the project.
---

# Flutter Structure Guide (Detailed)

Use this skill to keep the project aligned with the preferred structure references in this folder.

## Additional references (read when needed)

- `references/01-directory-tree.md`
- `references/02-placement-rules.md`
- `references/03-decision-tree.md`
- `references/04-checklists-and-mistakes.md`

## Canonical directory map (lib/)

- `main.dart`: app bootstrap only (init + `runApp()`); keep minimal.
- `config/`: app-wide configuration
  - `app_config.dart`: env/base URLs/feature flags/version constants
  - `theme.dart`: `ThemeData`, color/type tokens, component themes
  - `routes.dart`: routing table, guards, deep links
- `screens/`: full-page UI, organized by feature
  - `screens/<feature>/<feature>_screen.dart`
  - `screens/<feature>/widgets/`: widgets used only by that screen/feature
- `widgets/`: reusable UI used across multiple screens, grouped by category (`common/`, `buttons/`, `dialogs/`, `input/`, `cards/`, `app_bars/`)
- `models/`: data models/DTOs/enums/exceptions; keep data-only (serialization ok)
- `services/`: external integrations + low-level operations
  - `services/api/`: Dio client, interceptors, endpoint wrappers
  - `services/storage/`: Hive CRUD, secure storage wrapper, preferences (optional)
  - `services/auth/`: auth + token handling
  - `services/notifications/`, `services/analytics/`: optional integrations
- `repositories/`: data access layer that coordinates services and caching decisions
- `providers/`: Riverpod state management; providers call repositories
- `utils/`: pure helpers/constants/validators/extensions/formatters/logger
- `hive/`: Hive setup + adapter registrations (not CRUD)
- `l10n/`: optional localization `.arb` files

## Placement decision tree (use this exactly)

1. Full-screen UI?
   - Yes → `screens/<feature>/...`
2. Reusable UI used by multiple screens?
   - Yes → `widgets/<category>/...`
3. Data model / DTO / enum / exception?
   - Yes → `models/...`
4. External integration / API / storage / auth / notifications?
   - Yes → `services/<category>/...`
5. Riverpod state management?
   - Yes → `providers/...`
6. Data access logic coordinating services + cache?
   - Yes → `repositories/...`
7. Pure helpers/constants/extensions/formatters/validators?
   - Yes → `utils/...`
8. Otherwise → reconsider architecture; avoid dumping into random folders.

## Layering rules (don’t break these)

- **UI** (`screens/`, `widgets/`) must not call network/storage directly.
- **Providers** talk to **repositories** (not directly to Dio/Hive).
- **Repositories** coordinate between **services** and caching, handle mapping, retries, error translation.
- **Services** contain Dio/Hive/secure storage details and platform concerns.

## Package-specific constraints

- **Riverpod**: keep all providers in `providers/`, one file per domain (`<feature>_provider.dart`).
- **Hive**:
  - Models with `@HiveType()` live in `models/`.
  - Adapter registration and box opening live in `hive/`.
  - CRUD lives in `services/storage/hive_service.dart` (or similar).
- **flutter_secure_store**:
  - Never store tokens/credentials in Hive/SharedPreferences.
  - Wrap secure storage access in `services/storage/secure_storage.dart`.
- **Dio**:
  - Client setup + interceptors in `services/api/`.
  - Endpoint wrappers in `services/api/api_service.dart` (or feature-specific api services if the project grows).

## Naming conventions (default)

- Screens: `<feature>_screen.dart`
- Providers: `<feature>_provider.dart`
- Repositories: `<feature>_repository.dart`
- Services: `<name>_service.dart`
- Models: `<name>.dart` (snake_case)

## When generating or moving code

- Put each new file in the canonical folder.
- If a file violates layering (e.g., a widget calling Dio), refactor so the call flows:

  `UI → Provider → Repository → Service → (Dio/Hive/SecureStorage)`

## Reference

If anything is ambiguous, follow the references listed above in `references/`.

