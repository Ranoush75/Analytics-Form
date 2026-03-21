# 📊 Analytics Dashboard Requirements Intake Form — Project Brief

**Project:** Interactive Requirements Intake Form for Analytics Dashboards  
**Created:** March 2026  
**Owner:** Rana Ghazzi

---

## Overview

This document tracks all requirements, decisions, and iterations made during the design and build of a domain-agnostic analytics dashboard requirements intake form. The form is intended to collect structured specifications before building any interactive dashboard, regardless of dataset type or industry domain.

---

## Request Log

### Request 1 — Initial Concept
**Ask:** Build a data analytics dashboard requirements intake form with selectable options.

**Selections captured via interactive widget:**
- Dashboard Type: Business / KPI Dashboard, Data Engineering / Pipeline Monitoring, Financial Analytics, Operational / Real-Time Monitoring
- Data Sources: PostgreSQL / SQL Database, Databricks / Delta Lake, REST API / JSON, CSV / Flat Files
- Output Format: Interactive Web App (React/HTML), Tableau Workbook, Python Script + Charts, Word / PDF Report

---

### Request 2 — Output Format Decision
**Ask:** Clarify what to write and in what format.

**Decision:**
- Output: Requirements intake form
- Format: React / HTML artifact

---

### Request 3 — Print Summary Feature
**Ask:** Output should print the selections from the HTML form.

**Changes made:**
- Added a **Generate Summary** button that compiles all form selections into a formatted summary panel
- Added a **Print / Export PDF** button that triggers `window.print()`
- Added print-specific CSS that hides the form and renders only the summary panel cleanly on paper

---

### Request 4 — Add Seller / Entity Options + Interactive Dashboard Parameters
**Ask:** Add seller options and any parameters/specifications needed to build an interactive dashboard.

**Changes made:**
- Added interactivity parameters section covering filters, controls, and interactive behaviors
- Added entity/record type field for flexible domain modeling
- Expanded filter options to include user/assignee filter (covering seller-type roles generically)
- Added parameters: Top-N selector, granularity toggle, comparison period, custom segment/tag
- Added interactive behaviors: cross-chart filtering, drill-down, tooltips, export, alerts, annotations, embed

---

### Request 5 — Make Form Domain-Agnostic + Project Brief
**Ask:** Make the form generic for any dataset, not specific to orders/selling. Options should be generic enough for any domain. Also create a markdown file documenting all requests in the project.

**Changes made:**

#### Form redesign (v2.0)
- Removed all sales/order-specific language
- Replaced fixed domain options with a neutral domain/industry dropdown (12 categories)
- Replaced "sellers" with generic **User / Assignee Filter**
- Made primary entity field free-text (e.g. Customer, Patient, Transaction, Event)
- Expanded data source types to cover IoT, NoSQL, SaaS, manual/survey data
- Added **Historical Depth Needed** field
- Expanded metric types to cover distributions, forecasts, anomaly flags
- Added **Benchmark / Target Values** field
- Added **Grouping / Dimensions** field for flexible breakdowns
- Expanded chart types: added histogram, funnel/waterfall, tree map, Gantt/timeline
- Added **Device / Viewport** selector for responsive delivery planning
- Expanded output platform list: Metabase, Superset, Grafana, Streamlit
- Added **Row-level Security** and **IP Allowlist** to access model options
- Print summary updated to reflect all new fields

#### This markdown brief
- Created to document the full request history and decisions made throughout the project

---

## Form Sections (v2.0)

| # | Section | Key Fields |
|---|---------|-----------|
| 01 | Project Overview | Name, owner, domain, go-live, priority, purpose, objective |
| 02 | Dataset & Data Sources | Description, entity type, row volume, source types, freshness, history depth, DQ issues |
| 03 | Metrics & KPIs | Primary KPIs, metric types, dimensions, benchmarks |
| 04 | Visualizations & Layout | Chart types, layout style, color theme, layout notes |
| 05 | Interactivity & Parameters | Filters/controls, interactive behaviors, custom parameters |
| 06 | Audience & Delivery | Audience, platform, access model, viewport, concurrent users, notes |

---

## Technical Details

- **Format:** Single-file HTML with embedded CSS and JavaScript
- **Fonts:** Space Mono (headings), DM Mono (body)
- **Aesthetic:** Dark/neon — consistent with ranaghazzi.com brand
- **Interactions:** Checkbox toggle, radio pill selection, range slider, dynamic summary generation
- **Print:** CSS `@media print` hides form, renders summary only — clean PDF export

---

## Files

| File | Description |
|------|-------------|
| `dashboard-intake-form.html` | Production-ready intake form (v2.0) |
| `dashboard-intake-brief.md` | This project brief |
