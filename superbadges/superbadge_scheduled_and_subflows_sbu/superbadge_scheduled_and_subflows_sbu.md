# Superbadge: Scheduled Flow and Subflow

Show your strength with subflows, autolaunched flows, and scheduled flows.

Trailhead: [Superbadge: Scheduled Flow and Subflow](https://trailhead.salesforce.com/content/learn/superbadges/superbadge_scheduled_and_subflows_sbu)

## CH 1 - Configure Automation for Daily Appointment Checks

- Convert a record-triggered draft into a scheduled flow that creates follow-up tasks for past-due appointments.

**Problem:** The Hive Foundation tracks appointments on a custom **Provider Appointment** object. A draft flow named **Appointment Follow Up** is intended to surface missed appointments to care coordinators, but it is currently configured to run on record changes — the wrong trigger for this use case. Rebuild it as a scheduled flow that runs every day at midnight, selects appointments that are past due using the same criteria as the draft, and assigns a follow-up task to the appointment's care coordinator. Save the new flow as **Daily Appointment Check** and activate it before verification.

**Flow:** Daily Appointment Check

**Solution:**

- Saved the existing **Appointment Follow Up** draft as a new autolaunched flow named **Daily Appointment Check** and switched the start element to a **Scheduled** trigger running **Daily** at `00:00:00.000Z`, with **Provider Appointment** (`Provider_Appointment__c`) as the scheduled object.
- Reused the draft's filter criteria on the start element so the schedule only iterates over appointments that are still open: `Stage__c != "Canceled"` AND `Stage__c != "Completed"`.
- Keep everything as it is.

## CH 2 - Streamline Program Participation Management

- Extract reusable participant-status logic into a subflow and have the existing weekly flow call it.

**Problem:** The foundation runs periodic programs where participants enroll, and program managers rely on the **Number of Active Participants** field on the **Program** object to stay accurate. The existing **Weekly Program Participant Check** flow already performs this maintenance on a schedule, but the participant-status logic needs to be reused elsewhere. Duplicate the applicable logic into a new subflow named **Program Count and Participant Status - Subflow**, exposing a `Current_Participant` input variable so callers can pass the participant record in. Then refactor **Weekly Program Participant Check** to delegate to the subflow — passing the current participant record as the input — and activate the updated parent flow.

**Flows:**
- Subflow: Program Count and Participant Status - Subflow
- Parent flow: Weekly Program Participant Check

**Solution:**

- Cloned the existing **Weekly Program Participant Check** flow using **Save As → A New Flow** and saved the copy as **Program Count and Participant Status - Subflow**, then changed the flow type from **Scheduled** to **AutoLaunchedFlow** so it can be invoked as a subflow.
- Edited the existing `Current_Participant` resource and enabled **Available for input** so callers can pass the participant record in (object type stays `Program_Participant__c`, single value).
- Walked through each pre-existing element (`Get_related_Program`, `Check_if_Program_needs_to_be_updated`, `Mark_*`, `Increment_*` / `Decrement_*`, `Update_Program_Participant`, `Update_Program_Values`) and re-saved them so the builder recompiles the references that switched from `$Record` to `Current_Participant`, clearing the validation errors that surfaced after the trigger change.
- Removed elements that no longer belong in the subflow — the `Set_current_participant` Assignment that previously copied `$Record` into `Current_Participant` — since the variable is now populated from the input.
- Activated the subflow.
- Back in **Weekly Program Participant Check**, deleted the inline assignments, decision, lookups, and updates that were duplicated into the subflow, then dropped a **Subflow** element `Program_Count_and_Participant_Status_Subflow` on the canvas, mapped its `Current_Participant` input to `{!$Record}`, and reconnected the scheduled start to it.
- Reactivated the trimmed-down **Weekly Program Participant Check**.

## CH 3 - Trigger the Subflow When a Participant's End Date Changes

- Build a record-triggered flow that reuses the new subflow to keep participant data current.

**Problem:** **Program Participant** records carry start and end dates, and the program count needs to react when a participant's end date moves. Create a record-triggered flow named **Program Participant Updated** on **Program Participant** that fires when **End Date** (`End_Date__c`) is changed. The flow should call the **Program Count and Participant Status - Subflow** subflow, passing in the relevant data for the current participant record. Activate the flow before verification.

**Flow:** Program Participant Updated

**Solution:**

- Created the **Program Participant Updated** record-triggered flow on **Program Participant** (`Program_Participant__c`) with `triggerType = RecordAfterSave` and `recordTriggerType = Update`.
- Set the start element's entry condition to fire only when **End Date** changes: `End_Date__c IsChanged = true`.
- Wired the start element directly to a **Subflow** call `Program_Count_and_Participant_Status_Subflow`, mapping `Current_Participant` to `{!$Record}` so the triggering Program Participant record is handed straight to the shared subflow.
- Activated the flow.
