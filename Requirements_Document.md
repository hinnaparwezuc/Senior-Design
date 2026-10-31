# Requirements Document

**Project:** ReFrame, CEAS Co-op Reflection System  
**Team members:** Hinna Parwez, Aradana Nair, Maria Malik, Priyanshi Kulshrestha, Mbathio Lo  
**Course / Assignment:** Assignment 9, Requirements Document and Technical Specification Document  
**Project advisors:** Dr. David Reeping, Dr. Cedrick Kwuimy

### DRAFT REVIEW NOTES (delete this block before submission).

- This draft consolidates `User_Stories.md`, `Project-Constraints.md`, D0, D1, D2, and the Assignment 5-6 plan. Priorities, thresholds, owners, and dates are proposals for the team to accept or change.
- `*` marks a value or date that is not in an earlier team document. Each `*` is listed in section 9.
- Priorities here are stricter than the D2 traceability table, which marked almost everything Must. The change is logged in section 1.
- Keep IDs stable and never reuse them. Mark a dropped row WITHDRAWN and log it in section 1. Never renumber.
- Will turn into .MD before submission

## 1. Version and Change Log

| Version | Date | Change | Author |
| --- | --- | --- | --- |
| 0.1 | 2026-10-29 | Initial skeleton with section headings | Hinna Parwez |
| 0.2 | 2026-10-29 | Populated all sections from US-01 to US-04, UC-01/02, AC-01.1 to AC-02.3, DR-1 to DR-6, NFR-1 to NFR-5 (D2), the Week 3 constraints essay, and OQ-1 to OQ-11 (D2). Assigned REQ-01 to REQ-14, NFR-01 to NFR-07, CON-01 to CON-09. Re-set MoSCoW priorities (D2 marked 21 of 25 rows Must). NFR-01 to NFR-05 keep the D2 numbers NFR-1 to NFR-5 with zero padding. NFR-06 and NFR-07 are new. AI-assisted draft, pending team review. | Hinna Parwez |

## 2. Purpose and Scope

**Purpose.** This document states what the CEAS Co-op Reflection System must do and for whom. `Technical_Specification.md` states how it will be built and cites the version of this document it traces to.

**Goal (same wording as D0).** Let CEAS co-op students reflect on each completed rotation using rotation-specific questions, give assigned advisors a clear summary with workplace supervisor observations, and keep each reflection private to the student and their assigned advisor.

**In scope for this release.** A student writes and submits one reflection per completed rotation. An assigned advisor reads it with a summary. A workplace supervisor submits an end-of-rotation evaluation. Access to reflection content is limited to the student and the student's assigned advisor.

**Out of scope.** Everything on the Won't list in section 7.

**Related documents.**

- `User_Stories.md` (Week 4) and `Project-Constraints.md` (Week 3)
- Diagrams, linked and not copied: D0, D1, D2
- `Tasklist.md` (Assignment 5-6 plan)

## 3. Stakeholders and User Goals

| Stakeholder | Type | Goal (Week 4) | Story |
| --- | --- | --- | --- |
| CEAS student, including international students | Primary | Reflect on accomplishments and challenges at their experience level and set one goal for the next rotation. | US-01 |
| CEAS co-op advisor | Secondary | Review a student's learning and development to guide the next advising conversation. | US-02 |
| Co-op workplace supervisor | Secondary | Give end-of-rotation observations of the student's performance. | US-03 |
| UC IT staff responsible for student-data access | Hidden | Make sure reflection content is disclosed only to authorized people. | US-04 |

## 4. Glossary

Each term is defined once, here. Both documents use these terms.

| Term | Definition |
| --- | --- |
| Student | A CEAS student enrolled in a course designated as eligible for co-op reflection. |
| Advisor | A CEAS co-op advisor. Reads reflections only for students currently assigned to them. |
| Supervisor | A workplace supervisor at the co-op employer. Submits evaluations. Never reads reflections. |
| UC IT staff | University IT staff responsible for student-data access. They define access rules and have no content access unless a separate role is approved. |
| CEAS administrator | A CEAS staff member who loads prompts and corrects submitted records. Corrections are not a user feature (OQ-3). |
| Co-op | A paid work placement completed as part of a CEAS program. |
| Placement | One student's co-op position with one employer. |
| Rotation | One completed co-op work period within a placement. |
| Rotation number | The ordinal of the rotation (1, 2, 3, ...). |
| Supported rotation number | A rotation number that has a configured prompt set. |
| Prompt | One question shown to a student. Each belongs to one category: accomplishment, challenge, or goal. |
| Prompt set | The prompts configured for one supported rotation number. |
| Required response | A response to a prompt that must contain at least one non-whitespace character. |
| Improvement goal | The student's stated goal for the next co-op (the "goal" category). |
| Reflection | A student's submitted responses to a prompt set, for one placement and rotation number. |
| Student-supplied rotation | A rotation number typed by the student when the university record has none. It is not verified. |
| Summary | A short view of one reflection showing its accomplishment, challenge, and goal text. |
| Supervisor evaluation | A supervisor's accomplishments and improvement areas for one student, placement, and rotation. It is separate from the reflection. |
| Advisor assignment | A dated link between an advisor and a student. It is current when its start date is on or before today and its end date is empty or on or after today. |
| Assigned advisor | An advisor with a current advisor assignment to the student. |
| Data-use notice | Versioned text explaining how reflections are used. A student must acknowledge the current version before reflecting. |
| Access-denied response | The single response returned when a request is not allowed. It carries no reflection content and is identical for forbidden and nonexistent reflections. |
| E1 University Sign-In | The external university sign-in system that identifies users. |
| E2 University Records | The external university records source for enrollment, rotation numbers, advisor assignments, and supervisor assignments. |
| MoSCoW | Must, Should, Could, Won't priority scheme (section 7). |

## 5. Functional Requirements

Verification methods: **Test** (automated or scripted with seeded data), **Inspection** (review of design or configuration), **Demonstration** (walk-through with stakeholders).

| ID | Requirement | Priority | Source | Verification |
| --- | --- | --- | --- | --- |
| REQ-01 | The system shall present to a student the prompt set configured for the student's recorded rotation number, with required prompts marked, and shall present no prompt set when the rotation number is unsupported or the set lacks an accomplishment, challenge, or goal prompt. | Must | US-01, AC-01.1, DR-1, DR-2 | Test: each seeded supported rotation returns its mapped set; rotation numbers 0 and an unconfigured number return an error. |
| REQ-02 | The system shall, when a student requests review, identify 100% of required responses that are empty or whitespace-only, keep all other entered text, block submission, and create no stored reflection. | Must | US-01, AC-01.4, DR-2 | Test: submit with 1, 2, and 3 blank responses; the stored reflection count stays 0. |
| REQ-03 | The system shall store exactly one reflection per student, placement, and rotation number, with all responses, and shall reject a second submission for the same placement and rotation while returning the ID of the first. | Must | US-01, AC-01.2 | Test: two simultaneous submits leave exactly 1 stored reflection. |
| REQ-04 | The system shall accept a student-supplied rotation number only when the university record has none and the number is supported, and shall mark the reflection as student-supplied. | Should | US-01, AC-01.3, DR-3 | Test: supply 1 supported number and 1 unsupported number; the first is stored and marked, the second is rejected. |
| REQ-05 | The system shall start a reflection only when E2 confirms the student's eligible enrollment and ownership of the placement and the student has acknowledged the current data-use notice version, and shall otherwise refuse to start and create no reflection. | Must | US-01, UC-01 preconditions, DR-6 | Test: ineligible student, other student's placement, E2 unreachable, and no acknowledgment each refuse. |
| REQ-06 | The system shall show an assigned advisor a summary of one reflection's accomplishment, challenge, and goal in which each field holds only text the student wrote, is cut to at most 300 characters at a sentence end (otherwise at a word boundary plus an ellipsis), and shall still show the full reflection if the summary cannot be produced. | Should | US-02 | Test: 5 seeded reflections, including one over 300 characters per field; Inspection: no added words. |
| REQ-07 | The system shall accept one evaluation per placement and rotation from a supervisor whom E2 confirms as assigned, with accomplishments and improvement areas each 1 to 5,000 characters containing a non-whitespace character, shall reject unassigned supervisors and duplicates, and shall make the evaluation available to the student's assigned advisor. | Should | US-03, DR-5 | Test: valid, unassigned, duplicate, empty-field, and E2-down cases. |
| REQ-08 | The system shall display a submitted reflection to the student who wrote it and to that student's assigned advisor, without changing the stored reflection. | Must | US-04, UC-02, AC-02.1, AC-02.2 | Test: owner and assigned advisor each read 1 seeded reflection; the stored record is unchanged. |
| REQ-09 | The system shall return the access-denied response and no reflection content to every other authenticated actor (other students, unassigned advisors, supervisors, UC IT staff), including on a direct request by reflection ID and for IDs that do not exist, in 100% of tested cases. | Must | US-04, AC-02.3, DR-4 | Test: one denied case per actor type plus a nonexistent ID; response bodies are identical. |
| REQ-10 | The system shall give a supervisor no access to reflection content: after a supervisor submits an evaluation, every request by that supervisor for reflection content shall return the access-denied response. | Must | US-03, US-04, DR-5 | Test: submit, then request 3 reflections; Inspection: supervisor service has no read right on reflection data. |
| REQ-11 | The system shall let an advisor list the students currently assigned to them, 20 per page by default and at most 50, before opening a reflection. Opening a reflection still follows REQ-08 and REQ-09. | Should | UC-02 step 1, US-02 | Test: advisor with 3 current and 1 ended assignment sees exactly 3 students. |
| REQ-12 | The system shall keep every earlier version of a prompt set so that each stored reflection still points to the prompts the student saw. | Should | US-01, AC-01.1 | Test: load a new version, then read a reflection saved under the old one. |
| REQ-13 | The system shall write one log line per access decision, with identifiers and the decision only and no reflection text. | Could | US-04 | Inspection: sample log lines; Test: line count equals decision count in the denial tests. |
| REQ-14 | The system shall keep a student's unsaved answers in the browser session after a failed save or an expired sign-in, restore them after sign-in, and store no draft on the server. | Should | US-01, UC-01 E1 | Test: force a save failure and a token expiry; the answers reappear and the database has 0 draft rows. |

## 6. Non-Functional Requirements

| ID | Requirement | Priority | Source | Verification |
| --- | --- | --- | --- | --- |
| NFR-01 | Performance. The system shall complete each page load or submit in under 2 seconds for at least 95%* of requests with up to 25* concurrent users, excluding requests delayed by E2, which has a 2-second timeout. | Should | D0 working target (D2 NFR-1) | Test: load test with seeded data, measure 95th percentile. |
| NFR-02 | Usability (compatibility). The system shall work in the latest two versions* of Chrome, Edge, Firefox, and Safari, on a laptop and on a phone with a 360-pixel-wide* screen, with nothing to install. | Should | D0 hardware constraint (D2 NFR-2) | Test: run all three flows in each browser; Demonstration on 1 phone. |
| NFR-03 | Reliability (no partial records). The system shall leave 0 partial reflections or evaluations in the database after a failure forced at each step of a save. | Must | D0 error handling (D2 NFR-3), US-01 | Test: fault injection at each step; row counts checked. |
| NFR-04 | Security (confidentiality). The system shall contain reflection and evaluation text in 0 log lines and 0 error responses across the full automated test suite, including every denied and failed case. | Must | US-04 (D2 NFR-4) | Test: search all test logs and responses for seeded marker text. |
| NFR-05 | Reliability (recovery). The system shall take a backup at least every 24 hours*, restart a failed service within 5 minutes*, and recover 100% of reflections present in the latest backup in a restore trial. | Should | D2 NFR-5 | Test: restore a backup into a clean instance; Demonstration of restart. |
| NFR-06 | Usability (task time). At least 4 of 5* student volunteers shall complete a reflection from opening the form to confirmation in under 15 minutes* without help, and at least 4 of 5* advisor volunteers shall find and open a named student's reflection in under 2 minutes*. | Should | US-01, US-02 | Test: usability session with volunteers (plan task T20). |
| NFR-07 | Security (authentication and transport). The system shall serve all traffic over HTTPS and shall require a valid E1 sign-in token on every request except sign-in, returning 401 and no content in 100% of tested requests with a missing, altered, or expired token (after 60* seconds of clock allowance). | Must | US-04 | Test: one case each for missing, altered, expired, and wrong-audience token. |

## 7. MoSCoW Summary

**Result:** 10 of 21 requirements are Must (48%). That is the minimum viable product: a student reflects, the owner and assigned advisor can read, everyone else is denied, and nothing partial or private leaks. Supervisor evaluations are Should because supervisor sign-in is unresolved (OQ-1, risk R3 rated High/High in the plan).

**Must (10).** REQ-01, REQ-02, REQ-03, REQ-05, REQ-08, REQ-09, REQ-10, NFR-03, NFR-04, NFR-07.

**Should (10).** REQ-04, REQ-06, REQ-07, REQ-11, REQ-12, REQ-14, NFR-01, NFR-02, NFR-05, NFR-06.

**Could (1).** REQ-13 (access-decision logging). D2 calls logging the closest thing to scope creep.

**Won't, for this release.** Written down so these cannot creep back in.

- AI-written summaries, AI scoring, or sentiment analysis of reflections. The release summarizes by rule only (D2 R3). See open question 10 in section 9.
- A Power BI dashboard. See open question 10.
- Email or other notifications (OQ-11).
- Deleting data or applying retention rules (OQ-5). The system never deletes data.
- Editing a submitted reflection or evaluation in the application (OQ-3). A CEAS administrator corrects records outside the application.
- Server-side drafts (D2 R4).
- A supervisor viewing or editing a submitted evaluation (D2 R8).
- Our own account and password system (D0 decision log). Sign-in is E1 only.
- Any use of reflections for research (Week 3 essay, CON-09).

## 8. Inherited Constraints

From the Week 3 essay (`Project-Constraints.md`). `Technical_Specification.md` restates each with what it rules out or forces.

| ID | Constraint | Category | Related |
| --- | --- | --- | --- |
| CON-01 | Assessment must be applied consistently: the same criteria for every student, with no unfair or biased evaluation. | Professional | REQ-06 |
| CON-02 | Any sentiment analysis informs advisors only and has no effect on grading. | Professional | Won't list |
| CON-03 | Students are told clearly how their reflections are used and scored. | Professional | REQ-05 |
| CON-04 | An advisor reviews any automated evaluation before it affects a student's standing. | Ethical | REQ-06 |
| CON-05 | The system performs consistently across writing styles and English proficiency, and does not score non-native speakers lower. | Ethical | REQ-06, REQ-02 |
| CON-06 | Data is protected at a level appropriate for student educational records. The essay sizes the pilot at about 25 students. | Security | NFR-04, NFR-07 |
| CON-07 | Access to individual reflections and any dashboard or sentiment output is limited to the advisor responsible for that student. | Security | REQ-08, REQ-09 |
| CON-08 | Storage location is not final: a UC-managed system or a third-party cloud. | Security | NFR-05, OQ-9 |
| CON-09 | Reflections stay confidential until the student opts in to research or similar use. | Security | Won't list |

## 9. Assumptions and Open Questions

Owners and dates are proposals, taken from the plan's task owners (T23, R-register) where one exists. Dates use the plan's M2 checkpoint (2026-11-12) where no earlier date is recorded.

| # | Type | Description | Owner | Date |
| --- | --- | --- | --- | --- |
| 1 | Open question (OQ-1, PR-03) | How do supervisors sign in, given E1 is a university sign-in? Design assumes a CEAS-issued login or a one-time link. | Aradana Nair | 2026-11-12 |
| 2 | Open question (OQ-4) | Which rotation numbers are supported and what is the exact prompt wording? Rotations 1 to 3 use placeholders. | Aradana Nair | 2026-11-12 |
| 3 | Open question (OQ-3, PR-01) | How is a mistake in a submitted reflection or evaluation corrected? OQ-3, R8, and EC-22 disagree. PR-01 was due 2026-10-14; no result is recorded in the files. | Priyanshi Kulshrestha | 2026-11-12* |
| 4 | Open question (OQ-6, PR-02) | May students read the supervisor evaluation? D2 says yes, but no student read path exists. REQ-07 avoids the question until decided. | Maria Malik | 2026-11-12* |
| 5 | Open question (OQ-5) | What are the retention and deletion rules? Needs university guidance. | Aradana Nair | 2026-11-12* |
| 6 | Open question (OQ-7) | Does E2 offer a JSON API with the fields needed, and how fast? Assumed yes with a 2-second timeout. | Hinna Parwez | 2026-11-12* |
| 7 | Open question (OQ-8) | What are the wording and version of the data-use notice? | Aradana Nair | 2026-11-12* |
| 8 | Open question (OQ-9) | Will UC IT approve the hosting platform for student data? Demo uses synthetic data until approved. | Priyanshi Kulshrestha | 2026-11-12* |
| 9 | Open question (OQ-10) | When may a supervisor submit? Assumed at end of rotation. | Aradana Nair | 2026-11-12* |
| 10 | Open question | The Week 3 essay mentions sentiment analysis, a Power BI dashboard, and AI evaluation. D2 designs a rule-based summary only. Is the AI and dashboard work cut, or does it need requirements? Affects CON-02, CON-04, CON-05, CON-07. | Aradana Nair* | 2026-11-12* |
| 11 | Open question | The essay sizes the pilot at about 25 students. D1 sizes data for about 500. Which one sets the NFR-01 load? | Hinna Parwez* | 2026-11-12* |
| 12 | Open question | Confirm every * threshold in section 6 (load, percentile, browsers, task times, backup and restart times, clock allowance). | Maria Malik* | 2026-11-12* |
| 13 | Assumption | A student can have two current advisors, and both can read (OQ-2). | Hinna Parwez | 2026-11-12* |
| 14 | Assumption | The advisor assignment copy is refreshed from E2 every 15 minutes, so access can last up to 15 minutes after unassignment. D1 text says 10 in one place; D2 corrects it to 15. | Hinna Parwez | 2026-11-12* |
| 15 | Assumption | Eligible courses and advisor assignments are supplied by E2 and confirmed with CEAS before implementation (DR-6). | Aradana Nair | 2026-11-12* |
