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
   left. A grand total column appears showing full-year FY25 actuals.
2. On the **Planning** ribbon, select **Number** → **Copy from another series** →
   **2025 Gross Revenue**. Title it `2026 Target` and select **Create**.

The new column arrives **prepopulated with the FY25 values**, around $25.9M in total.
That is a starting point, not the target.

> [!NOTE]
> Native columns are read-only. You cannot type into a measure that comes from the
> semantic model. Copying creates an editable series and keeps FY25 actuals intact as a
> comparison baseline. You will do this repeatedly throughout the lab.

### Allocate the target

3. Double-click the grand total cell of the **2026 Target** column, enter `28.5m`, and
   select the check mark.

You just overwrote the $25.9M baseline with the board's first ask, about 10% growth.
Planning redistributes that number across every region, category, and subcategory
**proportionally to the FY25 revenue mix**. One number in, a fully allocated plan out.
That is top-down planning in a single gesture.

### Now lock the first half of the year

Harborlight's Q1 and Q2 are already committed. Trade promotions are booked and the
supply plan is locked. Growth has to come from the back half.

4. Expand the column hierarchy to quarters.
5. Select the **Q1** row total cell in the **2026 Target** column, select the
   distribution icon, and select **Lock all children**. Repeat for **Q2**. Locked cells
   turn grey.
6. Double-click the grand total cell of **2026 Target**, enter `30m`, and press Enter.

The board raised the ask from $28.5M to $30M, and **the extra $1.5M lands only in Q3 and
Q4**. Q1 and Q2 do not move.

This is the mechanic that makes allocation trustworthy. Without locking, every
top-down revision silently rewrites commitments people have already made.

7. Select the **Q1** total cell, select the distribution icon, and select
   **Unlock all children**. Repeat for **Q2**.

> [!TIP]
> Unlock once allocation is done. Leaving quarters locked means the *next* change also
> skips them, which is rarely what anyone intended two weeks later.

---

## Part 2: Build the bottom-up plan

The target is a frame. The plan is what the regions commit to.

1. On the **Planning** ribbon, select **Number** → **Copy from another series** →
   **2025 Gross Revenue**. Title it `2026 Sales Plan` and select **Create**.
2. In the footer, select the **Settings** icon and set rows per page to **All**. Every
   category now sits on one scrollable page.
3. Hover near the *Americas* row, select the row gripper, and choose **Collapse** →
   **Region** to collapse to region level.

### Adjust a subcategory

The Americas sales lead is confident about Energy & Sports drinks.

4. Expand *Americas* → *Beverages* and find the **Energy & Sports** row.
5. Double-click its grand total cell in **2026 Sales Plan** and enter `+ 8%` in the
   formula bar. Press Enter.

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
