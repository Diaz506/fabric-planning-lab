# Module 02: Allocation & collaboration

**Est. time:** 25 minutes
**You'll build:** a $30M top-down target allocated by prior-year mix, a bottom-up sales
plan with regional adjustments, and a two-level approval workflow.

---

## Where we are

The board wants **$30M** for FY26. Harborlight closed FY25 at roughly **$25.9M**. Nobody
has yet said where the extra $4M comes from, and that question is the entire job.

This module works the problem from both ends: top-down allocation to set the frame, then
bottom-up adjustment where the regional leads actually know something. Along the way you
will meet the single most useful control in the product: **locking**.

The target arrives in two steps, which is deliberate. The board's first ask was $28.5M,
about 10% growth. It then went to $30M, about 16%. You will allocate the first number,
lock the quarters that are already committed, and let the increase land only where there
is still room.

Work in the `Plan Intro` sheet from Module 01.

---

## Part 1: Set the top-down target

### Create a column you can actually type in

1. On the **Planning** ribbon, select **Totals** and enable **Column Grand Total** on the
   left. A single full-year column appears to the left of the monthly columns, showing
   FY25 actuals totalled across all periods.

> [!TIP]
> **Vocabulary, because the lab uses it constantly.** The **grand total column** is that
> full-year column. A **grand total cell** is that column intersected with whichever row
> you are pointing at. On the *All* row it is the whole company for the year; on the
> *Energy & Sports* row it is that subcategory for the year. Entering a value there
> pushes it down to everything beneath.
>
> You will notice the grand total column and the *2025* column show identical numbers.
> That is expected: *2025 Gross Revenue* is scoped to a single year, so the total across
> all periods is simply that year. The distinction starts to matter in the next section,
> when you expand to quarters.
2. On the **Planning** ribbon, select **Number** → **Copy from another series** →
   **2025 Gross Revenue**.
3. In the **Data Input** pane, **clear the Title field** and enter `2026 Target`.
4. Leave every other setting at its default and select **Create**.

> [!NOTE]
> The **Title** field arrives prepopulated with a generated name. Clear it before typing,
> or you end up with a column called something like `2026 TargetNorthwind_FMCGNumber`.

The new column arrives **prepopulated with the FY25 values**, around $25.9M in total.
That is a starting point, not the target.

<details>
<summary>What the other settings do, and why the defaults are right here</summary>

| Setting | Default | Why it matters |
|---|---|---|
| **Insert as** | Visual Measure | Inserts the column across every category when a column hierarchy exists. Because this sheet has a Date hierarchy in Columns, you want the measure repeated per period. *Visual Column* would insert one column regardless of hierarchy |
| **Input type** | Number | Correct for currency values |
| **Column aggregation type** | Sum | How values roll up across the column hierarchy |
| **Row aggregation type** | Sum | How values roll up across the row hierarchy |
| **Distribute parent value to children** | Checked | **The setting this whole module depends on.** It automatically allocates a parent value proportionally to child members. Uncheck it and typing $28.5M into the grand total will not flow down to regions and categories |
| **Enable Multi-Dimension Allocation** | Off | For allocating across dimension breakdowns simultaneously. Used in cube scenarios, not here |
| **Minimum / Maximum Value** | None | Optional guardrails on leaf-level input |

Two things worth knowing before you click Create:

- **You cannot change Insert as or Input type after the column exists.** Everything else
  can be edited later.
- **Distribute parent value to children only works with** Sum, Average (Leaf), Minimum,
  Maximum, First, or Last aggregation. Change the aggregation to something else and the
  distribution behavior silently stops being available.

</details>

> [!NOTE]
> Native columns are read-only. You cannot type into a measure that comes from the
> semantic model. Copying creates an editable series and keeps FY25 actuals intact as a
> comparison baseline. You will do this repeatedly throughout the lab.

### Allocate the target

You are about to type one number into one cell and have Planning build the rest.

**Find the cell first.** It is the intersection of two things you already have on screen:

- the **All** row, the top row that totals every region and category
- the **grand total column** in **2026 Target**, the single full-year column you created
  in step 1 by enabling *Column Grand Total*, not the monthly columns

That cell currently shows around **25.9M**, the FY25 total for the whole company. It is
the one number that represents everything.

5. Double-click that cell, type `28.5m`, and select the check mark to confirm.

> [!NOTE]
> Double-click rather than single-click: one click selects a cell, two clicks open it for
> editing.
>
> The `m` suffix means million. When the sheet is already displaying in millions, as this
> one is, typing plain `28.5` works identically. The suffix is worth using anyway, because
> it means the same thing regardless of what scaling the sheet happens to be showing.

You just overwrote the $25.9M baseline with the board's first ask, about 10% growth.
Planning redistributes that number across every region, category, and subcategory
**proportionally to the FY25 revenue mix**.

**Check it worked.** Every row should have grown by the same factor, 28.50 / 25.87, or
about 1.1017:

| Row | FY25 actual | 2026 Target |
|---|---|---|
| All | 25.87 | **28.50** |
| Americas | 7.14 | **7.87** |
| Asia Pacific | 9.76 | **10.75** |
| Energy & Sports | 0.49 | **0.54** |

If your numbers match, the allocation cascaded correctly. If only the All row changed,
**Distribute parent value to children** was unchecked when you created the column; delete
it and start again from step 2.

One number in, a fully allocated plan out. That is top-down planning in a single gesture.

### Now lock the first half of the year

Harborlight's Q1 and Q2 are already committed. Trade promotions are booked and the
supply plan is locked. Growth has to come from the back half.

6. Expand the column hierarchy to quarters.
7. Select the **Q1** row total cell in the **2026 Target** column, select the
   distribution icon, and select **Lock all children**. Repeat for **Q2**. Locked cells
   turn grey.
8. Double-click the same grand total cell of **2026 Target** you used before, enter
   `30m`, and press Enter.

The board raised the ask from $28.5M to $30M, and **the extra $1.5M lands only in Q3 and
Q4**. Q1 and Q2 do not move.

This is the mechanic that makes allocation trustworthy. Without locking, every
top-down revision silently rewrites commitments people have already made.

9. Select the **Q1** total cell, select the distribution icon, and select
   **Unlock all children**. Repeat for **Q2**.

> [!TIP]
> Unlock once allocation is done. Leaving quarters locked means the *next* change also
> skips them, which is rarely what anyone intended two weeks later.

---

## Part 2: Build the bottom-up plan

The target is a frame. The plan is what the regions commit to.

1. On the **Planning** ribbon, select **Number** → **Copy from another series** →
   **2025 Gross Revenue**. **Clear the prepopulated Title**, enter `2026 Sales Plan`,
   leave the other settings at their defaults, and select **Create**.
2. In the footer, select the **Settings** icon and set rows per page to **All**. Every
   category now sits on one scrollable page.
3. Hover near the *Americas* row, select the row gripper, and choose **Collapse** →
   **Region** to collapse to region level.

### Adjust a subcategory

The Americas sales lead is confident about Energy & Sports drinks.

4. Expand *Americas* → *Beverages* and find the **Energy & Sports** row.
5. Double-click the **2026 Sales Plan** grand total cell **on the Energy & Sports row**,
   and enter `+ 8%` in the formula bar. Press Enter.

The increase rolls up through *Beverages* to *Americas* automatically.

### Shape it across the year

6. Select the **Energy & Sports** grand total cell, select **Distribute to column with
   trend**, and drag the slider to **4%**.

The annual total redistributes across quarters on a 4% growth curve: Q1 smallest, Q4
largest. Sensible for a product line ramping through the year, and far more honest than
spreading it evenly.

### Bulk edit across regions

APAC and Europe both expect a Q3 lift in beverages and personal care.

7. On the **Planning** ribbon, select **Bulk Edit** and configure:

   | Setting | Value |
   |---|---|
   | **Measure** | 2026 Sales Plan |
   | **Region Name** | Asia Pacific, Europe |
   | **Category** | Beverages, Personal Care |
   | **Quarter Name** | Q3 |
   | **Apply to Row Level** | Subcategory |
   | **Apply to Column Level** | Month Short |
   | **Type** | Append By |
   | **Value** | 8% |

Q3 rises 8% across both regions and both categories, applied down to subcategory and
month. In the old workbook that was four tabs and a prayer.

---

## Part 3: Make the assumptions visible

A number without its reasoning is a number nobody can defend in a board meeting.

1. Right-click the **Energy & Sports** grand total cell under *Americas* → *Beverages*.
   Select **Add Comment** and enter:

   > 8% uplift agreed with the Americas sales lead. Annual total distributed across
   > quarters with a 4% growth trend.

   Select **Post**.

2. On the **Planning** ribbon, select **Comments** → **Settings**. Enable
   **Comments Column** and select **Save**.
3. Double-click the comments cell for the *Americas* row. Enter
   `Sales Plan reviewed and ready for submission`, type `@` to tag a colleague, and
   select **Post**.

### Add a status field

4. Collapse the column hierarchy. On the **Planning** ribbon, select **List** →
   **Single Select**.
5. Title it `Status`, select **Options** → **Preset** → **Process Status**, select
   **Apply**, then **Create**.
6. Set the *Status* cell for the *Americas* row to **In Progress**.

---

## Part 4: Route it for approval

1. On the **Model** ribbon, select **Approval**.
2. Select the edit icon next to **Level 1**. Rename it `Regional Manager` and choose the
   first-level approver.
3. Select the edit icon next to **Level 2**. Rename it `Finance Director`, choose the
   second-level approver, and select **Next**.
4. Enable **Reset on Rejection** and **Teams Notifications**. Select **Submit**.

*Regional Manager* and *Finance Director* columns appear in the sheet.

### Test it

5. Expand *Asia Pacific*, double-click the **Beverages** cell in the **2026 Sales Plan**
   grand total column, enter `+10%`, and press Enter.
6. In the **Regional Manager** column for *Asia Pacific* → *Beverages*, select
   **Submitted**.

**Finance Director** flips to **Pending** on its own. The change is routed, and the audit
trail starts here rather than in somebody's inbox.

---

## Where you landed

Harborlight has a $30M target allocated on prior-year mix, a bottom-up plan with regional
adjustments that roll up correctly, documented assumptions, and a two-level approval
chain. The eleven-day consolidation is gone because there is nothing to consolidate.
There is one sheet.

What you do not yet have is proof that $30M in revenue delivers $12.5M in gross profit.
That is the next module, and it is the one that changes the conversation.

## Check yourself

1. Why copy a measure instead of typing directly into *2025 Gross Revenue*?
2. You lock Q1 and Q2, raise the annual total, and then forget to unlock. What happens at
   the next revision?
3. What does **Distribute to column with trend** do that plain allocation does not?

<details>
<summary>Answers</summary>

1. Native and semantic model measures are read-only. Copying makes an editable series and
   preserves the actuals as a baseline.
2. The next change also skips Q1 and Q2 and lands entirely in the unlocked quarters,
   usually without anyone noticing until the shape looks wrong.
3. It distributes an annual total along a growth curve across periods, rather than
   proportionally or evenly.

</details>

---

**← Back:** [Module 01](01-environment-and-first-sheet.md) · **Next:** [Module 03: Optimizer →](03-optimizer.md)

### Sources

- [Fabric planning tutorial part 1: Allocation and collaboration](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/planning/tutorial-1-allocation-collaboration)
- [Top-down planning](https://learn.microsoft.com/fabric/iq/plan/planning-concept-top-down-planning) · [Bottom-up planning](https://learn.microsoft.com/fabric/iq/plan/planning-concept-bottom-up-planning)
- [Approval workflows](https://learn.microsoft.com/fabric/iq/plan/planning-concept-approval-workflow)
