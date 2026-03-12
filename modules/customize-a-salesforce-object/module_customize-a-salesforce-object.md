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


### Unit 4: Create Formula Fields
- Trailhead: [Create Formula Fields](https://trailhead.salesforce.com/content/learn/projects/customize-a-salesforce-object/create-formula-fields)

#### Create Discount Percentage Field
- [x] New **Percent** field on Opportunity
- [x] Field Label: **Discount Percentage** / API Name: `Discount_Percentage__c`
- [x] Precision: 3 / Scale: 0
- [x] Field-level security: editable for Admin; read-only for Sales User, Support User

#### Create Amount After Discount Formula Field
- [x] New **Formula** field on Opportunity (return type: **Currency**)
- [x] Field Label: **Amount After Discount** / API Name: `Amount_After_Discount__c`
- [x] Description: *Calculates the opportunity amount after any discount has been applied.*
- [x] Help text: *Opportunity amount after discount has been applied.*
- [x] Formula: `Amount * ( 1 - Discount_Percentage__c )`
- [x] Treat blanks as: **Zeros**
- [x] Precision: 18 / Scale: 2

#### Create Commission Formula Field
- [x] New **Formula** field on Opportunity (return type: **Currency**)
- [x] Field Label: **Commission** / API Name: `Commission__c`
- [x] Description: *Calculates sales rep commission of 10 percent when opportunity is won.*
- [x] Help text: *Sales rep commission when opportunity is won.*
- [x] Formula: `IF( ISPICKVAL( StageName, "Closed Won" ), Amount * 0.1, 0)`
- [x] Treat blanks as: **Zeros**
- [x] Precision: 18 / Scale: 2

#### Create Region/Zone Formula Field
- [x] New **Formula** field on Opportunity (return type: **Text**)
- [x] Field Label: **Region/Zone** / API Name: `Region_Zone__c`
- [x] Description: *Displays the Region and Zone values from the account record.*
- [x] Help text: *Account region and zone.*
- [x] Formula: `TEXT( Account.Region__c ) & "/" & TEXT( Account.Zone__c )`


### Unit 5: Create Record Types
- Trailhead: [Create Record Types](https://trailhead.salesforce.com/content/learn/projects/customize-a-salesforce-object/create-record-types)

#### Create Customer Account Record Type
- [x] Object Manager > Account > Record Types > New
- [x] Record Type Label: **Customer Account** / API Name: `Customer_Account`
- [x] Description: *For customers and prospects*
- [x] Active: true; available to all profiles

#### Create Partner Account Record Type
- [x] Object Manager > Account > Record Types > New
- [x] Record Type Label: **Partner Account** / API Name: `Partner_Account`
- [x] Description: *For consulting partners*
- [x] Active: true; available to all profiles

#### Assign Page Layouts to Record Types
- [x] All profiles: **Account Layout** assigned to default, Customer Account, and Partner Account record types
