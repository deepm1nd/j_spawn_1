# gRiTOS Agile Sub-Efforts Guide

## 1. Introduction

### 1.1. Purpose
This guide provides practical instructions and best practices for gRiTOS engineering teams utilizing Agile methodologies (e.g., Scrum, Kanban) for the development of specific components, modules, or sub-systems. It details how these Agile practices integrate within the overarching gRiTOS V-Model framework and adhere to project-wide PLM processes.

### 1.2. Scope
These guidelines apply to gRiTOS project work packages that have been explicitly approved for an Agile development approach, based on criteria outlined in the `./Development Model Overview.md` and `./Development Methodology Tailoring Guide.md`.

### 1.3. Audience
- Engineering teams participating in Agile sub-efforts (developers, testers, architects).
- Sub-Effort Product Owners (SEPOs) / Technical Leads for Agile components.
- Scrum Masters / Agile Facilitators (if formally assigned).
- Project Management and QA ensuring alignment with overall project goals.

### 1.4. Core Agile Principles for gRiTOS Sub-Efforts
While specific practices may be tailored, the following core Agile principles will be emphasized:
- **Iterative Development:** Delivering working increments of the component.
- **Collaboration:** Close collaboration within the Agile team and with the SEPO.
- **Working Software/Hardware:** Demonstrating functional increments regularly.
- **Responding to Change:** Adapting to evolving understanding or technical challenges within the scope of the component and sprint goals.
- **Technical Excellence:** Maintaining high standards for design, code quality, and testing.
- **Sustainable Pace:** Ensuring teams can maintain productivity over the long term.

### 1.5. Referenced Documents
- `./Development Model Overview.md`
- `./V-Model Process Guide.md`
- `./Development Methodology Tailoring Guide.md`
- `../plm/Configuration Management Plan.md` (SCMP)
- `../plm/Quality Management Plan.md` (QMP)
- `../plm/Documentation Plan.md`
- `../plm/Change Management Process.md`

## 2. Agile Roles in gRiTOS Engineering Sub-Efforts

### 2.1. Sub-Effort Product Owner (SEPO)
- Typically a Lead Engineer, Component Architect, or designated technical stakeholder for the specific component(s) being developed.
- **Responsibilities:**
    - Defines the vision and objectives for the component/sub-effort.
    - Creates, maintains, and prioritizes the Product Backlog for the sub-effort (consisting of technical user stories, features, tasks, bug fixes).
    - Clearly communicates requirements and priorities to the Agile Development Team.
    - Is the primary point of contact for the team regarding scope and requirements of the component.
    - Accepts or rejects completed work items based on the Definition of Done and acceptance criteria.
    - Interfaces with overall Project Management and System Architects to ensure alignment with the larger V-Model milestones and system integrity.

### 2.2. Agile Development Team
- A cross-functional team of engineers possessing the necessary skills (e.g., software design, Rust development, embedded systems knowledge, component testing) to deliver a working increment of the product.
- **Responsibilities:**
    - Self-organizes to plan and execute the work for each iteration/sprint.
    - Collaboratively designs, implements, tests, and documents the component features.
    - Commits to realistic sprint goals based on their capacity.
    - Ensures the quality of their deliverables and adherence to the Definition of Done.
    - Actively participates in all Agile events (planning, daily stand-ups, reviews, retrospectives).
    - Raises impediments to the Scrum Master/Agile Facilitator.

### 2.3. Scrum Master / Agile Facilitator
- This role may be formally assigned or could be a rotating responsibility within smaller, experienced teams.
- **Responsibilities:**
    - Guides and coaches the team on Agile principles and selected practices (Scrum, Kanban).
    - Facilitates Agile events to ensure they are productive.
    - Helps remove impediments identified by the team.
    - Protects the team from external distractions and scope changes within a sprint.
    - Promotes continuous improvement within the team.

## 3. Agile Practices for gRiTOS Engineering

*(This section can be expanded based on whether Scrum, Kanban, or a mix is preferred for different types of sub-efforts. Below is a Scrum-centric example, with Kanban as an alternative.)*

### 3.1. Applying Scrum for Component Development
#### 3.1.1. Component Product Backlog
- The SEPO is responsible for creating and maintaining the Product Backlog for the component.
- Items are typically expressed as technical user stories (e.g., "As a [kernel service], I need [interface X] to [perform action Y] so that [system goal Z is achieved]"), tasks, features, or identified defects.
- The backlog is continuously refined and prioritized by the SEPO, with input from the Development Team regarding technical feasibility and effort.

#### 3.1.2. Sprint
- Sprints are fixed-length iterations (e.g., 1-4 weeks, determined by the team and SEPO based on the nature of the work) during which a usable and potentially releasable product increment (meeting DoD) is created.

#### 3.1.3. Sprint Planning
- **Part 1 (What):** The SEPO presents the highest priority backlog items. The Development Team discusses them, asks clarifying questions, and selects the items they can commit to completing in the upcoming sprint. A Sprint Goal is defined.
- **Part 2 (How):** The Development Team plans the work needed to deliver the selected backlog items, breaking them into smaller engineering tasks (e.g., design tasks, coding tasks, test creation tasks, documentation tasks).

#### 3.1.4. Daily Scrum (Daily Stand-up)
- A short (e.g., 15-minute) daily meeting for the Development Team to synchronize activities and create a plan for the next 24 hours. Each member typically addresses: What did I do yesterday? What will I do today? Are there any impediments?

#### 3.1.5. Sprint Review (Increment Demonstration)
- At the end of the sprint, the Development Team demonstrates the completed work (the increment) to the SEPO and other relevant stakeholders (e.g., system architects, QA representatives for V-Model integration points).
- The focus is on a live demonstration of functional software/hardware and discussion of the achieved Sprint Goal. Feedback is gathered.

#### 3.1.6. Sprint Retrospective
- After the Sprint Review and before the next Sprint Planning, the Agile team (including SEPO and Scrum Master/Facilitator) discusses what went well, what could be improved, and actions to enhance productivity and quality in future sprints.

### 3.2. Applying Kanban for Continuous Flow
- For sub-efforts focused on continuous improvement, bug fixing, or a steady stream of small tasks, Kanban may be more appropriate.
- **Visualize Workflow:** Use a Kanban board with columns representing stages of work (e.g., To Do, Design, Implement, Test, Ready for Integration).
- **Limit Work-In-Progress (WIP):** Set explicit limits for the number of items in each workflow stage to prevent bottlenecks and improve flow.
- **Manage Flow:** Monitor the movement of tasks across the board, identify areas where work is stalled, and address them.
- **Explicit Policies:** Define policies for how work is pulled through the system (e.g., when a task moves from 'Implement' to 'Test').
- **Feedback Loops & Continuous Improvement:** Regularly review the Kanban system and team performance to make improvements.

## 4. Definition of Done (DoD) for Agile Increments
A clear and stringent Definition of Done (DoD) is crucial for ensuring that increments produced by Agile sub-efforts are of high quality, well-documented, and ready for integration into the broader gRiTOS system, especially when interfacing with V-Model governed components. The DoD must be agreed upon by the SEPO, Development Team, and relevant QA/System Architects.
- **Example DoD criteria (to be tailored for each sub-effort):**
    - All code developed, reviewed (peer review mandatory), and committed to the component's feature branch.
    - Unit tests written and executed, achieving [agreed %] code coverage for new/modified code.
    - Component-level integration tests (with necessary stubs/mocks) written and passed.
    - Functionality successfully demonstrated to the SEPO and meets acceptance criteria for the PBI/User Story.
    - All relevant engineering documentation (e.g., updates to detailed design within the component, API documentation, test evidence) completed to the standard required by the `../plm/Documentation Plan.md` for the current integration point with the V-Model.
    - Successfully integrated with the main development branch (e.g., `develop` or a V-Model integration branch) and passes all CI checks.
    - No new critical or major defects introduced by the increment.
    - Traceability to backlog items/requirements maintained.

## 5. Integration with V-Model Lifecycle and PLM Processes

### 5.1. Inputs to Agile Sub-Efforts
- Component-level requirements derived from higher-level V-Model specifications (SysRS, SWADD, HRS, HWADD).
- Architectural constraints and interface definitions (ICDs) from the overall system architecture.
- Prioritized features/tasks from the SEPO's component backlog.

### 5.2. Outputs from Agile Sub-Efforts
- Working, tested, and documented software/hardware increments that meet their DoD.
- Updated component-level design documentation.
- Unit and component integration test cases and results.
- These outputs serve as inputs for broader V-Model integration testing, system testing, and safety/security V&V activities.

### 5.3. Synchronization with V-Model Phases and Baselines
- While Agile sprints provide short-term cadence, the overall progress of Agile sub-efforts must align with major V-Model milestones and PLM phase gates.
- The SEPO is responsible for ensuring that the component's development trajectory, as executed via Agile sprints, will meet the needs of the larger V-Model integration points.
- Completed and accepted Agile increments may contribute to formal V-Model baselines for the components they represent.

### 5.4. Adherence to Core PLM Processes
- **Configuration Management:** All source code, scripts, tests, and documentation produced by Agile sub-efforts are CIs and must be managed under the `../plm/Configuration Management Plan.md`. This includes versioning in Git, use of defined branching strategies for feature development, and integration back into mainlines.
- **Change Management:** Changes to the agreed scope of a sprint should be minimized. Changes that affect established baselines, external interfaces, or overall project requirements (even if identified during an Agile sub-effort) must be processed through the formal `../plm/Change Management Process.md` and approved by the CCB.
- **Quality Management:** Agile sub-efforts must adhere to the quality standards and practices defined in the `../plm/Quality Management Plan.md`. This includes defect management, participation in reviews/audits as appropriate, and ensuring the DoD reflects required quality levels.
- **Documentation Management:** Agile teams are responsible for producing the necessary engineering documentation for their components, as specified in the `../plm/Documentation Plan.md` and required by the DoD. The focus is on valuable, lean documentation that supports development, integration, testing, and maintenance.

## 6. Recommended Engineering Practices for Agile Sub-Efforts
- Continuous Integration (CI) / Continuous Delivery (for component-level builds and tests).
- Automated Testing at all levels feasible (unit, component, interface).
- Test-Driven Development (TDD) or Behavior-Driven Development (BDD) where appropriate.
- Regular Code Refactoring to maintain code quality and design integrity.
- Pair Programming (optional, based on team agreement and task complexity).
- Use of static analysis tools and linters integrated into the development workflow.
- Maintaining a high degree of transparency within the team and with the SEPO.

## 7. Agile Sub-Efforts Guide Maintenance
This guide will be reviewed periodically (e.g., at major project retrospectives or phase gates) and updated as necessary via the Change Management Process to incorporate lessons learned and evolving best practices for Agile development within gRiTOS.
