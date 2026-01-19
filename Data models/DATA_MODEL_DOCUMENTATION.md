# Data Model Documentation

## Request

A request is a shell that collects all information from the request process.

### Properties

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| SEMNumber | String (Primary Key) | Unique identifier for the request. | REQ-2025-001 |
| Stream | Enum | The business stream associated with the request. Values: DISTRIBUTION, MANUFACTURING, CSLOG_PLATFORM, GROUP_PLATFORM, OFFICE, MA, OTHER | DISTRIBUTION |
| UniqueCode | String | A unique code identifier for the request (non-null). | RC-001 |
| CurrencyId | String (Foreign Key) | Reference to the currency used in the request. | EUR |
| CreatedOn | DateTime | Timestamp when the request was created (non-null). | 2025-01-13T10:30:00Z |
| CreatedBy | String (Foreign Key) | User ID of the person who created the request (non-null). | user-12345 |
| UpdatedOn | DateTime | Timestamp when the request was last updated. | 2025-01-13T14:45:00Z |
| UpdatedBy | String (Foreign Key) | User ID of the person who last updated the request. | user-67890 |

### Associations

**Request → RequestVersion (1:N)**
- Description: Each request has at least one request version.
- Assumption: Each request will have a maximum of 3 versions.
- Purpose: Versions allow for tracking changes and revisions to the request throughout the approval process.

**Request → RequestRole (1:N)**
- Description: Each request can have multiple roles assigned.
- Purpose: Tracks the users responsible for different roles (Requester, Reviewer, Approver) at different scopes.

**Request → Activity (1:N)**
- Description: Each request can have multiple activity records.
- Purpose: Maintains an audit trail of all actions taken on the request.

---

## RequestVersion

A version of a request that captures the state of the request at a specific point in time.

### Properties

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| Id | String (Primary Key) | Unique identifier for the request version. | VERSION-001 |
| SEMNumber | String (Foreign Key) | Reference to the parent request. | REQ-2025-001 |
| Scope | Enum | The scope at which this version applies. Values: MARKETS, REGIONS, MAISONS, GROUP | REGIONS |
| CreatedOn | DateTime | Timestamp when this version was created. | 2025-01-13T10:30:00Z |
| CreatedBy | String (Foreign Key) | User ID of the person who created this version. | user-12345 |
| UpdatedOn | DateTime | Timestamp when this version was last updated. | 2025-01-13T14:45:00Z |
| UpdatedBy | String (Foreign Key) | User ID of the person who last updated this version. | user-67890 |

### Associations

**RequestVersion → RequestInformation (1:1)**
- Description: Each request version has detailed information about the specific request.
- Purpose: Stores specific details like location, boutique type, financial commitment details.

**RequestVersion → FinancialCommitmentComposition (1:1)**
- Description: Each request version has a financial commitment composition.
- Purpose: Tracks the breakdown of financial commitments for this version.

**RequestVersion → Attachments (1:N)**
- Description: Each request version can have multiple attachments.
- Purpose: Stores supporting documents and files for the request.

---

## RequestInformation

Detailed information about a specific request version, including location, type, and project details.

### Properties

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| Id | String (Primary Key) | Unique identifier for the request information record. | INFO-001 |
| RequestVersionId | String (Foreign Key) | Reference to the request version (one-to-one relationship). | VERSION-001 |
| FxRate | Decimal | Foreign exchange rate applied to the request (non-null). | 1.15 |
| Maison | Maison (Foreign Key) | The maison (brand) associated with this request (non-null). | CAR (Cartier) |
| Stream | String | The business stream for this request. | Manufacturing |
| RequestType | String | Type of request being made. | Relocation without lease renewal |
| RequestName | String | Name/identifier for the request. | Hyundai main |
| StreetName | String | Street address of the location. | 123 Main St |
| BoutiqueType | String | Type of boutique (e.g., Flagship, Concession). | Flagship |
| BoutiqueEnvironment | String | Environment type for the boutique. | Street Boutique |
| Location | String | City or location name. | Seoul |
| Country | String | Country where the request applies. | France |
| ReportingEntity | String | The reporting entity code and name. | 59 - Richemont Korea |
| CompletionDate | DateTime | Expected completion date (format: dd.mm.yyyy). | 31.12.2025 |
| BudgetProject | Enum | Whether this request is included in the budget (non-null). Values: YES, NO, PARTIALLY | PARTIALLY |
| BudgetProjectDetails | Text (Array) | Additional details about budget project status. | ["Partial approval for Q1"] |
| TransferToAnotherMaison | Boolean | Indicates if the request involves transfer to another maison (screen 4 only). | true |
| TransferToAnotherMaisonDetails | Text (Array) | Details about the transfer (screen 4 only). | ["Transfer from Cartier to Van Cleef & Arpels"] |
| PartnersName | String (up to 10000 chars) | Name of real estate partners involved (screen 5 only). | ABC Real Estate |
| ContractDuration | String (up to 10000 chars) | Duration of the contract (screen 5 only). | 10 years |
| ReviewByLegal | Boolean | Whether legal review is required (screen 8 only). | true |
| ReviewByProcurement | Boolean | Whether procurement review is required (screen 8 only). | false |

### Associations

**RequestInformation ← RequestVersion (1:1)**
- Description: Belongs to a single request version.
- Purpose: Provides detailed context for the request version.

---

## FinancialCommitmentComposition

Breakdown of financial commitments for a request version, including capex, opex, and other costs.

### Properties

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| Id | String (Primary Key) | Unique identifier for the financial composition record. | FIN-001 |
| RequestVersionId | String (Foreign Key) | Reference to the request version (one-to-one relationship). | VERSION-001 |
| TotalCapex | Decimal | Total capital expenditure (screens 1, 2, 5, 6, 7, 8, 9). | 2536.99 |
| LandLordContribution | Decimal | Contribution from the landlord (screens 1, 2, 5). | 1200.00 |
| MaisonCapex | Decimal | Capital expenditure from the maison (screens 1, 2, 5). | 1336.99 |
| KeyMoney | Decimal | Key money for lease (screens 1, 2, 3). | 500.00 |
| LeaseDeposit | Decimal | Security deposit for lease (screens 1, 2, 3, 6, 9). | 300.00 |
| InternationalisationCost | Decimal | Internationalization-related costs (screens 1, 2). | 100.00 |
| Inventories | Decimal | Inventory costs (screens 1, 2, 5). | 200.00 |
| FixedLeaseCommitment | Decimal | Fixed lease commitment amount (screens 1, 2, 3, 6, 9). | 5000.00 |
| TemporaryBoutique | Decimal | Costs for temporary boutique (screens 1, 2). | 150.00 |
| OtherCommitments | Decimal | Other financial commitments (screens 1, 2, 5, 7, 8, 9). | 250.00 |
| TotalInvestedCapital | Decimal | Total invested capital (screens 1, 2, 3, 5). | 3286.99 |
| OneOffCosts | Decimal | One-time costs (screens 6, 7). | 400.00 |
| OpexCommitments | Decimal | Operational expenditure commitments (screens 6, 8, 9). | 1000.00 |
| TotalFinancialCommitment | Decimal | Total financial commitment (screens 6, 7, 9). | 4686.99 |

### Associations

**FinancialCommitmentComposition ← RequestVersion (1:1)**
- Description: Belongs to a single request version.
- Purpose: Provides detailed financial breakdown for the request.

---

## RequestRole

Tracks roles and responsibilities assigned to users for a specific request.

### Properties

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| Id | String (Primary Key) | Unique identifier for the request role record. | ROLE-001 |
| UserId | String (Foreign Key) | Reference to the user assigned this role (non-null). | user-12345 |
| RequestId | String (Foreign Key) | Reference to the request. | REQ-2025-001 |
| Scope | Enum | The scope at which this role applies. Values: MARKETS, REGIONS, MAISONS, GROUP | REGIONS |
| Responsibility | Enum | The responsibility assigned to the user. Values: REQUESTER, REVIEWER, APPROVER | APPROVER |
| Role | String (non-null) | The title or name of the role. | Chairman |
| IsPendingAction | Boolean | Indicates if this role has pending actions required. | true |
| CreatedOn | DateTime | Timestamp when this role assignment was created. | 2025-01-13T10:30:00Z |
| CreatedBy | String (Foreign Key) | User ID of the person who created this assignment. | user-admin-001 |
| UpdatedOn | DateTime | Timestamp when this role assignment was last updated. | 2025-01-13T14:45:00Z |
| UpdatedBy | String (Foreign Key) | User ID of the person who last updated this assignment. | user-admin-002 |

### Associations

**RequestRole → User (N:1)**
- Description: Multiple roles can be assigned to users across different requests.
- Purpose: Tracks user involvement in the request process.

**RequestRole → Request (N:1)**
- Description: Multiple roles can be assigned within a single request.
- Purpose: Manages the approval workflow and user responsibilities.

Audit trail and activity log for tracking all actions performed on a request.

### Properties

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| Id | String (Primary Key) | Unique identifier for the activity record. | ACT-001 |
| Type | String | Type of activity performed. | status_change, comment_added, file_uploaded |
| Status | Enum | The status related to this activity. Values: IN_PROGRESS, SUBMITTED, PENDING_REVIEW, FAVORABLE, FAVORABLE_WITH_CONDITIONS, CHANGES_REQUESTED, UNFAVORABLE, APPROVED, APPROVED_WITH_CONDITIONS, REJECTED, CANCELED, ROLE_CHANGED | APPROVED |
| HasConditions | Boolean | Indicates if there are conditions associated with this activity. | true |
| Conditions | Text | Description of any conditions or requirements. | Approval conditional on final budget confirmation |
| Comments | Text | Additional comments or notes about the activity. | Approved by regional director after budget review |
| RequestId | String (Foreign Key) | Reference to the request this activity is associated with. | REQ-2025-001 |
| CreatedDate | DateTime | Timestamp when this activity occurred. | 2025-01-13T14:45:00Z |
| CreatedBy | String (Foreign Key) | User ID of the person who performed this activity. | user-67890 |

### Associations

**Activity → Request (N:1)**
- Description: Multiple activities can be recorded for a single request.
- Purpose: Maintains a complete audit trail of all request-related actions.

---

## User

Extended user information with job title and maison assignment.

### Properties

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| Id | String (Primary Key) | Unique identifier for the user. | user-12345 |
| JobTitle | String | Job title of the user. | Regional Director |
| Picture | Blob | Profile picture of the user. | [binary data] |
| Maison | Maison (Foreign Key) | The maison (brand) the user is primarily associated with. | CAR (Cartier) |

### Associations

**User → RequestRole (1:N)**
- Description: A user can have multiple roles across different requests.
- Purpose: Enables flexible role assignment for the approval workflow.

**User ← Request (1:N)**
- Description: A user can be the creator or updater of multiple requests.
- Purpose: Tracks user responsibility for request creation and modification.

**User ← RequestVersion (1:N)**
- Description: A user can be involved in creating/updating multiple request versions.
- Purpose: Maintains audit trail of version changes.

**User ← Activity (1:N)**
- Description: A user can perform multiple activities.
- Purpose: Records user actions in the audit trail.

Supporting documents and files attached to a request version.

### Properties

| Property | Type | Description | Example |
|----------|------|-------------|---------|
| Id | String (Primary Key) | Unique identifier for the attachment record. | ATT-001 |
| FileName | String | Name of the attached file. | budget_proposal.pdf |
| Binary | Blob | Binary content of the file. | [binary data] |
| RequestVersionId | String (Foreign Key) | Reference to the request version this attachment belongs to. | VERSION-001 |
| CreatedOn | DateTime | Timestamp when the attachment was uploaded. | 2025-01-13T11:00:00Z |
| CreatedBy | String (Foreign Key) | User ID of the person who uploaded the attachment. | user-12345 |
| UpdatedOn | DateTime | Timestamp when the attachment was last updated. | 2025-01-13T15:00:00Z |
| UpdatedBy | String (Foreign Key) | User ID of the person who last updated the attachment. | user-67890 |

### Associations

**Attachments ← RequestVersion (1:N)**
- Description: Multiple attachments can be associated with a single request version.
- Purpose: Stores supporting documentation for the request.

---

## Master Data Tables

### Maison

Master data for maison (brand) information.

| Property | Type | Description |
|----------|------|-------------|
| Code | String | Brand code (e.g., CAR for Cartier) |
| Name | String | Brand name (e.g., Cartier) |
| Picture | Blob | Brand logo or picture |

### CarStream

Master data for CAR system streams.

| Property | Type | Description |
|----------|------|-------------|
| Id | String(2) | Stream identifier (e.g., 7) |
| Name | String(20) | Stream name (e.g., INTERNAL BOUTIQUE) |

### CarType

Master data for CAR request types.

| Property | Type | Description |
|----------|------|-------------|
| Code | String(3) | Type code (e.g., NEW) |
| Text | String(20) | Type description (e.g., Opening Perm.) |

### BoutiqueEnvironment

Master data for boutique environments.

| Property | Type | Description |
|----------|------|-------------|
| Code | String(4) | Environment code (e.g., RI02) |
| Text | String(20) | Environment description (e.g., Interior Boutique) |

### BoutiqueType

Master data for boutique types.

| Property | Type | Description |
|----------|------|-------------|
| Code | String(4) | Type code (e.g., RI02) |
| Text | String(20) | Type description (e.g., Interior Boutique) |

### Entity

Organizational entities with local currency information (updatable via Excel upload).

| Property | Type | Description |
|----------|------|-------------|
| Code | String(4) | Entity code (e.g., Z108) |
| LocalCurrency | String(5) | Local currency code (e.g., EUR) |
| Name | String(20) | Entity name (e.g., Richemont France) |

### RoleMatrix

Master data defining role-based access control and approval workflow.

| Property | Type | Description |
|----------|------|-------------|
| Stream | Enum | Business stream |
| Scope | Enum | Scope level (MARKETS, REGIONS, MAISONS, GROUP) |
| Responsibility | Enum | Role responsibility (REQUESTER, REVIEWER, APPROVER) |
| Order | Integer | Defines the order of approval in a given scope and responsibility |
| Role | String(200) | Role title or identifier (e.g., Chairman) |

---

## Enums Reference

**Stream:** DISTRIBUTION, MANUFACTURING, CSLOG_PLATFORM, GROUP_PLATFORM, OFFICE, MA, OTHER

**Scope:** MARKETS, REGIONS, MAISONS, GROUP

**Responsibility:** REQUESTER, REVIEWER, APPROVER

**BudgetProject:** YES, NO, PARTIALLY

**Status:** IN_PROGRESS, SUBMITTED, PENDING_REVIEW, FAVORABLE, FAVORABLE_WITH_CONDITIONS, CHANGES_REQUESTED, UNFAVORABLE, APPROVED, APPROVED_WITH_CONDITIONS, REJECTED, CANCELED, ROLE_CHANGED
