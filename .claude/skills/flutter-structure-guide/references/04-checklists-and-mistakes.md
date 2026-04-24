# Checklists & Common Mistakes (Reference)

## File naming conventions

```text
Screens:      <feature>_screen.dart
Widgets:      <name>.dart (or <name>_widget.dart when clarity helps)
Models:       <name>.dart
Services:     <name>_service.dart
Repositories: <feature>_repository.dart
Providers:    <feature>_provider.dart
Utilities:    constants.dart / validators.dart / extensions.dart / formatters.dart / helpers.dart
```

## Common mistakes to avoid

- Don’t put API logic in widgets/screens → keep it in `services/api/` (and coordinate via repos/providers).
- Don’t put business logic in screens → keep it in providers/repositories.
- Don’t use local `setState` for shared state → use Riverpod for shared state.
- Don’t store tokens/credentials in Hive/preferences → use secure storage.
- Don’t dump everything into one file → organize by feature and layer.
- Don’t call APIs directly from widgets → route through provider/repo/service.
- Don’t hardcode URLs/keys in random places → keep them in configuration/constants.

## Project initialization checklist

- [ ] Create folder structure
- [ ] Add dependencies (Riverpod, Hive, Dio, secure storage) in `pubspec.yaml`
- [ ] Bootstrap app in `main.dart` (minimal)
- [ ] Define theme + routes under `config/`
- [ ] Set up Dio client + interceptors under `services/api/`
- [ ] Set up local storage services under `services/storage/`
- [ ] Set up Hive initialization + adapters under `hive/`
- [ ] Add first feature: model → repository → provider → screen
