# Superbadge: Flow Data Collections Optimization

Demonstrate your skills designing optimized flows that work with collections of data.

Trailhead: [Superbadge: Flow Data Collections Optimization](https://trailhead.salesforce.com/content/learn/superbadges/superbadge_collections_optimization_sbu)

## Challenge 1 - Optimize a Loop and Update Operation

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
