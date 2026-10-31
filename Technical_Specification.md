# Technical Specification Document
Project: ReFrame, CEAS Co-op Reflection System Team members: Hinna Parwez, Aradana Nair, Maria Malik, Priyanshi Kulshrestha, Mbathio Lo Course / Assignment: Assignment 9, Requirements Document and Technical Specification Document Project advisors: Dr. David Reeping, Dr. Cedrick Kwuimy


## 1. Version and Change Log

```text
 Version           Date                             Change                              Author

 0.1         2026-10-30         Initial Technical Specification compiled from      Team ReFrame
                                D0, D1, D2, User Stories, Requirements
                                Document v0.2, and the Assignment 5–8
                                planning material. Added architecture
                                summary, interface contracts, technology
                                stack, constraint consequences, and updated
                                traceability using REQ, NFR, and CON
                                identifiers.

This document traces to Requirements Document Version 0.2. If the requirements document
is revised, this document's traceability matrix must be reviewed and the version change
recorded here.




```

## 2. Architecture Summary

ReFrame is a browser-based system for collecting CEAS co-op reflections, allowing assigned advisors to review them, and collecting separate workplace supervisor evaluations. The architecture follows the component structure established in D0 and refined in D1 and D2. The system contains seven internal components, two external university systems, and shared authentication, logging, and configuration functions.

The Web Front End (C1) is the single user-facing interface for students, advisors, and workplace supervisors. Students use it to acknowledge the data-use notice, complete a rotation-specific reflection, review their answers, and submit the reflection. Advisors use it to view their assigned students, select a submitted reflection, and view the reflection with its

rule-based summary. Supervisors use it only to submit an evaluation. C1 does not persist server-side application data. Unsaved student answers remain in browser sessionStorage until submission or sign-out.

The backend is divided into three user-facing services. The Access Control Service (C2) authorizes reflection reads and exposes advisor roster and reflection-read operations. The Reflection Service (C3) handles the student reflection workflow, including eligibility checks, prompt retrieval, validation, notice acknowledgement, and submission. The Supervisor Evaluation Service (C6) validates and stores supervisor evaluations while having no permission to read student reflections.

Supporting these services are the Prompt Configuration Store (C4), Summary Generator (C5), and Reflection Database (C7). C4 stores versioned prompt sets for supported rotation numbers. C5 generates deterministic summaries by copying and truncating student text without paraphrasing or AI generation. C7 is the persistent relational database containing students, placements, reflections, responses, advisor assignments, acknowledgements, and supervisor evaluations.

Two external systems provide authoritative information. E1 University Sign-In authenticates users. E2 University Records provides enrollment, placement, rotation, advisor-assignment, and supervisor-assignment information. Identity supplied by the browser is never trusted directly; authenticated identity is taken from E1.

The primary data flows are:


## 1. Student flow: Student → C1 → C3 → E2/C4 → C7.


## 2. Advisor flow: Advisor → C1 → C2 → C7/E2 → C5.


## 3. Supervisor flow: Supervisor → C1 → C6 → E2 → C7.


## 4. Authentication flow: Browser → E1 → authentication/token-check layer →

C1/C2/C3/C6.

All externally exposed application traffic uses HTTPS. Backend services are implemented as stateless Python/FastAPI components. The current deployment design uses a small Docker-based backend on a managed container platform, a static React front end, and managed PostgreSQL. The final hosting provider remains subject to UC IT approval before real student data is used.

The detailed component relationship and interface IDs remain defined by the D0 architecture diagram. D2's revisions to interfaces I3, I4, and I5 are authoritative where they differ from the original D0 interface descriptions.


## 3. Interfaces and Data Formats


### 3.1 Common API Conventions

The external application API uses REST over HTTPS and JSON request/response bodies.

All API versions are included in the URL path using /v1/.

All timestamps are represented as ISO 8601 UTC values, for example:

None‎ 2026-11-20T15:04:05Z

Identifiers for students, advisors, reflections, evaluations, and acknowledgements use UUIDs where specified by the data model. Placement IDs are strings between 1 and 32 characters.

The standard REST error response is:

None‎ { "error": { "code": "error_code", "message": "User-readable message", "details": {} } }

The details object is optional. Error responses must never contain reflection or evaluation text.

Authentication identity is derived from the E1 sign-in token rather than from request-body identity fields.


### 3.2 REST Endpoint Table

```text
 Compon         Method and Endpoint             Inputs        Success        Error Behavior
   ent                                                         Output

Authenti    GET /auth/login          Optional        302           400
cation                               return_to,      redirect to   invalid_request
                                     restricted to   E1            if return_to points
                                     a path on the                 outside the site
                                     ReFrame
                                     site

Authenti    GET /auth/callback       E1              302           400
cation                               authorization   redirect to   invalid_state;
                                     code and        ReFrame       403
                                     state           and secure    sign_in_denied;
                                                     token
                                                                   502
                                                     cookie
                                                                   sign_in_unavail
                                                                   able

Authenti    POST /auth/logout        None            204; token    No application error
cation                                               cookie
                                                     cleared

Authenti    GET /v1/me               Secure token Roles,           401
cation                               cookie       student_         not_signed_in;
                                                     id and/or     503
                                                     advisor_      records_unavail
                                                     id, and       able for a first-time
                                                     display_      user when E2
                                                     name          cannot be reached

C3          GET                      placement       Placement,    401
Reflectio   /v1/placements/{placem   _id; optional   rotation,     not_signed_in;
n           ent_id}/reflection-for   rotation_       rotation      403
Service                                              source,
            m                        number only                   not_eligible;
                                                     prompt-set
                                     when E2 has
                                                     version,      409
                                     no rotation                   reflection_alre
                                                     prompts,
                                                     notice        ady_submitted;
                                                     version       422
                                                     and           rotation_number
                                                     acknowled
                                                                   _needed; 422
                                                     gement
                                                                   unsupported_rot
                                                     status
                                                                   ation; 503
                                                                   records_unavail

                                                                able; 503
                                                                prompts_unavail
                                                                able; 500
                                                                internal_error

C3          POST                     notice_ve     201 and      400
Reflectio   /v1/notice-acknowledgm   rsion         acknowle     invalid_request;
n           ents                                   dged_at;     401
Service
                                                   repeat       not_signed_in;
                                                   acknowled    422
                                                   gement       unknown_notice_
                                                   returns
                                                                version; 503
                                                   200 and
                                                                save_failed
                                                   original
                                                   timestamp

C3          POST                     rotation_     200          401
Reflectio   /v1/placements/{placem   number,       {"valid"     not_signed_in;
n           ent_id}/reflection-che   prompt_se     : true};     403
Service
            cks                      t_version,    no           not_eligible;
                                     responses     reflection   422
                                     []            stored       invalid_respons
                                                                e; 422
                                                                unsupported_rot
                                                                ation; 422
                                                                prompt_set_not_
                                                                found; 503
                                                                records_unavail
                                                                able

C3          POST                     Same          201,         400
Reflectio   /v1/placements/{placem   validated     reflecti     invalid_request;
n           ent_id}/reflections      reflection    on_id,       401
Service                              payload
                                                   submitte     not_signed_in;
                                     used by the
                                     checks        d_at         403
                                     endpoint                   not_eligible;
                                                                409
                                                                reflection_alre
                                                                ady_submitted;
                                                                422

                                                                  invalid_respons
                                                                  e; 422
                                                                  unsupported_rot
                                                                  ation; 422
                                                                  notice_required;
                                                                  503
                                                                  records_unavail
                                                                  able; 503
                                                                  save_failed; 500
                                                                  internal_error

C2        GET                      Optional        Total count, 400
Access    /v1/advisors/me/studen   limit 1–50,     roster_s invalid_request;
Control   ts                       default 20;     tale,        401
                                   optional        checked_       not_signed_in;
                                   offset,         at, and        403
                                   default 0       assigned       not_an_advisor;
                                                   students       503
                                                                  backend_unavail
                                                                  able

C2        GET                      student_i       Submitted      400
Access    /v1/students/{student_   d; optional     reflection     invalid_request;
Control   id}/reflections          limit and       list, newest   401
                                                   first
                                   offset;                        not_signed_in;
                                   authorization                  identical 403
                                   token                          access_denied for
                                                                  unauthorized/nonexi
                                                                  stent students; 503
                                                                  backend_unavail
                                                                  able; 500
                                                                  internal_error

C2        GET                      Reflection      Reflection     400
Access    /v1/reflections/{refle   UUID;           and            invalid_request;
Control   ction_id}                optional        responses;     401
                                   include=s       evaluation
                                                                  not_signed_in;
                                   ummary          only when
                                                   authorized;    identical 403
                                                                  access_denied for

                                                           optional    forbidden and
                                                           summary     nonexistent IDs; 503
                                                                       backend_unavail
                                                                       able; 500
                                                                       internal_error

 C6            POST                         rotation_      201,        400
 Evaluatio     /v1/placements/{placem       number,        evaluati    invalid_request;
 n             ent_id}/evaluations          accomplis      on_id,      401
 Service
                                            hments,        submitte    not_signed_in;
                                            improveme      d_at        403
                                            nt_areas                   not_assigned_su
                                                                       pervisor; 409
                                                                       duplicate_evalu
                                                                       ation; 422
                                                                       invalid_evaluat
                                                                       ion; 422
                                                                       placement_not_s
                                                                       tarted; 503
                                                                       records_unavail
                                                                       able; 503
                                                                       save_failed; 500
                                                                       internal_error


```

### 3.3 Reflection Submission Format

The student reflection payload has the following logical JSON shape:

None‎ { "rotation_number": 2, "prompt_set_version": 1, "responses": [ { "prompt_id": "Q1", "text": "Student response" }, {

"prompt_id": "Q2", "text": "Student response" } ] }

Each prompt_id must correspond to the active prompt-set version. A response must contain at least one non-whitespace character and may contain at most 5,000 characters.

When E2 already provides the rotation number, C3 uses the E2 value and does not allow the browser to override it. When E2 has no rotation number, C3 may accept a supported student-supplied value and records its source as student_supplied.

C3 adds the prompt category before calling the database save operation so the stored response records carry accomplishment, challenge, or goal.


### 3.4 Supervisor Evaluation Format

None‎ { "rotation_number": 2, "accomplishments": "Supervisor observations", "improvement_areas": "Areas for improvement" }

Both text fields must contain between 1 and 5,000 characters and at least one non-whitespace character.

C6 determines the supervisor identity from authentication and verifies the supervisor-to-placement relationship with E2. The browser does not supply the student identity.


### 3.5 Internal Component Interfaces

```text
 Boundary           Inputs          Outputs         Protocol / Format        Error Behavior

C3 → C4   Rotation           Rotation          In-process Python        unsupported_ro
          number;            number,           interface backed by      tation,
          optional           version,          read-only PostgreSQL     version_not_fo
          prompt-set         ordered           access
                                                                        und,
          version            prompts with
                             category, text,                            config_incompl
                             required flag,                             ete,
                             and display                                store_unavaila
                             order                                      ble

C2 → C5   Reflection         Accomplishme      In-process Python call   invalid_input;
          responses and      nt, challenge,                             summary_failed.
          optional           goal,                                      C2 still returns the
          supervisor         truncation                                 full reflection when
          evaluation         flags, optional                            summary
                             supervisor                                 generation fails
                             observations

C3 → C7   Student ID,        Reflection        SQLAlchemy/PostgreS invalid_respon
          placement ID,      UUID and          QL transaction      se,
          rotation           submission                                 invalid_rotati
          number/source      timestamp
                                                                        on,
          , validated
          responses                                                     not_authorized,
                                                                        duplicate_refl
                                                                        ection,
                                                                        save_failed

C2 → C7   Actor identity,    Reflection        Parameterized            Database failure
          student/reflecti   records,          SQLAlchemy queries       returns no
          on IDs, advisor    response                                   protected content
          assignments        records,                                   and maps to
                             assignment                                 backend_unavai
                             data                                       lable

C6 → C7   Verified           Evaluation        Insert-only              Duplicate →
          placement,         UUID and          PostgreSQL role          duplicate_eval
          rotation,          timestamp                                  uation; database
          supervisor                                                    failure →
          identity,                                                     save_failed; no
          evaluation text
                                                                        partial record

 C3 → E2        Authenticated      Eligibility,       HTTPS/JSON through    Two-second
                university ID      placement          E2 wrapper; exact     timeout;
                and placement      ownership,         university endpoint   unavailable data
                reference          recorded           remains to be         maps to
                                   rotation           confirmed             records_unavai
                                                                            lable

 C2 → E2        Advisor            Current            HTTPS/JSON through    Fresh local
                identity and       advisor-assign     E2 wrapper            assignment may be
                student            ment decision                            used; otherwise an
                reference                                                   unavailable E2
                                                                            response results in
                                                                            denied reflection
                                                                            access

 C6 → E2        Supervisor         Supervisor-ass     HTTPS/JSON through    Failure to confirm
                reference,         ignment            E2 wrapper            assignment returns
                placement,         decision                                 records_unavai
                rotation                                                    lable; evaluation
                                                                            is not stored

 All            Secure token       Verified           E1 OpenID Connect     Invalid, altered,
 protected      cookie             university         token verification    expired, or
 APIs →                            identity/display                         wrong-audience
 authenticati                      name                                     token becomes
 on                                                                         401
                                                                            not_signed_in

 All backend    Event name         One JSON log       JSON Lines to         Logging failure
 component      and allowlisted    line               standard output       does not fail the
 s → logging    identifiers/stat                                            user request
                us fields

 All backend    Environment        Validated          Startup-time          config_missing
 component      variables and      settings object    in-process            or
 s→             platform                              configuration         config_out_of_
 configuratio   secrets                                                     range prevents the
 n
                                                                            affected service
                                                                            from starting

The E2 JSON API is currently an architectural assumption. If the university provides a batch file
rather than an online JSON API, the E2 wrapper and local-copy mechanism will be changed
without changing the C1 user-facing contracts.

```

### 3.6 Persistent Data Model

C7 uses PostgreSQL and contains eight primary application tables:

```text
             Table                              Purpose / Important Fields

 STUDENT                    student_id PK, unique university identifier, display name

 ADVISOR                    advisor_id PK, unique university identifier, display name

 ADVISOR_ASSIGNMEN          Links advisor and student; start date, optional end date, last E2
 T                          check time

 PLACEMENT                  Placement ID, student ID, employer and placement dates

 REFLECTION                 Reflection ID, student ID, placement ID, rotation number, rotation
                            source, submitted timestamp; unique placement/rotation
                            combination

 REFLECTION_RESPON          Response ID, reflection ID, prompt ID, category, response text
 SE

 SUPERVISOR_EVALUA          Evaluation ID, placement ID, rotation number, supervisor
 TION                       reference, accomplishments, improvement areas, submission
                            timestamp; unique placement/rotation combination

 ACKNOWLEDGMENT             Student ID, notice version, acknowledgement timestamp; unique
                            student/version combination

The prompt configuration data is kept in C4 using:

     Table                                Purpose / Important Fields

 PROMPT_S       One row per supported rotation number with its current prompt-set version
 ET

 PROMPT         Prompt ID, rotation number, set version, category, text, display order, required
                flag

Prompt rows are not edited after use. A wording change creates a new version and new prompt
rows, preserving the prompts associated with older reflections.

Reflection submission uses a single database transaction. The REFLECTION row and every
associated REFLECTION_RESPONSE row either commit together or roll back together. A unique
database constraint prevents two reflections from being stored for the same placement and
rotation.




```

### 3.7 Summary Format

C5 creates summaries at request time and does not persist them.

For each category—accomplishment, challenge, and goal—the generator joins the student's corresponding responses. If the result exceeds the configured summary limit of 300 characters, the generator cuts at the final sentence boundary at or before the limit when possible. Otherwise, it cuts at a word boundary and appends an ellipsis.

C5 does not reword, score, interpret, or paraphrase student text. If summary generation fails, C2 returns the complete reflection and reports the summary as unavailable.


### 3.8 Logging and Configuration Format

Logs are emitted as one JSON object per line to standard output.

Allowed fields include:

```text
   ● request_id
   ● actor_university_id
   ● reflection_id
   ● student_id
   ● placement_id
   ● decision
   ● reason
   ● status
   ● duration_ms
   ● error_code

Reflection text, evaluation text, authentication tokens, and cookies are explicitly excluded.

Important application settings include:

             Setting                      Default / Rule

 E2_BASE_URL                      Required HTTPS URL

 E2_TIMEOUT_SECONDS               2 seconds

 ASSIGNMENT_FRESHNESS_M           15 minutes
 INUTES

 PROMPT_CACHE_MINUTES             10 minutes

 MAX_RESPONSE_CHARS               5,000

 SUMMARY_FIELD_CHARS              300

 NOTICE_VERSION                   Required

 TOKEN_SKEW_SECONDS               60 seconds

 DATABASE_URL                     Required secret

 E1_CLIENT_ID                     Required secret

 E1_CLIENT_SECRET                 Required secret


Configuration is loaded and validated during service startup. A missing or invalid required
setting prevents that instance from starting.




```

## 4. Technology Stack

```text
       Layer                  Choice                              Justification

 Front end           React with Vite              React supports the student, advisor, and
                                                  supervisor browser flows while remaining
                                                  deployable as static files. This supports
                                                  REQ-02, REQ-11, REQ-14, NFR-02, and
                                                  NFR-06.

 Backend             Python 3 with FastAPI        FastAPI supports typed REST endpoints,
                                                  asynchronous E2 calls, and lightweight
                                                  services. Pydantic validation supports
                                                  measurable input limits in REQ-02, REQ-07,
                                                  NFR-01, and NFR-07.

Request           Pydantic                  Validates API types, lengths, required fields,
validation                                  and numeric ranges before service logic runs.
                                            Supports REQ-01, REQ-02, REQ-07, and
                                            NFR-03.

Database          PostgreSQL                Transactions and unique constraints directly
                                            enforce save-once and all-or-nothing
                                            persistence. Supports REQ-03, REQ-07,
                                            REQ-12, NFR-03, NFR-05, and CON-06.

Database access   SQLAlchemy 2.x            Provides parameterized queries and
                                            transaction management rather than
                                            handwritten SQL. Supports REQ-03, REQ-08,
                                            REQ-09, and NFR-03.

Schema            Alembic                   Provides tracked, repeatable database
migration                                   schema migrations as the design evolves.
                                            Supports REQ-03, REQ-12, and
                                            maintainability of C7.

PostgreSQL        psycopg 3; asyncpg is a   Provides Python/PostgreSQL connectivity
driver            possible fallback         while keeping database behavior within the
                                            selected relational design. Supports C7
                                            implementation.

Authentication    E1 University Sign-In     Avoids building a separate password system
                  using OpenID              and makes university identity authoritative.
                  Connect/Authlib when      Supports REQ-05, REQ-08, REQ-09,
                  supported by UC           NFR-07, CON-06, and CON-07.

External HTTP     httpx                     Provides HTTPS requests, timeouts, and
integration                                 reusable E2 client behavior. Supports the
                                            two-second E2 timeout in NFR-01 and E2
                                            verification in REQ-05, REQ-07, REQ-09, and
                                            REQ-11.

Browser draft     sessionStorage            Keeps unsaved text out of the server
storage                                     database while allowing restoration within the
                                            browser session. Supports REQ-14 and
                                            NFR-03.

Summary           Custom deterministic      Copies student text rather than generating
generation        Python function           new content, which avoids AI inference and
                                            supports REQ-06, CON-01, CON-02,
                                            CON-04, and CON-05.

 Automated             pytest                       Fixtures support seeded students, advisors,
 testing                                            placements, reflections, authorization cases,
                                                    and database failure cases. Supports the
                                                    verification methods attached to all REQ and
                                                    NFR rows.

 Containerization      Docker                       Keeps the backend environment reproducible
                                                    and supports deployment to a managed
                                                    container platform. Supports NFR-05 and
                                                    CON-08.

 Hosting               Managed container            Minimizes infrastructure maintenance while
                       platform with Git            supporting health checks, secrets, HTTPS,
                       deployments; final           and backups. Production use remains
                       provider TBD                 blocked until UC IT approves the provider
                                                    under CON-06 and CON-08.

 Secrets               Managed platform secret      Keeps database credentials and E1 client
                       store                        credentials out of source control and logs.
                                                    Supports NFR-07 and CON-06.

The system requires no specialized hardware and no message queue. Services communicate
using HTTPS or in-process/database interfaces.




```

## 5. Known Constraints

The following constraints are inherited directly from the Requirements Document. Each item states the technical consequence of the constraint.

```text
   ID               Constraint                           Technical Consequence

 CON-      Assessment must be            The current release uses one deterministic summary
 01        applied consistently using    rule for all students and does not use per-student
           the same criteria for every   scoring logic. This rules out individualized AI scoring
           student.                      behavior.

 CON-      Sentiment analysis may        Sentiment analysis is not included in this release. No
 02        inform advisors only and      grading or standing decision is generated by the system.
           may not affect grading.       If sentiment analysis is introduced later, it cannot write a
                                         grade or automatically change standing.

 CON-    Students must be clearly     The system uses a versioned data-use notice. C3 will
 03      told how reflections are     not permit reflection submission until the current notice
         used and scored.             has been acknowledged.

 CON-    Any automated evaluation Automated evaluation is excluded from this release. Any
 04      must receive advisor      later scoring feature would require a human-review state
         review before affecting a before its output could affect a student.
         student's standing.

 CON-    The system must behave       Reflection validation checks completeness, supported
 05      consistently across          prompts, and length only. The system does not score
         writing styles and English   grammar, fluency, tone, or English proficiency. C5 only
         proficiency.                 copies source text.

 CON-    Student data must be         The design requires university authentication, HTTPS,
 06      protected at a level         authorization before content reads, no reflection text in
         appropriate for              logs/errors, managed secrets, backups, and an
         educational records.         approved hosting environment. Real student data is not
                                      placed on an unapproved demo host.

 CON-    Individual reflection and    C2 checks a current advisor assignment before
 07      analysis access is limited   returning advisor-side reflection content. A general
         to the advisor responsible   advisor role, supervisor role, or IT role does not grant
         for that student.            content access. The student can still read their own
                                      reflection under REQ-08.

 CON-    Final storage location       Hosting-specific logic is kept outside the business rules.
 08      may be UC-managed or         The managed hosting provider remains undecided, and
         an approved third-party      production student data is blocked until UC IT approval.
         cloud system.

 CON-    Reflections remain           This release contains no research export, research
 09      confidential until the       dashboard, or secondary-use workflow. Reflection data
         student opts into research   remains inside the operational system.
         or similar use.


```

### 5.1 Open Technical Dependencies

The following issues remain dependencies rather than new design decisions:

- Supervisor authentication: E1 may not directly support workplace supervisors. The
system assumes a verified supervisor reference supplied through an approved CEAS login or one-time-link mechanism.

- E2 interface: The design currently assumes an HTTPS/JSON university-records
interface with a two-second timeout. If E2 instead provides a scheduled file, the E2 wrapper/local copy must be revised.
- Supported rotation numbers and prompt wording: C4 supports versioned
configuration, so the final CEAS-provided values can be loaded without changing the services.
- Hosting approval: The demo may use synthetic data on a managed platform, but
production student data requires UC approval.
- Retention/deletion: No retention rule has been provided. The current release therefore
contains no user-facing delete operation and does not automatically delete records.
- Supervisor evaluation visibility: The current design makes evaluation data available to
an assigned advisor but does not add a supervisor read endpoint.
- Notifications: Email and other submission notifications are not implemented for this
release.


## 6. Traceability Matrix

The test IDs below are the planned IDs for the Week 10 test plan. They should remain stable when the detailed test procedures are written.

```text
 Req               Description                   Design Component             Status    Test
  ID                                                 (D0/D1/D2)                         Case

 REQ-    Present the configured prompt      C3 reflection-form; C4            Designe   TC-0
 01      set for a supported rotation and   PROMPT_SET/PROMPT; D2             d         01
         reject unsupported/incomplete      Figure 1
         sets

 REQ-    Identify all blank required        C1 form state; C3                 Designe   TC-0
 02      answers and prevent storage        reflection-checks; D2 Figure 1;   d         02
                                            C7 validation

 REQ-    Store exactly one reflection per   C7 REFLECTION; D1 Algorithm       Designe   TC-0
 03      placement/rotation                 2; unique constraint and          d         03
                                            transaction

 REQ-    Allow supported                    C3 rotation state;                Designe   TC-0
 04      student-supplied rotation only     rotation_source; D2 Figure        d         04
         when E2 has none                   1

REQ-   Require eligible enrollment,         E1 authentication; C3; E2 I7;     Designe   TC-0
05     placement ownership, and             ACKNOWLEDGMENT                    d         05
       current notice
       acknowledgement

REQ-   Provide a rule-based advisor         C5 summarize; C2                  Designe   TC-0
06     summary with 300-character           include=summary; D2 I11           d         06
       limit and full-reflection fallback

REQ-   Accept one                           C6 evaluation endpoint; E2 I12;   Designe   TC-0
07     assigned-supervisor evaluation       C7 SUPERVISOR_EVALUATION          d         07
       with validated fields

REQ-   Permit the student owner and         C2 Algorithm 1; C7                Designe   TC-0
08     assigned advisor to read a           REFLECTION;                       d         08
       reflection                           ADVISOR_ASSIGNMENT

REQ-   Deny every other actor with the      C2 Algorithm 1; identical 403     Designe   TC-0
09     same no-content response             access_denied; D2                 d         09
                                            EC-05–EC-08

REQ-   Give supervisors no                  C6 insert-only role; no C6 read   Designe   TC-0
10     reflection-read access               endpoint; C2 access rule          d         10

REQ-   Provide paginated                    C2 GET                            Designe   TC-0
11     assigned-student roster              /v1/advisors/me/student           d         11
                                            s; local assignment copy

REQ-   Preserve earlier prompt-set          C4 versioned                      Designe   TC-0
12     versions                             PROMPT_SET/PROMPT;                d         12
                                            response prompt_id; D2 R5

REQ-   Log access decisions without         Logging glue; log_event field     Designe   TC-0
13     reflection content                   allowlist                         d         13

REQ-   Keep unsaved answers in              C1 sessionStorage;                Designe   TC-0
14     browser session across               authentication retry flow; D2     d         14
       recoverable failures                 EC-02/EC-30

NFR-   95% of requests under two            Indexed C7 queries; FastAPI;      Designe   TC-0
01     seconds at specified load,           prompt cache; two-second E2       d         15
       excluding delayed E2 calls           timeout

NFR-   Support required browsers and        C1 React/Vite responsive web      Designe   TC-0
02     360-pixel-wide phone viewport        UI                                d         16

NFR-   Leave zero partial records after   D1 Algorithm 2; PostgreSQL          Designe   TC-0
03     save failure                       transaction; no server-side         d         17
                                          drafts

NFR-   Keep reflection/evaluation text    Logging allowlist; standard error   Designe   TC-0
04     out of logs and error responses    envelope; C2 read-after-allow       d         18
                                          rule

NFR-   Daily backup, service restart,     Managed hosting; C7                 Planned   TC-0
05     and restore of latest backed-up    PostgreSQL backup;                            19
       reflections                        health-check/restart plan

NFR-   Meet student and advisor           C1 student/advisor screens;         Planned   TC-0
06     usability task-time thresholds     task T20 usability validation                 20

NFR-   Require HTTPS and valid E1         E1 sign-in/token-check glue;        Designe   TC-0
07     token for protected requests       HTTPS deployment; secure            d         21
                                          cookie

CON-   Apply assessment consistently      C5 deterministic rule-based         Designe   TC-0
01                                        summary; no variable scoring        d         22

CON-   Sentiment analysis cannot          D2 R3; feature excluded from        Designe   TC-0
02     affect grading                     current release                     d         23

CON-   Clearly communicate data           Versioned notice; C3 notice         Designe   TC-0
03     use/scoring                        acknowledgement;                    d         24
                                          ACKNOWLEDGMENT

CON-   Require human review before        No automated evaluation in          Designe   TC-0
04     automated evaluation affects       current release; future             d         25
       standing                           human-review gate required

CON-   Do not disadvantage writing        C3 content validation only; C5      Designe   TC-0
05     styles or English proficiency      copy-only summary; no               d         26
                                          language scoring

CON-   Protect student educational        C2 authorization; E1; HTTPS;        Designe   TC-0
06     data                               logging rules; secret store;        d         27
                                          approved hosting

CON-   Restrict advisor-side content to   C2 Algorithm 1;                     Designe   TC-0
07     responsible advisor                ADVISOR_ASSIGNMENT; E2              d         28
                                          assignment verification

 CON-    Finalize approved                  D1 hosting model; D2 OQ-9;        Planned    TC-0
 08      storage/hosting location           managed platform abstraction                 29

 CON-    Keep reflections confidential      No research/export component;     Designe    TC-0
 09      unless student opts into           current-release Won't scope       d          30
         research use

        There are no blank Design Component cells. The only items marked Planned are those
that depend on deployment selection or later usability/recovery validation rather than missing
architecture.




```

## 7. Advisor Review Record

Review status: The source design documents available when this specification was prepared do not contain a completed advisor-review record for this Assignment 9 draft. The fields below must be filled using the team's actual advisor meeting notes before final submission. Peer design review from D2 should not be reported as advisor review.

```text
               Item                                         Record

 Advisor(s)                         Dr. David Reeping; Dr. Cedrick Kwuimy

 Review date

 Documents reviewed                 Requirements_Document.md v0.2 and
                                    Technical_Specification.md v0.1

 Topics discussed


 Feedback received

 Changes produced


 Change-log version
 containing those changes




```

## 8. Specification Summary

This specification implements the requirements baseline through a stateless browser-and-service architecture centered on privacy and traceability. React provides the browser interface; Python/FastAPI provides the service layer; PostgreSQL stores persistent application data; E1 supplies authentication; and E2 supplies authoritative university relationships.

The design deliberately avoids AI-written summaries, automated scoring, server-side drafts, supervisor reflection access, research use, and notification features in this release. Reflection content is read only after authorization succeeds, saves are transactional, duplicates are prevented by database constraints, and sensitive text is excluded from logs and error responses.

The remaining external dependencies—supervisor authentication, exact E2 integration, final prompt content, retention guidance, and production hosting approval—are documented as open dependencies rather than silently assumed to be complete.
