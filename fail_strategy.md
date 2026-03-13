# Pipeline Fail Strategy

---

## 1. Fail Fast (Stop on First Error)
Wrap each phase in try/except — if one fails, log it and stop immediately. Nothing downstream runs.

**Best for:** Pipelines where bad data in one stage would corrupt the next.

```python
try:
    extract()
    load_raw()
    transform_bronze()
    run_dq_checks()
    transform_gold()
except Exception as e:
    logging.error(f"Pipeline failed: {e}")
    raise
```

---

## 2. Retry with Backoff
On failure, retry the stage N times with a wait between attempts before giving up.

**Best for:** Transient errors — network blips, DB connection drops, API timeouts.

```python
import time

def retry(fn, retries=3, delay=5):
    for attempt in range(1, retries + 1):
        try:
            return fn()
        except Exception as e:
            logging.warning(f"Attempt {attempt} failed: {e}")
            if attempt == retries:
                raise
            time.sleep(delay)
```

---

## 3. Dead Letter / Quarantine Bad Rows
Instead of failing the whole pipeline, route bad rows to a separate `quarantine` table and continue with clean rows.

**Best for:** DQ checks in the Bronze layer — don't let a few bad rows kill the whole run.

```python
good_rows = df[df['id'].notna()]
bad_rows  = df[df['id'].isna()]
load_to_table(good_rows, 'bronze.orders')
load_to_table(bad_rows,  'quarantine.orders')
```

---

## 4. Idempotent Stages + Safe Re-run
Design each stage so it can be safely re-run without duplicating data (use upsert, not plain insert). If it fails mid-run, just re-run — no cleanup needed.

**Best for:** All stages. This should be a baseline, not an option.

---

## 5. Checkpoint / Resume
Track which stage completed successfully in a `pipeline_runs` table. On re-run, skip completed stages and resume from the failed one.

**Best for:** Long-running pipelines where re-extracting from source is expensive.

```sql
CREATE TABLE pipeline_runs (
  run_id UUID,
  stage  TEXT,
  status TEXT,  -- 'success' | 'failed'
  ran_at TIMESTAMP
);
```

---

## Recommended Combination for This Pipeline

| Stage            | Strategy                              |
|------------------|---------------------------------------|
| Extract          | Retry with backoff (3×)               |
| Load Raw         | Idempotent append + retry             |
| Bronze transform | Quarantine bad rows + DQ checks       |
| Gold transform   | Fail fast — only runs if Bronze passed|
| Overall          | Log all failures, alert on Slack/email|
