# Fabric Planning Lab: Harborlight Provisions Co.

> Run a full annual planning cycle in Microsoft Fabric Planning, from a blank workspace to a board-ready plan, forecast, and P&L canvas.

![Fabric](https://img.shields.io/badge/Microsoft%20Fabric-Planning-blue?logo=microsoft)
![License](https://img.shields.io/badge/license-MIT-green)
![Modules](https://img.shields.io/badge/modules-8-orange)
![Time](https://img.shields.io/badge/time-~2.5%20hours-informational)

**Harborlight Provisions Co. (HPC)** is a mid-market consumer goods company: snacks,
beverages, and household care sold across North America, Europe, and APAC. FY25 closed
at roughly **$25.9M** in revenue.

The FY26 plan lives in a 40-tab Excel workbook. It takes eleven days to consolidate, the
regional tabs never tie to the corporate roll-up, and last year's forecast was stale by
the time the board saw it. The CFO has had enough.

**You've just been hired as Harborlight's FP&A Systems Lead.** Your mission: rebuild the
annual plan inside Microsoft Fabric Planning, on the same governed semantic model the
company already reports from. The board wants **$30M revenue** and **$12.5M gross
profit** for FY26. Your job is to prove the number is reachable, and show your work.

Each module advances the planning cycle, and your skills, from an empty workspace to a
live, writeback-connected planning environment.

---

## The modules

| # | Module | Topics covered | Est. time |
|---|--------|----------------|-----------|
| [00](labs/00-prerequisites.md) | **Prerequisites & briefing** | Capacity, tenant settings, roles, the session billing model | 15 min |
| [01](labs/01-environment-and-first-sheet.md) | **Environment & first sheet** | Plan item, Fabric SQL database, semantic model connection, first planning sheet | 20 min |
| [02](labs/02-allocation-and-collaboration.md) | **Allocation & collaboration** | Top-down target, proportional allocation, quarter locking, bottom-up plan, comments, approvals | 25 min |
| [03](labs/03-optimizer.md) | **Optimizer** | Goal-seeking a $12.5M gross profit across revenue growth and cost levers | 15 min |
| [04](labs/04-forecast-and-writeback.md) | **Forecast & writeback** | Statistical forecast, rolling horizon, closing a period, committing to Fabric SQL | 25 min |
| [05](labs/05-pl-models-and-scenarios.md) | **P&L models & scenarios** | Measure model, row model builder, best-case and cost-restructuring scenarios | 25 min |
| [06](labs/06-intelligence-canvas.md) | **Intelligence canvas** | Variance charts, KPI cards, matrix, filters, embedded planning sheet | 25 min |
| [07](labs/07-powertable.md) | **PowerTable** *(optional)* | Governed reference data, forms, approvals, access control, automations | 30 min |
| [08](labs/08-sizing-and-governance.md) | **Sizing & governance** | Capacity math, role behavior, RLS, limits, what breaks a plan item | 15 min |

**Core path:** modules 00–06 and 08, about **2.5 hours**.
**With PowerTable:** about **3 hours**. Modules can be done across multiple sittings.

---

## Data

| Source | Type | Description |
|--------|------|-------------|
| [`Northwind_FMCG.pbix`](https://github.com/microsoft/fabric-samples/blob/main/docs-samples/iq/plan/Northwind_FMCG.pbix) | First-party sample | Harborlight's sales and P&L model, revenue and cost actuals by region and product, driver-level P&L measures, and P&L line items as rows |
| [`Northwind-FMCG-assets-powertable-tutorial.xlsx`](https://github.com/microsoft/fabric-samples/blob/main/docs-samples/iq/plan/Northwind-FMCG-assets-powertable-tutorial.xlsx) | First-party sample | IT asset register, used in the optional PowerTable module |

Both come from Microsoft's own [`fabric-samples`](https://github.com/microsoft/fabric-samples/tree/main/docs-samples/iq/plan)
repository. There is nothing to generate and no API key to request. See
[`data/README.md`](data/README.md) for the data dictionary and download steps.

The semantic model ships under the name *Northwind FMCG*. Throughout the labs we treat
it as Harborlight's model; the region, category, and measure names line up with the
story as-is, so there is no relabeling to do.

---

## Quick start

1. **Get a Fabric capacity**: F2 or higher, or [start a free trial](https://learn.microsoft.com/fabric/fundamentals/fabric-trial).
2. **Confirm you are Member or Admin** on the workspace you plan to use. Contributor is not enough.
3. **Have an admin enable three tenant settings** and the XMLA endpoint: [Module 00](labs/00-prerequisites.md) covers exactly which.
4. **Download the sample model:**
   ```bash
   git clone https://github.com/<your-org>/fabric-planning-lab.git
   cd fabric-planning-lab
   ```
   Then grab `Northwind_FMCG.pbix` from `fabric-samples` (link above).
5. **Start with [Module 00: Prerequisites & briefing](labs/00-prerequisites.md).**

---

## Who this is for

- **FP&A analysts and finance systems owners** evaluating a move off spreadsheet-based planning
- **Power BI and Fabric practitioners** asked "can Fabric do budgeting?" and needing a real answer
- **Solution architects** sizing a planning deployment before committing capacity
- **Sellers and pre-sales engineers** who need a defensible, hands-on demo rather than a slide

You do not need to be a data engineer. There is no Spark, no Python, and no pipeline
authoring in this lab. If you can build a PivotTable and read a P&L, you can finish it.

---

## What you need

- Microsoft Fabric capacity: F2 or higher, or a Fabric trial
- Workspace role of **Member** or **Admin**
- Microsoft Edge or Google Chrome (Safari is not supported)
- Power BI Desktop: optional, only if you want to inspect the sample model
- Basic familiarity with budgeting concepts and Power BI semantic models

---

## Feature maturity

Fabric Planning reached general availability and billing began **August 1, 2026**. This
lab covers features at these maturity levels:

| Feature | Status |
|---------|--------|
| Planning sheets, allocation, locking | ✅ GA |
| Optimizer, statistical forecasting, scenarios | ✅ GA |
| Measure model, row model, cube | ✅ GA |
| Writeback to Fabric SQL database | ✅ GA |
| Intelligence sheets and reporting canvas | ✅ GA |
| PowerTable, approvals, automations | ✅ GA |
| Infobridge and connected planning | ✅ GA |

Behavior changes fast in this workload. Where a step depends on a date-sensitive
behavior, such as the billing role change on August 19, 2026, the lab says so and
links to the Microsoft Learn page that governs it.

---

## A word on cost before you start

Fabric Planning does not bill per user license. It bills **30-day sessions**, triggered
by what a user does, and **a session cannot be ended early**. Creating a single planning
sheet commits 847 CU-hours. That is not a reason to avoid the lab, since a trial capacity
covers it, but it is the single most misunderstood thing about this workload, so
[Module 00](labs/00-prerequisites.md) covers the math before you click anything.

---

## Status

The steps in this lab are verified against Microsoft Learn's Fabric Planning
documentation, including the official 12-part tutorial series, with sources cited at the
end of every module. A live-capacity walkthrough is in progress; findings from it are
folded back in as they surface. One already has: the transaction fact table is named
**Fact Transactions** in the shipped model, not *Sales Transactions* as the tutorial text
says. `assets/screenshots/` is deliberately empty for now.

If you run it and something has drifted, please open an issue. See
[CONTRIBUTING.md](CONTRIBUTING.md). Step drift is the most useful contribution you can
make, because this workload changes quickly.

---

## License

MIT. See [LICENSE](LICENSE).

---

*Built for practitioners who have to make the number.*
