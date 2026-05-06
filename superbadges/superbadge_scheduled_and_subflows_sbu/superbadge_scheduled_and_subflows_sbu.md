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

