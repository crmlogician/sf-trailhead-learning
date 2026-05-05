# Superbadge: Flow Collections and Loops

Configure flow collections and loops to deliver business insights.

Trailhead: [Superbadge: Flow Collections and Loops](https://trailhead.salesforce.com/content/learn/superbadges/superbadge_collections_loops_sbu)

## Overview

- **Points:** 1,500
- **Duration:** ~1 hour
- **Concepts tested:** Flow collections, Data Table screen input component, loop-driven bulk DML

## Prerequisites

- Recommended superbadges: Flow Fundamentals and Flow Optimization
- Sign up for the special Developer Edition org with preconfigured data
- Connect the org to Trailhead for challenge validation

## Challenge 1 — Create a Flow to Collect and Display Records for User Interaction

Configure a Data Table to display opportunity records alphabetically for user interaction. Provide a way for users to filter the records displayed in the Data Table.

**Problem:** 
Display opportunities from the last 30 days with stage-based filtering.
- Formula resource `Today_Minus_30` → `DATETIMEVALUE(Today() - 30)`
- Get Records for opportunities created in the past 30 days, sorted A–Z by Name
- Assignment `Store_Get_Records_Results` to load records into a collection
- Picklist Choice Set for Stage; include a choice with value `All` (Text) for "view all"
- Data Table showing Name, Stage, Amount, ExpectedRevenue, Description
- Decision → Assignment `Filter_for_StageName` to build the filtered collection based on the user's selection

### Target flow
- **Label:** `Opportunity Review`
- **API Name:** `Opportunity_Review`
- **Type:** Screen Flow

### Resources to create

| Type | API Name | Notes |
| --- | --- | --- |
| Formula (DateTime) | `Today_Minus_30` | Expression: `DATETIMEVALUE(Today() - 30)` — bounds the Get Records lookup |
| Choice | *(any label)* | Data type **Text**, value **`All`** — used as the "View All" picklist option |
| Picklist Choice Set | *(any name)* | Source: `Opportunity.StageName` — feeds the stage filter picklist |
| Variable (SObject collection) | *(working collection)* | Holds opportunities for the data table |

### Element-by-element

1. **Get Records** — query `Opportunity`
   - Filter: `CreatedDate >= {!Today_Minus_30}`
   - Sort: `Name` ascending (alphabetical)
   - Store all fields automatically
2. **Assignment `Store_Get_Records_Results`** — copy the Get Records output into the working collection used by the Data Table
3. **Screen** — display the Data Table and stage filter
   - **Picklist** field — choices: the `All` choice + the picklist choice set (default selection: the `All` choice)
   - **Data Table** component (`flowruntime:datatable`) bound to the working collection
     - Columns: **Name**, **Stage**, **Amount**, **ExpectedRevenue**, **Description**
     - Allow row selection so the chosen rows can be passed to `Opportunity_Updater` later
4. **Decision** — evaluate the picklist value
   - Default outcome → user picked **All** (no filter needed)
   - Rule outcome → user picked a specific stage (route to filter element)
5. **Filter `Filter_for_StageName`** — produce a filtered collection where `StageName` equals the picklist value
6. **Display** the filtered collection (either reactively on the same screen or on a follow-up screen bound to the filter output)

### Acceptance checks

- The flow is **Active** and named exactly `Opportunity Review` / `Opportunity_Review`.
- Formula `Today_Minus_30` exists with the exact expression above.
- A choice resource exists with value `All` (Text data type).
- A Picklist Choice Set on `Opportunity.StageName` is referenced by the screen picklist.
- An assignment named `Store_Get_Records_Results` populates the data table source collection.
- A filter element named `Filter_for_StageName` produces the filtered collection based on the picklist selection.
- The Data Table shows opportunities alphabetically by Name and includes Name, Stage, Amount, ExpectedRevenue, Description.

### Tips

- Labels and API names are case-sensitive — copy/paste them.
- Don't add extra elements; the challenge checker rejects deviations.
- Save and activate the flow before running the challenge check.

## Challenge 2 — Configure a Screen Flow to Collect and Save User Changes

Complete additional configuration to update opportunities based on changes users make in a screen. Optimize the flow for best performance and efficiency.

**Problem:**
Take the rows the user selected in the Challenge 1 Data Table, let the user edit each one on a per-record screen, then persist all edits in a **single bulk DML** — not one update per loop iteration.

### Target flow
- **Label:** `Opportunity Updater`
- **API Name:** `Opportunity_Updater`
- **Type:** Screen Flow (designed to be called as a subflow from `Opportunity_Review`)

### Resources to create

| Type | API Name | Notes |
| --- | --- | --- |
| Variable (SObject collection, **Input**) | `Selected_Opportunities` | Object: `Opportunity` — already in the starter; receives the rows selected on the parent flow's Data Table |
| Variable (SObject collection) | `oppsToUpdate` | Object: `Opportunity` — local; holds edited records, updated in one DML after the loop |

### Element-by-element

1. **Loop `Opportunity_Loop`** — iterate `Selected_Opportunities` ascending
   - `nextValueConnector` → `Update_Opportunity` screen
   - `noMoreValuesConnector` → `Update_Opportunities` record-update element
2. **Screen `Update_Opportunity`** (inside the loop) — editable fields default from the current loop record:
   - **Opp_Name** (String) ← `{!Opportunity_Loop.Name}`
   - **Opp_Close_Date** (Date) ← `{!Opportunity_Loop.CloseDate}`
   - **Opp_Account** (`flowruntime:lookup`, `AccountId`) ← `{!Opportunity_Loop.AccountId}`
   - **Opp_Description** (String) ← `{!Opportunity_Loop.Description}`
   - All input fields use `inputsOnNextNavToAssocScrn=UseStoredValues` so values persist on revisit
3. **Assignment `Update_Opportunity_Record`** — copy each screen value onto the loop record:
   - `Opportunity_Loop.Name` = `{!Opp_Name}`
   - `Opportunity_Loop.AccountId` = `{!Opp_Account.recordId}`
   - `Opportunity_Loop.CloseDate` = `{!Opp_Close_Date}`
   - `Opportunity_Loop.Description` = `{!Opp_Description}`
4. **Assignment `Capture_Record_in_the_Collection_for_Update`** — `oppsToUpdate Add Opportunity_Loop`, then connect back to the loop
5. **Update Records `Update_Opportunities`** (after the loop) — `inputReference = oppsToUpdate` (single bulk DML for all edits)

### Why bulk update (the optimization)

Putting Update Records **inside the loop** would issue one DML per opportunity — burns SOQL/DML governor limits and is the standard anti-pattern. Building `oppsToUpdate` inside the loop and updating **once after the loop ends** is the textbook fix and what the challenge checker looks for.

### Acceptance checks

- The flow is **Active** and named exactly `Opportunity Updater` / `Opportunity_Updater`.
- It accepts an **input** SObject collection for the selected opportunities (`Selected_Opportunities`, Object: `Opportunity`).
- A **Loop** iterates that input collection ascending.
- The in-loop screen pre-fills Name, Close Date, Account, and Description from the current loop record.
- An assignment inside the loop copies edited values back onto the loop record.
- A second assignment inside the loop **adds** the loop record to `oppsToUpdate`.
- A single **Update Records** element runs **after** the loop, bound to `oppsToUpdate`.
