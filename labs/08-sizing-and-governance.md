# Module 08: Sizing & governance

**Est. time:** 15 minutes
**You'll leave with:** a defensible capacity estimate for Harborlight, and a list of the
things that quietly break a planning deployment.

---

## Where the story lands

You have a plan, a forecast, a P&L model, and a canvas the CFO can read. The remaining
question is the one Finance will ask before anyone else gets access: **what does this
cost, and what can go wrong?**

This module has no clicking. It is the conversation you have before you roll out.

---

## Sizing Harborlight

Harborlight's planning population looks like this:

| Who | Count | What they actually do |
|---|---|---|
| FP&A Systems Lead (you) | 1 | Builds and edits planning sheet designs, writeback destinations |
| Regional finance leads | 3 | Enter and adjust plan numbers, submit for approval |
| Corporate controller | 1 | Reviews and approves, writes back |
| CFO and executives | 5 | Open the canvas, filter, read |

Map that onto behavior, because behavior is what bills:

| Role earned | Who | Count | Rate | 30-day CU-hours |
|---|---|---|---|---|
| **Planner** | FP&A Systems Lead | 1 | 847 | 847 |
| **Stakeholder** | Regional leads + controller | 4 | 168 | 672 |
| **Viewer** | CFO + executives | 5 | 37 | 185 |
| | | | **Subtotal** | **1,704** |

Then add the surrounding Fabric workloads (Fabric SQL, OneLake, XMLA operations) at a
**30% buffer**:

**1,704 × 1.3 ≈ 2,215 CU-hours per 30-day period.**

### Does that fit?

A capacity supplies its CU continuously, so over a 730-hour window it provides
**CU × 730 CU-hours**. Compare the requirement against the budget:

| SKU | CU-hours per 30 days | Harborlight (1,704) | With buffer (2,215) | Verdict |
|---|---|---|---|---|
| **F2** | 1,460 | 117% | 152% | Does not fit |
| **F4** | 2,920 | 58% | 76% | Fits, with little room |
| **F8** | 5,840 | 29% | 38% | Comfortable |
| **F16** | 11,680 | 15% | 19% | Ample |
| **F64** | 46,720 | 4% | 5% | Rounding error |

**F4 is the floor and F8 is the sensible choice** if the capacity does anything else at
all. A single Planner alone consumes 58% of an F2, which is why the smallest SKU is not
a serious option for a planning deployment.

> [!NOTE]
> **Put both numbers in the same time window before you compare them.** An F4 can be
> described as 14,400 CU-seconds per hour or 2,920 CU-hours per 30 days; both describe
> the same 4 CU capacity, just watched for different lengths of time.
>
> Subtract a Planner's 847 CU-hour session total from an hourly supply figure and you
> will understate the load roughly fivefold. [Module 00](00-prerequisites.md) works the
> example through both ways.

> [!IMPORTANT]
> This is a **budget** view, not a throughput view. It answers whether the work fits
> across 30 days, not whether you will be throttled at nine o'clock on a Monday. The
> capacity is also shared with every other Fabric workload, so these percentages sit on
> top of whatever warehouse, pipeline, and semantic model activity already runs there.
>
> Validate against Microsoft's
> [capacity estimator](https://community.fabricplan.com/capacity-pricing/) and your own
> capacity metrics before committing. Automation jobs add **2 CU each**, and only
> successful jobs bill.

### These numbers are a worst case

The table assumes all ten people hold **overlapping sessions simultaneously**. In
practice Harborlight's five executives are unlikely to all open the canvas in the same
30-day window, and sessions recur only on activity. A Viewer who reads the board pack
once a quarter costs four sessions a year, not twelve.

Size for the peak month, which for a finance team is budget season, rather than for a
theoretical month in which everyone happens to be active at once.

### The three sizing mistakes

1. **Assuming a light user is cheap because they log in once.** They are not billed for
   the minute, they are billed for 30 days. One curious executive who edits a cell has
   bought a Stakeholder session.
2. **Forgetting that upgrades are one-way inside a session.** A Viewer who enters a
   number becomes a Stakeholder, the Viewer session closes prorated, and the higher rate
   runs for a fresh 30 days. There is no path back until it expires.
3. **Splitting across capacities.** Tenant + user + capacity is the billing key. A user
   working in two capacities holds two sessions and pays twice.

And the one that surprises people: **if you assign the same capacity to multiple
workspaces, the user is billed at the highest role active across all of them.** Separate
workspaces do not separate the bill.

### Pausing is not a cost control

If you pause or delete a capacity mid-session, **the remaining CUs for every active
session are summed and added to your Azure bill**. Pausing does not defer the charge, it
brings it forward.

The same applies to the other two escape routes people reach for:

| You do this | What actually happens |
|---|---|
| Pause or delete the capacity | Remaining CUs for all active sessions are summed and billed immediately |
| Delete the plan item | Sessions continue and bill through the full 30 days |
| Let the capacity run out of credits | Sessions keep being recorded; billing continues periodically |

Once a session opens, its 30 days are a commitment. The only real lever is deciding who
can open one, which is the next section.

### Fabric SQL is billed separately

The role rates cover planning only. Every Fabric SQL database in the deployment bills
**compute plus storage** on top:

- **Compute** autoscales with a 2 GB minimum memory charge while online, and drops to
  zero after 15 minutes of inactivity. The database stays online 15 minutes past the last
  query, so short bursts bill longer than they run.
- **Storage** bills continuously, **even when compute is paused**, covering allocated
  storage plus backup storage beyond 100% of the provisioned size.

Remember that each plan item silently creates its own metadata database in addition to
any writeback destination you configure. Two plan items and one writeback target is three
databases, each with its own storage line.

---

## Controlling the bill

Sessions are driven by behavior, but behavior is governable. Three tenant settings decide
who can start an expensive session at all:

| Setting | Effect |
|---|---|
| **Users can upgrade to a Planner session** | Enable or disable Planner upgrades, for the entire organization or for named security groups |
| **Users can upgrade to a Stakeholder session** | The same control for Stakeholder upgrades |
| **Show Oversubscription Warning** | Warns a user before starting or upgrading a session that would likely oversubscribe the capacity |

All three are set at tenant level and can be **overridden per capacity** using Delegated
Tenant Settings, so a production capacity and a sandbox capacity can have different rules.

Two things to get right:

- **Planner access includes Stakeholder access.** Enable Planner for the whole
  organization and you have enabled Stakeholder for the whole organization too. Scope it
  to a security group instead.
- **Users with neither grant are not locked out.** They can still open plan items in
  Reading view as Viewers, which is the cheapest role. That is the correct default for
  most of the business.

Beyond the settings:

- **Keep planning-sheet authoring narrow.** Creating or redesigning a planning sheet is
  the only thing that mints a Planner. Everyone else can enter numbers, approve, and
  write back at Stakeholder rates.
- **Let report builders build.** Creating or editing PowerTable and intelligence sheets
  is Stakeholder work, not Planner work. For plan items that contain only PowerTable or
  only intelligence sheets, Planner is not even available.
- **Watch capacity, not workspace.** Billing is calculated and reported at capacity level.
  The workspace named "Planning" in your billing data is not a real workspace. It is a
  label so capacity-level reporting has somewhere to hang the numbers.
- **Do not overcommit small SKUs.** On F2 and F4, or on a capacity shared with other
  Fabric workloads, active sessions can exceed what the SKU supports. Turn on the
  oversubscription warning and control how many users you point at a small capacity.

---

## Row-level security, and one sharp edge

If the connected semantic model has RLS configured, planning honors it, with a
behavior worth knowing:

> Users **without an assigned RLS role see the union of the data defined by all roles.**
> Data not included in any role stays invisible.

So an unassigned user is not locked out. They see everything every role can see. Assign
roles deliberately rather than relying on absence as a restriction.

**PowerTable is different, and stricter in the wrong direction.** PowerTable does not
support user-specific database-level RLS when connecting to Fabric SQL tables. Every
query runs as the identity on the database connection, not the signed-in user, so people
may see rows you expected RLS to filter out. Blend (From Sheets) does not support RLS
either. Do not put data in PowerTable that some viewers must not see.

---

## Limits worth knowing before you design

| Limit | Value |
|---|---|
| Sheets per plan item | **25** |
| Visuals per plan item | **50** |
| Writeback | 1.2M cells per operation |
| Infobridge query | 1.2M cells (~5M across five sheets) |
| Bulk data input | 1M rows |
| PowerTable sort, group by, insight, find/replace | 5M rows |
| PowerTable Gantt and Resource layout | 30,000 rows |
| Excel export, raw / label mode | 20M cells / 5M cells |
| Automation cascading trigger depth | 2 levels |
| Automation database trigger writeback | 10 records per trigger type |

The first two shape architecture. Twenty-five sheets sounds generous until a plan has a
sheet per region per scenario. When you approach it, split into multiple plan items and
connect them through Infobridge rather than crowding one item.

---

## Connected planning, in one paragraph

Infobridge is how Harborlight's three regional finance leads eventually plan
independently without corporate reconciling anything. Each region keeps its own planning
sheet. Infobridge imports and appends them into a single consolidated dataset, and the
corporate sheet consumes that consolidated measure. When a regional lead raises an EU Q1
budget, the corporate roll-up moves on its own. No consolidation step, no drift, no
eleven-day lag.

That is the actual answer to the 40-tab workbook, and it is worth a follow-up session
of its own. Start with [connected planning in Infobridge](https://learn.microsoft.com/fabric/iq/plan/infobridge-concept-connected-planning).

---

## The operational checklist

Before handing a planning environment to real users:

- [ ] Workspace roles set: Member or Admin for anyone who will build
- [ ] Nobody who needs access is a **B2B guest** (unsupported)
- [ ] The workspace does **not** use private links (unsupported)
- [ ] The semantic model is **not** in My workspace and is **not** a composite model
- [ ] Direct Lake or DirectQuery models have a gateway with fixed credentials
- [ ] A naming freeze is agreed: **renaming the workspace or the semantic model breaks
      the plan item**
- [ ] Writeback destination confirmed as a Fabric SQL database, with format chosen
      (Long, Wide, or the "with changes" variants)
- [ ] Somebody owns the fact that **deleting a row in a sheet does not delete it from
      the SQL table**
- [ ] Sizing validated against the capacity estimator, with the 30% buffer applied
- [ ] Planner creation restricted to the modeling team via the **Users can upgrade to a
      Planner session** tenant setting, scoped to a security group rather than the
      organization
- [ ] **Show Oversubscription Warning** enabled if the capacity is shared

---

## Check yourself

1. A Viewer opens the canvas, gets curious, and types a number into a plan cell. What
   just happened to your bill?
2. Your semantic model has RLS roles for each region. A new analyst is added with no role
   assigned. What do they see?
3. You have 25 sheets in a plan item and Finance asks for a new scenario. Now what?

<details>
<summary>Answers</summary>

1. They upgraded from Viewer to Stakeholder. The 37 CU-hour Viewer session closed
   prorated, and a fresh 30-day Stakeholder session opened at 168 CU-hours. It cannot be
   reversed until it expires.
2. The union of all regional data. Unassigned does not mean restricted: only data
   outside every role stays hidden.
3. You are at the per-item limit. Split into a second plan item and connect them with
   Infobridge rather than trying to squeeze more sheets in.

</details>

---

## Where to go next

- **[Connected planning with Infobridge](https://learn.microsoft.com/fabric/iq/plan/infobridge-concept-connected-planning)**: the multi-region consolidation this lab only describes
- **[Cube-based consolidation](https://learn.microsoft.com/fabric/iq/plan/planning-concept-cube)**: keeping a sales plan, cost plan, and profitability sheet in sync across dimensions
- **[Approval workflows](https://learn.microsoft.com/fabric/iq/plan/planning-concept-approval-workflow)**: formalizing the submit-and-review cycle
- **[Fabric Planning community hub](https://fabricplanning.io)**: practitioner field reports and a capacity calculator

---

**← Back to:** [Module 07: PowerTable](07-powertable.md) · [README](../README.md)

---

### Sources

- [Billing and usage for planning in Fabric](https://learn.microsoft.com/fabric/iq/plan/resources/billing-fabric-plan)
- [Roles in planning in Fabric](https://learn.microsoft.com/fabric/iq/plan/overview-roles)
- [Known limitations in planning](https://learn.microsoft.com/fabric/iq/plan/overview-limitations)
- [Writeback](https://learn.microsoft.com/fabric/iq/plan/planning-concept-writeback)
- [Connected planning](https://learn.microsoft.com/fabric/iq/plan/infobridge-concept-connected-planning)
