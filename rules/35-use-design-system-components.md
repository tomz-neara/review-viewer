---
summary: "Checks that UI code uses existing design system components and tokens instead of ad-hoc styling"
file_patterns: [["*.dart"], ["*.tsx"], ["*.jsx"], ["*.vue"]]
tags: ["eric", "ui", "consistency"]
sources: ["D82174", "D75889", "D79528"]
---
# Use Design System Components

If this rule is not relevant to the code under review, state so and move on.

When a design system or shared component library exists, new UI code should use it. Eric flags ad-hoc styling — hardcoded colors, hand-rolled dialogs, one-off spacing — that duplicates or contradicts existing components. Bypassing the design system creates visual inconsistency and doubles maintenance cost when the system evolves.

- Flag hardcoded color values, font sizes, or spacing where the project defines theme tokens or constants for these.
- Flag hand-rolled UI widgets (dialogs, buttons, dropdowns) when a standard component already exists in the codebase.
- Flag inline styles or raw platform styling that duplicates what a shared widget or theme class provides.
- Flag UI elements whose appearance varies by feature flag or state in ways that break visual consistency (e.g., a button that looks different in one mode for no design reason).
- Introducing a genuinely new pattern is fine — flag reimplementing an existing one.

## Example

Bad:
```dart
Container(
  padding: EdgeInsets.all(12),       // magic spacing
  decoration: BoxDecoration(
    color: Color(0xFF2196F3),        // hardcoded color
    borderRadius: BorderRadius.circular(8),
  ),
  child: Text('Submit', style: TextStyle(fontSize: 14, color: Colors.white)),
)
```

Good:
```dart
AppButton(
  label: 'Submit',
  style: AppButtonStyle.primary,     // design system component with themed styles
)
```
