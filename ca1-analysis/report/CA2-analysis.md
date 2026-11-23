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
| Postconditions | - After successful recording, a complete request is available for assessment.<br>- An incomplete request is recorded as pending further information and is not available for assessment.<br>- No approval or equipment reservation is assumed at this stage. |
| Related Requirements / Rules | FR-01; NFR-02 |

#### Linked Gherkin Scenarios

ca1-analysis/features/equipment-lending.feature

- **SC-01:** Record a complete loan request.
- **SC-02:** Record a request with missing required information.
- **SC-04:** Loan request cannot be saved.

#### System Sequence Diagram

https://github.com/KaterynaKon/Community-Equipment/blob/main/ca1-analysis/diagrams/ssd/png_version_ssd_uc1.png

#### Operation Contracts

Related use case: UC-01  
Related scenarios: SC-01; SC-02; SC-04


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

**Controlled failure — E1 / SC-04:**
- No LoanRequest is recorded.
- Successful recording is not reported.
- Staff follow the agreed fallback procedure.

**Reservation:**
- Saving the request does not imply approval or equipment reservation.




## 4. Responsibilities and GRASP
The **Class-Responsibility-Collaborator (CRC)** model identifies the responsibilities assigned to each class in the Community Equipment Loan Service (CELS). It describes what information each class knows, what actions it performs and which other classes it collaborates with.

Responsibilities are divided into:
- **Knowing responsibilities:** Information that a class maintains or needs to know to perform its work.
- **Doing responsibilities:** Actions or operations that a class performs.
- **Collaborators**: Other classes that help it fulfil its responsibilities.

The **GRASP (General Responsibility Assignment Software Patterns)** principles are used to justify how responsibilities are distributed throughout the system.
The main principles considered in this design are:
- **Controller:** Assigns responsibility for handling system events to an appropriate controller.
- **Information Expert:** Assigns a responsibility to the class that has the information needed to fulfil it.
- **Creator:** Assigns responsibility for creating an object to a class that contains, aggregates, records or closely uses it.
- **High Cohesion:** Keeps responsibilities within a class focused and related.
- **Low Coupling:** Minimises unnecessary dependencies between classes.
- **Indirection:** Introduces an intermediate class to reduce direct dependencies.
- **Pure Fabrication:** Introduces a class for a technical responsibility that does not naturally belong to a domain entity.

### 4.1 CRC Cards
| Class | Knowing responsibilities | Doing responsibilities | Collaborators |
|---|---|---|---|
| LoanRequestController | Receives request information and knows available request operations. | Handles request submissions and delegates operations. | LoanRequestService |
|LoanRequestService | Knows request workflow and required information. | Coordinates request creation, validation and status changes. |LoanRequest, Borrower, LoanRequestRepository |
| LoanRequest | Knows request details, including received date, collection date, return date and status. Knows the borrower associated with the request and the requested equipment lines. | Checks whether the required information has been recorded. Creates and maintains its requested equipment lines. Maintains the request status. | Borrower, RequestedEquipmentLine, LoanRequestService |
| RequestedEquipmentLine | Knows the requested equipment type and requested quantity. | Provides requested equipment details for the loan request and subsequent availability checks. | LoanRequest, EquipmentType |
| LoanRequestRepository | Defines operations for saving, retrieving and updating loan requests. | Provides an abstraction for accessing loan request data without exposing database implementation details. | LoanRequest, LoanRequestService, LoanRequestRepositoryImpl |
| LoanRequestRepositoryImpl | Knows how loan request and equipment line data are stored and retrieved from the database. | Implements repository operations. Executes database queries to save, retrieve and update loan requests and their associated equipment lines. | LoanRequestRepository, LoanRequest, RequestedEquipmentLine |


### 4.2 GRASP Decision Records

## Decision 1: Receiving and coordinating loan requests

**Selected classes:** LoanRequestController, LoanRequestService

| Field | Content |
|---|---|
| Problem | A class must receive a new loan request submission from Staff Member and coordinate the process of recording the request, including handling incomplete information. |
| Candidate assignments | LoanRequestController, LoanRequestService, LoanRequest |
| Selected assignment | **LoanRequestController** receives the request from the user interface and delegates the operation to **LoanRequestService**. **LoanRequestService** coordinates request creation, checks required information and ensures the appropriate request status is recorded. |
| GRASP principle | **Controller:** The controller handles the system event and delegates business operations. **High Cohesion:** The controller remains focused on receiving requests, while the service handles the request workflow. |
| Rejected alternative | Assigning the entire process to **LoanRequestController** was rejected because it would combine interface handling, validation and business logic in one class. |
| Consequences | Separates interface communication from business logic and makes the request workflow easier to test and maintain. However, it introduces an additional service class and dependency between the controller and service. |

## Decision 2: Creating the loan request and equipment lines

**Selected classes:** LoanRequestController, LoanRequestService, LoanRequest, RequestedEquipmentLine

| Field | Content |
|---|---|
| Problem | The system must create a new loan request containing borrower information, requested collection and return dates, and one or more requested equipment lines. Each equipment line must identify an equipment type and its requested quantity. |
| Candidate assignments | LoanRequestController, LoanRequestService, LoanRequest, RequestedEquipmentLine |
| Selected assignment | LoanRequestController receives the request information from Staff Member and delegates the operation to **LoanRequestService**. **LoanRequestService** coordinates the creation of the new **LoanRequest** and associates it with the relevant **Borrower**. **LoanRequest** creates and maintains its **RequestedEquipmentLine** objects because they form part of the request. Each **RequestedEquipmentLine** stores the requested equipment type and quantity. |
| GRASP principle | **Controller:** **LoanRequestController** handles the system event of submitting a new loan request and delegates the operation. **Creator:** **LoanRequest** is responsible for creating its **RequestedEquipmentLine** objects because it contains and maintains them. **High Cohesion:** The service coordinates request creation, while the request maintains its own equipment lines. |
| Rejected alternative | Assigning **LoanRequestController** the responsibility for creating and managing all equipment lines was rejected because it would mix interface handling with domain-object creation and request-management logic. |
| Consequences | Request creation is separated from interface handling, and equipment lines are maintained by the loan request that contains them. This supports requests containing multiple equipment types and quantities. However, it introduces a relationship between **LoanRequest** and **RequestedEquipmentLine**, and the service must coordinate the creation and persistence of the complete request. |

## Decision 3: Storing and retrieving loan request information

**Selected classes:** LoanRequestRepository, LoanRequestRepositoryImpl, LoanRequestService

| Field | Content |
|---|---|
| Problem | The system needs to save and retrieve loan requests and update their information, including decisions and changes to requested quantities or dates. |
| Candidate assignments | LoanRequest, LoanRequestService,LoanRequestRepository, LoanRequestRepositoryImpl |
| Selected assignment | LoanRequestRepository defines the persistence operations, while **LoanRequestRepositoryImpl** performs the actual database operations. LoanRequestService uses the repository to save, retrieve and update loan requests. |
| GRASP principle | **Indirection:** The repository provides an intermediate layer between business logic and the database. **Low Coupling:** The service does not depend directly on database implementation details. |
| Rejected alternative | Allowing **LoanRequestService** to execute database queries directly was rejected because it would tightly couple business logic to the database technology. |
| Consequences | Database access is separated from business logic, making the system easier to test and maintain. The database implementation could be changed without significantly modifying the service. However, additional repository interfaces and implementations are required. |

## Decision 4: Checking request completeness

**Selected classes:** LoanRequest, LoanRequestService

| Field | Content |
|---|---|
| Problem | The system must ensure that a loan request contains all information required by the agreed request policy before it can be assessed. If required information is missing, the request must be recorded as pending further information and must not proceed to assessment. |
| Candidate assignments |LoanRequest, LoanRequestService, LoanRequestController |
| Selected assignment | LoanRequest knows its recorded information and determines whether the required request details are present. LoanRequestService coordinates the completeness check and ensures that the appropriate request status is recorded. If information is missing, the request is assigned the pending further information status. |
| GRASP principle | **Information Expert:** **LoanRequest** is responsible for checking its completeness because it holds the request information required for that check. **High Cohesion:** **LoanRequestService** coordinates the request workflow and status changes, while **LoanRequest** handles information related to its own completeness. |
| Rejected alternative | Assigning the completeness check entirely to **LoanRequestController** was rejected because it would place business validation logic in the controller and mix interface handling with request-management responsibilities. |
| Consequences | Request completeness rules are kept close to the information being checked, while the service coordinates the workflow. This supports NFR-02 by preventing incomplete requests from being assessed. However, the required information rules must be clearly defined and maintained. |

## 5. Object-Interaction Design
### 5.1 UC-01: Record Loan Request


## 6. Integrated Design Model and Structure
### 6.1 Design Class Diagram
https://github.com/KaterynaKon/Community-Equipment/blob/main/ca1-analysis/diagrams/class-diagrams/class-diagram-png.png
https://github.com/KaterynaKon/Community-Equipment/blob/main/ca1-analysis/diagrams/class-diagrams/classDiagrams.puml
### 6.2 Package / Layer View
### 6.3 Interfaces, Result Types and Error Conventions

## 7. Traceability Matrix

| Requirement | User Story | Use Case | Gherkin | SSD operation | Operation Contract | CRC / GRASP | DCD element | Interaction (5.1) |
|---|---|---|---|---|---|---|---|---|
| FR-01 | US-01 | UC-01 | SC-01 | startLoanRequest() | OC-UC01-01 | TBD | TBD | TBD |
| FR-01 | US-01 | UC-01 | SC-01 | recordBorrower() | OC-UC01-02 | TBD | TBD | TBD |
| FR-01 | US-01 | UC-01 | SC-01 | addRequestedEquipment() | OC-UC01-03 | TBD | TBD | TBD |
| FR-01 | US-01 | UC-01 | SC-01 | setRequestDates() | OC-UC01-04 | TBD | TBD | TBD |
| FR-01 | US-01 | UC-01 | SC-01 | saveLoanRequest() | OC-UC01-05 | TBD | TBD | TBD |
| FR-01, NFR-02 | US-01 | UC-01 (A2) | SC-02 | saveLoanRequest() | OC-UC01-05 | TBD | TBD | TBD |
| FR-01 | US-01 | UC-01 (E1) | SC-04 | saveLoanRequest() | OC-UC01-05 | TBD | TBD | TBD |

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
