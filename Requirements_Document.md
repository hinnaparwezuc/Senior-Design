# Requirements Document

**Team:** Hinna Parwez, Aradana Nair, Maria Malik, Priyanshi Kulshrestha, Mbathio Lo.

**Advisors:** Dr. David Reeping and Dr. Cedrick Kwuimy.

**Project:** ReFrame - CEAS Co-op Reflection System. Assignment 9.

> Review draft - advisor approval pending, both advisors have been made aware to review.

## 1. Version and change log

| Version | Date | Change | Author |
| --- | --- | --- | --- |
| 0.1 | 2026-10-29 | Initial skeleton (inherited history). | Hinna Parwez |
| 0.2 | 2026-10-29 | Populated 14 REQ, 7 NFR and 9 CON; revised priorities (inherited history). | Hinna Parwez |
| 0.3 | 2026-10-30 | Reformatted tables; clarified summary, authorization, draft isolation, performance, NFR sources, scope and unresolved decisions; paired with specification 0.1. | All team members, AI assistance was used |

IDs are stable and never reused. Mark dropped rows WITHDRAWN and log the change; never renumber. NFR- 01 to NFR-05 retain the D2 numbering with zero padding.

## 2. Purpose and scope

The system lets CEAS students reflect on completed co-op rotations, lets assigned advisors read their reflections and summaries, and supports separate supervisor evaluations. Student-owner and assigned-advisor access is enforced for reflection content. Must items define the MVP; Should items, including evaluations and summaries, remain planned scope subject to capacity and unresolved decisions. Won’t items are excluded for this release. Design diagrams are maintained in the project repository (github.com/hinnaparwezuc/Senior-Design) and are referenced, not duplicated, in this document.

| Reference | Contents | Repository path |
| --- | --- | --- |
| D0 High-Level Design | Components, interfaces I1 to I13, data flows, architecture decisions | Design_Diagrams/D0_High_Level_Design.pdf |
| D1 Detailed Design | C7 data model, C2 access algorithm, API contract | Design_Diagrams/D1_Detailed_Design.pdf |
| D2 Detailed Design | C1 and C3 to C6, edge cases, traceability, design rationale | Design_Diagrams/D2_Detailed_Design.pdf |

## 3. Stakeholders and user goals

| Stakeholder | Type | Goal / source story |
| --- | --- | --- |
| Student, including international students | Primary | Reflect on accomplishments, challenges and next-rotation goal; US-01. |
| Advisor | Secondary | Review learning and development for advising; US-02. |
| Supervisor | Secondary | Provide end-of-rotation performance observations; US-03. |
| UC IT staff | Hidden | Define and protect authorized student-data access; US-04. |

## 4. Glossary

This is the shared glossary for both documents; roles and domain terms are defined here once.

| Term | Definition |
| --- | --- |
| Student | A CEAS student enrolled in a course designated as eligible for co-op reflection. |
| Advisor | A CEAS co-op advisor. Reads reflections only for students currently assigned to them. |
| Supervisor | A workplace supervisor at the co-op employer. Submits evaluations. Never reads reflections. |
| UC IT staff | University IT staff responsible for student-data access. They define access rules and have no content access unless a separate role is approved. |
| CEAS administrator | CEAS staff responsible for prompt configuration. Submitted-record correction authority and procedure are unresolved; this role grants no reflection-read access. |
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
| Assigned advisor | An advisor with a current assignment in the E2-backed local copy, refreshed at most every 15 minutes. The proposed stale-copy rule is in section 9. |
| Data-use notice | Versioned text explaining how reflections are used. A student must acknowledge the current version before reflecting. |
| Access-denied response | The single response returned when a request is not allowed. It carries no reflection content and is identical for forbidden and nonexistent reflections. |
| E1 University Sign-In | The external university sign-in system that identifies users. |
| E2 University Records | The external university records source for enrollment, rotation numbers, advisor assignments, and supervisor assignments. |
| MoSCoW | Must, Should, Could, Won't priority scheme (section 7). |
| CEAS / UC | College of Engineering and Applied Science / University of Cincinnati. |
| Extractive summary | A summary formed from existing student text without generated interpretation or scoring. |
| API / endpoint | Application programming interface / a callable operation identified by method and path. |
| JSON / schema | JavaScript Object Notation / a definition of allowed fields, types and constraints. |
| E1 sign-in token | A credential issued by the approved identity provider and validated before protected requests. |
| D0 / D1 / D2 | High-level architecture / detailed data and interface design / edge cases and traceability design. |
| REQ / NFR / CON | Functional requirement / non-functional requirement / inherited constraint identifiers. |
| ORPHANED | A requirement or constraint whose design mapping remains unresolved. |
| p95 | The latency at or below which 95 percent of measured requests complete. |

## 5. Functional requirements

All priorities remain proposed pending team approval. Test means scripted or automated verification with seeded data; Inspection means configuration/design review; Demonstration means a stakeholder walkthrough.

| ID | The system shall / measurable condition | MoSCoW | Source story / design citation | Verification method |
| --- | --- | --- | --- | --- |
| REQ-01 | The system shall present to a student the prompt set configured for the student's recorded rotation number, with required prompts marked, and shall present no prompt set when the rotation number is unsupported or the set lacks an accomplishment, challenge, or goal prompt. | Must | US-01, AC-01.1, DR-1, DR-2 | Test: each seeded supported rotation returns its mapped set; rotation numbers 0 and an unconfigured number return an error. |
| REQ-02 | The system shall, when a student requests review, identify 100% of required responses that are empty or whitespace-only, keep all other entered text, block submission, and create no stored reflection. | Must | US-01, AC-01.4, DR-2 | Test: submit with 1, 2, and 3 blank responses; the stored reflection count stays 0. |
| REQ-03 | The system shall store exactly one reflection per student, placement, and rotation number, with all responses, and shall reject a second submission for the same placement and rotation while returning the ID of the first. | Must | US-01, AC-01.2 | Test: two simultaneous submits leave exactly 1 stored reflection. |
| REQ-04 | The system shall accept a student-supplied rotation number only when the university record has none and the number is supported, and shall mark the reflection as student-supplied. | Should | US-01, AC-01.3, DR-3 | Test: supply 1 supported number and 1 unsupported number; the first is stored and marked, the second is rejected. |
| REQ-05 | The system shall start a reflection only when E2 confirms the student's eligible enrollment and ownership of the placement and the student has acknowledged the current data-use notice version, and shall otherwise refuse to start and create no reflection. | Must | US-01, UC-01 preconditions, DR-6 | Test: ineligible student, other student's placement, E2 unreachable, and no acknowledgment each refuse. |
| REQ-06 | The system shall show an assigned advisor an extractive summary of accomplishment, challenge, and goal. Each field shall contain only student text, with at most 300 characters including an optional ellipsis; use the last sentence boundary that fits, otherwise a word boundary, or a character boundary for an unbroken string. If summary generation fails, the full authorized reflection shall remain available. | Should | US-02 | Test: short, exactly 300-character, long, multi-sentence, and unbroken text; every field is at most 300 characters. Force summary failure and verify full authorized view. |
| REQ-07 | The system shall accept one evaluation per placement and rotation from a supervisor whom E2 confirms as assigned, with accomplishments and improvement areas each 1 to 5,000 characters containing a non-whitespace character, shall reject unassigned supervisors and duplicates, and shall make the evaluation available to the student's assigned advisor. | Should | US-03, DR-5 | Test: valid, unassigned, duplicate, E2-down, whitespace-only, 1-character and 5,000/5,001-character fields; only valid assigned submissions persist. |
| REQ-08 | The system shall display a submitted reflection to the student who wrote it and to that student's assigned advisor, without changing the stored reflection. | Must | US-04, UC-02, AC-02.1, AC-02.2 | Test: owner and assigned advisor each read 1 seeded reflection; the stored record is unchanged. |
| REQ-09 | The system shall return the access-denied response and no reflection content to every other authenticated actor (other students, unassigned advisors, supervisors, UC IT staff), including on a direct request by reflection ID and for IDs that do not exist, in 100% of tested cases. | Must | US-04, AC-02.3, DR-4 | Test: one denied case per actor type plus a nonexistent ID; response bodies are identical. |
| REQ-10 | The system shall give a supervisor no access to reflection content: after a supervisor submits an evaluation, every request by that supervisor for reflection content shall return the access-denied response. | Must | US-03, US-04, DR-5 | Test: submit, then request 3 reflections; Inspection: supervisor service has no read right on reflection data. |
| REQ-11 | The system shall let an advisor list the students currently assigned to them, 20 per page by default and at most 50, before opening a reflection. Opening a reflection still follows REQ-08 and REQ-09. | Should | UC-02 step 1, US-02 | Test: 3 current and 1 ended assignment returns 3; 51 current assignments paginate with no duplicates, default 20 and maximum 50; invalid limits rejected. |
| REQ-12 | The system shall keep every earlier version of a prompt set so that each stored reflection still points to the prompts the student saw. | Should | US-01, AC-01.1 | Test: load a new version, then read a reflection saved under the old one. |
| REQ-13 | The system shall write one log line per access decision, with identifiers and the decision only and no reflection text. | Could | US-04 | Inspection: sample log lines; Test: line count equals decision count in the denial tests. |
| REQ-14 | The system shall retain unsaved answers in the same browser tab after a failed save or expired sign-in and restore them only after the same student signs in again; it shall create 0 server-side drafts and clear the browser draft after successful submission, explicit sign-out, or a different user signs in. | Should | US-01, UC-01 E1 | Test: failed save, expired token and same-user return preserve answers; different-user sign-in, explicit sign-out and successful submit clear them; database contains 0 draft rows. |

REQ-10 is a mandatory privacy invariant even if the optional supervisor submission flow is deferred. REQ- 08/09/11 use the bounded assignment-copy interpretation in section 9; its approval is pending.

## 6. Non-functional requirements

Numerical targets are proposed acceptance criteria, not measured results. The team must ratify them. Source- story mappings added in version 0.3 require verification against User_Stories.md.

| ID | The system shall / measurable condition | MoSCoW | Source story / design citation | Verification method |
| --- | --- | --- | --- | --- |
| NFR-01 | The system shall complete at least 95% of page-load and submit requests in under 2 seconds at 25 concurrent users during a 10-minute steady-state trial after a 1-minute warm-up, with E2 responding within 250 ms. Separately, an E2 timeout shall yield a content-free error within 3 seconds, using a 2-second upstream timeout. These are proposed acceptance targets. | Should | US-01, US-02, US-03; D0 target / D2 NFR-1 (as cited in supplied draft) | Test: record browser elapsed times, workload mix, seed size, environment, failures and p95; inject E2 timeout separately. No slow-request exclusions within either scenario. |
| NFR-02 | The system shall complete 100% of scripted student, advisor and supervisor core-flow steps without blocked controls or horizontal page scrolling in the latest two stable versions of Chrome, Edge, Firefox and Safari available at testing, at 1366 x 768 and 360 x 800 viewports, with no installation. Record tested versions; targets are proposed. | Should | US-01, US-02, US-03; D0 hardware / D2 NFR-2 (draft citation) | Test: run all three flows in each browser; Demonstration on 1 phone. |
| NFR-03 | Reliability (no partial records). The system shall leave 0 partial reflections or evaluations in the database after a failure forced at each step of a save. | Must | US-01, US-03; D2 NFR-3 (draft citation) | Test: fault injection at each step; row counts checked. |
| NFR-04 | Security (confidentiality). The system shall contain reflection and evaluation text in 0 log lines and 0 error responses across the full automated test suite, including every denied and failed case. | Must | US-04 (D2 NFR-4) | Test: search all test logs and responses for seeded marker text. |
| NFR-05 | Reliability (recovery). The system shall take a backup at least every 24 hours, restart a failed service within 5 minutes, and recover 100% of reflections present in the latest backup in a restore trial. | Should | US-01, US-02, US-03; D2 NFR-5 (draft citation) | Test: restore a backup into a clean instance; Demonstration of restart. |
| NFR-06 | The system shall allow at least 4 of 5 student volunteers to complete a seeded reflection from form opening to confirmation in under 15 minutes without help, and at least 4 of 5 advisor volunteers to find and open a named student reflection in under 2 minutes without help. Targets are proposed. | Should | US-01, US-02 | Test: usability session with volunteers (plan task T20). |
| NFR-07 | The system shall use HTTPS for all application traffic and validate E1 token signature, issuer, audience and expiry on every protected API request, returning HTTP 401 with no protected content in 100% of missing, altered, wrong-issuer, wrong-audience or expired-token tests. Proposed expiry allowance: 60 seconds. Only sign-in, callback and public static assets are exempt from token authentication. | Must | US-04 | Test: each invalid-token case, including expiry beyond 60 seconds; inspect HTTPS configuration and verify protected endpoints cannot bypass authentication. |

## 7. MoSCoW summary

Must: 10 of 21 requirements (47.6%). Supervisor evaluations remain Should while supervisor authentication is unresolved. The team must approve this prioritization. - Must: REQ-01, REQ-02, REQ-03, REQ-05, REQ-08, REQ-09, REQ-10, NFR-03, NFR-04, NFR-07. - Should: REQ-04, REQ-06, REQ-07, REQ-11, REQ-12, REQ-14, NFR-01, NFR-02, NFR-05, NFR-06. - Could: REQ-13. Excluding a user deletion feature does not establish indefinite retention. Institutional retention and correction procedures remain unresolved; no administrative access exception is approved here.

## 8. Inherited constraints

From the Project-Constraints.md

| ID | Constraint | Category | Related |
| --- | --- | --- | --- |
| CON-01 | Assessment must be applied consistently: the same criteria for every student, with no unfair or biased evaluation. | Professional | REQ-06 |
| CON-02 | Any sentiment analysis informs advisors only and has no effect on grading. | Professional | Won't list |
| CON-03 | Students are told clearly how their reflections are used and scored. | Professional | REQ-05 |
| CON-04 | An advisor reviews any automated evaluation before it affects a student's standing. | Ethical | REQ-06 |
| CON-05 | The system performs consistently across writing styles and English proficiency, and does not score non-native speakers lower. | Ethical | REQ-06, REQ-02 |
| CON-06 | Data is protected at a level appropriate for student educational records. The essay sizes the pilot at about 25 students. | Security | NFR-04, NFR-07 |
| CON-07 | Individual reflection access is restricted to its student author and currently assigned advisor(s). Advisor-only access in the Week 3 wording is interpreted as restricting other staff, not removing the student-owner access required by US-04; team/advisor confirmation is pending. | Security | REQ-08, REQ-09 |
| CON-08 | Storage location is not final: a UC-managed system or a third-party cloud. | Security | NFR-05, OQ-9 |
| CON-09 | Reflections remain confidential. Research access is outside this release; a future opt-in alone does not bypass access controls or institutional approval. | Security | Won't list |

## 9. Assumptions and open questions

Owners and November 12 dates are proposed implementation follow-ups inherited from the draft, not extensions of the Assignment 9 deadline. The current plan lists October 30, 2026 for Assignment 9.

| Item | Type | Decision / assumption to confirm | Proposed owner | Target date |
| --- | --- | --- | --- | --- |
| 1 | Open question (OQ-1, PR-03) | Confirm supervisor sign-in compatible with NFR-07. Do not implement a one-time-link exception unless approved and reflected in both documents. | Aradana Nair | 2026-11-12 |
| 2 | Open question (OQ-4) | Which rotation numbers are supported and what is the exact prompt wording? Rotations 1 to 3 use placeholders. | Aradana Nair | 2026-11-12 |
| 3 | Open question (OQ-3, PR-01) | How is a mistake in a submitted reflection or evaluation corrected? OQ-3, R8, and EC-22 disagree. PR-01 was due 2026-10-14; no result is recorded in the files. | Priyanshi Kulshrestha | 2026-11-12 |
| 4 | Open question (OQ-6, PR-02) | May students read the supervisor evaluation? D2 says yes, but no student read path exists. REQ-07 avoids the question until decided. | Maria Malik | 2026-11-12 |
| 5 | Open question (OQ-5) | What are the retention and deletion rules? Needs university guidance. | Aradana Nair | 2026-11-12 |
| 6 | Open question (OQ-7) | Does E2 offer a JSON API with the fields needed, and how fast? Assumed yes with a 2-second timeout. | Hinna Parwez | 2026-11-12 |
| 7 | Open question (OQ-8) | What are the wording and version of the data-use notice? | Aradana Nair | 2026-11-12 |
| 8 | Open question (OQ-9) | Will UC IT approve the hosting platform for student data? Demo uses synthetic data until approved. | Priyanshi Kulshrestha | 2026-11-12 |
| 9 | Open question (OQ-10) | When may a supervisor submit? Assumed at end of rotation. | Aradana Nair | 2026-11-12 |
| 10 | Open question | Confirm exclusion of scoring, sentiment analysis and Power BI; reconcile Week 3 scope with D2 rule-based summaries. | Aradana Nair | 2026-11-12 |
| 11 | Open question | The essay sizes the pilot at about 25 students. D1 sizes data for about 500. Which one sets the NFR-01 load? | Hinna Parwez | 2026-11-12 |
| 12 | Open question | Approve all proposed values, including 300-character summaries; evaluation 1-5,000 characters; pagination 20/50; performance workload/timing; browser viewports; backup/restart; usability sample/time; token allowance; draft isolation and assignment-expiry behavior. | Maria Malik | 2026-11-12 |
| 13 | Assumption | A student can have two current advisors, and both can read (OQ-2). | Hinna Parwez | 2026-11-12 |
| 14 | Assumption | Proposed: refresh assignment copy within 15 minutes; reject advisor reads when the copy is older than 15 minutes and E2 refresh fails. Revocation may lag at most 15 minutes. Confirm against D1/D2; this qualifies “current” in REQ-08/09/11. | Hinna Parwez | 2026-11-12 |
| 15 | Assumption | Eligible courses and advisor assignments are supplied by E2 and confirmed with CEAS before implementation (DR-6). | Aradana Nair | 2026-11-12 |
| 16 | Open question | Verify exact D0/D1/D2 filenames, interfaces, eight-table D1 schema and implementation stack; reconcile every proposed specification contract with original files. | Hinna Parwez / Mbathio Lo | 2026-11-12 |
| 17 | Open question | Confirm CON-07 student-owner exception and administrative correction authority without creating an unapproved content-access role. | Aradana Nair | 2026-11-12 |
| 18 | Submission action | Hold and record advisor review, approve priorities and thresholds, resolve traceability gaps, and commit sources/README links before submission. November 12 implementation follow-ups do not satisfy this submission action. | Aradana Nair / all members | Before submission; actual date pending |
