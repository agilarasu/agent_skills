# Directory Tree (Reference)

## Canonical layout

```text
lib/
├── main.dart
├── config/
│   ├── app_config.dart
│   ├── theme.dart
│   └── routes.dart
├── screens/
│   ├── home/
│   ├── profile/
│   ├── settings/
│   └── auth/
├── widgets/
│   ├── common/
│   ├── buttons/
│   ├── dialogs/
│   ├── input/
│   ├── cards/
│   └── app_bars/
├── models/
├── services/
│   ├── api/
│   ├── storage/
│   ├── auth/
│   ├── notifications/
│   └── analytics/
├── providers/
├── repositories/
├── utils/
├── l10n/       (optional)
└── hive/
    ├── adapters.dart
    └── hive_setup.dart

test/
├── unit/
├── widget/
└── integration/
```

## One-line intent per folder

- `config/`: theme, routes, app-level constants/config
- `screens/`: full pages, feature folders
- `widgets/`: reusable UI across screens
- `models/`: data-only types (DTOs, enums, exceptions)
- `services/`: low-level API/storage/auth integrations
- `repositories/`: data access coordination (API + cache + mapping)
- `providers/`: Riverpod state management
- `utils/`: pure helpers/constants/extensions/validators/formatters
- `hive/`: initialization + adapter registration (not CRUD)
