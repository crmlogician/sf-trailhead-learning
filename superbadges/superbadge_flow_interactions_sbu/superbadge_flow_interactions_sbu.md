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

## Challenge 2 - Case 5623: Flow Interactions with Assignment Rules and Custom Metadata

- Create a record-triggered flow to set the lead's Region based on Country using custom metadata, enabling automatic lead assignment via an existing assignment rule.

**Problem:** Leads need to be automatically assigned to the correct regional queue based on their country. A custom metadata type (`Region__mdt`) maps countries to regions (EMEA/APAC), and an existing Lead Assignment Rule (`Region Assignment`) assigns leads based on the Region field.

**Flow:** Set Lead Region (new record-triggered flow)

**Solution:** Created a before-save record-triggered flow on the Lead object:

**Start Element Configuration:**
- **Object:** Lead
- **Trigger:** When a record is created or updated
- **Trigger Type:** Before Save (Fast Field Updates)
- **Entry Conditions (Custom Condition Logic):**
  - Condition 1: `Country` Is Not Equal To `` (not empty)
  - Condition 2: `Id` Is Null `True` (new record)
  - Condition 3: `Country` Is Changed `True`
  - **Custom Condition Logic:** `1 AND (2 OR 3)`

  This logic ensures the flow fires when:
  - The lead has a country value **AND**
  - Either the lead is newly created (Id is null), **OR** the country value has changed

**Flow Elements:**

1. **Get Region Metadata** (`<Get_Region_Metadata>`) - Retrieves the first record from the `Region__mdt` custom metadata type, which contains `EMEA__c` and `APAC__c` fields listing associated country codes:
   - EMEA: ZM, GB, AE, ES
   - APAC: AU, NZ, JP, KR

2. **Validate Lead Country** (`<Validate_Lead_Country>`) - Decision element with three outcomes:
   - **is EMEA Region:** Checks if `Get_Region_Metadata.EMEA__c` is not empty AND contains the lead's Country value → routes to EMEA assignment
   - **is APAC Region:** Checks if `Get_Region_Metadata.APAC__c` is not empty AND contains the lead's Country value → routes to APAC update
   - **Default Outcome:** No action — the lead's Region is not updated if the country doesn't match either region

3. **Set Lead Region as EMEA** (`<Set_Lead_Region_as_EMEA>`) - Assignment element that sets `{!$Record.Region__c}` to `EMEA`

4. **Update Lead Region as APAC** (`<Update_Lead_Region_as_APAC>`) - Update Records element that sets `Region__c` to `APAC` on the triggering record

**Flow Path:**
`Start` → `Get Region Metadata` → `Validate Lead Country` →
  - (is EMEA) → `Set Lead Region as EMEA` (done)
  - (is APAC) → `Update Lead Region as APAC` (done)
  - (default) → no action (done)

**Integration with Assignment Rule:** The existing `Region Assignment` lead assignment rule references the `Region__c` field. Since this is a before-save flow, the Region value is set before the assignment rule evaluates, ensuring new leads are automatically routed to the correct regional queue.

**Status:** Flow activated.

## Challenge 3 - Case 5641: Flow Interactions with Other Flows

- Adjust two existing lead rating flows so each runs only for its designated region, preventing cross-firing between automations.

**Problem:** The Sales EMEA and Sales APAC teams each have separate record-triggered flows to set lead ratings based on annual revenue, but with different thresholds. Both flows fire on any lead when `AnnualRevenue` changes — neither filters by region. This causes both flows to evaluate and overwrite each other's rating, resulting in leads getting the wrong rating. The flows cannot be consolidated into one.

**Flows Modified:**
1. Sales EMEA Lead Rating (existing record-triggered flow)
2. Sales APAC Lead Rating (existing record-triggered flow)

**Root Cause:** Neither flow had a `Region__c` entry condition in its start element. Both flows triggered on **any** lead where `AnnualRevenue` changed, regardless of the lead's region. Since the flows have different revenue thresholds (EMEA: Hot ≥ $8M, Warm ≥ $4M vs. APAC: Hot ≥ $10M, Warm ≥ $6M), whichever flow ran last would overwrite the correct rating with its own thresholds.
