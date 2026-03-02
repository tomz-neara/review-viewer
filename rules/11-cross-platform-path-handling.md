---
summary: "Checks that file path operations handle platform differences (Windows vs Unix separators)"
file_patterns: [["*.dart"], ["*.java"], ["*.py"]]
tags: ["mikeh", "correctness", "cross-platform"]
sources: ["D86985", "D58987"]
---
# Use Platform-Agnostic Path Handling

If this rule is not relevant to the code under review, state so and move on.

When constructing or manipulating file paths, use platform-agnostic APIs instead of string concatenation with `/`. Hardcoded `/` separators break on Windows. Also watch for case-sensitivity assumptions — Windows paths are case-insensitive.

Mike's principle: *"be careful with `/` esp on windows"*

- Flag string concatenation used to build file paths (e.g. `dir + "/" + file`).
- Flag hardcoded `/` or `\` separators in path construction.
- Recommend `path.join()` (Dart), `os.path.join()` / `pathlib.Path` (Python), `Paths.get()` (Java).
- Watch for case-sensitive path comparisons that will fail on Windows.

## Example

Bad:
```dart
String path = baseDir + '/' + fileName;
if (path.endsWith('.Dart')) { ... }  // case-sensitive check
```

Good:
```dart
import 'package:path/path.dart' as p;
String path = p.join(baseDir, fileName);
if (p.extension(path).toLowerCase() == '.dart') { ... }
```
