---
summary: "Checks that CLI tools use stdout for structured data and stderr for logs and diagnostics"
file_patterns: [["*.py"], ["*.dart"]]
tags: ["mikeh", "cli", "correctness"]
sources: ["D56510", "D87014"]
---
# CLI Output Conventions: stdout for Data, stderr for Logs

If this rule is not relevant to the code under review, state so and move on.

Command-line tools must separate structured output (data) from diagnostic output (logs, progress). Structured data goes to stdout so it can be piped; logs, warnings, and progress indicators go to stderr. Mike enforces this distinction to ensure CLI tools compose correctly in pipelines.

- Flag `print()` used for diagnostic messages in CLI tools — use `stderr` or a logging framework.
- Flag structured output (JSON, CSV) written to stderr.
- Use proper `output()` or equivalent for data, not `print()`.

## Example

Bad:
```python
def main():
    print(f"Processing {len(files)} files...")  # log on stdout
    result = process(files)
    print(json.dumps(result))  # data on stdout — mixed with logs
```

Good:
```python
def main():
    print(f"Processing {len(files)} files...", file=sys.stderr)  # log on stderr
    result = process(files)
    print(json.dumps(result))  # data on stdout — clean for piping
```
