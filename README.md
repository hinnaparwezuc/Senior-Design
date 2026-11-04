# ReFrame — CEAS Co-op Reflection System

**Senior Design Project | University of Cincinnati**

ReFrame is a user-centered web application designed to enhance experiential learning through structured co-op reflections, personalized insights, and intuitive design.

The project aims to help students reflect on their cooperative education experiences while providing authorized academic advisors with information to support student learning and professional development.

## 1. Project Overview

ReFrame is being developed to support the University of Cincinnati College of Engineering and Applied Science (CEAS) co-op reflection process.

The proposed system allows students to document accomplishments, challenges, and goals following their co-op rotations. Assigned advisors can review submitted reflections and summaries to support meaningful advising conversations.

### Project Objectives

- Provide a structured reflection process for students completing co-op rotations.
- Support rotation-specific reflection prompts.
- Allow students to review and submit their responses.
- Enable authorized advisors to access student reflections.
- Support separate supervisor evaluations as a planned feature.
- Protect student information through authentication, authorization, and secure data handling.
- Improve usability and accessibility across supported browsers and devices.

## 2. Project Team and Development

### Team Members

- Hinna Parwez
- Aradana Nair
- Maria Malik
- Priyanshi Kulshrestha
- Mbathio Lo

### Faculty Advisors

- Dr. David Reeping
- Dr. Cedrick Kwuimy

### Project Structure

| Directory | Purpose |
|---|---|
| `frontend/` | Web application user interface |
| `backend/` | Server-side application development |
| `Design_Diagrams/` | System architecture and detailed design diagrams |
| `Planning/` | Project planning and milestone documentation |
| `Professional Biography/` | Team member professional biographies |

The project follows a requirements-driven development process. Functional requirements, technical specifications, test cases, and project milestones are documented in this repository.

## 3. Project Documentation

The following documents provide the project's scope, requirements, design, planning, and testing information.

| Document | Description |
|---|---|
| [Project Description](Project-Description.md) | Project background, purpose, and objectives |
| [Project Constraints](Project-Constraints.md) | Technical, professional, ethical, and security constraints |
| [User Stories](User_Stories.md) | Stakeholder needs and expected interactions |
| [Requirements Document](Requirements_Document.md) | Functional and non-functional requirements with requirement IDs |
| [Technical Specification](Technical_Specification.md) | System architecture, interfaces, design decisions, and requirements traceability |
| [Test Plan](Test_Plan.md) | Verification and validation strategy, measurable success criteria, test cases, environments, and acceptance conditions |
| [Task List](Tasklist.md) | Project activities and task tracking |

### Design Documentation

The `Design_Diagrams/` directory contains the project's system design materials, including high-level architecture and detailed component designs.

### Verification and Validation

The [Test Plan](Test_Plan.md) describes how the team intends to verify that ReFrame meets its documented requirements and validate that it addresses stakeholder needs.

The plan includes:

- In-scope and out-of-scope requirements.
- Verification and validation approaches.
- Measurable success criteria.
- Requirement-linked test cases.
- Development, staging, and pilot test environments.
- Objective pass and fail criteria.
- Testing milestones and responsibilities.
- Feature-level and project-level definitions of done.

Test cases are linked to requirement IDs in the Week 9 documentation. Testing results will be recorded during implementation and evaluation.

## 4. System Design

ReFrame's planned architecture includes the following logical components:

1. **Web Front End (C1):** Student, advisor, and supervisor user interfaces.
2. **Access Control Service (C2):** Authorization and controlled access to student records.
3. **Reflection Service (C3):** Reflection validation and submission.
4. **Prompt Configuration Store (C4):** Rotation-specific reflection prompts.
5. **Summary Generator (C5):** Extractive summaries of student reflections.
6. **Supervisor Evaluation Service (C6):** Supervisor evaluation submission and management.
7. **Reflection Database (C7):** Storage of reflection records and related information.

The system is also designed to integrate with university sign-in and student-record services, subject to technical and institutional approval.

## 5. Testing and Quality Assurance

The project uses a planned multi-layer testing approach:

| Testing Layer | Focus |
|---|---|
| Unit Testing | Individual functions, validation rules, and access decisions |
| Integration Testing | Communication between application components and services |
| System Testing | Complete student, advisor, and supervisor workflows |
| Acceptance Testing | Stakeholder review and usability evaluation |
| Non-Functional Testing | Performance, reliability, security, and browser compatibility |

Security, access control, and data integrity are prioritized because the application handles student educational information.

**Current testing status:** The test plan has been drafted. Test cases are currently marked *Not Run*, and execution results will be documented during the implementation and testing phase.

For the detailed test procedures, see [Test_Plan.md](Test_Plan.md).

## 6. Project Status and Next Steps

ReFrame is in the design and planning phase, with implementation and validation activities planned for subsequent development work.

Key next steps include:

- Confirming outstanding requirements and design decisions with project advisors.
- Finalizing technical integration and deployment decisions.
- Implementing the documented system components.
- Executing the test plan during CS 5002.
- Recording defects, test evidence, and acceptance results.
- Preparing the Final Design Report.

Requirements, technical contracts, and acceptance targets marked as proposed remain subject to team and advisor review.

## 7. Appendix — Repository Document Index

### A. Project Planning and Requirements

- [Project Description](Project-Description.md)
- [Project Constraints](Project-Constraints.md)
- [User Stories](User_Stories.md)
- [Requirements Document](Requirements_Document.md)
- [Task List](Tasklist.md)
- [Planning Folder](Planning/)

### B. Technical Design

- [Technical Specification](Technical_Specification.md)
- [Design Diagrams](Design_Diagrams/)

### C. Verification and Validation

- [Test Plan](Test_Plan.md)

The Test Plan supports the Final Design Report (Assignment #10) and establishes the planned verification and validation activities for CS 5002.

### D. Development

- [Frontend](frontend/)
- [Backend](backend/)

---

**Project:** ReFrame — CEAS Co-op Reflection System

**Institution:** University of Cincinnati

**Course:** Senior Design — CS 5001 / CS 5002
