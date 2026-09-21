# Module 01: Environment & first sheet

**Est. time:** 20 minutes
**You'll build:** a working plan item connected to Harborlight's semantic model and a
Fabric SQL database, plus your first planning sheet.

---

## Where we are

Harborlight's numbers live in a governed semantic model. The plan does not. This module
closes that gap. By the end you will have a planning surface sitting directly on top of
FY25 actuals.

> [!WARNING]
> Completing this module makes you a **Planner** and commits a 30-day session at
> **847 CU-hours**. You cannot end it early. If you skipped
> [Module 00](00-prerequisites.md), read the cost model section before continuing.

---

## Step 1: Import the semantic model

1. Go to [app.fabric.microsoft.com](https://app.fabric.microsoft.com) and sign in.
2. Open your training workspace. Select **New folder**, name it `Harborlight FY26 Plan`,
   and select **Create**.
3. Open the folder. Select **Import** → **Report, Paginated Report or Workbook** →
   **From this computer**.
4. Select `Northwind_FMCG.pbix` and select **Open**.

**Two artifacts appear in the folder: a report and a semantic model.** Both matter to the
story, for different reasons.

### Open the report before you go any further

Select the report and spend a minute with it.

This is Harborlight's reporting as it exists **today**. It tells you what happened in
FY25: revenue by region, by category, by quarter. It is accurate, it is governed, and the
finance team trusts it. It is also entirely backward-looking. Nowhere in it is there a
number for next year, because there is nowhere for such a number to live.

That is the gap this lab closes.

Now note what you are about to do to it: **nothing**. Over the next seven modules you
will build a complete FY26 plan on the semantic model underneath this report, and the
report itself will keep working exactly as it does now. Plan data is written to a
separate Fabric SQL database and never modifies the connected semantic model.

If your month-end close already works, nothing in this lab puts it at risk.

> [!NOTE]
> **In a real deployment you would not import anything.** You would point the plan item at
> a semantic model your organization already reports from. There are over 20 million
> semantic models in Power BI, and planning is designed to sit on one of them rather than
> ask you to build another.
>
> The `.pbix` import here manufactures "the model you already have" so the lab is
> self-contained. The model does not even need to live in the same workspace as the plan
> item, as long as you have at least Viewer access to its host workspace.

> [!TIP]
> You may see *"Refresh failed due to missing data source credentials."* Ignore it. The
> data is embedded in the model and the planning sheet connects correctly.

> [!IMPORTANT]
> Do not rename this semantic model at any point. Renaming it breaks the connection and
> the plan item stops working.

## Step 2: Create the Fabric SQL database

This becomes the writeback destination for the forecast in Module 04.

1. Select **New item**, search for **SQL database**, and select it.
2. Name it `Northwind_FMCG_` followed by your name: for example
   `Northwind_FMCG_YourName`, then select **Create**.

## Step 3: Create the plan item

1. Select **New item**, search for **Plan**, and select it.
2. Enter `Harborlight_FY26_Plan` in the **Name** field and select **Create**.

Planning opens on its launch screen.

> [!NOTE]
> Fabric quietly creates a second SQL database in your workspace to hold this plan's
> metadata. That is expected. Leave it alone. It is not the same thing as the writeback
> database you just created.

## Step 4: Connect the semantic model

1. On the launch screen, select **Semantic Model**.
2. Select **Create Connection**, then **Create a new connection**.
3. Name the connection `Northwind_FMCG`. Your account identifier is filled in for you.
   Select **Sign in**, authenticate, then select **Create**.
4. Confirm the new connection is selected. Under **Semantic model**, choose
   *Northwind FMCG* and select **Add**.
5. Select **Connect**.

The model's tables appear in the **Data** tab: dimensions, measures, and the date table.

> [!IMPORTANT]
> **This binding is permanent.** A plan item connects to exactly one semantic model for
> its entire life. Planning against a different source means creating a new plan item.

## Step 5: Create your first planning sheet

1. On the **Home** ribbon, select **New Planning Sheet**. Name it `Plan Intro` and
   select **Create**.
2. Assign the fields:

   | Field | Value | Source table |
   |---|---|---|
   | **Rows** | *Region* → *Category* → *Sub-Category* | *Geography* for Region; *Product* for Category and Sub-Category |
   | **Columns** | *Year*, *Quarter*, *Month Short* | *Date* |
   | **Values** | *2025 Gross Revenue* | *Measures Table* |

> [!TIP]
> In the **Data** pane, dimensions and measures are grouped by the table they belong to.
> Expanding **Semantic Model** shows seven tables: *Measures Table*, *Date*,
> *Fact Transactions*, *Geography*, *P&L Measures*, *P&L Rows*, and *Product*. If you
> cannot find *Region*, expand **Geography** rather than hunting through the fact tables.
> [`data/README.md`](../data/README.md) lists what lives in each.

You are now looking at Harborlight's actuals, roughly **$25.9M** for FY25, broken out by
region and product, quarter by quarter. This sheet is the foundation for everything that
follows.

## Step 6: Connect the SQL database

Planning uses this connection to save and share the plan.

1. In the top-right corner, select **Set up connection**.
2. Under **Fabric SQL connection**, select **Create connection**, then
   **Create new connection**.
3. Name it `Northwind_FMCG` and select **Create**.
4. Confirm the connection is selected and select **Connect**.

The plan item is now ready for collaboration.

---

## Step 7: Learn the room

Two minutes here saves twenty later. Select the down arrow at top right to expand the
full ribbon.

| Ribbon | What lives there |
|---|---|
| **Home** | New planning, PowerTable, and intelligence sheets |
| **Planning** | Layout, formatting, bulk edit, pivot, filters, audit log, inserted columns |
| **Model** | Forecasts, scenarios, driver-based models, cube, approvals |
| **Format** | Conditional and semantic formatting |
| **Writeback** | Destinations, settings, logs |

Also worth finding now:

- **Header icons:** Security, Comments, Editing/Reading view, Save
- **Left arrow:** the **Explorer** pane: add, manage, and navigate between sheets
- **Right sidebar:** Data, Fields, Filter, Comments, Bookmarks
- **Footer:** row and column counts, a zoom slider, and a **Settings** icon for rows per
  page, which you will use in the next module

---

## Where you landed

Harborlight now has a planning environment bound to its reporting model, with a writeback
database standing by and FY25 actuals on screen. No numbers have been planned yet. That
starts next, when the board's $30M target meets the prior-year mix.

## Check yourself

1. Why does the lab tell you not to rename the semantic model or the workspace?
2. You realize halfway through the quarter that you need to plan against a different
   semantic model. What are your options?
3. Two SQL databases now exist in your workspace. What is each one for?

<details>
<summary>Answers</summary>

1. Renaming either one breaks the plan item. A renamed model breaks the connection; a
   renamed workspace stops the item from opening at all.
2. Create a new plan item. The model binding on an existing item cannot be changed.
3. One you created, as the writeback destination for plan data. One Fabric created
   automatically, to store the plan item's metadata.

</details>

---

**← Back:** [Module 00](00-prerequisites.md) · **Next:** [Module 02: Allocation & collaboration →](02-allocation-and-collaboration.md)

### Sources

- [Fabric planning tutorial part 0: Introduction and environment setup](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/planning/tutorial-0-introduction)
- [Create a planning sheet](https://learn.microsoft.com/fabric/iq/plan/planning-how-to-get-started)
