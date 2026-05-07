# Superbadge - User Access Troubleshooting

Troubleshoot user access issues based on a scenario.

Trailhead: [Superbadge - User Access Troubleshooting](https://trailhead.salesforce.com/content/learn/superbadges/superbadge_user_access_troubleshooting_sbu)

## Challenge 1 - High Priority

- Complete the challenges to close the high-priority cases in your My Open Cases list view.

### Case 00001036: Opportunity Deletion Permissions

**Problem:** Maria G. reports opportunities have been deleted. Company policy states sales teams should not delete opportunities.

**Requirements:**
- Remove deletion permissions from the appropriate profiles
- Check for and restore any deleted opportunities
- Document actions and close the case

**Notes:**
- Updated the "Custom: Support Profile" profile to remove Opportunity delete access.

### Case 00001039: Unauthorized Opportunity Access

**Problem:** Jacqui B. unexpectedly has access to the Opportunity object despite the CoE principle of least privilege and private default sharing settings.

**Requirements:**
- Investigate the source of the access
- Remove unauthorized opportunity access
- Document findings and close the case

**Notes:**
- Updated the specified opportunity and marked **Private** as `true` to remove the unauthorized access.

## Challenge 2 - Medium Priority

- Complete the challenges to close the medium-priority cases in your My Open Cases list view.

### Case 00001038: Opportunity Field History Access

**Problem:** Need to fulfill a CoE-approved request for sales team access to the Opportunity Field History related list.

**Requirements:**
- Admins and sales users are permitted to view the Opportunity Field History related list, but the component should be hidden for all others
- Grant access via the existing Sales permission set
- Document and close the case

**Notes:**
- Adjusted the Sales permission set with the custom permission assignment that gates visibility of the Opportunity Field History related list.

### Case 00001034: Inline Edit Restriction

**Problem:** Tara struggles to make inline edits to the Next Step field in opportunity list views, despite this capability being demonstrated previously.

**Requirements:**
- Ensure users with Opportunity object access can edit the Next Step field in list views
- Identify the blocking issue
- Document resolution and close the case

**Notes:**
- Updated the Opportunity page layout to adjust field accessibility on `Next Step` so it is no longer read-only, restoring inline edit in list views.
