## Product Goal and Product Backlog

### Product Goal

Make Riverside's equipment-lending service more reliable, consistent and
transparent, so that staff can make informed lending decisions and community
borrowers can plan their activities with clear confirmation of equipment,
collection arrangements and return dates.

### Main User Story: US-01 Manage Equipment Loan Requests
As a Borrower,
I want my equipment request to be assessed and receive
clear confirmation of the decision and any approved loan details,
so that I can plan my community activity.

### Related Use Cases
### 6.2 Detailed Use Case: UC-01 Record Loan Request

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
