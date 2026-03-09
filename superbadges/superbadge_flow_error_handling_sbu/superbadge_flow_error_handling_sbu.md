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

**Solution:** Added a fault path to the `Create Contact Point Type Consent` (Create Records) element:

1. **Fault Connector** - Added a fault path from the `Create Contact Point Type Consent` element to a subflow for error messaging.
2. **Subflow: Flow Pro Error Messaging** (`<Create_Contact_Point_Type_Consent_Error_Messaging>`) - On fault, calls the existing `Flow_Pro_Error_Messaging` flow with:
  - **UserEmail:** `{!$User.Email}` (running user's email address)
  - **FaultMessage:** `{!$Flow.FaultMessage}` (system fault message)  
   This alerts the Flow Pro (user with the Flow Pro profile) about the error.
3. **Error Screen** (`<Display_Create_Contact_Point_Type_Consent_Errors>`) - After the subflow, displays a screen to the user with a DisplayText component (API Name: `Error_Display`) informing them that an error occurred.

**Flow Path on Fault:**  
`Create Contact Point Type Consent` → (fault) → `Flow Pro Error Messaging subflow` → `Error Screen with Error_Display`

**Flow Path on Success:**  
`Create Contact Point Type Consent` → `Create Individual Success` screen

**Status:** Flow saved and activated.

## Challenge 3 - Case Number 4036: Roll Back and Track Errors with Flows

- Enhance the user experience and track flow errors by adding records of incidents to a custom Error Log object.

**Problem:** The New Account Contact flow creates an opportunity and then a contact. When the contact creation fails (e.g., duplicate primary contact), the opportunity is still created, causing confusion. No records should be created if the contact creation fails.

**Flow:** New Account Contact

**Current Flow Order (not reordered per instructions):**  
`New Account Contact (screen)` → `Create Opp` → `Create Contact`

**Solution:** Added fault handling on the `Create Contact` element:

1. **Fault Connector** on `Create Contact` - On fault, redirects to a Roll Back element.
2. **Roll Back Created Data** (`<Roll_back_created_data>`) - Uses the Rollback Records element to undo all prior DML operations in the transaction (rolls back the opportunity created by `Create Opp`), ensuring no partial records remain.
3. **Log Error as Create Incident** (`<Log_an_error_as_Create_Incident>`) - Creates an `Error_Log__c` record with:
  - **Name:** "New Account Contact - Create Contact Error"
  - **Error_Date__c:** `{!$Flow.CurrentDate}`
  - **Message__c:** `{!$Flow.FaultMessage}`
4. **Error Screen** (`<Display_Error_Details>`) - Displays a screen with a DisplayText component (API Name: `User_Error_Display`) showing the fault message, then uses a GoTo connector to redirect the user back to the `New Account Contact` input screen.

**Flow Path on Fault:**  
`Create Contact` → (fault) → `Roll back created data` → `Log an error as Create Incident` → `Display Error Details (User_Error_Display)` → (GoTo) → `New Account Contact` screen

**Flow Path on Success:**  
`New Account Contact` → `Create Opp` → `Create Contact` (done)

**Key Constraint:** Existing elements were not reordered. The rollback element ensures the opportunity is undone if the contact fails.

**Status:** Flow saved and activated.