---
summary: "Checks that variables and parameters handling coordinates, timestamps, or measurements document their units"
file_patterns: [["*.dart"], ["*.java"], ["*.py"], ["*.cpp"]]
tags: ["mikeh", "documentation", "spatial"]
sources: ["D81681", "D56510", "D76209", "D80444"]
---
# Document Units and Coordinate Conventions

If this rule is not relevant to the code under review, state so and move on.

Any variable, parameter, or field handling coordinates, timestamps, distances, or measurements must make its units and conventions explicit — either through the name or a doc comment. For coordinate systems, document SRID, axis order (lat/lng vs lng/lat), and whether the representation is column-major or row-major.

Mike's principles: *"oh, column major? inverted in my head, just add a small note I guess"*, *"please use fuller names like `start_timestamp_seconds`"*, *"clarify... input/output coordinate order, units"*

- Flag variables named `t0`, `t1`, `x`, `y`, `dist`, `offset` etc. that lack unit documentation.
- Encode units in variable names where practical: `startTimestampSeconds`, `distanceMeters`, `angleDegrees`.
- For coordinate systems, document the SRID and axis order.

## Example

Bad:
```dart
double t0;
double t1;
List<double> coords;
```

Good:
```dart
/// Start of time range in UTC seconds since epoch.
double startTimestampSeconds;

/// End of time range in UTC seconds since epoch.
double endTimestampSeconds;

/// Coordinates as [longitude, latitude] pairs in EPSG:4326.
List<double> coordsLngLat;
```
