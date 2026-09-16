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
for **730 hours, which is 30 days, and you cannot end it early**. Pausing the capacity does not
help; if you pause or delete a capacity mid-session, the remaining CUs are summed and
added to your Azure bill.

| Role | Who it is | 30-day consumption |
|---|---|---|
| **Planner** | FP&A analysts, modelers, administrators | **847 CU-hours** |
| **Stakeholder** | Business users, reviewers, approvers | **168 CU-hours** |
| **Viewer** | Executives and report consumers | **37 CU-hours** |

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

---

## Check yourself

Before moving on, you should be able to answer:

1. Why is Contributor access not enough to complete this lab?
2. What action will make you a Planner, and what does that commit in CU-hours?
3. Can you end a planning session early if you finish your work in a day?
4. What happens to a plan item if someone renames the workspace it lives in?

<details>
<summary>Answers</summary>

1. A Contributor cannot create or share cloud connections, and cannot create plan items
   that require embed token generation. You need Member or Admin.
2. Creating a plan item, creating a planning sheet, or editing a planning sheet's design.
   It commits a 30-day Planner session at 847 CU-hours.
3. No. A session runs the full 730 hours once triggered and cannot be ended manually.
4. It breaks. The plan item no longer opens.

</details>

---

**Next:** [Module 01: Environment & first sheet →](01-environment-and-first-sheet.md)

---

### Sources

- [Prerequisites for planning in Fabric](https://learn.microsoft.com/fabric/iq/plan/overview-prerequisites)
- [Known limitations in planning](https://learn.microsoft.com/fabric/iq/plan/overview-limitations)
- [Roles in planning in Fabric](https://learn.microsoft.com/fabric/iq/plan/overview-roles)
- [Billing and usage for planning in Fabric](https://learn.microsoft.com/fabric/iq/plan/resources/billing-fabric-plan)
