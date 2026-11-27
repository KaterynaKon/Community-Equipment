# AI Interaction Log

## Entry 1 — 24 November 2026

**Tool:** Manus AI

**Request summary:** Review the explanation for using LoanRequestDTO
instead of creating LoanRequest at startLoanRequest().

**AI response summary:** AI explained that creating an unsaved object
does not necessarily leave a saved partial request after failure.
Using a DTO is a design choice, not a requirement of OC-UC01-01.

**My evaluation and decision:** I compared this explanation with
OC-UC01-01, which states that no saved LoanRequest is created.
I revised the rejected alternative in GRASP Decision 2 to explain
that the DTO separates incomplete data capture from domain-object
creation.

**Affected artefact:** CA2 dossier, Section 4.2, Decision 2.