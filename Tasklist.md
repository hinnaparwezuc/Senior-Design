# Tasklist

Project: CEAS Co-op Reflection System (Team ReFrame)

Team: Priyanshi Kulshrestha, Mbathio Lo, Maria Malik, Aradana Nair, Hinna Parwez

Module names follow D0 to D2: C1 Web Front End, C2 Access Control Service, C3 Reflection Service, C4 Prompt Configuration Store, C5 Summary Generator, C6 Supervisor Evaluation Service, C7 Reflection Database, E1 University Sign-In, E2 University Records, and the glue pieces (sign-in and token check, logging, configuration). Aradana Nair is the project manager.

Twenty-five tasks, five per member. Each statement starts with a verb, names the design module it concerns, and ends with the one member of primary responsibility.

## Tasks

T1. Develop the sign-in and token check glue (E1 University Sign-In login and callback, validate_token, GET /v1/me) and the supervisor sign-in fallback for the mechanism decided with CEAS (OQ-1 and PR03). Owner: Hinna Parwez.

T2. Develop the Access Control Service (C2) access decision (Algorithm 1) with the 15-minute local assignment copy and the E2 University Records check (I8), and its read endpoints (reflection read with ?include=summary, student reflection list, and GET /v1/advisors/me/students). Owner: Hinna Parwez.

T3. Develop the Reflection Service (C3) start, notice acknowledgment, reflection-form, and reflection-checks endpoints (revised I3, with the E2 University Records link I7). Owner: Hinna Parwez.

T4. Develop the Reflection Service (C3) submit endpoint with duplicate handling and error mapping from save_reflection (I9). Owner: Hinna Parwez.

T5. Develop the Summary Generator (C5) rule-based summarize function with the 300-character cut rule (I11). Owner: Hinna Parwez.

T6. Design and create the Reflection Database (C7) schema with eight tables, constraints, indexes, and Alembic migrations, and develop its save_reflection method (Algorithm 2, one transaction, unique pair) with the insert-only role for the Supervisor Evaluation Service (C6). Owner: Mbathio Lo.

T7. Develop the Prompt Configuration Store (C4) tables, versioning, and admin load script (I6), and load the rotation numbers and prompt wording supplied by the project manager. Owner: Mbathio Lo.

T8. Document the final system (API reference for I3 to I5, setup guide, and README) for the Reflection Database (C7) and all services. Owner: Mbathio Lo.

T9. Specify the test plan and curate the seed dataset (students, advisors, placements, and sample reflections) for all components. Owner: Mbathio Lo.

T10. Develop automated integration and end-to-end tests for the student, advisor, and supervisor flows (Flows A, B, and C from the D0 Data-Flow Diagram). Owner: Mbathio Lo.

T11. Investigate the needs of students, advisors, and supervisors and design wireframes for the Web Front End (C1). Owner: Priyanshi Kulshrestha.

T12. Develop the Web Front End (C1) student flow (notice, reflection form, review, submit, and draft kept in sessionStorage). Owner: Priyanshi Kulshrestha.

T13. Develop the Web Front End (C1) advisor screens (student roster, reflection list, and reflection view with summary). Owner: Priyanshi Kulshrestha.

T14. Develop the configuration glue (load_settings, environment variables, and secret store) for all services. Owner: Priyanshi Kulshrestha.

T15. Deploy the services and the Web Front End (C1) to the managed hosting platform with Git deploys, daily backup, and health check. Owner: Priyanshi Kulshrestha.

T16. Develop the Web Front End (C1) supervisor evaluation form and the error and retry screens for all roles. Owner: Maria Malik.

T17. Develop the Supervisor Evaluation Service (C6) evaluations endpoint with the E2 University Records assignment check (I12 and I13). Owner: Maria Malik.

T18. Develop the logging glue (log_event with field allowlist, no reflection text, and rate limit). Owner: Maria Malik.

T19. Test and validate the privacy rules (Access Control Service (C2) denials, no reflection text in logs or errors) against US-04, DR-4, DR-5, and NFR-4. Owner: Maria Malik.

T20. Test and validate the Web Front End (C1) usability with student and advisor volunteers and refine the screens. Owner: Maria Malik.

T21. Maintain the project schedule and Gantt chart covering all components (C1 to C7), updating milestones, the critical path, and slippage each week. Owner: Aradana Nair.

T22. Prepare the meeting deliverables (agendas, minutes, weekly status reports, and the assignment submissions) for the team and the CEAS stakeholders. Owner: Aradana Nair.

T23. Resolve the open questions with CEAS (OQ-1 to OQ-4, including the supervisor sign-in mechanism and the real rotation numbers and prompt wording for the Prompt Configuration Store (C4)) and maintain the open-question and risk log. Owner: Aradana Nair.

T24. Maintain the requirements traceability table and the design change notes for all components (C1 to C7), recording each approved change against the requirement it affects. Owner: Aradana Nair.

T25. Validate the deployed system against the requirements traceability table in a demo with the advisors and revise the plan from their feedback. Owner: Aradana Nair.

## Ownership summary

| Member | Tasks | Scope |
| --- | --- | --- |
| Hinna Parwez | T1 to T5 | Sign-in and token check glue, Access Control Service (C2), Reflection Service (C3), Summary Generator (C5) |
| Mbathio Lo | T6 to T10 | Reflection Database (C7), Prompt Configuration Store (C4), documentation, test plan and seed dataset, automated tests |
| Priyanshi Kulshrestha | T11 to T15 | Web Front End (C1) research and the student and advisor flows, configuration glue, deployment |
| Maria Malik | T16 to T20 | Web Front End (C1) supervisor and error screens, Supervisor Evaluation Service (C6), logging glue, privacy and usability validation |
| Aradana Nair (project manager) | T21 to T25 | Schedule and Gantt chart, meeting deliverables, CEAS open questions and risk log, requirements traceability and design change notes, acceptance demo |

## Sequencing constraints

- T23 must resolve before T7 and the supervisor sign-in fallback in T1 can be built.
- T9 must produce the seed dataset before T10, T19, and T20 can run.
- T14 must land before T15 can deploy.
