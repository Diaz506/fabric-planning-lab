# Module 03: Optimizer

**Est. time:** 15 minutes
**You'll build:** a gross profit sheet, and let Fabric back-solve the revenue and cost
combination that hits $12.5M.

---

## Where we are

Revenue is planned at $30M. The board also asked for **$12.5M gross profit**, and nobody
has checked whether those two numbers are compatible.

The traditional answer is a week of trial and error: nudge revenue up, nudge COGS down,
recalculate, repeat until the number appears or the deadline does. The Optimizer is
goal-seek for planning. You state the target, name the levers, and it back-calculates.

---

## Step 1: Build the gross profit sheet

1. On the **Home** ribbon, select **New Planning Sheet**. Name it `Gross Profit` and
   select **Create**.
2. Assign the fields:

   | Field | Value | Source |
   |---|---|---|
   | **Rows** | *Region* → *Category* → *Sub Category* | *Geography*, then *Product* |
   | **Columns** | Date hierarchy | *Date* |
   | **Values** | *2026 Sales Plan* | **From Sheets** → *Plan Intro* |
   | **Values** | *2025 COGS* | *Measures Table* |

3. Double-click the **Sum of 2026 Sales Plan** label in the **Values** field and rename it
   to `2026 Sales Plan`.

> [!TIP]
> **From Sheets** is how planning sheets talk to each other inside a plan item. The sales
> plan you built in Module 02 is now a live input here. Change it there, and this sheet
> follows. No copy-paste, no version drift.

## Step 2: Create editable input columns

The Optimizer needs columns it is allowed to change. Measures pulled from another sheet
or from the semantic model are read-only.

1. On the **Planning** ribbon, select **Number** → **Copy from another series** →
   **2026 Sales Plan**. **Clear the prepopulated Title**, enter `Sales Plan`, and select
   **Create**.
2. Select **Number** → **Copy from another series** → **2025 COGS**. Clear the Title,
   enter `COGS`, and select **Create**.

> [!NOTE]
> The **Title** field arrives prepopulated with a generated name, so clear it before
> typing. Leave the remaining settings at their defaults, as described in
> [Module 02](02-allocation-and-collaboration.md).

You now have editable copies of both levers, with the originals preserved as a baseline
for comparison afterwards. That comparison is the payoff at the end of this module.

## Step 3: Calculate gross profit

1. On the **Planning** ribbon, select **Formula**. The **Formula Measure** pane opens.
2. Configure it:

   | Field | Value | Note |
   |---|---|---|
   | **Title** | `Gross Profit` | Clear the prepopulated name first |
   | **Insert as** | Visual Measure | The default, and correct here |
   | **Data type** | Number | The default |
   | **Formula** | `[Sales Plan] - [COGS]` | Type it; **Suggestions** offers column names as you go |
   | **Column aggregation type** | **Formula** | **Defaults to Sum. Change it.** |
   | **Row aggregation type** | Formula | Already the default |

   Select **Create**.

> [!IMPORTANT]
> **Column aggregation defaults to Sum and must be changed to Formula.** Row aggregation
> is already Formula, so it is the column setting that catches people.
>
> Left on Sum, the quarterly and grand totals add up their children instead of
> recalculating `[Sales Plan] - [COGS]` at each level. For a straight subtraction the
> totals may still look plausible, which is what makes it dangerous. On a margin or
> percentage row the same mistake produces obvious nonsense.

3. Collapse the row hierarchy to category level. On the **Planning** ribbon, select
   **Totals** and enable **Column Grand Total** on the left.

## Step 4: Run the Optimizer

1. Select the **Gross Profit** grand total cell. On the **Planning** ribbon, select
   **Optimize**.
2. In **Optimizer: Objectives and Variables**, configure and select **Next**:

   | Setting | Value |
   |---|---|
   | **Objective** | Target |
   | **Target value** | `12.5m` |
   | **Variables to update** | Sales Plan, COGS |

3. On the **Add Constraints** page, select **Run** without adding constraints.
4. On the **Output** screen, confirm **Target Value** shows 12.5M and **Achieved** shows
   12.5M with a green check.
5. Select **Apply**.

## Step 5: See what it actually did

1. On the **Planning** ribbon, select **Show Columns** and enable **2026 Sales Plan** and
   **2025 COGS**.

The original and optimized columns now sit side by side:

| Lever | Original | Optimized | Movement |
|---|---|---|---|
| Sales Plan | $26.1M | **$26.38M** | +$0.28M |
| COGS | $14.17M | **$13.88M** | −$0.29M |

Together they deliver the $12.5M target.

This is the number that changes the board conversation. The ask is not "grow revenue 5%."
The ask is **$280K of incremental revenue and $290K of cost out**, two concrete
commitments somebody can own, rather than a percentage nobody can act on.

> [!NOTE]
> You ran this without constraints, so the Optimizer was free to move both levers however
> it liked. In practice you would constrain it, capping the COGS reduction at what
> procurement has actually agreed, or floor revenue at what the sales plan supports.
> Constraints are where this stops being a math trick and starts being a plan.

---

## Where you landed

Harborlight can now show the board not just that $12.5M is reachable, but exactly which
two levers get there and by how much. The Optimizer collapsed a week of iteration into a
dialog box.

Next: the plan assumes the year goes as written. It will not. Module 04 builds the
forecast that survives contact with reality.

## Check yourself

1. Why did you have to copy *2026 Sales Plan* and *2025 COGS* before optimizing?
2. What breaks if you leave row and column aggregation on their defaults for a formula
   column?
3. You run the Optimizer and it cuts COGS by 20%. Procurement says that is impossible.
   What should you have done?

<details>
<summary>Answers</summary>

1. The Optimizer can only adjust editable data input columns. Measures from another sheet
   or the semantic model are read-only.
2. Subtotals sum their children rather than recalculating the formula, so totals on
   margin and percentage rows come out wrong.
3. Added constraints on the **Add Constraints** page to bound how far each lever can
   move.

</details>

---

**← Back:** [Module 02](02-allocation-and-collaboration.md) · **Next:** [Module 04: Forecast & writeback →](04-forecast-and-writeback.md)

### Sources

- [Fabric planning tutorial part 2: Optimize data input measures](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/planning/tutorial-2-optimizer)
- [Optimizer overview](https://learn.microsoft.com/fabric/iq/plan/planning-optimize/optimizer-overview)
