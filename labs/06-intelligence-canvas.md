# Module 06: Intelligence canvas

**Est. time:** 25 minutes
**You'll build:** the report the CFO actually opens, with the live plan embedded on the
canvas, variance against prior year, a blended KPI, and a financial matrix.

---

## Where we are

Five modules of work sit inside planning sheets. Planning sheets are excellent for
planners and unreadable for executives.

**Intelligence sheets** are the reporting canvas of Fabric Planning. They live in the
same plan item, read the same semantic model, and can embed a planning sheet directly on
the canvas. That last part is what separates this from building another Power BI report:
once a planning sheet is embedded, its measures become live inputs to your visuals.
Change a number in the plan and the charts move, with no refresh and no data movement.

> [!NOTE]
> **A deliberate deviation.** Microsoft's intelligence tutorials use a separate SaaS
> sample model with pre-built Actuals, Plan, and Forecast base measures. This lab stays
> on the Harborlight model, because a plan item binds permanently to one semantic model
> and switching here would sever the canvas from everything you built in Modules 01–05.
>
> Harborlight's model has no *Plan* or *Forecast* base measure, so this module maps the
> plan measures you used in Module 01 instead. The visuals and the mechanics are the same;
> only the field names differ from the Learn walkthrough.

Work in the same `Harborlight_FY26_Plan` item.

---

## Step 1: Create the intelligence sheet

1. On the **Home** ribbon, select **New Intelligence Sheet**.
2. Name it `FY26 Plan Review` and select **Create**.

The canvas opens with a **Visualizations** pane. Unlike a planning sheet, this is a
free-form surface where visuals go where you place them.

## Step 2: Embed the planning sheet first

Do this before building any visual. Until a planning sheet is embedded, its measures do
not appear in the **Data** pane, and the rest of this module has nothing to reference.

1. On the **Visualization** tab, select **Planning**.
2. In the sheet picker, **Select an Existing Planning Sheet**, choose `Plan Intro`, then
   select **Add**.
3. Resize and reposition the embedded sheet so there is room for visuals beside it.

> [!IMPORTANT]
> The **Planning** visualization option is greyed out until at least one planning sheet
> exists in the plan item. If it looks disabled, you are in the wrong plan item.

Now open the **Data** pane.

> [!IMPORTANT]
> **From Sheets may read "No Other Sheets Available" even with a sheet embedded.** That is
> not a failure, and it does not block anything. Every measure this module needs lives in
> the semantic model, under **Measures Table**, because the Module 01 sheet was built from
> model measures rather than from measures created inside the sheet. Map every field well
> from the semantic model and search the Data pane by name.
>
> **From Sheets** carries measures that exist only inside a planning sheet, such as a
> Data Input column or a formula measure defined on the sheet itself. A plan built
> entirely on model measures produces none.

The embedded sheet is **live and editable**, not a snapshot. Adjusting a cell in it
updates every chart, KPI, table, and matrix on this canvas.

## Step 3: Variance, plan against prior year

1. In the **Visualizations** pane, select **Charts 100+**.
2. Search for `variance`. Results group by chart family with a count on each, rather than
   listing chart names, so hover the thumbnails under **Bar/ Column** to find the
   integrated variance chart. The **IBCS** filter pill narrows it faster.
3. Map the field wells. Every field comes from the semantic model:

   | Well | Field |
   |---|---|
   | **Values (Actuals)** | *2026 Sales Plan* |
   | **Comparison 1 (vs Actuals)** | *2025 Gross Revenue* |
   | **Category** | *Region*, then *Category* |

4. With the chart selected, use the visual header to drill down from *Region* to
   *Category*, then use **Expand all** to show both levels at once.
5. Select a data label and use the on-object options to set **Scaling** to `Thousands`.
6. Select a bar to set **Series colours**.

The chart now shows the FY26 plan against FY25 actuals by region and product, with the
variance calculated for you.

## Step 4: Trend over time

1. In the **Visualizations** pane, select **Charts 100+**, expand **Line**, and select
   **Grouped/Clustered Line**.
2. Map the field wells. **Actual(s)** and **Legend** are both required, marked with an
   asterisk, and the visual stays as a grey placeholder until they are filled:

   | Well | Field |
   |---|---|
   | **Category** | *Month Short* |
   | **Actual(s)** | *2025 Gross Revenue* |
   | **Forecast** | *2026 Sales Plan* |
   | **Legend / Color by / Stacked** | *Region* |

Last year's actual shape sits against this year's plan. The seasonality either matches or
it does not, and either answer is worth knowing.

## Step 5: The blended KPI

1. In the **Visualizations** pane, select **KPIs**. The placeholder reads *Card*.
2. Map the field wells. Only **Actual(s)** is required here:

   | Well | Field |
   |---|---|
   | **Category** | *Region* |
   | **Actual(s)** | *2026 Target* |
   | **Comparison 1 (vs Actuals)** | *2025 Gross Revenue* |
   | **Trellis Row** | *Category* |

   There are three comparison wells, so prior year, plan and forecast can sit on one card.

3. Change a value in the embedded planning sheet and watch the KPI update.

Plan and actual stop being two systems that disagree.

## Step 6: The financial matrix

1. In the **Visualizations** pane, select **Matrix**.
2. In the field well, map:

   | Well | Field | Source |
   |---|---|---|
   | **Rows** | *Category*, *Sub Category* | *Product* |
   | **Columns** | *Year*, *Quarter* | *Date* |
   | **Values (Actuals)** | *2025 Gross Revenue* | *Measures Table* |
   | **Compare to Prior Period (PY)** | *2026 Sales Plan* | *Measures Table* |

3. On the **Matrix** tab, select **Show Columns**, then enable **Variance** and
   **Variance %**.

The variance and variance percentage columns are added automatically rather than being
DAX you write and maintain. For a finance audience this is usually the most-used visual
on the page.

> [!TIP]
> The **Matrix** tab also offers **Templates** → **Financial**, which applies
> indentation, subtotals, and bold parent rows in one step. Worth trying on an
> account-structured matrix.

## Step 7: One filter, every visual

1. In the **Visualizations** pane, select **Super Filter**.
2. Map the *Date* dimension to the field well.
3. Select the pencil icon to open **Properties**. Set **Type** to **Date** and the date
   mode to **Calendar**.
4. Select a date range.

The variance chart, line chart, KPI, and matrix all update together.

## Step 8: Make it presentable

1. On the **Intelligence** tab, select **Elements** → **Text**.
2. Enter `Harborlight Provisions - FY26 Plan Review`.
3. Increase the font size, apply bold, and position the text box at the top of the canvas.

## Step 9: Collaborate in context

1. Select a cell or data point in the matrix.
2. On the top ribbon, select **Comments**, then **Add new comment**.
3. Enter your note, type `@` followed by a colleague's name to tag them, and press Enter.

Commentary here is context-aware and respects the current filter state. You can comment
at cell, row, column, or report level, so review notes live on the number they refer to
rather than in a thread nobody can find next quarter.

---

## Where you landed

Harborlight has a single canvas showing actuals, target, plan, and forecast together,
filterable, commentable, and driven by a live planning sheet on the same page. The CFO
sees the variance, asks why APAC is short, and expands the plan grid in place, including
the comment you left in Module 02 explaining the 8% uplift.

### Go back and look at the original report

Open the `Northwind_FMCG` report you imported in Module 01, the one showing FY25 actuals.

It is unchanged. Same numbers, same visuals, same refresh behavior. Eight modules of
planning work, a $30M target, an optimized margin, a rolling forecast committed to SQL,
and the existing reporting estate never moved.

That is the property that matters if your organization already has reporting that works:
**planning is additive**. It reads the same governed model, writes to its own database,
and leaves everything downstream alone. Nobody has to migrate a report or renegotiate a
definition to start planning.

And now there are two things in the workspace: the report that says what happened, and a
canvas that says what happens next, both reading the same trusted numbers.

That is the core lab complete. Two modules remain: **[07](07-powertable.md)** for
governed reference data, and **[08](08-sizing-and-governance.md)**, which you should not
skip. It is the conversation Finance will have with you before any of this reaches
production.

## Check yourself

1. Why is the embedded planning sheet worth having on the canvas at all?
2. What is the difference between semantic model data and **From Sheets** data?
3. Why is **From Sheets** empty in this lab?

<details>
<summary>Answers</summary>

1. It is live rather than a snapshot. Editing a cell in it updates every visual on the
   canvas at once, which is what makes this a planning report rather than a Power BI
   report next to a plan.
2. Semantic model data is read-only actuals and reference data from Power BI. **From
   Sheets** data exists only inside a planning sheet, such as a Data Input column or a
   formula measure defined on the sheet.
3. Every measure in this plan came from the semantic model's Measures Table, so no
   sheet-local measures were ever created. The canvas maps everything from the semantic
   model and the visuals behave identically.

</details>

---

**← Back:** [Module 05](05-pl-models-and-scenarios.md) · **Next:** [Module 07: PowerTable →](07-powertable.md)

### Sources

- [Tutorial part 9: Introduction to intelligence sheets](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/intelligence/tutorial-9-introduction-to-intelligence-sheets)
- [Tutorial part 11: Planning sheet, PowerTable, and canvas integration](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/intelligence/tutorial-11-planning-sheet-powertable-canvas)
- [Visualize simulations, budgets, and forecasts](https://learn.microsoft.com/fabric/iq/plan/infobridge-concept-blend-measures)
- [Blend measures](https://learn.microsoft.com/fabric/iq/plan/infobridge-how-to-blend-measures)
- [Intelligence overview](https://learn.microsoft.com/fabric/iq/plan/intelligence-overview)
