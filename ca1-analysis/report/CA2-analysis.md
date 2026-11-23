# CA2: Collaborative Object-Oriented Design
## Riverside Community Partnership Equipment Lending Service

## 1. Scenario Selection and Agreed Baseline
### 1.1 Scenario Comparison and Selection

| Project | Scope | Behavioural richness | OO potential | Sliceability | Testability | Comparability | Total score |
|---|---:|---:|---:|---:|---:|---:|---:|
| Community Equipment Loan Service | 4 | 5 | 5 | 4 | 4 | 5 | 4.5 |
| Farm Machinery Cooperative Service | 4 | 3 | 4 | 5 | 3 | 4 | 3.8 |
| Kerry Community Sports Facility Service | 3 | 5 | 5 | 4 | 4 | 5 | 4.3 |
| Harbour Equipment and Berth Service | 4 | 5 | 4 | 4 | 4 | 4 | 4.2 |


Our team analysed each teammate's scenario and report and selected
Community Equipment Loan Service based on the comparison of scope,
behavioural richness, OO potential, sliceability, testability and comparability.

### 1.2 Lecturer Approval and Expansion
The lecturer approved the Community Equipment Loan Service scenario
for CA2. No scenario-specific expansion was provided.

### 1.3 Reconciliation and Agreed Baseline Links
The multiplicity at the LoanRequest end of the StaffMember–LoanRequest
association was changed from 1..* to 0..*, allowing a staff member
to have no managed requests.

## 2. Product Goal, Backlog and Selected Design Scope

### 2.1 Product Goal

Make Riverside's equipment-lending service more reliable, consistent and
transparent, so that staff can make informed lending decisions and community
borrowers can plan their activities with clear confirmation of equipment,
collection arrangements and return dates.

### 2.2 Ordered Product Backlog

https://github.com/users/KaterynaKon/projects/2

### 2.3 Selected Use Case

#### Main User Story: US-01 Manage Equipment Loan Requests
As a Borrower,
I want my equipment request to be assessed and receive
clear confirmation of the decision and any approved loan details,
so that I can plan my community activity.

 ####Selected Use Cases

| Use Case | Value | Related Requirements / Rules | Dependencies | Selection Rationale |
|---|---|---|---|---|
| UC-01: Record Loan Request | Captures request information so staff can assess the borrowing request. | FR-01; NFR-02 | Staff have received a request by telephone or email and are authorised to record it. | Establishes the starting point of the lending process and supports multiple equipment lines and the alternative of missing required information. |


## 3. Behavioural Design

### 3.1 UC-01: Record Loan Request

#### Detailed Use Case

| Field | Content |
|---|---|
| Use Case ID | UC-01 |
| Name | Record Loan Request |
| Primary Actor | Office Staff |
| Goal | Record a new equipment loan request. |
| Trigger | Office Staff receives an equipment request from a borrower by telephone or email. |
| Preconditions | - Office Staff has received an equipment request from a borrower by telephone or email.<br>- Office Staff is authorised to record a loan request. |
| Success Flow | 1. Office Staff starts recording a new loan request.<br>2. Office Staff records the borrower information.<br>3. Office Staff records the requested equipment, quantity, collection date and return date.<br>4. Office Staff saves the request.<br>5. The service records the request and makes it available for assessment. |
| Alternative Flows | A1. The borrower is new: Office Staff records the available borrower information as part of the request.<br>A2. Required request information is missing: the request is recorded as pending further information and cannot be assessed until the missing information is recorded. |
| Exceptions | E1. The service cannot save the request, for example because internet access is unavailable. No loan request is recorded, and staff follow an agreed fallback procedure. |
| Postconditions | - A loan request is recorded and available for assessment.<br>- No approval or equipment reservation is assumed at this stage. |
| Related Requirements / Rules | FR-01; NFR-02 |

#### Linked Gherkin Scenarios

ca1-analysis/features/equipment-lending.feature

**SC-01: Record a complete loan request**

**SC-02: Record a request with missing required information**

#### System Sequence Diagram

https://github.com/KaterynaKon/Community-Equipment/blob/main/ca1-analysis/diagrams/ssd/png_version_ssd_uc1.png

#### Operation Contracts

Related use case: UC-01  
Related requirements: FR-01; NFR-02  
Related scenarios: SC-01; SC-02

##### Shared Preconditions and Design Assumption

- Office Staff is authorised to record loan requests.
- Operations after `startLoanRequest()` require an active request-recording context.
- Following the SSD, supplied information is held temporarily until
  `saveLoanRequest()`. The group must agree how this temporary information
  is represented; no additional class is prescribed here.

##### OC-UC01-01: Start Loan Request

**Operation:** `startLoanRequest()`

**Postconditions:**
- A new request-recording context was established.
- It is ready to receive borrower information, equipment lines and dates.
- No saved LoanRequest was created.

##### OC-UC01-02: Record Borrower Information

**Operation:** `recordBorrower(name, borrowerCategory, contactDetails)`

**Postconditions:**
- The supplied borrower information was retained in the active context.
- This operation does not yet create or update a saved Borrower record.

##### OC-UC01-03: Add Requested Equipment

**Operation:** `addRequestedEquipment(equipmentType, quantity)`

**Additional precondition:**
- `equipmentType` identifies an existing EquipmentType.

**Postconditions:**
- An equipment line containing the supplied type and quantity was added
  to the active context.
- Previously added equipment lines were retained.
- No physical EquipmentUnit was allocated or reserved.

##### OC-UC01-04: Set Request Dates

**Operation:** `setRequestDates(collectionDate, returnDate)`

**Postconditions:**
- The supplied collection and return dates were retained in the active context.
- An unknown date remains unset; it is not replaced with an invented date.

##### OC-UC01-05: Save Loan Request

**Operation:** `saveLoanRequest()`

**Additional precondition:**
- The collected information meets the agreed minimum needed to save a request.
  This minimum is distinct from the information required for assessment.

**Postconditions — successful recording:**
- A LoanRequest was created.
- Its supplied collectionDate and returnDate were set.
- A RequestedEquipmentLine was created for each collected equipment line.
- Each line's requestedQuantity was set.
- Each line was associated with the LoanRequest and its EquipmentType.
- The LoanRequest was associated with the identified Borrower.
- For a new borrower, a Borrower record was created from the supplied
  information, subject to the agreed minimum identification requirements.

**Complete information — SC-01:**
- The recorded request is available for assessment.
- No approval decision was recorded.

**Missing required assessment information — SC-02:**
- The request's status was set to pending further information.
- The request is not available for assessment.

**Controlled failure — E1:**
- No LoanRequest is recorded.
- Successful recording is not reported.
- Staff follow the agreed fallback procedure.

**Reservation:**
- Saving the request does not imply approval or equipment reservation.




## 4. Responsibilities and GRASP
### 4.1 CRC Cards
### 4.2 GRASP Decision Records



## 5. Object-Interaction Design
### 5.1 UC-01: Record Loan Request


## 6. Integrated Design Model and Structure
### 6.1 Design Class Diagram
https://github.com/KaterynaKon/Community-Equipment/blob/main/ca1-analysis/diagrams/class-diagrams/class-diagram-png.png
https://github.com/KaterynaKon/Community-Equipment/blob/main/ca1-analysis/diagrams/class-diagrams/classDiagrams.puml
### 6.2 Package / Layer View
### 6.3 Interfaces, Result Types and Error Conventions

## 7. Traceability Matrix

## 8. Design Evaluation
### 8.1 Cohesion, Coupling, Controllers and Object Validity
### 8.2 Assumptions, Risks, Rejected Alternatives and Limitations

## 9. Collaboration, Review and AI Disclosure
### 9.1 Iteration Goals and Definition of Done
### 9.2 Peer Review and Final Retrospective
### 9.3 Individual Contribution Evidence
### 9.4 Group and Individual AI Declarations
### 9.5 Evidence Links and Final Submission Tag

## References
