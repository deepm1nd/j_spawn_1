# gRiTOS Development Methodology Tailoring Guide

## 1. Introduction

### 1.1. Purpose
This document provides guidelines for tailoring the standard gRiTOS development methodologies (i.e., the overarching V-Model and the Agile practices for sub-efforts, as defined in the `./Development Model Overview.md`) to suit the specific characteristics and needs of different project components, modules, or distinct work packages. The aim is to optimize efficiency and effectiveness while ensuring that all tailoring decisions maintain project integrity, quality, safety, security, and compliance with applicable standards.

### 1.2. Scope
This guide applies when Project Management or Engineering Leads identify a potential need to deviate from, or adapt, the standard processes described in the `./V-Model Process Guide.md` or the `./Agile Sub-Efforts Guide.md`. It provides a framework for making and documenting such tailoring decisions.

### 1.3. Audience
- Project Management
- Engineering Leads (Architecture, Software, Hardware, Test)
- Quality Assurance (QA) Lead
- Safety Officer
- Security Officer
- Change Control Board (CCB)

### 1.4. Philosophy of Tailoring
Tailoring is not a mechanism to bypass essential controls or reduce necessary rigor, especially for safety-critical or security-critical aspects of gRiTOS. Instead, it is a structured approach to:
- Adapt processes to the specific risk level and complexity of a component.
- Improve efficiency for lower-risk or highly standardized tasks.
- Accommodate unique technical challenges or constraints.
- Ensure that the chosen methodology (or its adaptation) is fit for purpose for a given engineering activity.

### 1.5. Referenced Documents
- `./Development Model Overview.md`
- `./V-Model Process Guide.md`
- `./Agile Sub-Efforts Guide.md`
- `../plm/Risk Management Plan.md`
- `../plm/Quality Management Plan.md` (QMP)
- `../plm/Change Management Process.md`
- `../plm/Configuration Management Plan.md` (SCMP)
- Relevant gRiTOS Requirements documents
- Applicable standards (e.g., ISO 26262, ASPICE)

## 2. General Principles for Methodology Tailoring

### 2.1. Justification and Approval
- All tailoring decisions must be formally documented, clearly justified based on project or component needs, and risk-assessed.
- The level of approval required for tailoring will depend on the significance of the deviation and the criticality of the affected component/process. Minor tailoring might be approved by the relevant Engineering Lead and Project Manager, while significant deviations (especially those impacting safety, security, or key PLM processes) must be approved by the Change Control Board (CCB).
- All tailoring decisions are subject to the `../plm/Change Management Process.md`.

### 2.2. Risk-Based Assessment
- Any proposal to tailor a standard methodology must include an assessment of potential impacts on technical risks, safety (as per ISO 26262 requirements), security, overall product quality, maintainability, and integrability.
- Higher criticality (e.g., high ASIL/SIL components) will generally permit less tailoring away from the full rigor of the V-Model and associated verification/validation activities.

### 2.3. Compliance Integrity
- Tailoring actions must not compromise the project's ability to demonstrate compliance with applicable standards (e.g., ISO 26262, ASPICE, ISO 21434).
- If a standard mandates a specific process or artifact, tailoring cannot remove it unless the standard itself allows for such tailoring with justification.

### 2.4. Documentation of Tailoring
- All approved tailoring decisions, their justifications, associated risk assessments, and any specific control measures for the tailored process must be documented.
- This documentation might reside in a component-specific development plan, the project's main Development Plan (if a global tailoring), or within CCB records. It must be managed under the SCMP.

## 3. Tailoring Decision Framework (Engineering Focus)

This framework guides Engineering Leads and Project Management in evaluating the need for and appropriateness of tailoring:

### 3.1. Identify Candidate for Tailoring
- Clearly define the component, module, project phase, specific activity, or deliverable for which tailoring is being considered.

### 3.2. Analyze Characteristics and Context
Evaluate the candidate against factors such as:
- **Criticality:** Safety Integrity Level (ASIL/SIL), Security Assurance Level (CAL), impact on core OS functionality.
- **Requirements Stability & Clarity:** Are requirements for this item well-defined and stable, or are they expected to evolve?
- **Technical Complexity & Novelty:** Is this a simple, well-understood component, or does it involve new technologies, complex algorithms, or high uncertainty?
- **Interface Stability & Complexity:** Are interfaces to other system parts clearly defined and stable?
- **Team Size, Experience & Distribution:** Does the team have specific experience that makes a tailored approach more effective? Is the team small and co-located, or large and distributed?
- **Project Constraints:** While schedule and resource constraints should not be the primary driver for reducing rigor on critical items, they can be a factor for tailoring non-critical elements for efficiency.

### 3.3. Identify Standard Process/Artifact to be Tailored
- Pinpoint the specific part of the `V-Model Process Guide.md` or `Agile Sub-Efforts Guide.md` (or other project plans like QMP, Documentation Plan) that is proposed for tailoring.

### 3.4. Propose Specific Tailoring
- Clearly describe the proposed deviation or adaptation (e.g., "Combine Detailed Design Document and source code comments for non-critical utility X," "Use a simplified review process for internal tool Y's documentation," "Adapt Kanban for managing maintenance tasks for component Z instead of full Scrum sprints").

### 3.5. Justify Tailoring
- Explain why the standard process is not optimal or necessary for this specific case.
- Detail the expected benefits of the tailored approach (e.g., increased efficiency, faster feedback, better resource utilization) without compromising essential objectives.

### 3.6. Assess Impact and Risks of Tailoring
- Analyze potential negative impacts on quality, safety, security, maintainability, integrability, and compliance.
    - Identify any new risks introduced by the tailoring and propose mitigation for them (update `../plm/Risk Management Plan.md`).

### 3.7. Define Control Measures for Tailored Process
- If a process is streamlined, what alternative or strengthened controls will ensure quality and correctness? (e.g., if formal review stages are reduced, perhaps increased automated testing or pair programming is mandated).

### 3.8. Obtain Approval
    - Submit the tailoring proposal, justification, and risk assessment through the formal `../plm/Change Management Process.md`.

## 4. Examples of Permissible Tailoring for V-Model Activities

*(This section provides illustrative examples, not exhaustive lists or pre-approvals. Each case needs individual justification.)*

### 4.1. Documentation Scope and Formality
- For very simple, non-critical internal tools or components with limited interfaces, the level of detail in design documents might be reduced, or some documents combined, provided all essential information for development, test, and maintenance is captured and traceable.
- Use of informal engineering notes or Wiki pages for internal design brainstorming before formalizing into baselined documents for certain non-critical items.

### 4.2. Review and Inspection Intensity
- For low-risk, non-critical components, peer reviews might be sufficient without requiring a full formal inspection meeting, provided review evidence is still captured.
- The number and roles of mandatory reviewers for certain documents might be adjusted based on the document's impact and criticality.

### 4.3. Testing Scope and Methods
- For non-critical components with a very stable history of reuse, the scope of regression testing might be optimized based on a documented risk assessment.
- The formality of test documentation (plans, cases, reports) for internal development tools might be less than for safety-critical OS components.

### 4.4. Combining or Iterating within V-Model Phases
- For specific, well-understood, and low-risk components, some detailed design and implementation activities might be more tightly coupled in an iterative loop, provided each loop produces verifiable outputs that feed into the V-Model's testing stages. This must not compromise the overall V-Model structure of specification followed by corresponding V&V.

## 5. Examples of Permissible Tailoring for Agile Sub-Efforts

*(This section provides illustrative examples.)*

### 5.1. Agile Ceremony Adjustments
- For very small Agile teams (e.g., 1-2 engineers), daily stand-ups might be informal check-ins. Sprint Reviews might be combined with technical deep-dives with the SEPO and relevant peers rather than large stakeholder meetings.
- Sprint lengths can be adjusted (e.g., from 1 to 4 weeks) to suit the nature of the engineering work (e.g., research spikes vs. feature implementation).

### 5.2. Role Adaptations
- In small, highly experienced teams, the Scrum Master role might be a rotating facilitation duty rather than a dedicated person, provided the responsibilities are fulfilled. The SEPO role must remain clearly defined.

### 5.3. Backlog Item Granularity and Format
- The format of technical user stories or PBIs can be adapted as long as they are clear to the team and SEPO, and allow for proper estimation and tracking. For highly technical internal components, tasks might be more engineering-centric than user-centric.

### 5.4. DoD Adjustments
- While the core principles of the Definition of Done (coded, tested, reviewed, documented, integrated) must be met, the specific artifacts or level of detail for documentation within a sprint might be tailored if the component is non-critical and internal, provided the outputs are sufficient for V-Model integration and overall project quality goals.

## 6. Tailoring Activities Generally NOT Permitted

The following are generally not acceptable areas for tailoring, especially for safety- or security-critical aspects of gRiTOS:
- Skipping mandatory safety analyses (FMEA, FTA, HARA) or security analyses (TARA) required by standards or the project's safety/security plans.
- Bypassing formal Configuration Management for any identified CI.
- Circumventing the formal Change Management Process for changes to baselined items.
- Eliminating independent verification or validation for safety-critical functions.
- Significantly reducing documentation required for certification, safety arguments, or long-term maintenance of critical components.
- Compromising the traceability between requirements, design, implementation, and tests for critical components.

## 7. Documenting and Communicating Tailoring Decisions
- All approved tailoring decisions must be recorded, typically as part of the Change Request that approved them, or in a dedicated section of the relevant component's development plan or architectural description.
- The Configuration Management Plan should specify how tailored process descriptions are themselves version controlled.
- Approved tailoring decisions must be clearly communicated to all affected engineering team members and stakeholders.

## 8. Tailoring Guide Maintenance
This Development Methodology Tailoring Guide will be reviewed at major project milestones (e.g., after the completion of an increment involving significant tailoring) and updated as necessary via the Change Management Process to reflect lessons learned and evolving best practices for the gRiTOS project.
