## General

1. Preserve behavior unless explicitly asked otherwise.
2. Do not introduce new architecture or frameworks.
3. Prefer procedural helper functions over new abstractions.
4. Prioritize readability over compactness.
5. When consolidating flows into a single file, DELETE old entry files.
6. Do NOT keep compatibility wrappers/redirect files unless I explicitly ask for backward compatibility.
7. If unsure between compatibility vs cleanup, ask before implementing.

## Conservative coding
Do not introduce:
- new UI systems
- new architecture layers
- new helper libraries
- new CSS conventions
- generic abstractions

## Code style

 - Use functional patterns
 - Procedural PHP Only - No classes, OOP, or object-oriented patterns, except when creating plugins for third party software, or Javascript files that are hosted externally
 - Helper functions - Create standalone functions, not class methods
 - Arrays over Objects - Use associative arrays, not objects
 - Do not mix unrelated cleanup (formatting, renames, reorder) with logic moves.
 - Do not “modernize everything” (no sweeping rewrites).

Prefer:
- clear multi-line logic
- intermediate variables
- simple control flow
- explicit steps

Avoid:
- collapsing logic into one-line expressions
- large inline arrays inside function calls

Code should be easy to visually scan.


## Database stuff
 - All database scheme changes i want to review them first, display them to me first 
 - Never mutate the DB scheme at runtime, have me review and apply the changes first/myself

## Runtime environment constraints (production)
 - Production stack: Apache + PHP-FPM + MySQL (strict SQL mode enabled).
 - Tenant schema uses `DATE` columns for item/invoice dates (`datebought`, `datesold`, `invoice_date`), not timestamp columns.
 - Zero/invalid legacy dates may exist (`0000-00-00`); all date SQL must be strict-mode safe.
 - Never use `DATE(col) = '0000-00-00'` in queries; use null/low-date guards instead.
 - For date-validity guards, use SQL-safe comparisons (for example `col >= '1000-01-01'`) appropriate to the SQL string context.
 - `mysqli->prepare()` limitation: do not use parameter placeholders in `SHOW COLUMNS ... LIKE ?`; use `information_schema.COLUMNS` queries instead.
 - Be careful with quoting by PHP string type:
   - SQL in PHP single-quoted strings: escape inner SQL single quotes (`\\'...\\'`).
   - SQL in PHP double-quoted strings: use plain SQL single quotes (`'...'`), no backslashes.

## UI things

 - Keep all CSS in a single file, no CSS in html or php files
 - Never add things to that file unless really needed, try to re-use what is in there first to make sure styling is consistent
 - Delete buttons are always red, form submit buttons always have the color from the applied theme, other buttons are white
 - Keep sizing of buttons consistent for the page they are on
 - Never change existing button size as a workaround for wrapping/overflow issues; fix layout/container behavior instead.
 - Never implement UI behavior that depends on a specific language string length (e.g. Dutch vs English).
 - All layout fixes must be language-agnostic and work for current + future locales.
 - When fixing wrapping/overflow, solve at component/layout level (container sizing, flex behavior, spacing), not per-language conditions.
 - Do not introduce locale-specific CSS selectors, conditions, or hacks unless explicitly requested.
 - Before finalizing UI changes, test mentally against at least EN + NL labels and longer future labels.
 - If a fix could regress other pages, propose one generic CSS pattern and apply it consistently.
 - Theme rules (tenant/app theming, delete buttons always red, sold rows orange).
 - Specific topbar/layout rules (link placement, spacing, button style consistency).

## Flows
 - When creating/editing flows for a user, always use a single file to insert/edit/view items, no multiple files
 - Respect Expert mode: delete actions hidden/blocked when off (items, invoices, users, etc.).

## Other things
 - If the user corrects behavior/expectation, explicitly ask whether that corrected rule should be added to AGENTS.md.
 - When the user asks about a concrete behavior/bug, inspect the actual relevant code/files first and answer from verified evidence; do not speculate or infer root cause before checking.
 - When adding to changelog, make sure the date is specified for the entry
 - When a new feature is requested, add to changelog
 - When a bug is found and fixed, add to changelog
 - Changelog scope rule: changes go to `CHANGELOG_REQUESTS.md`
 - Changelog gate (mandatory): if any code file is changed in a turn, update the matching app changelog in the same turn before final response.
 - Changelog order/date rule: newest entries must be on top, and entry date must match current day for the actual change being made.
 - Pre-response verification: before final response after code edits, verify both code files and changelog file are in diff.
 - Keep UI/flows exactly like pre-multi-tenant unless you explicitly ask to change them.

