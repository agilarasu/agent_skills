# Placement Decision Tree (Reference)

```text
Is it a full-screen UI?
├─ YES → screens/<feature>/<feature>_screen.dart (and screens/<feature>/widgets/)
└─ NO ─┐
       Is it a reusable UI component (used in multiple screens)?
       ├─ YES → widgets/<category>/<name>.dart
       └─ NO ─┐
              Is it a data model / DTO / enum / exception?
              ├─ YES → models/<name>.dart
              └─ NO ─┐
                     Is it external API, storage, auth, analytics, notifications?
                     ├─ YES → services/<category>/<name>.dart
                     └─ NO ─┐
                            Is it Riverpod state management?
                            ├─ YES → providers/<feature>_provider.dart
                            └─ NO ─┐
                                   Is it data access logic coordinating API/cache?
                                   ├─ YES → repositories/<feature>_repository.dart
                                   └─ NO ─┐
                                          Is it a utility/helper/constant/formatter/validator/extension?
                                          ├─ YES → utils/<file>.dart
                                          └─ NO → reconsider architecture
```

## Layering (preferred flow)

`UI → Provider → Repository → Service → (Dio / Hive / SecureStorage)`
