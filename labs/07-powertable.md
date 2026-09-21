# Module 07: PowerTable *(optional)*

**Est. time:** 30 minutes
**You'll build:** a governed asset register with typed columns, lookups, approvals,
access control, and an automation that retires assets on its own.

---

## Where we are

Harborlight's plan is done. This module solves a different problem: the one sitting next
to it.

Every planning process depends on reference data nobody owns: the asset register, the
cost centre list, the product hierarchy, the headcount roster. It lives in a shared
spreadsheet, four people have edit rights, three have opinions, and there is no record of
who changed what.

**PowerTable** is a governed data app inside Fabric Planning. It gives that spreadsheet a
schema, an approval workflow, row-level permissions, and an audit log, without anyone
writing code.

> [!NOTE]
> This module is self-contained and uses a separate plan item and dataset. Skip it if you
> only care about the planning path, and go straight to
> [Module 08](08-sizing-and-governance.md).
>
> **On cost:** creating or editing PowerTable sheets is Stakeholder work, not Planner
> work. Microsoft's roles documentation is explicit that these actions no longer upgrade
> your session to Planner, and that for plan items containing **only** PowerTable sheets,
> only the Stakeholder and Viewer roles are available. So this module costs a Stakeholder
> session (168 CU-hours) rather than a Planner one, and nothing extra if you already hold
> a Planner session on the same capacity.

**You'll need:** `Northwind-FMCG-assets-powertable-tutorial.xlsx` from
[fabric-samples](https://github.com/microsoft/fabric-samples/tree/main/docs-samples/iq/plan).
It contains three sheets: assets, employees, and locations.

> [!WARNING]
> **This module will fail on a Fabric trial capacity.** A trial allows only three SQL
> databases, and Modules 01 through 06 already use three: the writeback database, the
> plan item's automatic metadata database, and nothing spare. This module needs two more,
> `fabric_plan_training` plus a second plan item's automatic metadata database.
>
> The symptom is error `GEN_DB_010`, *"SQL database capacity limit reached, so a new plan
> item can't be created."* Delete the Module 01 plan item first, or run this module on a
> paid capacity.

---

## Part 1: Set up

1. In your workspace, select **New item** → **SQL database**. Name it
   `fabric_plan_training` and select **Create**.
2. Select **New item** → **Plan**. Name it `Harborlight_Asset_Management` and select
   **Create**.
3. On the launch screen, create a **Fabric SQL connection** to `fabric_plan_training`.

> [!NOTE]
> PowerTable writes to a real SQL database you control, not to the plan's internal
> metadata store. That is the point. This data is meant to be consumed by other systems.

## Part 2: Import the tables

Build one sheet per table, starting with the lookups so the asset sheet can reference
them.

1. On the Plan welcome screen, select **PowerTable**.
2. In the **New PowerTable Sheet** dialog, choose the import option and upload
   `Northwind-FMCG-assets-powertable-tutorial.xlsx`.
3. Create a sheet for the **locations** table. Map the fields and set the primary key.
4. Repeat for **employees**.
5. Repeat for **assets**.

> [!IMPORTANT]
> Every PowerTable sheet needs a **primary key**. It is how updates find the right row and
> how lookups resolve. Choose a column that is genuinely unique: an asset tag, not an
> asset name.

## Part 3: Configure the columns

This is where a spreadsheet becomes an application. Open the **assets** sheet and set
each column's input type.

| Column | Type | Why |
|---|---|---|
| Asset image | **Image** | Renders a thumbnail instead of a URL string |
| Asset status | **Single Select** | Constrains entry to valid states, no more "Retired", "retired", "RETIRED" |
| Assigned employee | **Single Select**, values type **Lookup** | Resolves against the employees sheet; cannot reference someone who left |
| Location | **Single Select**, values type **Lookup** | Resolves against the locations sheet |
| Expected EOL Date | **Formula** | Calculated, not typed |

> [!NOTE]
> Lookup is not a separate input type. It is a **values type** available within the
> Single Select input type, which is why you configure it from the Single Select
> settings rather than from the type list.

For the formula column, enter:

```
DATEADD([Purchase Date], (365*[Expected Lifetime In Years]))
```

> [!WARNING]
> **Type this formula manually. Do not copy and paste it.** Column references are
> resolved as you type, and pasted text does not bind to the underlying columns. It will
> look correct and fail silently.

Lookup columns are the real upgrade here. In the spreadsheet version, "assigned to" was
free text and eventually contained three spellings of the same person. Now it is a
reference with referential integrity.

## Part 4: Approvals

Asset changes should not be unilateral.

1. On the **assets** sheet, go to the **PowerTable** tab and select **Approvals**.
2. Enable **Requires Approval for All Changes**.
3. Configure the workflow: simple, multi-level (up to three levels), or rule-based
   depending on how much ceremony the change deserves.

The **Save** button becomes **Submit for Review**. Nothing lands until it is approved.

> [!TIP]
> Rule-based approval is the useful middle ground: route a location change straight
> through, but send a status change to *Retired* for review. Governance that only fires
> when it matters is governance people keep using.

## Part 5: Access control

1. Go to the **Setup** tab and select **Manage Access**.
2. Set row-level **Add**, **Update**, and **Delete** permissions per user or group.

Regional IT can add and update assets in their own location. Only the asset manager can
delete.

> [!WARNING]
> PowerTable does **not** support user-specific database-level row-level security. Every
> query runs as the identity on the database connection, not the signed-in user, so
> people may see rows you expected RLS to hide. Blend (From Sheets) does not honour RLS
> either.
>
> Access control governs **what users can change**, not always what they can see. Do not
> put data in PowerTable that some viewers must not read.

## Part 6: Automate the boring part

Assets past their end-of-life should retire themselves.

1. Go to the **Setup** tab and select **Automations**.
2. Create an automation named `Retire Asset`.
3. Configure a trigger, then a **Find Record** action followed by an **Update Record**
   action to set status to *Retired*.

### Limits that will bite you

| Limit | Value |
|---|---|
| **Find Action** record fetch | First **1,000** records only |
| Repeating group iteration | First **1,000** items |
| Cascading trigger depth | **2 levels**, including the initial trigger |
| Database trigger writeback | **10 records per trigger type** |
| Multiple record operations | Not supported in subsequent actions, single-record only |

That writeback limit is the sharp one: if a user writes back more than 10 records, the
automation **does not fire for any of them**. It does not partially process the first ten.
Design for small, frequent changes rather than bulk loads.

Each successful automation job costs **2 CU**. Failed jobs are free.

## Part 7: Verify

1. On the **assets** sheet, select **View audit logs**.

Every change, who made it, and when. That is the thing the spreadsheet never had, and the
reason this module exists.

---

## Where you landed

Harborlight's asset register is now a governed application with typed columns, referential
integrity, an approval chain, scoped permissions, an audit log, and an automation that
maintains status without human involvement. The shared spreadsheet is retired. One hopes
somebody routed that change through an approval workflow.

## Check yourself

1. Why must the formula column be typed rather than pasted?
2. A user bulk-updates 40 rows and the retirement automation does not fire. Why?
3. You set row-level access so regional IT only manages their own location's assets. Can
   they still see other locations' rows?

<details>
<summary>Answers</summary>

1. Column references bind as you type. Pasted text does not resolve against the
   underlying columns, and the failure is silent.
2. Database trigger writeback supports 10 records per trigger type. Above that the
   automation is skipped entirely, not partially applied.
3. Probably yes. PowerTable does not support user-specific database-level RLS; queries
   run as the connection identity. Access control limits what they can change, not
   necessarily what they can see.

</details>

---

**← Back:** [Module 06](06-intelligence-canvas.md) · **Next:** [Module 08: Sizing & governance →](08-sizing-and-governance.md)

### Sources

- [Tutorial part 7: Get started with PowerTable](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/powertable/tutorial-7-get-started-with-powertable)
- [Tutorial part 8: Approvals, access controls, and automations](https://learn.microsoft.com/fabric/iq/plan/planning-tutorial/powertable/tutorial-8-set-up-approvals-access-controls-automations)
- [PowerTable overview](https://learn.microsoft.com/fabric/iq/plan/powertable-overview)
- [PowerTable automation](https://learn.microsoft.com/fabric/iq/plan/powertable-concept-automation)
- [Known limitations](https://learn.microsoft.com/fabric/iq/plan/overview-limitations)
