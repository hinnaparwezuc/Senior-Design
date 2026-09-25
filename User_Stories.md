# Assignment 4 - User Stories and Use Cases

**Project:** CEAS Co-op Reflection System  
**Team members:** Maria Malik, Aradana Nair, Mbathio Lo, Priyanshi Kulshrestha, Hinna Parwez  
**Milestone:** Week 04 - Part 1 Draft

## Stakeholder map

| Category | Stakeholder | Need |
| --- | --- | --- |
| Primary | CEAS students, including international CEAS students, completing a co-op rotation | Reflect on accomplishments and challenges at their current experience level and identify a next-rotation goal. |
| Secondary | CEAS co-op advisors | Review students' learning and development to guide advising conversations. |
| Secondary | Co-op workplace supervisors | Provide end-of-rotation observations of student performance. |
| Hidden | UC IT staff responsible for student-data access | Enforce access restrictions so reflection content is disclosed only to authorized people. |

UC IT staff are hidden stakeholders because their access-control needs affect the system even though they do not complete or routinely review reflections.

## User stories

### US-01 (primary): Reflect on a completed rotation

As a **CEAS student completing a co-op rotation**,  
I want to reflect on my accomplishments and challenges using questions appropriate to my completed rotation number,  
so that I can identify what I learned and one improvement goal for my next co-op.

### US-02 (secondary): Review reflection insights

As a **CEAS co-op advisor**,  
I want a summary of an assigned student's accomplishments, challenges, and improvement goal from one submitted co-op reflection,  
so that I can identify topics to address in that student's next advising conversation.

### US-03 (secondary): Submit workplace observations

As a **co-op workplace supervisor**,  
I want to submit an end-of-rotation evaluation of my assigned student's workplace accomplishments and areas for improvement,  
so that the student's co-op advisor can consider workplace observations when assessing the student's development.

### US-04 (hidden): Restrict access to reflection content

As a **UC IT staff member responsible for student-data access**,  
I want access to each student's reflection content restricted to that student and their assigned co-op advisor,  
so that people without an authorized relationship cannot read the student's reflection responses.

## Draft rules and assumptions

These are proposed requirements for team and stakeholder review, not statements of existing university policy.

- Each supported rotation number has a configured prompt set. The team must confirm the supported rotation numbers and prompt wording with CEAS stakeholders. Tests use known rotation-to-prompt mappings.
- Each prompt set requires responses addressing an accomplishment, a challenge, and one improvement goal. A required response must contain at least one non-whitespace character.
- If a rotation number cannot be retrieved, the student may supply a supported rotation number. The reflection records that number as student-supplied; this does not verify it against university records.
- A student's own reflections and an assigned advisor's student reflections are readable under the proposed access rule. Other students, unassigned advisors, workplace supervisors, and IT staff without a separately approved content-access role cannot read those reflections. IT responsibility alone does not grant content access.
- Supervisor evaluations are separate from student reflections; submitting an evaluation does not grant access to the student's reflection content.
- Eligible course enrollment, advisor assignments, the data-use notice, and any additional access roles must be confirmed before implementation. Retention and deletion rules require separate requirements after university guidance is obtained.

## Use cases

### UC-01: Complete a rotation-specific reflection

**Expands:** US-01  
**Primary actor:** CEAS student completing a co-op rotation  
**Secondary actors:** None directly participating. The assigned co-op advisor is a downstream recipient of the submitted reflection.

**Preconditions**

- The student is authenticated through university sign-in and authorized to access the reflection service.
- The student's record indicates enrollment in a course designated as eligible for co-op reflection.
- The student has acknowledged the displayed data-use notice before starting this use case.
- The reflection service is available, and prompt sets are configured for supported rotation numbers.
- No submitted reflection exists for the student's selected co-op placement and rotation.

**Main success flow**

1. **Student:** Starts a reflection for a completed co-op placement.
2. **System:** Retrieves the recorded rotation number and presents the configured prompt set mapped to that number, identifying which responses are required.
3. **Student:** Enters responses to the prompts and requests to review the reflection.
4. **System:** Validates all required responses and presents the entered responses with the placement and rotation number for review.
5. **Student:** Confirms the reflection and submits it.
6. **System:** Saves one submitted reflection linked to the student, placement, and rotation number, and displays a submission confirmation.

**Alternate flow A1: Rotation number unavailable**

Branches from step 2.

1. **System:** Reports that the rotation number could not be retrieved and requests a supported rotation number from the student.
2. **Student:** Supplies a supported rotation number.
3. **System:** Presents the prompt set mapped to that number and records that the number was student-supplied. The flow resumes at main step 3.

**Exception flow E1: Required response missing**

Branches from step 4.

1. **System:** Identifies every required response that is empty or contains only whitespace, retains the entered responses in the current session, and prevents progression to submission.
2. **Student:** Supplies the missing responses and requests review again.
3. **System:** Revalidates the responses. If all required responses are present, the flow resumes at main step 4; otherwise, E1 repeats.

**Postconditions**

- **Success:** One submitted reflection is stored with the student, placement, rotation number, and responses; the student receives confirmation.
- **Exception before correction:** No submitted reflection is created; the entered responses remain available in the current session for correction.

### UC-02: Read an authorized student reflection

**Expands:** US-04  
**Primary actor:** CEAS co-op advisor  
**Secondary actors:** None directly participating. UC IT staff define the access requirement but do not participate in each read request.

**Preconditions**

- The actor is authenticated with an active university account.
- A submitted student reflection exists.
- Advisor-to-student assignments are available to the reflection service.
- The reflection service is available.

**Main success flow**

1. **Advisor:** Requests a submitted reflection belonging to an assigned student.
2. **System:** Checks the actor's advisor role and current assignment to the student, then displays the requested reflection content.

**Alternate flow A1: Student reads their own reflection**

1. **Student:** Requests their own submitted reflection while authenticated.
2. **System:** Confirms that the authenticated student owns the reflection and displays its content.

**Exception flow E1: Actor lacks permission**

1. **Authenticated actor:** Requests another student's reflection without being that student's assigned advisor.
2. **System:** Denies access, returns no reflection response content, and displays an access-denied message.

**Postconditions**

- **Success:** The authorized actor can read the requested reflection; the stored reflection is unchanged.
- **Exception:** No reflection response content is disclosed to the unauthorized actor; the stored reflection is unchanged.

## Given / When / Then acceptance criteria

### UC-01 criteria

**AC-01.1 - Rotation-specific prompts (main flow)**

- **Given** an eligible, authenticated student has acknowledged the data-use notice, has recorded rotation number R, and the configured prompt set for R is P,
- **When** the student starts a reflection for that completed placement,
- **Then** the system presents prompt set P and identifies its required responses.

**AC-01.2 - Completed submission (main flow)**

- **Given** the student has supplied a non-whitespace response to every required prompt and no submitted reflection exists for the selected placement and rotation,
- **When** the student reviews the reflection and confirms submission,
- **Then** the system stores one submitted reflection containing those responses and the student's identifier, placement, and rotation number, and displays a submission confirmation.

**AC-01.3 - Student-supplied rotation (alternate flow)**

- **Given** the student's rotation number cannot be retrieved and supported rotation number R maps to configured prompt set P,
- **When** the student starts the reflection and supplies R when requested,
- **Then** the system presents P and records R as student-supplied for the reflection.

**AC-01.4 - Missing required responses (exception flow)**

- **Given** at least one required response is empty or contains only whitespace,
- **When** the student requests review of the reflection,
- **Then** the system identifies every unanswered required prompt, retains the other entered responses in the current session, prevents progression to submission, and creates no submitted reflection.

### UC-02 criteria

**AC-02.1 - Assigned advisor access (main flow)**

- **Given** an authenticated advisor is currently assigned to student S and S has a submitted reflection,
- **When** the advisor requests that reflection,
- **Then** the system displays the requested reflection content without changing the stored reflection.

**AC-02.2 - Student access (alternate flow)**

- **Given** an authenticated student owns a submitted reflection,
- **When** that student requests the reflection,
- **Then** the system displays its content without changing the stored reflection.

**AC-02.3 - Unauthorized access (exception flow)**

- **Given** an authenticated actor neither owns the requested reflection nor is its owner's currently assigned advisor,
- **When** the actor requests the reflection, including by directly requesting its identifier,
- **Then** the system returns no reflection response content, displays an access-denied message, and leaves the stored reflection unchanged.

## INVEST self-check

| Story | Independent | Negotiable | Valuable | Estimable | Small | Testable |
| --- | --- | --- | --- | --- | --- | --- |
| US-01 | Can be developed using configured prompt sets without summaries or supervisor evaluations. | Prompt wording and interaction design remain open. | Produces a learning record and next-rotation goal. | Estimate after supported rotations and prompt sets are confirmed. | One reflection for one placement and rotation. | UC-01 and AC-01.1 through AC-01.4 define observable outcomes. |
| US-02 | Can use a seeded submitted reflection; no supervisor evaluation is required. | Summary format and generation method remain open. | Prepares an advisor for an advising conversation. | Estimate after summary rules are agreed. | One student's single reflection summary. | Verify that the summary covers the source reflection's accomplishment, challenge, and goal without adding unsupported claims. |
| US-03 | A supervisor evaluation can be submitted without a student reflection. | Evaluation wording and collection method remain open. | Adds workplace observations to advising. | Estimate after supervisor identity and assignment rules are agreed. | One evaluation for one student and rotation. | Verify that submitted observations are stored against the assigned student and rotation and are available to the assigned advisor. |
| US-04 | Access checks can be built and tested using seeded reflections and assignments. | Authorization implementation remains open; the proposed access rule needs stakeholder confirmation. | Prevents unauthorized disclosure of reflection responses. | Estimate after identity and assignment sources are confirmed. | Read access only; retention and deletion are separate. | UC-02 and AC-02.1 through AC-02.3 cover allowed and denied reads. |

The stories are independently testable with fixtures, but share identity, assignment, and reflection data. Estimability remains provisional until the listed rules are confirmed. No story requires a particular UI element.

## Elicitation and remaining validation

This draft is based on the team's supplied requirements. No completed interviews, observations, surveys, or policy reviews are claimed.

Before finalizing requirements, interview at least one CEAS student, one co-op advisor or workplace supervisor, and one UC IT representative about current workflows and pain points. Use a second technique, such as observing the current reflection workflow. Record actual findings in the design notebook or docs/elicitation/ and revise assumptions based on the evidence.
