# Module 05: P&L models & scenarios

**Est. time:** 25 minutes
**You'll build:** a driver-based P&L from semantic model measures, the same P&L built
from line-item rows, and two scenarios compared side by side.

---

## Where we are

Harborlight has a revenue plan, a margin target, and a rolling forecast. What it does not
have is a **P&L structure**, the thing that turns "revenue went up 8%" into "net profit
went up 3%, because half the gain was eaten by raw materials."

Fabric Planning offers two ways to build that structure, and which one you choose depends
on the shape of your source data:

| Approach | Use when | Source in this model |
|---|---|---|
| **Measure model** | Your P&L lines exist as *measures*, drivers like volume and price | P&L Measures table |
| **Row model** | Your P&L lines exist as *rows*, account-level line items | P&L Rows table |

You will build both. They take about ten minutes each, and seeing the same P&L assembled
two ways is the fastest way to understand which one your own data calls for.

---

## Part 1: The measure model

### Set up the sheet

1. On the **Home** ribbon, select **New Planning Sheet**. Name it `P&L – Measure model`
   and select **Create**.
2. Assign the fields:

   | Field | Value | Source table |
   |---|---|---|
   | **Rows** | *Region* → *Cities* | *Geography* |
   | **Columns** | *Year*, *Quarter*, *Month* | *Date* |
   | **Values** | Every measure in the table | **P&L Measures** |

3. Double-click each measure label and remove the `Sum of` prefix.
4. Select the three dots next to **Avg Selling Price** in the Values field and change it
   to **Average**.

> [!IMPORTANT]
> Average selling price is a rate, not a quantity. Summing it across cities produces a
> number with no meaning. This is the most common modeling error in driver-based P&Ls, and
> it is silent. The total just looks oddly large.

### Build the hierarchy

5. On the **Model** ribbon, select **Measure Model**. The canvas opens.
6. Drag **Sales Volume** and **Avg Selling Price** into the
   **Drop from measure list** section.
7. Select **Add Measure** → **Formula**:

   | Name | Formula |
   |---|---|
   | `Gross Revenue` | `[Sales Volume]*[Avg Selling Price]` |

   Select **Create**. Drag *Gross Revenue* onto the canvas, then drag *Sales Volume* and
   *Avg Selling Price* beneath it.

8. Drag **Discounts and Returns** onto the canvas. Add another formula measure:

   | Name | Formula |
   |---|---|
   | `Net Revenue` | `[Gross Revenue] – [Discounts and Returns]` |

   Drag *Net Revenue* onto the canvas with *Gross Revenue* and *Discounts and Returns*
   beneath it.

9. Add the cost side:

   | Name | Formula |
   |---|---|
   | `COGS` | `[Raw Material Cost]+[Labor Cost]+[Other Direct Exp]` |

   Drag *COGS* onto the canvas. Then select *Raw Material Cost*, *Labor Cost*, and
   *Other Direct Exp*, choose the **Insert Measure** dropdown → **COGS** → **Insert**.

10. Complete the P&L with three more formula measures:

    | Name | Formula |
    |---|---|
    | `Gross Profit` | `[Net Revenue] – [COGS]` |
    | `Operating Expenses` | `[Admin Expenses] + [Employee Expenses] + [R&D] + [Selling and Marketing Expenses]` |
    | `Net Profit` | `[Gross Profit] – [Operating Expenses]` |

> [!IMPORTANT]
> On every calculated measure, set **Column aggregation type** to **Formula**. It
> defaults to Sum. **Row aggregation type** is already Formula. Same trap as
> [Module 03](03-optimizer.md): left on Sum, the totals add up their children instead of
> recalculating the formula at each level, and the subtotals lie.

### View it

11. Select **Back to Home**. On the **Planning** ribbon, select the **Layout** dropdown →
    **Measures In Rows**. The P&L appears as a structured hierarchy.
12. Select **Layout** → **Tree** for the node view.
13. On the **Tree View** ribbon, select **Display** → **Display Settings** and toggle
    **Show Header KPI** off.

---

## Part 2: Scenarios

Harborlight's exec team wants two versions of FY26: an upside case and a cost-out case.

### Best Case: grow the top line

1. Select **Create Scenario**, name it `Best Case`, confirm all semantic model measures
   are included, and select **Create**.
2. Expand *Asia Pacific* and select the *Sydney* card.
3. Open the **Measure Simulation** dropdown and apply:

   | Measure | Change |
   |---|---|
   | Sales Volume | `5%` |
   | Avg Selling Price | `2%` |
   | Discounts and Returns | `−5%` |

4. Close the dialog. Sydney, Asia Pacific, and the All card update in real time.

Because the model is driver-based, a volume change flows through gross revenue, net
revenue, gross profit, and net profit automatically. You changed three inputs and the
entire P&L responded.

### Cost Restructuring: take cost out

5. Select **Create Scenario**, name it `Cost Restructuring`, and select **Create**.
6. Expand *Europe*, select the *London* card, choose **Value**, scroll, and apply:

   | Measure | Value |
   |---|---|
   | Raw Material Cost | `400k` |
   | Labor Cost | `150k` |
   | Employee Expenses | `120k` |
   | Selling and Marketing Expenses | `160k` |

### Compare them

7. On the **Tree View** ribbon, select **Compare Scenario**. Set **Compare** to
   `Best Case` and **With** to `Cost Restructuring`.
8. Review the side-by-side variance, then select **Exit Compare**.

This is the slide the exec team actually wants: grow Sydney, or restructure London.
Here is what each does to net profit. Neither scenario overwrote the baseline.

---

## Part 3: The row model

Same P&L, built from account-level rows instead of drivers.

1. On the **Home** ribbon, select **New Planning Sheet**. Name it `P&L – Row model` and
   select **Create**.
2. Assign the fields:

   | Field | Value | Source table |
   |---|---|---|
   | **Rows** | *Account* | **P&L Rows** |
   | **Columns** | *Year*, *Quarter*, *Month* | *Date* |
   | **Values** | *Value* | **P&L Rows** |

3. On the **Model** ribbon, select **Row Model** → **Enable**.

### Clear the decks

4. In the **Row Model** window, select the box for **Row Name**, retain only the **All**
   row, select **Delete** for the rest, and confirm.

> [!NOTE]
> Starting from a single root feels destructive, but the row model is a hierarchy you
> author top-down. Keeping the imported rows just means deleting them later, in a worse
> order.

### Build from the root down

5. Select the **All** row, select the edit icon, set **Row Name** to `Net Profit` and
   **Configure as** to `Formula`. Select **Apply**.
6. Select *Net Profit* → **Add Child** → **Formula**. Name it `Gross Profit`. **Apply**.
7. Select *Gross Profit* → **Add Child** → **Formula**. Name it `Net Revenue`. **Apply**.
8. Select *Net Revenue* → **Add Child** → **Data Source**. Name it `Gross Revenue`,
   select **Choose Close Period Source Row**, pick the matching row from the semantic
   model, and **Apply**.
9. Select *Gross Revenue* → **Add Sibling** → **Data Source** for each of:
   `Returns and Breakage`, `Distribution Allowance & Rebates`,
   `Federal & State Excise Taxes`. Map each to its source row.
10. Select the **Configure Formula** box on *Net Revenue* and enter:

    ```
    [Gross Revenue] - [Returns and Breakage] - [Distribution Allowance & Rebates] - [Federal & State Excise Taxes]
    ```

    Select **Apply**.

> [!IMPORTANT]
> Enter formulas **after** creating all the child nodes. A formula referencing a row that
> does not exist yet cannot resolve.

11. Select *Net Revenue* → **Add Sibling** → **Aggregate**. Name it `COGS`. **Apply**.
12. Under *COGS*, use **Add Child** → **Data Source** for `Brewing Materials`,
    `Packaging, Plant Overhead and Maintenance`, and `Water & Utilities`.
13. Select **Configure Formula** on *Gross Profit* and enter `[Net Revenue] - [COGS]`.
    **Apply**.
14. Build the `Operating Expenses` branch the same way, using **Add Child** →
    **Data Source** for each expense line.
15. Select **Configure Formula** on *Net Profit* and enter
    `[Gross Profit]-[Operating Expenses]`. **Apply**.
16. Select **Back to Home**.

The full P&L now cascades from Net Profit down through every line item, recalculating in
real time as values change.

---

## Where you landed

Harborlight has a P&L that responds to its drivers, two comparable scenarios, and the
same statement expressed as an account hierarchy. The exec team can now ask "what if" and
get an answer in the meeting rather than the following week.

## Check yourself

1. When would you choose a row model over a measure model?
2. Why change Avg Selling Price from Sum to Average, and what goes wrong if you forget?
3. You create a Best Case scenario and simulate a 5% volume increase. What happened to
   the baseline plan?

<details>
<summary>Answers</summary>

1. When the P&L lines exist as account-level rows in the source rather than as
   driver measures. The measure model suits driver-based planning; the row model suits
   account-based statements.
2. It is a rate. Summing it across cities inflates the total meaninglessly, and nothing
   warns you. The P&L just computes from a wrong price.
3. Nothing. Scenarios are alternative versions layered over the baseline; the original
   values are untouched and remain the comparison point.

</details>

---

**← Back:** [Module 04](04-forecast-and-writeback.md) · **Next:** [Module 06: Intelligence canvas →](06-intelligence-canvas.md)

### Sources

- [Tutorial part 4: Build a P&L hierarchy](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/planning/tutorial-4-measure-model)
- [Tutorial part 5: Row model builder](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/planning/tutorial-5-row-model)
- [Measure model](https://learn.microsoft.com/fabric/iq/plan/planning-concept-measure-model) · [Scenario planning](https://learn.microsoft.com/fabric/iq/plan/planning-concept-scenario-planning)
