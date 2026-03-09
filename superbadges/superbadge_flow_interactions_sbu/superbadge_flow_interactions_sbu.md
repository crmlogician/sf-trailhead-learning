# Superbadge - Flow Interactions

Design and enhance flows with relationships to other automations and existing flows.

Trailhead: [Superbadge - Flow Interactions](https://trailhead.salesforce.com/content/learn/superbadges/superbadge_flow_interactions_sbu)

## Challenge 1 - Case 5612: Flow Interactions with Approval Processes

- Automate high-discount opportunity review by submitting records to an existing approval process from a record-triggered flow.

**Problem:** Sales leadership wants to review every deal with a discount over 15%. Some reps manually submit via the Submit for Approval button, but not all do. Automation is needed to catch high-discount opportunities that aren't manually submitted.

**Flow:** Discount Approval Request (new record-triggered flow)

**Solution:** Created a record-triggered flow on the Opportunity object:

**Start Element Configuration:**
- **Object:** Opportunity
- **Trigger:** When a record is created or updated
- **Entry Conditions (Custom Condition Logic):**
  - Condition 1: `StageName` Equals `Negotiation/Review`
  - Condition 2: `Discount__c` Greater Than `15`
  - Condition 3: `Approved__c` Equals `False`
  - Condition 4: `Discount__c` Is Changed `True`
  - **Custom Condition Logic:** `1 AND 2 AND (3 OR 4)`

  This logic ensures the flow fires when:
  - Stage is Negotiation/Review **AND** discount is over 15%
  - **AND** either the opportunity hasn't been approved yet, **OR** the discount has changed (requiring re-approval)

  Note: The `NOT` operator was not used in the custom condition logic per the challenge constraint.

**Flow Action:**
- **Submit for Approval** action element targeting the existing `Discount_Approval` approval process
- Submits the current record (`{!$Record.Id}`) for approval

**Status:** Flow activated.
