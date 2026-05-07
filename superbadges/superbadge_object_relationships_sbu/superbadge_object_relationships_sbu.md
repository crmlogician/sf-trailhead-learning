# Superbadge - Object Relationships

Configure standard objects, design custom object relationships, and build a Lightning App so the band can track tours, venues, and related information.

Trailhead: [Superbadge - Object Relationships](https://trailhead.salesforce.com/content/learn/superbadges/superbadge-object-relationships-sbu)

## CH 1 - Configure Standard Objects

- Sign up for the Developer Edition org with special configuration.
- Configure objects and features for the band to track tours.

**Problem:** Art Tenor needs visibility into venue security planning and a way to relate venues to tours. Certain Account fields should only appear for concert venues, and the Campaign object needs to be reshaped to represent tours and shows with the right picklist values, relationships, and page layout.

**Requirements:**

- **Account object — conditional field visibility:**
  - `Capacity` and `Security Provided` are visible only when `Account Type = Concert Venue`.
  - `Security Percent` is visible only when `Security Provided` is checked.
- **Venue ↔ Tour relationship:** Create a lookup from Campaign (Tours) to Account (Venues).
- **Campaign Type picklist values:** `Tour`, `Show`, `Digital Show`, `Interview`, `Venue`, `Other`.
- **Campaign (Tour) page layout fields:** Campaign Owner, Parent Campaign, Campaign Name, Active, Account, Type, Status, Start Date, End Date, Contacts in Campaign, Responses in Campaign.
- **Tour records to create:**

  | Campaign Name | Type | Parent Campaign | Status |
  |---|---|---|---|
  | Errors Tour | Tour | — | Planned |
  | Camp Success | Interview | Errors Tour | Planned |
  | Demo Jam | Digital Show | Camp Success | Planned |
  | The Trailblazer Tour | Tour | — | Planned |

**Notes:**

- Updated `Account_Record_Page.flexipage-meta.xml` and activated it as the org default Account record page.
- Updated Campaign `Type` picklist with the new values (`Tour`, `Show`, `Digital Show`, `Interview`, `Venue`, `Other`).
- Added `Account__c` lookup field on Campaign (Tour → Venue) and surfaced it on the Campaign layout.
- Created the four Tour/Show campaign records per the challenge guidelines.
