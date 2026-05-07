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
