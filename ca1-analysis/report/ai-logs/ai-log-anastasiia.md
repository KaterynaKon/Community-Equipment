# AI Interaction Log — Anastasiia Tkachenko

**Tool:** Claude
**Period:** CA2 design work, November 2026

## Entry 1 — Finding inconsistencies

**AI use:** Compared the SSD, Gherkin, operation contracts, CRC/GRASP and DCD with each other and with the Session 14 walkthrough.
**Findings:** US-01 actor did not match UC-01; SSD E1 returned no result while the DCD returns RecordingResult; SC-04 was under the wrong Gherkin Rule; DCD contained classes not used by UC-01; DCD image link was broken.
**My check and decision:** Checked each finding against the brief and Session 14. Accepted corrections were agreed with the group and submitted in PR #18 for review.

## Entry 2 — Alternative responsibility assignments

**AI use:** Suggested alternatives for status changes, temporary request data and borrower creation.
**My check and decision:** Compared them using GRASP. Accepted markRecorded() instead of a public changeStatus(), a DTO for unsaved data and LoanRequestService as Creator of Borrower. Recorded in Decisions 2, 4 and 5.

## Entry 3 — Checking for missing states and failures

**AI use:** Checked the SSD, UC-01 and sequence diagrams SD01–SD05 for missing failure paths and for messages without DCD operations.
**My check and decision:** Changed the SSD E1 branch to return a failure message; added a minimal guarantee to UC-01; sent the DCD mismatches in SD01–SD05 to Arsen, who updated his diagrams.

## Entry 4 — Risks

**AI use:** Suggested design risks for section 8.2.
**My check and decision:** Kept only risks that follow from our design: one request context per service, duplicate borrowers, DTO duplicating LoanRequest fields, atomic saving depending on the repository.

