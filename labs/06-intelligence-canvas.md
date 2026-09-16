# Module 06: Intelligence canvas

**Est. time:** 25 minutes
**You'll build:** the report the CFO actually opens: variance against plan, a KPI that
blends actuals with your planning sheet, a financial matrix, and an embedded live plan.

---

## Where we are

Five modules of work sit inside planning sheets. Planning sheets are excellent for
planners and unreadable for executives.

**Intelligence sheets** are the reporting canvas of Fabric Planning. They live in the
same plan item, read the same semantic model, and, critically, can read your planning
sheets too. That last part is what separates this from just building another Power BI
report: the KPI on this canvas can compare FY25 actuals to the plan you built in Module
02, live, with no data movement in between.

> [!NOTE]
> **A deliberate deviation.** Microsoft's intelligence sheet tutorials use a separate
> SaaS sample model with pre-built Actuals, Plan, and Forecast measures. This lab stays on
> the Harborlight model instead, because a plan item binds to exactly one semantic model
> for life. Switching datasets here would force a second plan item and sever the canvas
> from everything you have built. Field names differ from the Learn walkthrough; the
> mechanics are identical.

Work in the same `Harborlight_FY26_Plan` item.

---

## Step 1: Create the intelligence sheet

1. On the **Home** ribbon, select **New Intelligence Sheet**.
2. Name it `FY26 Plan Review` and select **Create**.

The canvas opens with a **Visualizations** pane on the right. Unlike a planning sheet,
this is a free-form surface. Visuals go where you place them.

## Step 2: Variance: plan against prior year

1. In the **Visualizations** pane, select **Charts 100+**, search for `variance`, and
   choose **Integrated Variance Bar Chart**.
2. Map the field wells:

   | Well | Field |
   |---|---|
   | **Values (Actuals)** | *2025 Gross Revenue* |
   | **Comparison 1** | *2026 Sales Plan*, from **From Sheets** → *Plan Intro* |
   | **Comparison 2** | *2026 Target*, from **From Sheets** → *Plan Intro* |
   | **Category** | *Region*, then *Category* |

3. With the chart selected, drill from *Region* down to *Category* and select
   **Expand all**.
4. Select a data label and set **Scaling** to **Thousands** in the on-object options.
5. Select a bar to adjust **Series colours**.

You are now looking at the gap between what Harborlight sold last year, what the regions
committed to, and what the board asked for, in one chart, by region and category. That
picture took the old process eleven days to assemble.

## Step 3: Trend over time

1. In the **Visualizations** pane, select **Charts 100+**, expand **Line**, and choose
   **Grouped line**.
2. Map:

   | Well | Field |
   |---|---|
   | **Series 1** | *2025 Gross Revenue* |
   | **Series 2** | *Forecast*, from **From Sheets** → *Forecast* |
   | **Category** | *Month Short* |

The forecast you generated in Module 04 now sits against last year's actual shape. The
seasonality either matches or it does not, and either answer is useful.

## Step 4: The KPI that matters

1. In the **Visualizations** pane, select **KPI**.
2. Map:

   | Well | Field |
   |---|---|
   | **Values (Actuals)** | *2025 Gross Revenue* |
   | **Comparison** | *2026 Target*, from **From Sheets** → *Plan Intro* |
   | **Trellis Row** | *Category* |

This is the blend worth pausing on. The value comes from the **semantic model**. The
comparison comes from a **planning sheet**. Change the target in `Plan Intro` and this
card moves. Plan and actual stop being two systems that argue.

## Step 5: The financial matrix

1. In the **Visualizations** pane, select **Matrix**.
2. Map:

   | Well | Field |
   |---|---|
   | **Rows** | *Category*, *Sub-Category* |
   | **Columns** | *Year*, *Quarter* |
   | **Values (Actuals)** | *2025 Gross Revenue* |
   | **Compare to** | *2026 Sales Plan*, from **From Sheets** |

3. On the **Matrix** tab, select **Show Columns** and enable **Variance** and
   **Variance %**.

Variance and variance percentage are built-in columns here rather than DAX you write and
maintain. For a finance audience, the matrix usually ends up being the most-used visual
on the page.

> [!TIP]
> The **Matrix** tab also has **Templates** → **Financial**, which applies indentation,
> subtotals, and bold parent rows in one click. Worth trying on an account-structured
> matrix.

## Step 6: One filter, every visual

1. In the **Visualizations** pane, select **Super Filter** and map the *Date* dimension
   to its field well.
2. Select the pencil icon → **Properties**. Set **Type** to `Date` and the date mode to
   `Calendar`.
3. Select a date range and confirm every visual on the page responds.

## Step 7: Make it presentable

1. On the **Intelligence** ribbon, select **Elements** → **Text**.
2. Enter `Harborlight Provisions: FY26 Plan Review`, make it bold, increase the font
   size, and position it at the top of the canvas.

## Step 8: Embed the live plan

A report that raises a question and cannot answer it sends the reader back to email.

1. On the **Home** ribbon, confirm your `Plan Intro` sheet exists.
2. On the canvas, embed the planning sheet as a visual so reviewers can see the
   underlying plan alongside the charts.

The CFO sees the variance, asks why APAC is short, and expands the plan grid in place,
including the comment you left in Module 02 explaining the 8% uplift.

## Step 9: Collaborate in context

1. Select a cell or data point in the matrix.
2. On the top ribbon, select **Comments** → **Add new comment**.
3. Enter your note, type `@` to tag a colleague, and press Enter.

Review commentary now lives on the number it refers to, instead of in a thread nobody can
find next quarter.

---

## Where you landed

Harborlight has a single canvas showing actuals, target, plan, and forecast together,
filterable, commentable, and reading live from the planning sheets underneath. The
eleven-day consolidation and the stale board pack are both gone.

That is the core lab complete. Two modules remain: **[07](07-powertable.md)** for
governed reference data, and **[08](08-sizing-and-governance.md)**, which you should not
skip. It is the conversation Finance will have with you before any of this reaches
production.

## Check yourself

1. Why does this lab keep the canvas on the Harborlight model instead of importing the
   sample Microsoft uses?
2. What makes the Module 04 KPI different from the same card built in Power BI?
3. Where does **From Sheets** get its data, and what happens when the source sheet
   changes?

<details>
<summary>Answers</summary>

1. A plan item binds permanently to one semantic model. Importing a different model would
   require a second plan item, which would disconnect the canvas from the planning sheets
   built in Modules 01–05.
2. Its comparison value comes from a live planning sheet rather than a modeled table, so
   it reflects plan edits immediately with no refresh or data movement.
3. From other planning sheets in the same plan item. The reference is live: editing the
   source sheet updates every visual and sheet consuming it.

</details>

---

**← Back:** [Module 05](05-pl-models-and-scenarios.md) · **Next:** [Module 07: PowerTable →](07-powertable.md)

### Sources

- [Tutorial part 9: Introduction to intelligence sheets](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/intelligence/tutorial-9-introduction-to-intelligence-sheets)
- [Tutorial part 11: Planning sheet, PowerTable, and canvas integration](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/intelligence/tutorial-11-planning-sheet-powertable-canvas)
- [Intelligence overview](https://learn.microsoft.com/fabric/iq/plan/intelligence-overview)
- [Build financial reports](https://learn.microsoft.com/fabric/iq/plan/intelligence-how-to-build-financial-reports)
