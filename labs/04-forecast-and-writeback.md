# Module 04: Forecast & writeback

**Est. time:** 25 minutes
**You'll build:** a statistical forecast from 24 months of history, a rolling horizon
that advances as actuals land, and a writeback that puts plan data into Fabric SQL.

---

## Where we are

The plan is set. Now January closes, and the number comes in at **$1.92M** against a
forecast of **$2.09M**. In the old workbook, that discovery arrived eleven days later and
triggered a manual re-forecast that took three people a week.

This module builds the version that updates itself, then pushes the result out to
Fabric SQL, where Power BI, semantic models, and data agents can reason against it.

This is the module that makes planning matter to everyone outside Finance.

---

## Part 1: Build the forecast sheet

1. On the **Home** ribbon, select **New Planning Sheet**. Name it `Forecast` and select
   **Create**.
2. Assign the fields:

   | Field | Value | Source table |
   |---|---|---|
   | **Rows** | *Category* → *Sub Category* | *Product* |
   | **Columns** | *Year*, *Quarter*, *Month Short* | *Date* |
   | **Values** | *Gross Revenue* | *Measures Table* |

> [!NOTE]
> This module uses the **unscoped** *Gross Revenue*, not *2025 Gross Revenue*. That is
> deliberate. A statistical forecast needs multiple years of history, and the 2025-scoped
> measure would give it one. Modules 01 and 03 use the scoped version because they plan a
> single year; this one is the exception.

3. In the **Filter** panel, apply a year filter for **2024** and **2025**.

> [!IMPORTANT]
> Apply this filter **before** creating the forecast measure. The statistical model reads
> whatever history is in scope. Filter it afterwards and you will be forecasting from
> thin air.

4. On the **Planning** ribbon, select **Totals** and enable **Column Subtotal** on the
   left.

> [!NOTE]
> Nothing will appear to happen. A subtotal totals the **visible children** of a
> hierarchy level, so with the columns collapsed at Year there is nothing beneath to
> total, and each Year column is already the aggregate. Expand a year with the `>` and
> the subtotal column appears alongside the quarters. Enabling it now means it is ready
> when you need it.

## Part 2: Define the forecast measure

1. On the **Model** ribbon, select **Forecast**.
2. Measure name: `Forecast`. Forecast period: **Jan 2026 to Dec 2026**. Select **Next**.
3. Closed period configuration: select **Link to Measure**, source measure
   **Gross Revenue**. Select **Next**.
4. Open period configuration: select **Data Input**, default value **None**. Select
   **Save**.

That split is the whole idea of a rolling forecast. **Closed periods** read actuals
automatically. **Open periods** accept planning input. As the year advances, months move
from one side to the other and the forecast re-bases itself.

**Check it worked.** The sheet should now show a **Forecast** column beside Gross Revenue
for each year, and a **2026** column group appears on its own, because the forecast
period covers it:

| Year | Forecast column shows | Why |
|---|---|---|
| 2024 | Exactly the Gross Revenue figure, 24.57 on the All row | Closed period, linked to the measure |
| 2025 | Exactly the Gross Revenue figure, 25.87 on the All row | Closed period, linked to the measure |
| 2026 | **Empty** | Open period, data input with no default |

If 2024 and 2025 do not mirror Gross Revenue exactly, the closed period source was not
set. If 2026 is not empty, the open period picked up a default value.

## Part 3: Generate the statistical forecast

1. Select the **All** row cell under the **2026 Forecast** column. It is empty, which is
   correct: the statistical forecast is what fills it.

> [!TIP]
> Leave the column hierarchy collapsed at Year. With 2026 collapsed, that one cell is the
> whole-year figure, which is exactly what you want the solver to work from. Microsoft's
> tutorial calls it the "2026 Forecast subtotal", but no separate subtotal column appears
> until you expand the year into quarters.

2. On the **Model** ribbon, select **Predict**. The Predict panel opens.
3. At the top of the panel, confirm **Row Selected** reads `GrandTotal` and
   **Measure Selected** reads `Forecast`. Each field has its own lock icon; select both
   to freeze the selection so a stray click in the sheet cannot retarget the forecast.
4. Under **Set Period Range**, confirm **Historic Data Range** reads `Jan 2024 - Dec 2025`
   and **Forecast Date Range** reads `Jan 2026 - Dec 2026`. Both should already be
   correct, derived from the filter and the forecast period you set earlier.
5. Under **Configure Forecast**, leave **Select Profile** on `Profile 1` and set:

   | Setting | Value | Default |
   |---|---|---|
   | **Confidence (%)** | `90` | already 90 |
   | **Growth Factor (%)** | `4` | **0, you must change this** |
   | **Evaluation** | Bottom Up | already Bottom Up |
   | **Round all negative values to zero** | No | already No |

   Confidence and Growth Factor each have a slider and a number box. Typing in the box is
   easier than dragging.

<details>
<summary>What these settings do</summary>

**Select Profile** is a saved collection of forecast settings: confidence, growth factor,
evaluation, negative handling, algorithm and seasonality. Use **More options (...)** to
create, duplicate, delete, or reset profiles. Naming them descriptively means a forecast
configuration can be reproduced later rather than rebuilt from memory. One profile is
enough for this lab.

**Confidence (%)** sets the confidence interval around the prediction, the probability
that the actual value falls inside the predicted range. 80% gives a narrower band suited
to central planning, 90% is the balanced choice for most business forecasts, and 95% is
wider and more conservative.

**Growth Factor (%)** is a business adjustment applied on top of the statistical result.
0% leaves the statistics alone; 4% lifts every forecast value by that much to reflect an
expected uplift; a negative value trims it. This is where judgement enters a model that
otherwise only knows history.

**Evaluation** decides the direction of the calculation. **Bottom Up** forecasts each
detailed row and aggregates upward, which respects the behaviour of individual
subcategories. **Top Down** forecasts the total and distributes it down.

**Round all negative values to zero** matters for measures that cannot go below zero.
Revenue cannot, but the statistical model does not know that, so a sharply declining
series can produce negative predictions. Left at No here because the data does not
decline that far.

**Choose Algorithm** offers three:

| Algorithm | Suits |
|---|---|
| **Trend Decomposition with MSTL** | Data with several overlapping repeating patterns, split into parts that are easier to model |
| **Exponential Smoothing** | Smooth trends with one clear seasonal cycle, weighting recent data more heavily |
| **ARIMA** | Series where recent spikes, drops and lags carry the signal |

</details>

6. Select **Choose Algorithm** → **Trend Decomposition with MSTL**. Select
   **Customize Algorithm** and check **Year** and **Quarter** for seasonality.
7. Select **Run Forecast**.
8. Review the preview: grey is historical, green is predicted. Select **Save Forecast**,
   confirm the measure and date range, and select **Save**.

> [!NOTE]
> MSTL decomposes the series into trend and multiple seasonal components. Harborlight is
> a consumer goods business with both an annual cycle and a quarterly promotional rhythm,
> which is exactly why both seasonality levels are checked.

9. In the **Filter** panel, check the year filter. The **2026 Forecast** column appears as
   soon as the forecast measure exists, but **2026 Gross Revenue** only appears once 2026
   is ticked in the filter. Tick it so you can compare forecast against actual.
10. Expand **2026** with the `>` to see the monthly figures the forecast produced.

**Check it worked.** At year level on the **All** row, one run produced:

| Year | Gross Revenue | Forecast |
|---|---|---|
| 2024 | 24.57 | 24.57 |
| 2025 | 25.87 | 25.87 |
| **2026** | **26.56** | **28.01** |

Closed years still mirror actuals exactly. 2026 is the first year where the two diverge,
and that divergence is the point.

<details>
<summary>Where 28.01 comes from</summary>

Revenue grew 5.29% from 2024 to 2025. The statistical model projected forward from that
trend and landed on about **26.93**, roughly 4.1% above 2025. The 4% **Growth Factor**
then lifted it to **28.01**.

So the number has two distinct parts: what the history implies, and what the planners
asserted on top. Set Growth Factor to 0 and re-run to see the statistical forecast alone.

</details>

Expanded to months, January forecasts at around **$2.09M** against an actual of
**$1.92M**. Across the full year the forecast sits about 5.5% above actual. The forecast
was optimistic. Good. That is the situation worth practising.

## Part 4: Close the period and roll forward

January is done. Lock it as history and extend the horizon so the forecast always looks
twelve months ahead.

1. On the **Model** ribbon, select the **Period** dropdown → **Close Period**.
2. Set **Close period till** to **Custom** → **January 2026**.
3. Select **Extend Forecast Range**, set **Duration** to **1 Month**, and select
   **Preview**.
4. The **Confirm Close Period** dialog appears, listing the affected measure with its
   before and after ranges. Changed values are highlighted. Confirm the **Forecast** row
   reads:

   | Measure | Closed Period | Open Period |
   |---|---|---|
   | Forecast | Jan 2024 to **Jan 2026** | **Feb 2026** to **Jan 2027** |

   Select **Save**. **Back** returns to the settings and **Discard** abandons the change.

The horizon rolled. January 2026 is now actuals, and January 2027 has appeared as a new
open period with nothing in it.

### Seed the new period

5. On the **Model** ribbon, select **Reforecast** → **Reforecast Column** and configure:

   | Setting | Value |
   |---|---|
   | **Target period** | Jan 2027 – Jan 2027 |
   | **Copy source** | Gross Revenue |
   | **Apply operation** | Single Period |
   | **Source periods** | Jan 2026 |

   Select **Apply**.

Jan 2027 seeds from Jan 2026 actuals at **$1.92M**, a defensible starting point rather
than a guess.

6. Double-click the Jan 2027 grand total and **append** `+4%` after the existing value,
   then select the check mark.

> [!NOTE]
> As in [Module 02](02-allocation-and-collaboration.md), this is an append. Double-clicking
> selects the current value, so type after it rather than over it. The cell should read
> something like `1.92+4%` before you confirm.

Jan 2027 lands at **$2M**. The planner's judgement sits on top of the statistical base,
and both are visible.

---

## Part 5: Write it back

Everything so far lives inside the plan item. Writeback is what lets the rest of Fabric
see it.

1. On the **Writeback** ribbon, select **Add destination** and configure:

   | Setting | Value |
   |---|---|
   | **Select connection** | Northwind_FMCG |
   | **Database name** | Northwind_FMCG_[YourName] |
   | **Schema** | dbo |
   | **Table name** | Forecast |
   | **Decimal precision** | 2 |
   | **Text length** | 512 |

   Select **Add**.

2. On the **Writeback** ribbon, select **Settings**. The Settings dialog has its own tabs.
   On the **General** tab, choose **Long with Changes**.

> [!TIP]
> Four formats are available. **Long** stores one row per cell; **Wide** stores one column
> per measure. The plain variants *replace* matching rows on each writeback. The
> **with Changes** variants keep history and write only what moved, which is what you
> want when an auditor asks why the February forecast changed three times.

3. Still inside the **Settings** dialog, switch to its **Data** tab. Deselect
   **Gross Revenue** and keep **Forecast** selected. Gross Revenue is read-only actuals;
   there is no reason to persist a copy.
4. On the **Destinations** tab, select the Fabric SQL destination.
5. Close Settings, then on the **Writeback** ribbon select **Writeback** and confirm.

### Verify it

6. Open the `Northwind_FMCG_[YourName]` database, expand **dbo** → **Tables**, and
   confirm the **Forecast** table is populated. **Refresh the explorer and run a query**
   rather than trusting the preview, which caches:

   ```sql
   SELECT COUNT(*) FROM dbo.Forecast;
   SELECT TOP 100 * FROM dbo.Forecast;
   ```

7. Back in the plan, select **Writeback** → **Logs** to review status, duration, the user
   who ran it, measures written, and writeback type.

> [!TIP]
> **If the table exists but looks empty**, the table was created so the connection and
> permissions are fine. Work through it in this order:
>
> 1. **Check the logs first.** If they report success with *Forecast* among the measures
>    written, the rows are there and the SQL view is stale. Refresh and query again.
> 2. **Confirm Forecast stayed selected** on the Settings **Data** tab. If deselecting
>    Gross Revenue left nothing ticked, writeback reports success and writes nothing.
> 3. **Check the writeback filter** on the Settings **Advanced** tab. A restrictive filter
>    can exclude every row.
> 4. **Check the scenario.** If you wrote back one scenario and are previewing another,
>    the rows will not match.

> [!WARNING]
> Deleting a row in a planning sheet does **not** delete it from the destination table.
> Removing data from SQL is a database operation. Somebody needs to own that, or the
> table slowly fills with rows the plan no longer contains.

---

## Where you landed

Harborlight's forecast now regenerates from 24 months of history, rolls forward as months
close, and lands in a Fabric SQL table that Power BI and data agents can query. The
question "at our current rate, will we hit the number?" is now answerable from the
warehouse rather than from somebody's laptop.

> [!NOTE]
> Writeback stores plan data **separately** in Fabric SQL. It does not update the
> connected semantic model. If you want plan and actual side by side in a report, you
> model that downstream. The planning sheet is not the delivery mechanism.

## Check yourself

1. Why must the year filter be applied before creating the forecast measure?
2. What is the practical difference between **Long** and **Long with Changes**?
3. After closing January, where do actuals come from, and where does planner input go?

<details>
<summary>Answers</summary>

1. The statistical model forecasts from whatever history is in scope. Filtering afterwards
   means it trained on the wrong range.
2. **Long** replaces rows whose dimension values all match. **Long with Changes** retains
   prior values as change history and writes only modified cells.
3. Closed periods link to the Gross Revenue measure automatically. Open periods accept
   data input. Closing a period moves a month across that line.

</details>

---

**← Back:** [Module 03](03-optimizer.md) · **Next:** [Module 05: P&L models & scenarios →](05-pl-models-and-scenarios.md)

### Sources

- [Fabric planning tutorial part 3: Forecasting and writeback](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/planning/tutorial-3-forecasting-writeback)
- [Writeback](https://learn.microsoft.com/fabric/iq/plan/planning-concept-writeback)
- [Known limitations in planning](https://learn.microsoft.com/fabric/iq/plan/overview-limitations): writeback does not update the connected semantic model, and sheet row deletions do not propagate to SQL
- [Statistical forecasts with Predict](https://learn.microsoft.com/fabric/iq/plan/planning-forecasting/planning-how-to-generate-statistical-forecasts-using-predict-feature)
- [Rolling forecasts](https://learn.microsoft.com/fabric/iq/plan/planning-forecasting/planning-how-to-manage-rolling-forecasts)
