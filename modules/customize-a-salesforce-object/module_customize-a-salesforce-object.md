# Customize a Salesforce Object

Use picklists, filters, formulas, and other tools to customize an object in your org.

## Units


### Unit 1: Work with Standard and Custom Fields
- Trailhead: [Work with Standard and Custom Fields](https://trailhead.salesforce.com/content/learn/projects/customize-a-salesforce-object/standard-custom-fields)

#### Create Profiles
- [ ] Clone Standard User profile → name it **Sales User**
- [ ] Clone Standard User profile → name it **Support User**

#### Rename Rating Field
- [ ] Setup > Rename Tabs and Labels > Accounts
- [ ] Change **Rating** field singular label to **Prospect Rating**

#### Modify Prospect Rating Field
- [ ] Object Manager > Account > Fields & Relationships > Prospect Rating
- [ ] Add help text describing cold/warm/hot likelihood scale
- [ ] Add new picklist value: **Not Known** — Manual steps:
  1. Object Manager > Account > Fields & Relationships > **Prospect Rating**
  2. In the "Account Rating Picklist Values" section, click **New**
  3. Enter `Not Known` in the Add Picklist Values box
  4. Click **Save**
- [ ] Set field-level security to **read-only** for all profiles except Sales User

#### Create Has Support Plan Field
- [ ] New **Checkbox** field on Account
- [ ] Field Label: **Has Support Plan** / API Name: `Has_Support_Plan__c`
- [ ] Default value: unchecked
- [ ] Field-level security: **read-only** for all except Sales User and Support User

#### Create Support Plan Expiration Date Field
- [ ] New **Date** field on Account
- [ ] Field Label: **Support Plan Expiration Date** / API Name: `Support_Plan_Expiration_Date__c`
- [ ] Field-level security: **read-only** for all except Sales User and Support User

> **Note:** Including the Admin profile in the deployed metadata just to have FLS for the new fields — needed to pass the challenge.
