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


### Unit 2: Create Picklists and Field Dependencies
- Trailhead: [Create Picklists and Field Dependencies](https://trailhead.salesforce.com/content/learn/projects/customize-a-salesforce-object/picklists-field-dependencies)

#### Create Region Global Value Set
- [x] Setup > Picklist Value Sets > New
- [x] Label: **Region** / API Name: `Region`
- [x] Description: *For use in region fields throughout AW's org.*
- [x] Values: **APAC**, **EMEA**, **LATAM**, **US**, **Canada**

#### Create Region Field on Account
- [x] New **Picklist** field on Account using the **Region** global value set
- [x] Field Label: **Region** / API Name: `Region__c`
- [x] Description: *Customer's geographical region—for sales operations use only.*
- [x] Help text: *In which region is the customer based?*

#### Create Zone Field on Account (Dependent Picklist)
- [x] New **Picklist** field on Account
- [x] Field Label: **Zone** / API Name: `Zone__c`
- [x] Description: *Customer's zone within the selected region—for sales operations use only.*
- [x] Help text: *In which zone is this customer based? Depends on region.*
- [x] Set up field dependency: controlling field = **Region**, dependent field = **Zone**
  - APAC → East Asia, Oceania, Southeast Asia
  - EMEA → Africa, Europe, Middle East, UK + Ireland
  - LATAM → Mexico, Caribbean, Central America, South America
  - US → Midwest US, Northeast US, Southeast US, Southwest US, West US
  - Canada → Northern Canada, Mountains and the West, The Prairies, Central Canada, East Coast

#### Create Region Field on Lead
- [x] New **Picklist** field on Lead using the **Region** global value set
- [x] Field Label: **Region** / API Name: `Region__c`
- [x] Description: *Customer's geographical region—for sales operations use only.*
- [x] Help text: *In which region is the customer based?*

#### Create Close Reason Field on Opportunity (Dependent Multi-Select Picklist)
- [x] New **Multi-Select Picklist** field on Opportunity
- [x] Field Label: **Close Reason** / API Name: `Close_Reason__c`
- [x] Description: *Created for the VP of Global Sales to track wins and losses.*
- [x] Help text: *When you close the opportunity, select one or more values that best describe your reason for closing.*
- [x] Visible lines: **6**
- [x] Set up field dependency: controlling field = **Stage**, dependent field = **Close Reason**
  - Closed Lost → Lost: Competitor, Lost: Price, Lost: Product Features, Lost: Project Abandoned, Lost: Company Budget Constraints, Lost: Other Reason
  - Closed Won → Won: Competitor, Won: Price, Won: Product Features, Won: Other Reason


### Unit 3: Create Lookup Filters
- Trailhead: [Create Lookup Filters](https://trailhead.salesforce.com/content/learn/projects/customize-a-salesforce-object/create-lookup-filters)

#### Create Backup Agent Field on Case
- [x] New **Lookup** field on Case → related to **User**
- [x] Field Label: **Backup Agent** / API Name: `Backup_Agent__c`
- [x] Description: *Used to identify the assigned support rep when case owner is away — for support use only.*
- [x] Help text: *Who is the assigned support rep when case owner is away?*
- [x] Add lookup filter: **User Profile Name** equals **Support User**
- [x] Set filter as **Required**
- [x] Field-level security: read-only for Admin, Sales User, Support User

#### Add Lookup Filter to Case Contact Field
- [x] Object Manager > Case > Fields & Relationships > Contact Name
- [x] Add lookup filter: **Contact Account** equals **Case Account** (`Contact.AccountId` equals `$Source.AccountId`)
- [x] Set filter as **Required**
