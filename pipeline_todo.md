# Data Pipeline To-Do List

---

## Phase 1 — Environment & Project Setup
- [ ] Create project folder structure (`/src`, `/config`, `/tests`, `/logs`)
- [ ] Create and activate virtual environment
- [ ] Install dependencies: `psycopg2`, `pandas`, `python-dotenv`, `logging`
- [ ] Create `.env` file with source and warehouse DB credentials
- [ ] Create `config.yaml` with source table list, schema names, batch size
- [ ] Add `.env` to `.gitignore`

**Test:** Import all libraries, load `.env`, print config values — confirm no errors.

---

## Phase 2 — Source Extraction
- [ ] Write `extract()` function to connect to source DB (psycopg2)
- [ ] Query source table(s) and return data as a DataFrame
- [ ] Add `_extracted_at` timestamp column to each row
- [ ] Support full extract (first run) and incremental extract (watermark on `updated_at`)
- [ ] Log row count extracted

**Test:** Run `extract()`, print first 5 rows and row count, confirm `_extracted_at` column exists.

---

## Phase 3 — Raw / History Landing Layer
- [ ] Create raw schema in PostgreSQL (`raw`)
- [ ] Write `load_raw()` function — append-only, never overwrite
- [ ] Add metadata columns: `_source`, `_batch_id`, `_ingested_at`
- [ ] Load extracted DataFrame into raw table using `COPY` or `INSERT`
- [ ] Log rows inserted

**Test:** Run `load_raw()`, query raw table, confirm row count grows on each run (history retained).

---

## Phase 4 — Bronze Layer (Cleansing & Transformation)
- [ ] Create bronze schema in PostgreSQL (`bronze`)
- [ ] Write `transform_bronze()` function — reads from raw, writes to bronze
- [ ] Apply cleansing:
  - [ ] Remove duplicates
  - [ ] Handle nulls (fill or flag)
  - [ ] Cast data types correctly
  - [ ] Standardize text fields (trim, lowercase)
- [ ] Add surrogate key if needed
- [ ] Use `INSERT ... ON CONFLICT` (upsert) to keep bronze current

**Test:** Run `transform_bronze()`, query bronze table, check for nulls, duplicates, and correct types.

---

## Phase 5 — Data Quality Checks
- [ ] Write `run_dq_checks()` function after bronze load
- [ ] Check: no null primary keys
- [ ] Check: row count > 0
- [ ] Check: no duplicate keys
- [ ] Check: date fields within expected range
- [ ] Log pass/fail result per check — raise alert on failure

**Test:** Introduce a bad row manually, run checks, confirm the check catches and logs it.

---

## Phase 6 — Gold Layer (Aggregation & Business Logic)
- [ ] Create gold schema in PostgreSQL (`gold`)
- [ ] Write `transform_gold()` function — reads from bronze, writes to gold
- [ ] Apply business logic (e.g. revenue calc, status mapping, joins)
- [ ] Apply aggregations (e.g. daily totals, customer summaries)
- [ ] Write results to gold table using `INSERT ... ON CONFLICT` or full refresh

**Test:** Run `transform_gold()`, query gold table, manually verify one aggregation against bronze source.

---

## Phase 7 — Orchestration & Logging
- [ ] Write `main()` to run all phases in order: extract → raw → bronze → DQ → gold
- [ ] Use Python `logging` module to log each step with timestamps
- [ ] Write logs to `/logs/pipeline.log`
- [ ] Add try/except around each phase — log error and stop on failure

**Test:** Run `main()` end-to-end, review `pipeline.log`, confirm all phases logged with row counts.

---

## Phase 8 — Scheduling & Final Verification
- [ ] Schedule `main()` with cron (or Airflow DAG) on desired interval
- [ ] Run pipeline twice — confirm raw grows, bronze/gold refresh correctly
- [ ] Confirm history is preserved in raw, no data loss
- [ ] Document runbook: how to run, backfill, and troubleshoot

**Test:** Run pipeline twice back-to-back, query `SELECT COUNT(*) FROM raw` — count must increase each run.
