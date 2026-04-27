# Superbadge: Flow Data Collections Optimization

Demonstrate your skills designing optimized flows that work with collections of data.

Trailhead: [Superbadge: Flow Data Collections Optimization](https://trailhead.salesforce.com/content/learn/superbadges/superbadge_collections_optimization_sbu)

## CH 1 - Optimize a Loop and Update Operation

- Work with multiple collections for use in a loop and a subflow.

**Problem:** Main Stage Analytics is consolidating sales practices across business units, and older flows need to be brought in line with current best practices. The **Suspended Account - Opportunity Updater** flow processes opportunities when their account is suspended, but its current design performs DML inside a loop, which risks hitting governor limits. It also contains inline logic for **Negotiation/Review** opportunities (reassigning ownership to a hardcoded user) that should be delegated to a dedicated subflow. Refactor the flow to bulkify the update operation, remove the hardcoded user ID, and call the **Negotiation/Review - Opps** subflow for opportunities in that stage while keeping the existing behavior of setting all other opportunities to **Needs Analysis**. Activate the new version before verification.

**Flow:** Suspended Account - Opportunity Updater

**Solution:**

- Added two SObject collection variables on Opportunity: `oppOwnerChanges` (Negotiation/Review opps to hand off to the subflow) and `updateOpportunityStage` (opps whose stage moves to Needs Analysis).
- Inside the existing loop, replaced the in-loop record updates with two Assignment elements that **Add** the current loop variable to the appropriate collection:
  - `Capture_Owner_Change_Opportunity` on the Negotiation/Review branch.
  - `Capture_Stage_Change_Opportunity` on the default branch (after `Update_Stage` sets `StageName = Needs Analysis`).
- Removed the hardcoded user ID assignment (`Update_Owner`) and the per-iteration record updates (`Update_Current_Opp`, `Update_Opportunity_Record`).
- Wired the loop's **No More Values** path to a single bulk `Update_Opportunities_Collection` record update using `updateOpportunityStage` as the input reference.
- Followed it with a `Negotiation_Review_Opps` subflow call, passing `oppOwnerChanges` into the subflow's `Review_Opps` input.
- Activated the new version of the flow.

## CH 2 - Eliminate an Unnecessary Loop from an Existing Flow

- Replace Loop, Decision, and Assignment elements for simple and efficient processing.

**Problem:** The **30 Day Case Review** screen flow lets users browse cases created in the last 30 days and filter them by **Status** through a picklist on the screen. Its current design uses a Loop to iterate over the queried cases, with a Decision to test each record's Status and an Assignment to add matches to a collection — work that can be done in-place by a Filter collection element. Replace the Loop/Decision/Assignment trio with a single Filter element named **Filter for Status** (`Filter_for_Status`) that filters the case collection by the user-selected status, keep the screen's existing user interaction (status picklist + cases datatable), and ensure the final element on the canvas is an Assignment that updates the current filter selection. Activate the new version before verification.

**Flow:** 30 Day Case Review

**Solution:**

- Replaced the Loop + Decision + Assignment combo with a single **Filter Collection** element `Filter_for_Status` that filters `Get_Cases` where `Status` equals the `Current_Filter` variable (using `currentItem_Filter_for_Status` as the iterator reference).
- Wired `Get_Cases` directly to `Filter_for_Status`, and `Filter_for_Status` on to the existing `Case_View` screen.
- Repointed the datatable's `tableData` input on `Case_View` from `Filtered_Cases` to the `Filter_for_Status` output so the screen renders the filtered cases for the chosen status.
- Kept `Case_Status_ChoiceSet` (Case Status picklist) on the screen for user selection, and made the trailing `Update_Filter` Assignment (which sets `Current_Filter = Case_Current_Status`) the final element on the canvas — removing its outgoing GoTo back into the loop.
- Removed the now-unused `Apply_Filter_Choice` (Loop), `Apply_Filter` (Decision), `Add_current_element` (Assignment), and `Filtered_Cases` (SObject collection) elements.

## CH 3 - Make Decisions Based on Collection Count

- Configure a new flow that evaluates a collection based on the number of records in it.

**Problem:** Account managers need different downstream behavior depending on how many open opportunities exist on an account when its **Industry** changes. Build a record-triggered flow on **Account** that runs after save when `Industry` is changed. Query all related opportunities that are not **Closed Won** or **Closed Lost**, then branch on the size of that collection: if **more than 3** open opportunities exist, send an email to the account owner; otherwise, loop the records and set each opportunity's **Stage** to **Needs Analysis** and commit the updates in a single bulk operation. Activate the flow before verification.
