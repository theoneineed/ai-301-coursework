## Diagnosis
The `_check_skip()` function passes the string `"IngestedSource"` to `db_session.query()` instead of the model class, throwing an `ArgumentError`. However, that is only the first failure. The `IngestedSource` model lacks a `source_id` column, so `.filter_by(source_id=...)` will still fail. Finally, `_record_ingested_source` is a log-only placeholder that never writes a row to the database, meaning re-ingestion will never be skipped even if the query is fixed.

## Scope
- **In scope:** Updating `_check_skip()` to use the `IngestedSource` model class and filtering by valid columns (such as `source_url` or `content_hash`). Implementing `_record_ingested_source` to correctly construct and save an `IngestedSource` record to the database.
- **Out of scope:** Refactoring the broad `except Exception` block.

## Files touched
- `ingestion/pipeline.py`

## Approach
1. Modify `_check_skip()` to query the `IngestedSource` class and filter using valid model columns instead of `source_id`.
2. Implement `_record_ingested_source()` to insert and commit an `IngestedSource` row into `db_session`.

## Test Plan
1. Run existing repository unit tests to ensure no regressions.
2. Update `repro.py` to seed an `IngestedSource` row using valid schema columns (e.g., `source_url` instead of `source_id`).
**Expected before fix:** The script throws the `ArgumentError`.
**Expected after fix:** The warning disappears, `_check_skip` finds the properly seeded row, and returns `skipped=True`.

## Risks and Unknowns
Mapping the incoming `source_id` to valid columns like `source_url` or `content_hash` requires verifying how the rest of the pipeline passes these arguments.

## Deviations
Column Mapping Choice: Mapped the incoming source_id to the existing source_url database column inside both _check_skip() and _record_ingested_source() because the IngestedSource model schema lacks a dedicated source_id column.
Database Recording: Fully implemented the database insertion and commit/rollback logic in _record_ingested_source() to ensure records persist and can be matched by subsequent ingestion runs.
