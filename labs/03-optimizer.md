# Module 03: Optimizer

**Est. time:** 15 minutes
**You'll build:** a gross profit sheet, and let Fabric back-solve the revenue and cost
combination that hits $12.5M.

---

## Where we are

Revenue is planned at $30M. The board also asked for **$12.5M gross profit**, and nobody
has checked whether those two numbers are compatible.

The traditional answer is a week of trial and error: nudge revenue up, nudge COGS down,
recalculate, repeat until the number appears or the deadline does. The Optimizer does
that search for you.

### What it actually does

It is a **multivariate goal seek**, an iterative solver rather than a rearranged formula.
Each pass it:

1. tries a value for each input,
2. recalculates the result measure,
3. compares that to your target,
4. adjusts the inputs and goes round again.

It repeats until the result is within tolerance of the target, or it runs out of
iterations. That is why the split between two levers is not predictable in advance, and
why the same target can be reached more than one way.

Three pieces have to be in place:

| Piece | In this module |
|---|---|
| **Independent variables**, the inputs it may change | `Sales Plan` and `COGS`, the editable copies from Step 2 |
| **Dependent measure**, a formula measure to solve for | `Gross Profit`, built in Step 3 |
| **Goal**, target a value or push it to a maximum or minimum | Target of `12.5m` |

It offers two modes. **Target-based** finds the inputs that hit a specific number, which
is what you use here. **Direction-based** maximizes or minimizes instead, for questions
like "what is the most margin available without breaching these limits?"

Common uses beyond this example: finding the volume and price needed for a revenue
target, the reductions needed across expense categories to meet a budget, or the
collection and payment changes needed to reach a cash balance.

---

## Step 1: Build the gross profit sheet

1. On the **Home** ribbon, select **New Planning Sheet**. Name it `Gross Profit` and
   select **Create**.
2. Assign the fields. **Values takes two measures**, one from each source:

   | Field | Value | Source |
   |---|---|---|
   | **Rows** | *Region* → *Category* → *Sub Category* | *Geography*, then *Product* |
   | **Columns** | Date hierarchy | *Date* |
   | **Values**, first | *2026 Sales Plan* | **From Sheets** → *Plan Intro* |
   | **Values**, second | *2025 COGS* | *Measures Table* |

   Tick both in the **Data** pane. The grid should end up with two measure columns, and
   the sheet title above it should read *2026 Sales Plan, 2025 COGS by Region...*. If it
   names only one measure, the other did not attach.

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

**Check before moving on.** The grid should now have **four** measure columns:

| Column | Origin | Editable? |
|---|---|---|
| 2026 Sales Plan | From Sheets, Plan Intro | no |
| 2025 COGS | Measures Table | no |
| **Sales Plan** | your copy | **yes** |
| **COGS** | your copy | **yes** |

If you see only two, Step 2 has not taken effect, and the formula in Step 3 will not find
the columns it needs.

## Step 3: Calculate gross profit

1. On the **Planning** ribbon, select **Formula**. The **Formula Measure** pane opens.
2. Configure it:

   | Field | Value | Note |
   |---|---|---|
   | **Title** | `Gross Profit` | Clear the prepopulated name first |
   | **Insert as** | Visual Measure | The default, and correct here |
   | **Data type** | Number | The default |
   | **Formula** | `[Sales Plan] - [COGS]` | See the note below on the picker |
   | **Column aggregation type** | **Formula** | **Defaults to Sum. Change it.** |
   | **Row aggregation type** | Formula | Already the default |

   Select **Create**.

> [!TIP]
> Start typing in the **Formula** box and a picker appears with two tabs. **References**
> lists what you can point at: the columns on this sheet, such as *2026 Sales Plan*, plus
> dimension members like *2025*, *GrandTotal*, *Category*, *Month Short* and *Quarter*.
> **Functions** lists the built-in functions.
>
> Pick from **References** rather than typing names by hand. The entries must match your
> column titles exactly, and picking them avoids a typo that will not resolve. Toggle
> **Suggestions** off if you would rather type, and use the expand icon for a larger
> editor.

> [!IMPORTANT]
> **Column aggregation defaults to Sum and must be changed to Formula.** Row aggregation
> is already Formula, so it is the column setting that catches people.
>
> Left on Sum, the quarterly and grand totals add up their children instead of
> recalculating `[Sales Plan] - [COGS]` at each level. For a straight subtraction the
> totals may still look plausible, which is what makes it dangerous. On a margin or
> percentage row the same mistake produces obvious nonsense.

> [!IMPORTANT]
> **Reference the editable copies, not the originals.** The picker lists both, and their
> names are similar:
>
> | Pick this | Not this |
> |---|---|
> | `Sales Plan`, your copy from step 2 | `2026 Sales Plan`, the read-only original |
> | `COGS`, your copy from step 2 | `2025 COGS`, the semantic model measure |
>
> The Optimizer can only adjust editable data input columns. Point the formula at the
> originals and it has nothing to work with, because those two columns are exactly the
> baseline you want left untouched for comparison at the end.

3. Collapse the row hierarchy to category level. On the **Planning** ribbon, select
   **Totals** and enable **Column Grand Total** on the left.

> [!NOTE]
> The two columns may display at different scales, Sales Plan *in Millions* and COGS
> *in Thousands*. That is display formatting only. The formula operates on the underlying
> values, so the subtraction is correct even though the columns look like they are in
> different units.

Note the Gross Profit figure on the **All** row before you continue. In one run it landed
at **12,144.89 thousands**, which is $12.14M against the $12.5M target, leaving the
Optimizer a gap of about **$355K** to close.

> [!TIP]
> **Your exact figures will differ slightly from the ones quoted later in this module.**
> They depend on how the bottom-up plan in Module 02 turned out, including whether you ran
> the `+10%` approval test. The numbers below come from one run; what matters is that
> Gross Profit finishes at exactly 12.5M and that you can see which two levers moved to
> get there.

Two things confirm the formula is wired correctly:

- The sheet title now reads *2026 Sales Plan, 2025 COGS, Sales Plan, COGS, Gross Profit*,
  five columns.
- Gross Profit on any row equals that row's Sales Plan minus its COGS. On the All row,
  26,311.20 minus 14,166.31 gives 12,144.89.

## Step 4: Run the Optimizer

The Optimizer is a three-step wizard: **Objective & Variables**, **Add Constraints**,
**Output**.

1. Select the **Gross Profit** grand total cell. On the **Planning** ribbon, select
   **Optimize**. The command stays greyed out until a valid target cell is selected.
2. On **Objective & Variables**, set **Objective** to `Target` and **Target Value** to
   `12.5m`.

   Beneath those two fields the pane names the cell it is about to solve for, reading
   **GrandTotal** and **2025 > Gross Profit**. **Check it.** That is your confirmation
   that the right cell was selected before you opened the wizard. There is also a
   **Show Formula** expander if you want to see the calculation being targeted.

3. Open **Variables to Update**, the **Choose Series** dropdown, and select both
   `Sales Plan` and `COGS`. **Next** stays disabled until at least one series is chosen.
4. Select **Next**.
5. On **Add Constraints**, select **Run** without adding any.
6. On **Output**, confirm **Target Value** shows 12.50m and **Achieved** shows 12.50m with
   a green check. The **Variables** panel beside it lists the values the solver settled
   on.
7. Select **Apply**.

The **Set Parameters** panel on the right carries the three tuning controls, preset to
sensible defaults: **Strategy** at *Balanced*, **Tolerance** at *Standard (0.1%)*, and
**Iterations** at *Standard (200 tries)*. A **Re-Run** button sits beneath them. Leave
all three alone unless the solver misses; the section at the end of this module covers
what to change when it does.

> [!NOTE]
> Only the editable copies appear in **Choose Series**. If `Sales Plan` or `COGS` is
> missing from the list, you are looking at a sheet where the copies from Step 2 were
> never created.

## Step 5: See what it actually did

1. On the **Planning** ribbon, select **Show Columns** and enable **2026 Sales Plan** and
   **2025 COGS**.

The original and optimized columns now sit side by side. From one run, starting at
$12.14M gross profit with a $355K gap to close:

| Lever | Before | After | Movement |
|---|---|---|---|
| Sales Plan | $26.31M | **$26.49M** | +$179K |
| COGS | $14.17M | **$13.99M** | −$176K |
| **Gross Profit** | $12.14M | **$12.50M** | **+$355K** |

The solver split the gap almost exactly in half, roughly 50% from revenue and 50% from
cost. That is a consequence of leaving it unconstrained, not a rule. Your own figures
will differ, since they depend on how your Module 02 plan finished. The shape is what
matters: revenue up a little, cost down a little, gross profit landing on target.

This is the number that changes the board conversation. The ask is not "grow revenue 5%."
The ask is **$179K of incremental revenue and $176K of cost out**, two concrete
commitments somebody can own, rather than a percentage nobody can act on.

> [!NOTE]
> You ran this without constraints, so the Optimizer was free to move both levers however
> it liked. In practice you would constrain it, capping the COGS reduction at what
> procurement has actually agreed, or flooring revenue at what the sales plan supports.
> Constraints are where this stops being a math trick and starts being a plan.

### Constraints, when you do want them

The **Add Constraints** page you skipped offers two kinds:

- **Range-based**: give an input a minimum and a maximum, and the solver keeps it inside
  that band. Use it for what is negotiable and by how much, such as a COGS reduction
  capped at 2% because that is what procurement signed up to.
- **Fixed-value**: pin an input so it cannot move at all, and the solver works the others
  harder to compensate. If price is set for the year, fix it and let volume absorb the
  target.

Without constraints the Optimizer will happily propose something arithmetically perfect
and operationally impossible. A 20% cost reduction hits the target and ends the
conversation with procurement in about four seconds.

### If it does not reach the target

An unconstrained two-variable problem like this one converges easily. Tighter problems
may not, and the **Set Parameters** panel on the Output page exposes three controls to
retune before selecting **Re-Run**:

| Parameter | Default | What it controls |
|---|---|---|
| **Strategy** | Balanced | The size of each adjustment. Smaller steps converge more slowly but overshoot less; larger steps are faster and can sail past the answer |
| **Tolerance** | Standard, 0.1% | How close counts as done. At 0.1% a 12.5m target is satisfied anywhere between 12.4875m and 12.5125m |
| **Iterations** | Standard, 200 tries | How many times it may go round the loop before giving up |

If the solver fails, the usual cause is that the constraints make the target
unreachable. Check whether the target is actually achievable before loosening tolerance
to force a result you cannot defend.

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
4. Two people run this module and reach $12.5M with different Sales Plan and COGS values.
   Did one of them do it wrong?

<details>
<summary>Answers</summary>

1. The Optimizer can only adjust editable data input columns. Measures from another sheet
   or the semantic model are read-only.
2. Subtotals sum their children rather than recalculating the formula, so totals on
   margin and percentage rows come out wrong.
3. Added constraints on the **Add Constraints** page to bound how far each lever can
   move, either a range or a fixed value.
4. No. It is an iterative solver, not a formula with one answer. Many combinations of the
   two levers reach the same target, and the path it takes depends on the starting values
   and the step size.

</details>

---

**← Back:** [Module 02](02-allocation-and-collaboration.md) · **Next:** [Module 04: Forecast & writeback →](04-forecast-and-writeback.md)

### Sources

- [Fabric planning tutorial part 2: Optimize data input measures](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/planning/tutorial-2-optimizer)
- [Optimizer overview](https://learn.microsoft.com/fabric/iq/plan/planning-optimize/optimizer-overview)
