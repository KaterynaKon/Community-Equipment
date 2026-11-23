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
**SC-04: Loan request cannot be saved**

#### System Sequence Diagram

https://github.com/KaterynaKon/Community-Equipment/blob/main/ca1-analysis/diagrams/ssd/png_version_ssd_uc1.png

#### Operation Contracts

Related use case: UC-01  
elated scenarios: SC-01; SC-02; SC-04
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
| EquipmentService | Knows equipment availability-checking operations. | Coordinates availability and extension-conflict checks. | EquipmentRepository, EquipmentUnit, LoanRequestRepository |
| CollectionService | Knows collection operations. | Records equipment collection. | CollectionRecord, LoanRequestRepository, EquipmentRepository |
| ReturnInspectionService | Knows return-inspection operations. | Records return inspections and coordinates damage recording. | ReturnInspectionRecord, DamageRecord, EquipmentRepository |
| ReportService | Knows the information required for operational and funder reports. | Generates reports using relevant system information. | Relevant repositories |
### 4.2 GRASP Decision Records

## Decision 1: Receiving and coordinating loan requests

**Selected classes:** LoanRequestController, LoanRequestService

| Field | Content |
|---|---|
| Problem | A class must receive a new loan-request submission from Office Staff and coordinate the process of recording the request, including handling incomplete information. |
| Candidate assignments | LoanRequestController, LoanRequestService, LoanRequest |
| Selected assignment | **LoanRequestController** receives the request from the user interface and delegates the operation to **LoanRequestService**. **LoanRequestService** coordinates request creation, checks required information and ensures the appropriate request status is recorded. |
| GRASP principle | **Controller:** The controller handles the system event and delegates business operations. **High Cohesion:** The controller remains focused on receiving requests, while the service handles the request workflow. |
| Rejected alternative | Assigning the entire process to **LoanRequestController** was rejected because it would combine interface handling, validation and business logic in one class. |
| Consequences | Separates interface communication from business logic and makes the request workflow easier to test and maintain. However, it introduces an additional service class and dependency between the controller and service. |

## Decision 2: Checking equipment availability

**Selected classes:** EquipmentService, EquipmentRepository, EquipmentUnit

| Field | Content |
|---|---|
| Problem | The system must support checking whether the requested quantity of equipment is available for a specified loan period. It must also support checking whether an extension conflicts with later approved requests. |
| Candidate assignments | EquipmentUnit, EquipmentType, EquipmentService, EquipmentRepository, LoanRequestService |
| Selected assignment | EquipmentService coordinates availability checks. It retrieves equipment information through **EquipmentRepository** and uses the information maintained by **EquipmentUnit** to determine availability for the requested period. It also supports extension-conflict checks. |
| GRASP principle | **Information Expert:** **EquipmentUnit** maintains its own condition and availability information. **EquipmentService** coordinates the wider operation using information from multiple units and requests. **Low Coupling:** Equipment availability logic is kept separate from request-interface handling. |
| Rejected alternative | Assigning the entire availability-checking operation to **LoanRequestController** was rejected because it would mix interface handling with equipment-management logic. |
| Consequences | Equipment-related logic is separated from request handling and can be reused for availability and extension checks. However, the service must consider overlapping approved requests, equipment condition, maintenance and inspection restrictions. |

**Design consideration:** Checking availability does not automatically reserve equipment. UC-01 explicitly states that recording a request does not imply approval or reservation.

## Decision 3: Storing and retrieving loan-request information

**Selected classes:** LoanRequestRepository, LoanRequestRepositoryImpl, LoanRequestService

| Field | Content |
|---|---|
| Problem | The system needs to save and retrieve loan requests and update their information, including decisions and changes to requested quantities or dates. |
| Candidate assignments | LoanRequest, LoanRequestService,LoanRequestRepository, LoanRequestRepositoryImpl |
| Selected assignment | LoanRequestRepository defines the persistence operations, while **LoanRequestRepositoryImpl** performs the actual database operations. LoanRequestService uses the repository to save, retrieve and update loan requests. |
| GRASP principle | **Indirection:** The repository provides an intermediate layer between business logic and the database. **Low Coupling:** The service does not depend directly on database implementation details. |
| Rejected alternative | Allowing **LoanRequestService** to execute database queries directly was rejected because it would tightly couple business logic to the database technology. |
| Consequences | Database access is separated from business logic, making the system easier to test and maintain. The database implementation could be changed without significantly modifying the service. However, additional repository interfaces and implementations are required. |

## Decision 4: Recording equipment collection and return

**Related requirements:** FR-04, FR-05, FR-10  
**Selected classes:** CollectionService, ReturnInspectionService, CollectionRecord, ReturnInspectionRecord

| Field | Content |
|---|---|
| Problem | The system must record which approved equipment was collected, who collected it, who recorded the return and the results of the return inspection. |
| Candidate assignments | CollectionService, ReturnInspectionService, LoanRequest, CollectionRecord, ReturnInspectionRecord, EquipmentUnit |
| Selected assignment | **CollectionService** coordinates collection and creates a **CollectionRecord** containing the collection date, actual collector and equipment issued. **ReturnInspectionService** coordinates the return process and creates a **ReturnInspectionRecord** containing the return date, quantity, missing components, damage, cleanliness, safety and maintenance findings. |
| GRASP principle | **Controller:** The service classes coordinate their respective system operations. **Creator:** The services create records that they coordinate and that capture the results of those operations. |
| Rejected alternative | Assigning collection and return operations directly to **LoanRequest** was rejected because these are separate operational processes involving equipment units, staff members and inspection information. |
| Consequences | Collection and return responsibilities are separated, making the design easier to understand and maintain. It also provides a record of the people involved in each operation. However, it introduces additional service classes and relationships. |

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
