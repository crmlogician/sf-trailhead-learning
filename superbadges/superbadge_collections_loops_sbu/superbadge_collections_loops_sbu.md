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
