# Fabric Planning — Research Brief

Compiled 2026-09-16. Sources: Microsoft Learn (`docs/iq/plan`, 227 md files mirrored
to `research/learn-docs/`), `microsoft/fabric-samples`, fabricplanning.io,
and `Diaz506/fabric-space-lab` (structural reference only).

---

## 1. What Fabric Planning is

An EPM/CPM (enterprise performance management) workload built natively into
Microsoft Fabric, under **Fabric IQ**. It replaces the standalone-planning-tool +
spreadsheet pattern: budgets, forecasts, and scenarios live on the same governed
semantic models as the analytics.

Powered by **Lumel** (BPM Partners' Best Overall EPM Vendor 2025), but shipped and
positioned as a first-party Fabric workload with a dedicated dev team.

**Status:** GA worldwide. Billing began August 1, 2026.

### Four components
| Component | Purpose |
|---|---|
| **Planning sheets** | Budgeting, forecasting, scenario modeling. Excel-like grid over a semantic model. |
| **PowerTable** | Governed data app — reference/master data at scale, forms, approvals, automations, Gantt, SCD. |
| **Intelligence sheets** | Reporting canvas — variance charts, KPI cards, matrices, IBCS, paginated reports. |
| **Infobridge** | Data integration + **connected planning** — consolidates sheets so a regional change flows to the corporate roll-up automatically. |

---

## 2. Prerequisites (the demo will die without these)

**Capacity:** F2 or higher, or a Fabric trial. Power BI Pro and PPU are **not**
supported for XMLA/embed-token scenarios. P1–P5 Premium works.

**Workspace role:** Member or Admin. **Contributor is not sufficient** — it cannot
create cloud connections or generate embed tokens.

**Tenant settings** (admin portal):
1. Integration → *Allow XMLA endpoints and Analyze in Excel with on-premises semantic models*
2. Developer → *Embed content in apps*
3. Developer → *Service principals can call Fabric public APIs* (SPN auth only)

**Capacity setting:** XMLA Endpoint = Read Only or Read Write.

**Browser:** Edge or Chrome. Safari unsupported.

**Semantic model:** Admin or Build permission. Not in *My workspace*. Not a
composite model. Direct Lake / DirectQuery need a gateway with fixed credentials
(no SSO yet).

---

## 3. Billing model — the part people get wrong

Session-based, not per-user-license. A session triggers when a user opens, creates,
or edits a plan item, runs **730 hours (30 days)**, and **cannot be ended early**.

| Role | Who | 30-day consumption |
|---|---|---|
| **Planner** | FP&A analysts, modelers, admins | 847 CU-hours |
| **Stakeholder** | Business users, reviewers, approvers | 168 CU-hours |
| **Viewer** | Execs, report consumers | 37 CU-hours |

- Scoped per **tenant + user + capacity**. Two capacities = two sessions.
- Multiple workspaces on one capacity → billed at the **highest role active**.
- Role upgrade prorates the old session and starts a new one at the higher tier.
  Downgrades only take effect after expiry.
- Automation jobs (PowerTable) and connected-planning instances (Infobridge):
  **2 CU per successful job**. Failed jobs are free.
- Budget a **~30% capacity buffer** for the surrounding Fabric workloads
  (SQL, OneLake, XMLA).
- If capacity is paused/deleted mid-session, remaining CUs land on the Azure bill.

**Role assignment is behavioral, not pre-assigned** (per fabricplanning.io, verified
against Learn 2026-09-12):
- Opening/viewing anything → **Viewer**
- Entering or changing plan numbers, approving, writing back, creating/editing
  PowerTables or Intelligence sheets → **Stakeholder**
- Creating a Planning sheet or editing its design/rules/writeback destinations →
  **Planner**. Also: creating a new plan item upgrades the creator to Planner.
- Since 2026-08-19, building a PowerTable or Intelligence report no longer makes
  someone a Planner.

Estimator: https://community.fabricplan.com/capacity-pricing/

---

## 4. Writeback

Only destination supported is a **Fabric SQL database**. Writeback does *not*
update the connected semantic model — it stores plan data separately.

Four formats:
- **Long** — one row per cell
- **Wide** — one column per measure
- **Long with changes** / **Wide with changes** — change history, only modified cells

Features: auto-writeback, comment capture, runtime measure selection, runtime
filtering, column validation (non-empty, formula rules like `Value > 500M`, or
hard-stop on failure), and per-scenario control over what gets committed.

Gotcha: deleting a row in a planning sheet does **not** delete it from the SQL table.

A Fabric SQL database is auto-created in the workspace when you create a plan item
(it stores the plan's metadata) — separate from any writeback destination you set up.

---

## 5. Hard limits worth calling out in the lab

| Limit | Value |
|---|---|
| Sheets per plan item | 25 |
| Visuals per plan item | 50 |
| Writeback | 1.2M cells per operation |
| Infobridge query | 1.2M cells (~5M across 5 sheets) |
| Bulk data input | 1M rows |
| PowerTable sort / group by / insight / find-replace / scrollbar | 5M rows |
| PowerTable Gantt & Resource layout | 30,000 rows |
| Excel export raw / label | 20M cells / 5M cells |
| Automation cascading trigger depth | 2 levels |
| Automation DB trigger writeback | 10 records per trigger type |

Other traps: don't rename the workspace or the semantic model (both break the plan
item). One semantic model per plan item, permanently — plan for a different source
means a new plan item. RLS: users with no assigned role see the **union** of all roles.
PowerTable ignores database-level RLS (queries run as the connection identity).

---

## 6. The official tutorial series (12 parts) — our skeleton

Source: `learn.microsoft.com/fabric/iq/plan/planning-tutorial/`

### Planning sheets (parts 0–6)
| # | Title | Substance |
|---|---|---|
| 0 | Introduction & environment setup | Import pbix, create SQL DB, create Plan app, connect semantic model + DB, first sheet, tour the UI |
| 1 | Allocation and collaboration | Top-down $28.5M → $30M target with proportional allocation by prior-year mix; **quarter locking** so increments only hit Q3/Q4; bottom-up sales plan; 8% segment uplift; collaboration columns; approval workflow |
| 2 | Optimizer | Goal-seek: back-calculate editable input measures (sales growth %, COGS) to hit a **$12.5M gross profit** target. Requires editable copies — native/semantic measures are read-only |
| 3 | Forecasting & writeback | Rolling 2026 forecast, statistical forecast (Predict) from 24 months actuals, close January as actuals land, extend horizon to Jan 2027, commit to Fabric SQL |
| 4 | Measure model | Organize semantic model measures into a P&L hierarchy; Best-Case and Cost-Restructuring scenarios compared side by side |
| 5 | Row model builder | Build the P&L from scratch: Net Profit → Gross Profit → Net Revenue, COGS, OpEx as connected nodes |
| 6 | Cube | Regional Plan + Product Plan + Profitability sheet; cube measures auto-allocate across dimensions and keep all three in sync |

### PowerTable (parts 7–8)
| # | Title | Substance |
|---|---|---|
| 7 | Get started with PowerTable | Move IT asset data out of spreadsheets into a governed data app: create DB, sheet per table, format/connect columns, insert & update |
| 8 | Approvals, access controls, automations | Approval workflows on the assets sheet, access control, automation jobs |

### Intelligence sheets (parts 9–11)
| # | Title | Substance |
|---|---|---|
| 9 | Introduction to intelligence sheets | Variance chart, grouped line chart, KPI card, matrix, date filter, dashboard header, comments |
| 10 | P&L intelligence sheet | Calculated + input rows, formatting, custom columns, report header, export |
| 11 | Planning sheet + PowerTable + canvas | Planning simulation, embed planning sheet in intelligence sheet, KPI blending semantic model + plan, PowerTable embedded in the canvas |

---

## 7. Sample data available

From `microsoft/fabric-samples` → `docs-samples/iq/plan/`:

| File | Size | Use |
|---|---|---|
| `Northwind_FMCG.pbix` | 9.3 MB | Main planning tutorial model. Seven tables: **Measures Table**, **Date**, **Fact Transactions** (revenue/cost actuals by geography + product; tutorial text calls it "Sales Transactions"), **Geography**, **P&L Measures** (driver-level: sales volume, avg selling price, COGS components, OpEx), **P&L Rows** (line items as rows), **Product** |
| `planning-tutorial-dataset.pbix` | 9.3 MB | Identical size — likely the same model under the tutorial name |
| `Northwind-FMCG-assets-powertable-tutorial.xlsx` | 36 KB | IT asset data for PowerTable labs |
| `region_dimension_pt.xlsx` | 6 KB | Region dimension for PowerTable |
| `gantt-data.csv` | 1 KB | Gantt layout sample |
| `Enterprise Dataset.pbix` | 65 MB | Large-scale model |
| `transaction-dataset.pbix` | 44 MB | Large transaction model |
| `semantic-modeling-sample.pbix` | 6 MB | Semantic modeling sample |

**Implication:** unlike fabric-space-lab (which generates synthetic data from NASA
APIs + Python), Fabric Planning has ready-made first-party sample models. The lab
does not need a data-generation step — a big reason it will be shorter.

---

## 8. Structural lessons from fabric-space-lab

What to carry over:
- Fictional company + role-based framing ("You've just been hired as ZOSA's Lead
  Data Engineer"), with each module advancing the story
- Numbered module table with topics + estimated time, total time stated up front
- A maturity table (GA / Preview) so nobody trips over a preview feature
- Repo layout: `README.md`, `labs/`, `data/`, `notebooks/`, `assets/diagrams/`,
  `assets/screenshots/`
- "No API key? sample data included" — the frictionless fallback
- Explicit prerequisites and who the lab is for

What to change:
- 15 modules / 9.5 hrs → target **6–8 modules / ~2–2.5 hrs**
- No Python data generation (first-party pbix samples exist)
- Audience shifts from data engineers to **FP&A + finance-facing data pros**
- Add a **cost/sizing module** — the session billing model is the single most
  misunderstood thing about this workload and belongs in the lab, not a footnote

---

## 9. Design decisions this lab made

1. **Narrative** — a fictional company (Harborlight Provisions Co.) wrapped around the
   Northwind FMCG sample, so the story and the sample numbers agree without relabeling
   the model.
2. **Format** — a GitHub repository with numbered modules, following the fabric-space-lab
   structure.
3. **Scope** — planning sheets, optimizer, forecasting and writeback, P&L models, and the
   intelligence canvas as the core path; PowerTable optional; Infobridge described rather
   than built.
4. **Cost coverage** — session billing gets first-class treatment in Modules 00 and 08,
   because it is the most misunderstood aspect of the workload.

---

## 10. Reference links

- Overview: https://learn.microsoft.com/fabric/iq/plan/overview
- Planning sheets: https://learn.microsoft.com/fabric/iq/plan/planning-overview
- Prerequisites: https://learn.microsoft.com/fabric/iq/plan/overview-prerequisites
- Limitations: https://learn.microsoft.com/fabric/iq/plan/overview-limitations
- Roles: https://learn.microsoft.com/fabric/iq/plan/overview-roles
- Billing: https://learn.microsoft.com/fabric/iq/plan/resources/billing-fabric-plan
- Writeback: https://learn.microsoft.com/fabric/iq/plan/planning-concept-writeback
- Connected planning: https://learn.microsoft.com/fabric/iq/plan/infobridge-concept-connected-planning
- Tutorial part 0: https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/planning/tutorial-0-introduction
- Samples: https://github.com/microsoft/fabric-samples/tree/main/docs-samples/iq/plan
- Community hub: https://fabricplanning.io
- Capacity estimator: https://community.fabricplan.com/capacity-pricing/
- GA announcement: https://community.fabric.microsoft.com/t5/Fabric-Updates-Blog/Bring-Enterprise-planning-to-the-AI-Era-with-Planning-in/ba-p/5326315
