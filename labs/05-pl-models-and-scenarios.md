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
2. Assign the fields. **Everything comes from the P&L Measures table**, including the
   dimensions:

   | Field | Value | Source table |
   |---|---|---|
   | **Rows** | *Region Hierarchy*, with **both** the Region and Cities levels | **P&L Measures** |
   | **Columns** | *Date Hierarchy*: Year, Quarter, Month | **P&L Measures** |
   | **Values** | The ten driver measures listed below | **P&L Measures** |

> [!NOTE]
> Add **both** levels to Rows. Selecting only Cities gives a flat list of ten cities under
> All, with no regional grouping. Part 2 asks you to expand *Asia Pacific* and work on the
> *Sydney* card, which needs Region sitting above Cities.

> [!IMPORTANT]
> **Use the hierarchies inside P&L Measures, not the Geography and Date tables.**
>
> P&L Measures carries its own *Region Hierarchy* and *Date Hierarchy*. The driver
> measures are related to those, not to the Geography and Date dimension tables you used
> in earlier modules.
>
> Point Rows at Geography and Columns at Date and every cell returns the same number: the
> grand total repeated down every city and across every year, because the measures have no
> relationship to those tables to slice by. It looks like a rendering fault rather than a
> modeling one. If you see identical values everywhere, this is why.

> [!NOTE]
> **Expand P&L Measures, not Measures Table.** They are different. *Measures Table* holds
> the four headline measures used in Modules 01 to 04. *P&L Measures* holds the drivers
> and hierarchies this module needs.
>
> Microsoft's tutorial says to take "all the measures from the P&L Measures table", but
> the table contains more than measures. Tick these ten:
>
> Admin Expenses · Avg Selling Price · Discounts and Returns · Employee Expenses ·
> Labor Cost · Other Direct Exp · R&D · Raw Material Cost · Sales Volume ·
> Selling and Marketing Expenses
>
> Leave these:
>
> | Also in the table | Why |
> |---|---|
> | *Invoice ID* | Has no sigma icon, so it is a field rather than an aggregating measure |
> | *COGS*, *Gross revenue*, *net revenue* | Native measures whose names collide with three of the six formula measures you are about to create. Two columns called COGS makes formula references ambiguous |
>
> *Date Hierarchy* and *Region Hierarchy* are not measures, but you do need them, in Rows
> and Columns as above.

<details>
<summary>What these measures are, and the P&L they build</summary>

These are **drivers**: the operational quantities a business actually manages. Ten of
them, in three groups.

**Revenue drivers**

| Measure | What it is |
|---|---|
| Sales Volume | Units sold |
| Avg Selling Price | Price per unit. A rate, not a quantity, which is why it needs Average rather than Sum |
| Discounts and Returns | What comes off the top: promotional discounts, returned goods |

**Cost of goods drivers**

| Measure | What it is |
|---|---|
| Raw Material Cost | Materials that go into the product |
| Labor Cost | Direct labor to make it |
| Other Direct Exp | Remaining costs attributable to production |

**Operating expense drivers**

| Measure | What it is |
|---|---|
| Admin Expenses | Running the business: finance, legal, facilities |
| Employee Expenses | Staff costs not tied directly to production |
| R&D | Product development |
| Selling and Marketing Expenses | Getting the product to market |

Nothing in that list is a P&L line. That is the point of this module: the six formula
measures you are about to build assemble them into one.

```
Sales Volume x Avg Selling Price          = Gross Revenue
Gross Revenue - Discounts and Returns     = Net Revenue

Raw Material + Labor + Other Direct       = COGS
Net Revenue - COGS                        = Gross Profit

Admin + Employee + R&D + Selling & Mktg   = Operating Expenses
Gross Profit - Operating Expenses         = Net Profit
```

The table already contains native *Gross revenue*, *net revenue* and *COGS* measures,
which is why they are left unticked. You are rebuilding those three from their drivers so
the model knows how they are composed. A native measure is a number; a formula measure is
a number with a reason.

A driver-based model is worth the effort because of what it does next. Change Sales
Volume by 5% and every line below it moves, all the way to net profit, without touching
anything else. That is what makes the scenarios in Part 2 possible.

</details>

3. Double-click each measure label in the **Values** field and remove the `Sum of` prefix.
4. Select the three dots next to **Avg Selling Price** in the Values field and change it
   to **Average**.

> [!IMPORTANT]
> **Do both of these before opening the measure model canvas.**
>
> The rename is cosmetic. The aggregation change is not. Your first formula is
> `Gross Revenue = [Sales Volume] * [Avg Selling Price]`, and average selling price is a
> rate, not a quantity. Left on Sum, you multiply volume by the sum of prices across every
> city, and the whole P&L is wrong from the top line down. Nothing errors. The total just
> looks oddly large.
>
> This is the most common modeling error in driver-based P&Ls.
>
> If the measure list in the canvas still shows `Sum of` against every measure, these two
> steps have not been applied. Close the canvas, apply them, and reopen.

**Check the sheet before building the model.** Two things should be true:

- **Cities and years hold different values.** If every cell shows the same number, Rows or
  Columns is pointing at the Geography or Date table rather than the hierarchies inside
  P&L Measures.
- **Avg Selling Price is nowhere near the sum of its cities.** In one run the ten cities
  ranged from 136 to 225 while the All row read **194.84**. The sum would have been
  1,849. A sum-like total here means step 4 did not take, and every revenue figure below
  it will be wrong.

Sum-based measures should behave normally alongside it: Admin Expenses totalled **178.95**
against city values summing to 178.96, a rounding difference.

### Build the hierarchy

5. On the **Model** ribbon, select **Measure Model**. The canvas opens.
6. Add **Sales Volume** and **Avg Selling Price** to the canvas. Two ways:

   | Method | How |
   |---|---|
   | **Insert**, no dragging | Tick both in the **Measures** list on the left, leave **Insert Measure** set to **As roots**, then select **Insert** |
   | **Drag** | Tick both first, then drag them across together into the dashed **Drop from measure list** box at the **bottom** of the canvas |

> [!IMPORTANT]
> Dragging them one at a time risks dropping the second onto the first, which nests it as
> a child. You will see it as an indent with a collapse chevron on the parent. Both
> measures should sit at the same level at this point. If one is indented, drag it out.
>
> The **Aggregation** column reads `Sum` against *Avg Selling Price* even when the Values
> field is correctly set to Average, because it shows the measure's native default rather
> than the setting in force. Leave it alone. The check in step 7 confirms whether the
> Average is being honoured.

7. Select **Add Measure** → **Formula**. The **Formula Measure** pane opens, the same one
   you used in [Module 03](03-optimizer.md). Configure it:

   | Field | Value | Note |
   |---|---|---|
   | **Title** | `Gross Revenue` | |
   | **Insert as** | Visual Measure | The default |
   | **Data type** | Number | The default |
   | **Formula** | `[Sales Volume]*[Avg Selling Price]` | Pick names from the **References** tab rather than typing them |
   | **Column aggregation type** | **Formula** | **Defaults to Sum. Change it.** |
   | **Row aggregation type** | Formula | Already the default |

   Select **Create**, then place the two drivers underneath it. Select the
   **Gross Revenue** row and use **Add child** on the toolbar, or drag *Sales Volume* and
   *Avg Selling Price* beneath it.

   The result should be two levels, not three:

   ```
   Gross Revenue          Formula    [Sales Volume]*[Avg Selling Price]
     Sales Volume         Native
     Avg Selling Price    Native
   ```

> [!IMPORTANT]
> **Sales Volume and Avg Selling Price are siblings, both children of Gross Revenue.**
> Dragging one onto the other makes the second a grandchild, which you will see as a
> deeper indent and a chevron appearing on Sales Volume. Price is not a component of
> volume; they are two inputs to the same multiplication.
>
> The toolbar's **Add child** and **Add sibling** buttons build the structure without
> dragging, and cannot produce accidental nesting. Select the row you want to attach to
> first.

> [!IMPORTANT]
> **Column aggregation type defaults to Sum and must be changed to Formula, on every one
> of the six formula measures in this section.** Row aggregation is already Formula, so
> the column setting is the one that catches people.
>
> Left on Sum, each total adds up its children instead of recalculating the formula at
> that level. Here that would sum the city-level gross revenues rather than recomputing
> volume times price for the region, and the error compounds as you stack Net Revenue,
> Gross Profit and Net Profit on top.

> [!TIP]
> **Sanity check the first formula before building five more on top of it.**
>
> The measure model canvas shows no values, so select **Back to Home** to return to the
> sheet, where *Gross Revenue* now appears as a column.
>
> On the **All** row, multiply the two numbers already on screen. Gross Revenue should
> equal Sales Volume times Avg Selling Price. One run gave 311.19 thousand units at an
> averaged price of 196.10, and Gross Revenue read **61.03M**, matching to the cent.
>
> A figure around **578M** instead would mean the summed price of 1,858 was used, about
> nine times too high. Watch the column scale labels, since Sales Volume displays in
> thousands and Gross Revenue in millions.

> [!NOTE]
> **The measure model's Aggregation column shows the native default, not the setting in
> force.** It reads `Sum` against *Avg Selling Price* even when the Values field is
> correctly set to Average. The check above is what settles it: if Gross Revenue matches
> volume times the displayed average price, the Average setting is being honoured.
>
> The same figures also prove **Column aggregation type** is doing its job. The ten city
> Gross Revenues summed to 57.85M while the All row read 61.03M, a gap of more than 3M.
> A total that merely added its children would have shown 57.85M. Instead it recomputed
> volume times price at the regional level, which is exactly what setting it to Formula
> buys you.

8. Add **Discounts and Returns** to the canvas as a root, then select **Add Measure** →
   **Formula** and configure:

   | Field | Value |
   |---|---|
   | **Title** | `Net Revenue` |
   | **Formula** | `[Gross Revenue]-[Discounts and Returns]` |
   | **Column aggregation type** | **Formula**, changed from Sum |
   | **Row aggregation type** | Formula, already set |

   Select **Create**, then place *Gross Revenue* and *Discounts and Returns* beneath it:

   ```
   Net Revenue                    Formula
     Gross Revenue                Formula
       Sales Volume               Native
       Avg Selling Price          Native
     Discounts and Returns        Native
   ```

   Gross Revenue keeps its own two children as it moves, so the tree deepens rather than
   flattening.

> [!IMPORTANT]
> **A new formula measure arrives as a root, not as a parent.** Creating Net Revenue
> leaves it sitting alongside Gross Revenue and Discounts and Returns rather than above
> them, three roots at the same indent.
>
> Attach the children one at a time, watching the indent after each:
>
> - With **Net Revenue** selected, **Add child** puts the measure one level in.
> - With **Gross Revenue** selected, **Add sibling** puts it at Gross Revenue's level,
>   which is the same thing.
>
> Using **Add child** while a measure that is already indented is selected pushes the new
> one a level too deep. Discounts and Returns landing beside Sales Volume rather than
> beside Gross Revenue is the usual result, and it says discounts are a component of gross
> revenue rather than a deduction from it. The formula still calculates; the structure
> describes something else.
>
> The chevron is the quickest check that a measure has children at all. Order within a
> level does not matter, only depth.

9. Add the cost side. Select **Add Measure** → **Formula**:

   | Field | Value |
   |---|---|
   | **Title** | `COGS` |
   | **Formula** | `[Raw Material Cost]+[Labor Cost]+[Other Direct Exp]` |
   | **Column aggregation type** | **Formula** |

   Select **Create**. Then tick *Raw Material Cost*, *Labor Cost* and *Other Direct Exp*
   in the **Measures** list, set **Insert Measure** to **COGS**, and select **Insert**.

   ```
   COGS                           Formula
     Raw Material Cost            Native
     Labor Cost                   Native
     Other Direct Exp             Native
   ```

> [!TIP]
> The **Insert Measure** dropdown is not limited to *As roots*. Once a formula measure
> exists you can select it there, and ticked measures attach as its children. It is the
> quickest way to build a parent with several children, and it cannot nest by accident.

10. Complete the P&L with three more formula measures, each configured the same way and
    each needing **Column aggregation type** set to **Formula**:

    | Title | Formula |
    |---|---|
    | `Operating Expenses` | `[Admin Expenses]+[Employee Expenses]+[R&D]+[Selling and Marketing Expenses]` |
    | `Gross Profit` | `[Net Revenue]-[COGS]` |
    | `Net Profit` | `[Gross Profit]-[Operating Expenses]` |

    Build Operating Expenses first and attach its four drivers, as you did for COGS. Then
    Gross Profit over Net Revenue and COGS, and finally Net Profit over Gross Profit and
    Operating Expenses. The finished tree:

    ```
    Net Profit                       Formula
      Gross Profit                   Formula
        Net Revenue                  Formula
          Gross Revenue              Formula
            Sales Volume             Native
            Avg Selling Price        Native
          Discounts and Returns      Native
        COGS                         Formula
          Raw Material Cost          Native
          Labor Cost                 Native
          Other Direct Exp           Native
      Operating Expenses             Formula
        Admin Expenses               Native
        Employee Expenses            Native
        R&D                          Native
        Selling and Marketing Expenses  Native
    ```

> [!IMPORTANT]
> **Create each formula measure before the one that references it.** Net Profit refers to
> Gross Profit and Operating Expenses, so both must exist first. Working outward from
> Gross Revenue, as these steps do, keeps every reference resolvable when you type it.

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
2. Assign the fields. As with the measure model, **the dimensions live in the fact table**:

   | Field | Value | Source table |
   |---|---|---|
   | **Rows** | *Account* | **P&L Rows** |
   | **Columns** | *Date Hierarchy*: Year, Quarter, Month | **P&L Rows** |
   | **Values** | *Value* | **P&L Rows** |

> [!NOTE]
> If P&L Rows carries its own Date hierarchy, use it rather than the Date table, for the
> same reason as the measure model: the values relate to the hierarchy inside their own
> table. Identical numbers repeating across every column is the symptom of pointing at the
> wrong one.

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
