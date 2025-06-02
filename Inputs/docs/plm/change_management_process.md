# Change Management Process for gRiTOS

## 1. Introduction

### 1.1. Purpose
This document defines the standardized Change Management (ChM) process for the gRiTOS project. Its purpose is to ensure that all changes to established baselines (including requirements, design, code, tests, documentation, and environments) are formally proposed, evaluated for impact (technical, schedule, cost, safety, security), approved or rejected by appropriate authorities, implemented in a controlled manner, and verified. This process aims to minimize disruption, maintain product integrity, and ensure traceability of all changes.

This Change Management Process is governed by the overall gRiTOS [Project Management Plan](./project_management_plan.md), particularly Section 5.8 which outlines its role in managing changes to baselined project artifacts.


### 1.2. Scope
This process applies to all Configuration Items (CIs) managed under the gRiTOS Configuration Management Plan (SCMP) once they are baselined. It covers both product-related changes (e.g., software, hardware configurations) and project document changes (e.g., plans, specifications). Minor, non-baselined iterative changes during early component development (e.g., within an Agile sprint before a component is first baselined) may follow a more lightweight internal team process, but any change affecting an established baseline or an interface to other components must follow this formal ChM process.

### 1.3. Audience
- All gRiTOS project members (engineering, QA, PM)
- Change Control Board (CCB)
- Stakeholders impacted by or requesting changes

### 1.4. Referenced Documents
- `../../plan/gritOS Development Plan.md`
- `./Configuration Management Plan.md` (SCMP)
- `./Quality Management Plan.md` (QMP)
- `./Risk Management Plan.md`
- `../process/Development Model Overview.md`

### 1.5. Definitions and Acronyms
- **Change Request (CR):** A formal proposal for a change to be made to a baselined CI.
- **Change Control Board (CCB):** The group of stakeholders responsible for reviewing CRs, evaluating their impact, and approving or rejecting them.
- **Impact Analysis:** The assessment of the potential consequences of a proposed change.
- **Baseline:** A formally reviewed and agreed-upon set of CIs.
- **Configuration Item (CI):** Any artifact managed under the SCMP.

## 2. Change Management Roles and Responsibilities

### 2.1. Change Requestor
Any project team member or stakeholder can submit a Change Request. The requestor is responsible for clearly describing the proposed change, its justification, and providing any initial assessment of urgency or impact.

### 2.2. Configuration Manager (CM) / CM Officer
- Administers the CR tracking system.
- Facilitates the ChM process, ensuring CRs are processed according to this document.
- Supports CCB meetings (scheduling, minutes, communication of decisions).
- Tracks and reports on CR status.
- Ensures CR traceability.

### 2.3. Technical Assessors / Impact Analysts
- Typically Engineering Leads (Architecture, Software, Hardware, Test, Safety, Security) or other Subject Matter Experts (SMEs).
- Responsible for conducting a thorough impact analysis of proposed changes within their domain. This includes assessing effects on:
    - Technical feasibility and design integrity.
    - Source code, interfaces, and other CIs.
    - Test cases and testability.
    - System performance, reliability, safety, and security.
    - Documentation.
    - Project schedule and resource requirements.
- Documenting the impact analysis results.

### 2.4. Change Control Board (CCB)
- **Composition:** Project Manager (Chair), CM Manager, Lead Architect, Lead Developer, Lead Tester, QA Lead, Safety Officer, Security Officer. Other SMEs or stakeholder representatives may be invited as needed.
- **Responsibilities:**
    - Reviewing submitted CRs and their associated impact analyses.
    - Evaluating the justification, benefits, costs, and risks of proposed changes.
    - Making the final decision to approve, reject, defer, or request further information for each CR.
    - Ensuring approved changes are prioritized and aligned with project objectives.
    - Overseeing the overall effectiveness of the Change Management process.

### 2.5. Implementers
- Primarily engineering team members responsible for implementing the approved change(s) to the affected CIs (code, design, documents, etc.).
- Responsible for unit testing or initial verification of their implemented changes.
- Responsible for updating associated documentation related to the change.

### 2.6. Verifiers
- QA team, Test Engineers, and/or peer reviewers.
- Responsible for formally verifying that the implemented change meets its objectives, correctly addresses the original issue (if a defect fix), does not introduce unintended side effects (regressions), and that all associated documentation is updated accurately.

## 3. Change Management Workflow

### 3.1. Change Identification and Request (CR) Initiation
- **Sources of Change:** Defect reports from testing or field use, new or evolving requirements from stakeholders, improvement proposals from the engineering team, outputs from risk mitigation activities, audit findings.
- **CR Submission:** Changes are proposed by submitting a formal Change Request using the designated CR tracking system.
- **CR Form Content:**
    - Unique CR Identifier (assigned by system).
    - Title/Brief Description of Change.
    - Detailed Description of Proposed Change.
    - Justification/Rationale for the Change (e.g., problem solved, benefit provided).
    - Affected CI(s) (if known).
    - Submitted By & Date.
    - Suggested Priority/Urgency (e.g., Critical, High, Medium, Low).
    - Proposed Solution (if applicable).

### 3.2. CR Logging and Initial Screening
- The CM Officer logs the CR in the tracking system and assigns a unique ID.
- The CM Officer or a designated lead performs an initial screening for completeness, clarity, and potential duplication.
- The CR is assigned an initial priority and may be categorized (e.g., defect, enhancement, new feature, documentation change).

### 3.3. Impact Analysis
- The CR is assigned by the CM Officer or CCB Chair to relevant Technical Assessor(s) / Engineering Lead(s).
- The assessors perform a detailed impact analysis, considering:
    - Technical feasibility and complexity.
    - Impact on other system components, modules, and interfaces.
    - Required modifications to design, code, tests, and documentation.
    - Potential impact on safety (requiring safety assessment update) and security (requiring security assessment update).
    - Estimated effort, resources, and time required for implementation.
    - Risks associated with implementing (or not implementing) the change.
- The results of the impact analysis are documented and attached to the CR.

### 3.4. CCB Review and Disposition
- The CCB meets regularly (e.g., weekly, bi-weekly, or ad-hoc for urgent CRs) to review CRs that have completed impact analysis.
- The CR originator and key technical assessors may be invited to present the CR and impact analysis.
- Possible CCB dispositions:
    - **Approved:** The change is approved for implementation. A priority and target for implementation may be assigned.
    - **Rejected:** The change is not approved. Rationale for rejection must be documented.
    - **Deferred:** The change is not approved at this time but may be reconsidered later. Conditions for reconsideration should be noted.
    - **More Information Required:** The CCB requires further clarification or analysis before making a decision. The CR is returned to the requestor or assessors.
- All CCB decisions, rationale, and any assigned actions are formally documented in the CR tracking system and meeting minutes.

### 3.5. Change Implementation and Scheduling
- Approved CRs are assigned to the appropriate engineering team(s) or individuals by Project/Engineering Management.
- The implementation is scheduled according to its priority and project timelines.
- Changes are implemented in accordance with the SCMP (e.g., proper branching, versioning of CIs).
- All affected CIs (code, design, documentation, tests) are updated.

### 3.6. Change Verification and Validation
- **Verification:**
    - Implemented changes undergo peer reviews (code reviews, design document reviews).
    - Unit tests and component integration tests are executed by developers.
    - QA performs verification testing to ensure the change was implemented as specified and that related requirements are met.
    - Regression testing is performed to check for unintended side effects on other parts of the system.
- **Validation (if applicable):**
    - For changes impacting user-visible functionality or system behavior, validation (e.g., via system testing, acceptance testing, or stakeholder review) ensures the change meets the original need.
- All verification and validation results are documented and linked to the CR.

### 3.7. Change Closure
- Once a change has been successfully implemented and verified (and validated, if applicable), the CR is updated to a 'Closed' or 'Resolved' state by the CM Officer or designated QA personnel.
- Relevant baselines are updated or new baselines created as per the SCMP.
- Communication of the change implementation and closure is sent to relevant stakeholders.

Approved and implemented changes result in new versions of Configuration Items or new baselines, which are managed as per the [Configuration Management Plan](./configuration_management_plan.md).

## 4. Emergency Change Process

### 4.1. Definition of an Emergency
An emergency change is required to address a critical issue that severely impacts system operation, safety, security, or blocks critical project progress, and cannot wait for the standard CCB review cycle. Examples:
- Critical defect in a released version causing system failure or data corruption.
- Urgent safety or security vulnerability requiring immediate patching.
- Showstopper defect blocking all further development or testing.

### 4.2. Expedited Workflow
- **Immediate Notification:** The issue is immediately reported to Project Management and relevant Engineering Leads.
- **Rapid Assessment:** A small, pre-designated Emergency CCB (eCCB) or authorized individuals (e.g., Project Manager, Lead Architect, Safety Officer) conduct a rapid impact assessment and risk evaluation.
- **Expedited Approval:** Verbal or electronic approval is obtained from the eCCB or authorized individuals.
- **Immediate Implementation:** The change is implemented and verified by the engineering team as quickly as possible.
- **Retrospective Formalization:** As soon as the emergency is resolved, a full CR must be created retrospectively, with complete impact analysis and documentation. This CR must be formally reviewed and ratified by the full CCB at its next scheduled meeting to ensure proper process adherence and to confirm the change.

## 5. Interface with other PLM Processes
- **Configuration Management:** Change Management is intrinsically linked to CM. All approved changes result in new versions of CIs and potentially new baselines, managed by the SCMP.
- **Risk Management:** Risks may drive the need for changes (mitigation actions). Conversely, changes can introduce new risks or modify existing ones, requiring updates to the Risk Register.
- **Quality Management:** The ChM process includes verification and validation steps to ensure changes maintain or improve product quality. Defect reports (part of QM) often initiate CRs.
- **Documentation Management:** Changes almost always necessitate updates to associated documentation. The ChM process ensures these updates are tracked and completed.

## 6. Change Management Metrics
- Number of CRs submitted (by type, priority, source).
- CR approval/rejection/deferral rates.
- Average time to process CRs (from submission to closure).
- Number of emergency CRs.
- Number of changes that had to be backed out or resulted in new defects (indicating issues in impact analysis or verification).
- Backlog of open CRs.
Metrics will be collected and reported periodically to assess the effectiveness and efficiency of the ChM process.

## 7. Tools for Change Management
The project will utilize [Specify CR tracking system, e.g., JIRA, GitLab Issues, dedicated Change Management tool] for logging, tracking, and managing the workflow of all Change Requests. This tool will be integrated with the Defect Tracking System and Configuration Management System where possible.

## 8. Change Management Process Maintenance
This Change Management Process document will be reviewed at least annually, or in response to significant project events, audit findings, or process improvement suggestions. Updates will follow this very Change Management Process.
