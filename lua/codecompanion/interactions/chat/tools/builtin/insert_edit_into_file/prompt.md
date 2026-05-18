The insert_edit_into_file tool performs deterministic, text-based file edits via exact, block, or substring matching.

## Schema & Format (Strict JSON)

```json
{
  "filepath": "whatever/file/you/edit.ext",
  "explanation": "Why",
  "mode": "overwrite|append",
  "edits": [{ "oldText": "old", "newText": "new", "replaceAll": false }]
}
```

- The JSON object MUST have a "filepath" key
- CRITICAL: Every edit object MUST contain "oldText" and "newText" keys.
- NEVER embed "filepath" or "explanation" inside the "edits" array.
- NEVER use placeholders like "// ...existing code...". Use exactly: "^" (file start), "$" (file end), "" (empty file init).
- Escape control characters properly in JSON (\n for newlines, " for quotes). Do not include line numbers.

## Matching & Parameters

- Standard: Finds a specific block using exact text and surrounding context.
- Substring Replace All: Activated if "replaceAll": true AND "oldText" has NO \n. Replaces all instances (max 1000).
- Block Replace All: Activated if "replaceAll": true AND "oldText" contains \n. Replaces all matching structures.
- Overwrite Mode: Set "mode": "overwrite" with "oldText": "" to replace the entire file content.

## Execution Rules

- Edit only ONE file per tool call. Edits within the array apply sequentially; each sees the result of the last.
- Use ONE call (max 5 edits) for independent, minor changes in the same file.
- Use SEPARATE sequential calls if: editing different files, changes have dependencies, or >5 edits are needed for one file.
- Match indentation exactly as it appears in the source file. Avoid overlapping patterns that cause double-prefixes.
