---
summary: "Checks that _state.dart files do not import UI framework dependencies"
file_patterns: [["*_state.dart"]]
tags: ["dart", "architecture", "separation-of-concerns"]
sources: ["D88164:245515"]
---
# No UI Dependencies in State Classes

If this rule is not relevant to the code under review, state so and move on.

State classes (`*_state.dart`) should contain pure business logic and be independent of the UI framework. Importing UI widgets, widget base classes, popup controllers, or other presentation-layer types into a state file couples logic to the view, making it harder to test, reuse, and refactor. The whole point of extracting a `_state.dart` class is to separate concerns — leaking UI deps back in defeats that purpose.

- Flag any `*_state.dart` file that imports widget framework packages (e.g. `hxdom.dart`, `ui.dart`, widget base classes).
- Flag state classes that accept UI-layer types as constructor parameters (e.g. `PopupStackCtrl`, `Widget`, `Element`, `MenuCtrl`).
- Callback parameters are acceptable — a state class may accept a `Function` or `Future Function()` that the widget layer provides, as long as the state class does not depend on the UI type itself.
- Pure data types, model classes, and service interfaces are fine to import.

## Example

Bad:
```dart
// file: file_system_sync_state.dart
import '../../../../../ui/ui.dart';  // pulls in PopupStackCtrl, Widget, etc.

class ModuleFileSystemSyncState {
  final PopupStackCtrl _popupStack;  // UI type leaked into state
  // ...
}
```

Good:
```dart
// file: file_system_sync_state.dart
// No UI imports

typedef ShowConfirmation = Future<bool> Function(String title, String message);
typedef ShowLoadingAlert = Future<void> Function(Future<void> job, {String doneMessage, String errorMessage, String loadingMessage});

class ModuleFileSystemSyncState {
  final ShowConfirmation _showConfirmation;
  final ShowLoadingAlert _showLoadingAlert;
  // UI interactions are abstracted behind plain function types
}
```
