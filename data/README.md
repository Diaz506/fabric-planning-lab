# Data

Everything this lab needs is a first-party Microsoft sample. There is no data
generation step, no API key, and no Python.

## Download

Both files live in Microsoft's [`fabric-samples`](https://github.com/microsoft/fabric-samples/tree/main/docs-samples/iq/plan)
repository under `docs-samples/iq/plan/`:

| File | Size | Used in |
|---|---|---|
| [`Northwind_FMCG.pbix`](https://github.com/microsoft/fabric-samples/blob/main/docs-samples/iq/plan/Northwind_FMCG.pbix) | 9.3 MB | Modules 01–06 |
| [`Northwind-FMCG-assets-powertable-tutorial.xlsx`](https://github.com/microsoft/fabric-samples/blob/main/docs-samples/iq/plan/Northwind-FMCG-assets-powertable-tutorial.xlsx) | 36 KB | Module 07 (optional) |

Download the `.pbix` directly from GitHub rather than cloning the whole repository, because
`fabric-samples` is large and you need one file from it.

You import the `.pbix` into your Fabric workspace in
[Module 01](../labs/01-environment-and-first-sheet.md). You do not need Power BI Desktop
unless you want to inspect the model first.

---

## The model: Northwind FMCG

Importing the `.pbix` creates **two artifacts**: a report and a semantic model.

The **semantic model** is what matters. The plan item binds to it, permanently, and every
module reads from it. In this lab it stands in as Harborlight Provisions' governed
reporting layer, the trusted numbers finance already uses.

The **report** is Harborlight's existing reporting. The lab opens it once in
[Module 01](../labs/01-environment-and-first-sheet.md) to establish what the company has
today, and returns to it in [Module 06](../labs/06-intelligence-canvas.md) to show it
unchanged after a full planning cycle. Planning writes to a separate Fabric SQL database
and never modifies the connected semantic model, so the existing reporting estate is
untouched.

In a real deployment you would skip the import entirely and point the plan item at a
semantic model your organization already reports from.

Three fact tables share a common date dimension.

### Sales Transactions

Revenue and cost actuals at geography and product level. This is the base the FY26
revenue plan is built from. It supplies the prior-year mix that allocation uses to
distribute a top-down target.

Dimensions you will use repeatedly:

| Dimension | Role in the lab |
|---|---|
| Region | Top level of the row hierarchy |
| Category, then Sub-Category | The product breakdown beneath Region |
| Date hierarchy: Year, Quarter, Month Short | The column hierarchy |

Key measure: **2025 Gross Revenue**, full-year FY25 actuals, totaling roughly **$28.5M**.
That number is the anchor for the whole lab.

### P&L Measures

Driver-level measures for the key financial metrics:

- Sales volume
- Average selling price
- COGS components (including **2025 COGS**)
- Operating expenses

These are the levers the Optimizer adjusts in [Module 03](../labs/03-optimizer.md) and
the building blocks of the measure model in
[Module 05](../labs/05-pl-models-and-scenarios.md).

### P&L Rows

P&L line items structured as rows, with cost data at individual line-item level. Used by
the row model builder in Module 05, where you assemble a P&L hierarchy from Net Profit
downward.

---

## The asset register (optional module)

`Northwind-FMCG-assets-powertable-tutorial.xlsx` is an IT asset register, the sort of
reference data that normally lives in a shared spreadsheet nobody owns. Module 07 moves
it into PowerTable and puts governance around it.

---

## A note on naming

The sample ships as *Northwind FMCG*, and the lab does not rename it. Renaming a semantic
model that is connected to a plan item **breaks the connection and the plan item stops
working**, so leaving it alone is both easier and safer.

Read *Northwind FMCG* as *Harborlight Provisions* wherever you see it. The regions,
categories, and measures line up with the story without any edits.

---

## Known quirk during import

After importing the `.pbix`, Fabric may show:

> Refresh failed due to missing data source credentials

Ignore it. The data is embedded in the model and fully available, and the planning sheet
connects correctly. You do not need to configure a data source for this lab.
