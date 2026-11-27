# AI log of my interaction with ChatGPT
## ENTRY 1:
**My question**: So I need you to check  relationship between scenario, use case 1 and renewed class diagram. Do I need to add something to class diagram.

**Answer**: 
Yes. After comparing the Riverside scenario, UC-01 and your renewed design class diagram, 
I think your diagram is already a good foundation. However, I would make a few changes to represent UC-01 more accurately.

...

**A.** Borrower – add organisation information
The scenario mentions that the spreadsheet may contain:
- Borrower name.
- Organisation.
- Contact details.
Your current class is:
class Borrower {
  - borrowerId: int
  - name: String
  - contactDetails: String
  - borrowerCategory: String
}

**B.** LoanRequest – represent the request information more accurately
Your current class:
class LoanRequest {
  - requestId: int
  - receivedDate: LocalDate
  - collectionDate: LocalDate
  - returnDate: LocalDate
  - status: RequestStatus
  - activityDescription: String
  - namedCollector: String
  + changeStatus(status: RequestStatus): void
}


I recommend adding:
- activityStartDate: LocalDate
- activityEndDate: LocalDate
- missingInformation: String

**C.** RequestStatus – distinguish between different pending states

Your current enumeration is:
enum RequestStatus {
  Pending
  Approved
  Rejected
  Collected
  Withdrawn
  Returned
}


The scenario identifies these states:
- Approved
- Rejected
- Withdrawn
- Awaiting further information
However, UC-01 also says that a successfully recorded request becomes available for assessment.

I suggest:
enum RequestStatus {
  AwaitingAssessment
  AwaitingInformation
  Approved
  Rejected
  Withdrawn
  Collected
  Returned
}

**D.** Add a LoanRequestController
This is probably the most important structural improvement.

Your current diagram has LoanRequestService, but it does not have a controller.

Your UC-01 has an external actor, Office Staff, who initiates the process. In a typical layered software design, a controller receives that action and delegates the work to a service.

I would add:
class LoanRequestController {
  + recordLoanRequest(request: LoanRequest): LoanRequest
}

**E.** Add BorrowerRepository
There is another detail in your alternative flow A1:
The borrower is new: Office Staff records the available borrower information as part of the request.

Your diagram currently has a LoanRequestRepository, but no BorrowerRepository.

I recommend adding:
interface BorrowerRepository {
  + findById(id: int): Borrower
  + save(borrower: Borrower): void
}

class BorrowerRepositoryImpl

BorrowerRepositoryImpl ..|> BorrowerRepository

## ENTRY 2:
**My question**: In what format is better to write this (Asked this about CRC cards and decisions

**Answer**: 

For your assignment, I recommend using a combination of tables and short explanatory paragraphs. This will make the GRASP decisions easy to compare while still showing that you understand the reasoning behind each decision.

I would avoid putting everything into one large table because the consequences and GRASP justifications need explanation.

Recommended format:

**1. Short introduction**

Start with one paragraph explaining the purpose of the CRC/responsibility model and how GRASP is used.

**2. CRC cards – use a table**

Present the main classes, their knowing responsibilities, doing responsibilities and collaborators.

For example:

| Class | Knowing responsibilities | Doing responsibilities | Collaborators | Rationale |
|---|---|---|---|---|
| LoanRequestController | The system operations available to Office Staff for UC-01. | Receives each SSD system event and delegates it to LoanRequestService. Returns the RecordingResult of saveLoanRequest(). | LoanRequestService, RecordingResult | Controller (Decision 1): keeps user-interface events separate from the workflow. |
| LoanRequestService | The current request-recording context (LoanRequestDTO) until saveLoanRequest(). | Stores supplied information in the DTO. On saving, finds or creates the Borrower, creates the LoanRequest, asks it to add its lines and set its status, saves it and builds the RecordingResult. | LoanRequestDTO, Borrower, LoanRequest, BorrowerRepository, LoanRequestRepository, RecordingResult | Pure Fabrication that coordinates the use-case workflow (Decisions 1, 2 and 5). |

This makes the CRC part compact and easy to read.

**3. GRASP responsibility decisions – use numbered subsections**

Give each decision its own subsection. Use the same structure for all six decisions.
