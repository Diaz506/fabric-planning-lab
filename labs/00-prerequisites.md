# Module 00: Prerequisites & briefing

**Est. time:** 15 minutes
**You'll leave with:** a working environment, and an honest understanding of what this
workload costs before you spend anything.

---

## The briefing

Harborlight Provisions closed FY25 at **$28.5M**. The board has approved an FY26 target
of **$30M revenue** and **$12.5M gross profit**.

The current planning process is a 40-tab Excel workbook. Three regional finance leads
each own a tab, corporate owns the consolidation, and nobody trusts the roll-up because
the tabs drift apart the moment anyone touches a formula. Last year's forecast reached
the board eleven days after it was produced, which is to say it reached the board as
history.

Everything the plan needs already exists in a governed Power BI semantic model that the
company reports from every month. The plan just doesn't live there.

Your job over the next eight modules is to move it.

---

## What Fabric Planning actually is

An enterprise performance management workload built natively into Microsoft Fabric,
under Fabric IQ. It puts budgets, forecasts, and scenarios on the same semantic models
your analytics already run on, so plan and actual stop being two different truths.

It has four components. You will use the first three in this lab, and meet the fourth.

| Component | What it does | Where you meet it |
|---|---|---|
| **Planning sheets** | Budgeting, forecasting, scenario modeling in an Excel-like grid over a semantic model | Modules 01–05 |
| **Intelligence sheets** | The reporting canvas, variance charts, KPI cards, financial matrices | Module 06 |
| **PowerTable** | A governed data app for reference and master data, with forms, approvals, and automations | Module 07 |
| **Infobridge** | Data integration and connected planning, regional sheets roll up to corporate automatically | Module 08 |

---

## Before you click anything: the cost model

Fabric Planning does not sell per-user licenses. It bills **active 30-day sessions**.

A session starts the moment a user opens, creates, or edits a plan item. It then runs
for **730 hours, which is 30 days, and you cannot end it early**.

The role rates below cover the **planning workload only**. Fabric SQL, OneLake, and Power
BI XMLA operations consume capacity separately, which is what the 30% buffer later in this
module is for.

| Role | Who it is | Per 30-day session |
|---|---|---|
| **Planner** | FP&A analysts, modelers, administrators | **847 CU-hours** |
| **Stakeholder** | Business users, reviewers, approvers | **168 CU-hours** |
| **Viewer** | Executives and report consumers | **37 CU-hours** |

### CU-hours are not CUs

This trips up almost everyone, including people holding the documentation, so it is worth
thirty seconds.

Picture a tap that flows at **4 litres per minute**. Over an hour it delivers 240 litres.
Over a day, 5,760. Same tap the whole time. Nothing changed except how long you watched.

A capacity works the same way. **An F4 is a tap that flows at 4 CU.** That is the only
real number:

| Watched for | Same F4, described as |
|---|---|
| a second | 4 CU-seconds |
| an hour | 14,400 CU-seconds (4 x 3,600) |
| 30 days | 2,920 CU-hours (4 x 730) |

Every row says "4 CU." Microsoft reports the hourly view in the Fabric Capacity Metrics
app, because that app watches throughput in short slices, and the 30-day view in the
planning billing article, because that article describes a total charge. Two jobs, one
tap.

So a **CU** is a rate and a **CU-hour** is an amount. A Planner does not need an F847,
any more than a 1,000-watt heater running ten hours needs a 10,000-watt supply. Spread
across the 730 hours of a session, 847 CU-hours is a little over **one CU** of average
draw:

| Role | Per 30-day session | Average draw |
|---|---|---|
| **Planner** | 847 CU-hours | **1.16 CU** |
| **Stakeholder** | 168 CU-hours | **0.23 CU** |
| **Viewer** | 37 CU-hours | **0.05 CU** |

#### The one rule: same window on both sides

Every costing mistake here comes from comparing a supply figure and a usage figure that
were measured over different lengths of time. Pick a window, put both numbers in it, and
the arithmetic behaves.

One Planner on an F4, done twice:

| Window | F4 supplies | Planner uses | Left over |
|---|---|---|---|
| **Per hour** | 14,400 CU-seconds | 4,177 CU-seconds | **10,223, or 71%** |
| **Per 30 days** | 2,920 CU-hours | 847 CU-hours | **2,073, or 71%** |

Same answer both ways, because it is the same tap: a Planner takes **29% of an F4**.

The trap is mixing the rows. Subtract the Planner's 30-day total of 847 from the hourly
supply of 14,400 and you get a tidy-looking 13,553, which says the Planner costs 6%. That
is wrong by roughly five times, and it is the kind of wrong that survives a meeting
because the number looks reasonable.

If you ever need to move between the two units: **CU-hours x 3,600 = CU-seconds**, and
**CU-hours / 730 = average CU draw**.

### Per session, not per month

This distinction is the whole pricing argument, so it is worth being precise. A session
runs 30 days **from the moment it is triggered**, not from the first of the month. Within
that window the user can work every day at no extra cost, because a user returning in the
same role, tenant, and capacity does not start a second session. When it expires, a new
session begins **only when they next act**.

So annual cost follows usage, not headcount:

| Usage pattern | Sessions per year | Planner CU-hours per year |
|---|---|---|
| Continuously active | 12 | 10,164 |
| Quarterly reforecast | 4 | 3,388 |
| Budget season only (3 months) | 3 | 2,541 |
| Opened it once, in March | 1 | 847 |

That is the argument against per-seat EPM licensing: occasional reviewers and approvers
cost you only when they participate. A CFO who signs off twice a year is two Viewer
sessions, not an annual seat.

The trap runs the other way. Because a session cannot be ended early, someone who touches
the plan once on day one costs exactly the same as someone who works it daily for 30 days.
There is no partial credit for light usage inside an open session.

### What that looks like against a real capacity

A capacity supplies its CU continuously, so over a 730-hour window it provides
**CU × 730 CU-hours**. One Planner session spends 847 of them.

| SKU | CU-hours per 30 days | One Planner | Ten-person team (1,704) | Plus 30% buffer (2,215) |
|---|---|---|---|---|
| **F2** | 1,460 | 58% | **117%, does not fit** | **152%, does not fit** |
| **F4** | 2,920 | 29% | 58% | 76% |
| **F8** | 5,840 | 15% | 29% | 38% |
| **F16** | 11,680 | 7% | 15% | 19% |
| **F32** | 23,360 | 4% | 7% | 9% |
| **F64** | 46,720 | 2% | 4% | 5% |

The ten-person team is the one sized in [Module 08](08-sizing-and-governance.md): one
Planner, four Stakeholders, five Viewers.

Two caveats. This is a **budget** view, not a throughput view. It tells you whether the
work fits across 30 days, not whether you will be throttled at nine o'clock on a Monday.
And the capacity is **shared with every other Fabric workload**, so if that F8 already
runs a warehouse and semantic model refreshes, planning is additive on top of it.

### Pausing the capacity does not help

If you pause or delete a capacity mid-session, **the remaining CUs for every active
session are summed and added to your Azure bill**. Pausing does not defer the cost; it
brings it forward. Start a Planner session on Monday, pause on Tuesday, and the remaining
twenty-eight days arrive on the bill anyway.

Two related behaviors worth knowing:

- **Deleting the plan item does not stop the session.** Active sessions run and bill
  through the full 30 days regardless.
- **Exhausting the capacity does not stop the session either.** Sessions keep being
  recorded even when other workloads have consumed the credits, and billing continues
  periodically.

### You do not assign these roles. Users earn them.

Everyone starts as a Viewer. The system upgrades them based on what they do:

- **Opening** any sheet to look at it keeps you a **Viewer**.
- **Entering or changing plan numbers, approving, writing back, or creating and editing
  PowerTables and Intelligence sheets** makes you a **Stakeholder**.
- **Creating a planning sheet, or editing a planning sheet's design** (its structure,
  rules, or writeback destinations) makes you a **Planner**. Creating a new plan item
  also upgrades the creator to Planner.

> [!NOTE]
> Before August 19, 2026, building a PowerTable or an Intelligence report also made
> someone a Planner. It no longer does. If you are reading older guidance that says
> otherwise, it predates that change.

Three consequences worth internalizing:

1. **A role upgrade opens a new session.** The lower session closes and is prorated, and
   billing continues at the higher tier. You cannot downgrade inside an active session.
2. **One user, one capacity, one bill**: at the highest role they reach. But work across
   two capacities and you have two sessions.
3. **Automation jobs bill separately.** Each successful PowerTable automation or
   connected-planning instance costs **2 CU**. Failed jobs are free.

Budget roughly a **30% capacity buffer** on top, because Fabric SQL, OneLake, and XMLA
operations consume capacity outside the planning meters.

### Administrators can cap this

Sessions are not a runaway train. Three tenant settings control who may upgrade:

| Setting | What it does |
|---|---|
| **Users can upgrade to a Planner session** | Enable or disable Planner upgrades, for the whole organization or named security groups |
| **Users can upgrade to a Stakeholder session** | Same control for Stakeholder upgrades |
| **Show Oversubscription Warning** | Warns a user before they start or upgrade a session that would likely oversubscribe the capacity |

All three can be overridden per capacity through **Delegated Tenant Settings**. Note that
Planner access includes Stakeholder access, so enabling Planner org-wide also enables
Stakeholder org-wide. Users with neither can still open plan items in Reading view.

**What this means for you right now:** finishing Module 01 will make you a Planner and
commit 847 CU-hours over 30 days. On a trial capacity that is free and fine. On a shared
production F-SKU, do the arithmetic first. Microsoft publishes a
[capacity estimator](https://community.fabricplan.com/capacity-pricing/) for exactly this.

Module 08 returns to sizing once you have seen what the roles actually do.

---

## Prerequisites checklist

Work through these in order. The first three are the ones that silently break everything
if they are missed.

### 1. Capacity

- [ ] A Microsoft Fabric capacity at **F2 or higher**, or an active
      [Fabric trial](https://learn.microsoft.com/fabric/fundamentals/fabric-trial).

Power BI Pro and Premium Per User are **not supported** for the XMLA and embed-token
scenarios this lab depends on. Power BI Premium P1–P5 works. Very small SKUs may hit
XMLA and memory limits even when nominally supported.

### 2. Workspace role

- [ ] You are **Member** or **Admin** on the workspace you will use.

**Contributor is not sufficient.** A Contributor cannot create or share cloud
connections, and cannot create plan items that require embed token generation. This is
the single most common reason a first attempt fails, and the error message does not make
the cause obvious.

> [!NOTE]
> Microsoft's roles article recommends Admin, Member, **or Contributor** as the workspace
> role for a Planner persona. The tutorial prerequisites and the known-limitations article
> are stricter, and say Contributor cannot create cloud connections or plan items that
> need embed tokens. This lab follows the stricter guidance, because that is the one that
> matches what happens when you try it. If your admin offers Contributor, push for Member.

### 3. Tenant settings

An admin enables these in the Fabric admin portal under **Tenant settings**:

- [ ] **Integration settings** → *Allow XMLA endpoints and Analyze in Excel with
      on-premises semantic models*
- [ ] **Developer settings** → *Embed content in apps*
- [ ] **Developer settings** → *Service principals can call Fabric public APIs*
      (only needed if you will authenticate with a service principal

### 4. Capacity setting

- [ ] Under **Capacity settings**, the **XMLA Endpoint** is set to **Read Only** or
      **Read Write**.

### 5. Browser

- [ ] Microsoft Edge or Google Chrome. **Safari is not supported.**

### 6. Sample data

- [ ] Download **`Northwind_FMCG.pbix`** from Microsoft's sample repository:
      [fabric-samples/docs-samples/iq/plan](https://github.com/microsoft/fabric-samples/tree/main/docs-samples/iq/plan)

If you plan to do the optional PowerTable module, also grab
`Northwind-FMCG-assets-powertable-tutorial.xlsx` from the same folder.

See [`data/README.md`](../data/README.md) for what is inside the model.

---

## "We don't have a semantic model. Does this still work?"

This comes up in almost every customer conversation, so it is worth answering before you
start rather than mid-demo.

First, a correction to the usual version of the question. Planning binds to a **semantic
model**, not to a report. Whether a Power BI report exists is irrelevant: plenty of
organizations have governed models consumed through Excel or Analyze in Excel with no
formal report on top, and planning connects over XMLA without caring either way.

So the real question is about the model. Three situations:

### You have a model but no report

Nothing changes. Connect the plan item to the model and every module in this lab works.
Step 1 becomes "select your existing model" instead of importing a sample.

### You have data in Fabric but no model

Build a semantic model over the Lakehouse or Warehouse first. Direct Lake is supported,
with one setup cost worth knowing up front: **Direct Lake and DirectQuery models require
a gateway connection using fixed credentials**, because single sign-on is not supported
yet.

### You have neither

Planning does offer entry points that do not start from a model:

| Path | Accepts |
|---|---|
| Plan item creation | Data from a semantic model **or from Excel**, or start with a planning sheet and connect data afterwards |
| PowerTable, **New Table** | Excel, CSV, or column headers alone to create an empty table for manual entry |
| Intelligence sheets | Excel and CSV import |

These are legitimate shapes. The roles article confirms it indirectly when it describes
plan items that contain only PowerTable sheets, or only intelligence sheets.

**But think carefully before recommending that route.** Every tutorial, every
prerequisite, and the whole permissions model is written around semantic model
connections, so the Excel path is thinly documented and less travelled.

More to the point, it usually solves the wrong problem. A company with no semantic model
that starts by uploading spreadsheets into planning has recreated the situation it was
trying to escape: ungoverned numbers, no single definition, now hosted somewhere new. The
40-tab workbook does not stop being a 40-tab workbook because it moved to Fabric.

The sequence that works is: get the actuals into Fabric, build the semantic model, then
plan on it. Planning earns its value because plan and actual share one governed
definition. Remove that and you have bought a spreadsheet with approval workflows.

Where the file-based path genuinely fits is **reference and master data with no system of
record**, the cost centre list, the headcount roster, the asset register. That is exactly
what [Module 07](07-powertable.md) does, and it is why PowerTable accepts Excel while
planning sheets expect a model.

---

## Things that will not work

Read this list once now, so you recognize the symptom later.

| Constraint | Consequence |
|---|---|
| Microsoft Entra **B2B guest accounts** are not supported | Guests cannot use plan items at all |
| Workspaces or tenants using **private links** | Plan items are not supported |
| Semantic models in **My workspace** | Cannot be used |
| **Composite models** | Not supported |
| **Direct Lake and DirectQuery** models | Need a gateway connection with fixed credentials, SSO is not supported yet |
| Semantic model permission | You need **Admin** or **Build** on the model |
| **Renaming the semantic model** | Breaks the connection; the plan item stops working |
| **Renaming the workspace** | Breaks the plan item; it no longer opens |
| One semantic model per plan item | The binding is **permanent**. A different source means a new plan item |

That last one deserves emphasis. When you create a plan item in Module 01 and connect it
to a model, that pairing is for the life of the item. Choose deliberately.

---

## What gets created on your behalf

When you create a plan item, Fabric **automatically creates a Fabric SQL database** in
your workspace to store the plan's metadata. You do not configure it and you should not
delete it.

That database is separate from any **writeback destination** you set up later. Writeback
is where your plan numbers land so the rest of Fabric can read them, and you configure it
explicitly in Module 04.

### Fabric SQL is not free, and it is not in the role rates

Each Fabric SQL database bills **compute plus storage**, independently of the planning
meters:

- **Compute** autoscales, with a 2 GB minimum memory charge while the database is online.
  After 15 minutes of inactivity, compute drops to zero. The database stays online for 15
  minutes past your last query, so two minutes of work bills as roughly seventeen.
- **Storage** bills **continuously, even when compute is paused**. Allocated storage plus
  any backup storage beyond 100% of the provisioned database size.

A lab environment you abandon still accrues storage charges, and the 30-day session keeps
running whether or not you ever open it again.

> [!WARNING]
> **On a trial capacity you can create only three SQL databases.**
>
> This lab creates four if you do every module:
>
> | # | Database | Created in |
> |---|---|---|
> | 1 | `Northwind_FMCG_YourName`, the writeback destination | Module 01 |
> | 2 | the plan item's metadata database, automatic | Module 01 |
> | 3 | `fabric_plan_training` | Module 07 |
> | 4 | the second plan item's metadata database, automatic | Module 07 |
>
> Modules 00 through 06 and 08 fit exactly at the limit. **Adding Module 07 needs a
> fourth database and will fail on a trial capacity.** The symptom is error
> `GEN_DB_010`, *"SQL database capacity limit reached, so a new plan item can't be
> created."*
>
> To do Module 07 on a trial, delete the Module 01 plan item first, or run it on a paid
> capacity. Also check your trial's size under **Capacity settings** → **Trial**: trials
> are provisioned as either **F4 or F64**, and you may be eligible to change it.

---

## Check yourself

Before moving on, you should be able to answer:

1. Why is Contributor access not enough to complete this lab?
2. What action will make you a Planner, and what does that commit in CU-hours?
3. Can you end a planning session early if you finish your work in a day?
4. What happens to a plan item if someone renames the workspace it lives in?
5. A Planner session costs 847 CU-hours. How much of an F4 is that?
6. A customer says they have no Power BI reports. Does that stop them using planning?

<details>
<summary>Answers</summary>

1. A Contributor cannot create or share cloud connections, and cannot create plan items
   that require embed token generation. You need Member or Admin.
2. Creating a plan item, creating a planning sheet, or editing a planning sheet's design.
   It commits a 30-day Planner session at 847 CU-hours.
3. No. A session runs the full 730 hours once triggered and cannot be ended manually.
4. It breaks. The plan item no longer opens.
5. 1.16 CU of average draw, which is 29% of an F4. Divide 847 by the 730 hours in the
   session. Comparing 847 against an hourly supply figure understates it roughly
   fivefold.
6. No. Planning binds to a semantic model, not a report, and never reads the report at
   all. The question that matters is whether they have a governed semantic model.

</details>

---

**Next:** [Module 01: Environment & first sheet →](01-environment-and-first-sheet.md)

---

### Sources

- [Prerequisites for planning in Fabric](https://learn.microsoft.com/fabric/iq/plan/overview-prerequisites)
- [Known limitations in planning](https://learn.microsoft.com/fabric/iq/plan/overview-limitations)
- [Roles in planning in Fabric](https://learn.microsoft.com/fabric/iq/plan/overview-roles)
- [Billing and usage for planning in Fabric](https://learn.microsoft.com/fabric/iq/plan/resources/billing-fabric-plan)
