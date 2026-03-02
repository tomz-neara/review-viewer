---
summary: "Checks that duplicated logic is factored into shared helpers"
file_patterns: [["*"]]
tags: ["mikeh", "readability", "maintainability"]
sources: ["D56510", "D70026", "D65292"]
---
# Avoid Code Duplication

If this rule is not relevant to the code under review, state so and move on.

When the same logic appears in multiple places, extract it into a named helper function. Duplicated code creates maintenance burden — when the logic needs updating, all copies must be found and changed. Even 3-4 duplicated lines warrant extraction if they represent a coherent operation.

Mike's principles: *"factor out a helper? Phabricator is highlighting copied code"*, *"potentially factor this out to its own function `def get_bytes_per_point(laz_file)` since it looks like a common operation"*

- Flag blocks of logic that appear more than once with only minor variations.
- Suggest extracting a named helper that captures the shared operation.
- The helper name should describe the operation, making both call sites more readable.

## Example

Bad:
```python
# In test_a:
with open(laz_file, 'rb') as f:
    header = f.read(227)
    point_count = struct.unpack('<I', header[107:111])[0]
    point_size = struct.unpack('<H', header[105:107])[0]

# In test_b (same block copy-pasted):
with open(laz_file, 'rb') as f:
    header = f.read(227)
    point_count = struct.unpack('<I', header[107:111])[0]
    point_size = struct.unpack('<H', header[105:107])[0]
```

Good:
```python
def get_laz_point_info(laz_file: str) -> tuple[int, int]:
    """Read point count and point record size from a LAZ file header."""
    with open(laz_file, 'rb') as f:
        header = f.read(227)
        point_count = struct.unpack('<I', header[107:111])[0]
        point_size = struct.unpack('<H', header[105:107])[0]
    return point_count, point_size
```
