# Superbadge - Flow Error Handling

Show your capabilities building error management into flow automations.

Trailhead: [https://trailhead.salesforce.com/content/learn/superbadges/superbadge_flow_error_handling_sbu](https://trailhead.salesforce.com/content/learn/superbadges/superbadge_flow_error_handling_sbu)

## Challenge 1 - Case Number 4014: Enhance a Flow to Support Valid Data Inputs

- Improve data quality and reduce errors with updates to an existing flow. 

**Problem:** A sales team manager reported that opportunities are being created with close dates in the past, interfering with reporting and forecasting.

**Flow:** Opportunities for Main Stage Analytics

**Solution:** Added a validation rule directly on the `ops_Close_Date` screen input field (no new flow elements or validation rules created):

- **Formula:** `Today() < {!ops_Close_Date}`
- **Error Message:** "Close Date must be a future date."

This ensures the close date must be in the future. If a user enters a past date, the flow displays the error message inline on the screen and prevents progression until a valid future date is provided.

**Key Constraint:** Completed without creating validation rules or adding elements to the flow - the validation is handled entirely within the existing screen element's field-level validation.

**Status:** Flow activated.

## Challenge 2 - Case Number 4021: Manage Fault Handling and Notifications

- Add fault handling when flow errors occur.

**Problem:** Users reported that the Consent Capture for Main Stage Analytics flow sometimes doesn't run correctly, with no error feedback.

**Flow:** Consent Capture for Main Stage Analytics
