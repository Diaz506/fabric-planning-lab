# Lab flow

How the pieces connect, and what each module adds.

```mermaid
flowchart TB
    subgraph SRC["Source"]
        PBIX["Northwind_FMCG.pbix<br/><i>Harborlight's model</i>"]
        XLSX["assets.xlsx<br/><i>optional</i>"]
    end

    subgraph ITEM["Plan item: Harborlight_FY26_Plan"]
        direction TB
        PI["<b>Plan Intro</b><br/>M01 · FY25 actuals<br/>M02 · $30M target + sales plan"]
        GP["<b>Gross Profit</b><br/>M03 · Optimizer → $12.5M"]
        FC["<b>Forecast</b><br/>M04 · MSTL + rolling horizon"]
        PL["<b>P&amp;L models</b><br/>M05 · measure + row<br/>+ 2 scenarios"]
        IS["<b>FY26 Plan Review</b><br/>M06 · intelligence canvas"]
    end

    subgraph PT["Plan item: Asset Management (optional)"]
        PTS["<b>PowerTable</b><br/>M07 · approvals, access,<br/>automation"]
    end

    subgraph OUT["Fabric SQL"]
        SQL[("Forecast table<br/><i>Long with Changes</i>")]
        ADB[("fabric_plan_training")]
    end

    DS["Downstream<br/>Power BI · semantic models · data agents"]

    PBIX -->|semantic model connection| PI
    XLSX --> PTS

    PI -->|From Sheets| GP
    PI -->|From Sheets| IS
    FC -->|From Sheets| IS
    PBIX --> FC
    PBIX --> PL
    PL --> IS

    FC -->|writeback| SQL
    PTS --> ADB
    SQL --> DS

    classDef src fill:#e8f0fe,stroke:#4285f4,color:#111
    classDef sheet fill:#fff4e5,stroke:#f59e0b,color:#111
    classDef out fill:#e6f4ea,stroke:#34a853,color:#111
    classDef ds fill:#f3e8fd,stroke:#9334e6,color:#111
    class PBIX,XLSX src
    class PI,GP,FC,PL,IS,PTS sheet
    class SQL,ADB out
    class DS ds
```

## Reading the diagram

**One plan item holds almost everything.** Modules 01 through 06 all build inside
`Harborlight_FY26_Plan`, because a plan item binds permanently to a single semantic
model. Sheets reference each other through **From Sheets**, which is a live link rather
than a copy. Edit the sales plan and the gross profit sheet, the canvas, and every KPI
consuming it all move.

**Writeback is the only exit.** Plan data stays inside the plan item until Module 04
pushes the forecast into a Fabric SQL table. That table is what Power BI, semantic models,
and data agents can read. Writeback does not update the connected semantic model.

**PowerTable is a separate item.** Module 07 stands alone, with its own plan item and its
own SQL database, because it solves a reference-data problem rather than a planning one.

## Module dependencies

```mermaid
flowchart LR
    M00["00<br/>Prereqs"] --> M01["01<br/>Environment"]
    M01 --> M02["02<br/>Allocation"]
    M02 --> M03["03<br/>Optimizer"]
    M03 --> M04["04<br/>Forecast"]
    M04 --> M05["05<br/>P&amp;L"]
    M05 --> M06["06<br/>Canvas"]
    M06 --> M08["08<br/>Sizing"]
    M01 -.-> M07["07<br/>PowerTable<br/><i>optional</i>"]
    M07 -.-> M08

    classDef core fill:#fff4e5,stroke:#f59e0b,color:#111
    classDef opt fill:#f1f3f4,stroke:#9aa0a6,color:#111,stroke-dasharray: 4 4
    class M00,M01,M02,M03,M04,M05,M06,M08 core
    class M07 opt
```

Modules 02 through 06 build on each other in order: Module 03 consumes the sales plan
from Module 02, Module 06 visualizes the forecast from Module 04. Module 07 branches off
and can be done any time after the environment exists, or skipped.
