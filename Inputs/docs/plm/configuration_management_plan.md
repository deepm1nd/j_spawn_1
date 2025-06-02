# Software Configuration Management Plan (SCMP) for gRiTOS

## 1. Introduction

### 1.1. Purpose of the SCMP
This Software Configuration Management Plan (SCMP) defines the methods and practices to be used for Configuration Management (CM) throughout the gRiTOS project lifecycle. It aims to establish and maintain the integrity of gRiTOS products (software, hardware configurations, documentation, and other artifacts) by providing a systematic approach to identifying, controlling, tracking, and auditing configuration items.

### 1.2. Scope
This SCMP applies to all hardware, software, firmware, documentation, tools, test environments, and other items identified as Configuration Items (CIs) developed or procured for the gRiTOS project, from project inception through to final retirement. It covers all PLM phases as defined in the gRiTOS Development Plan.

### 1.3. Audience
- Engineering teams (architecture, development, test, safety, security)
- Project Management
- Quality Assurance
- Release Management
- External auditors/certifiers (if applicable)

### 1.4. Referenced Documents
- `../../plan/gritOS Development Plan.md`
- `./Quality Management Plan.md`
- `./Change Management Process.md`
- `./Risk Management Plan.md`
- `../process/Development Model Overview.md`
- Relevant ISO/IEC/IEEE standards (e.g., ISO 26262, IEEE 828)

### 1.5. Definitions and Acronyms
- **Baseline:** A formally reviewed and agreed-upon specification or product that thereafter serves as the basis for further development, and that can be changed only through formal change control procedures.
- **Build:** An operational version of a system or component that incorporates a defined set of capabilities.
- **Change Control Board (CCB):** A group of people responsible for evaluating and approving or disapproving proposed changes to configuration items.
- **Configuration Audit (CA):** The process of verifying that all required Configuration Items have been produced, that current version control is correct, and that all documentation is complete and up-to-date. Includes Functional Configuration Audit (FCA) and Physical Configuration Audit (PCA).
- **Configuration Control:** The systematic evaluation, coordination, approval or disapproval, and implementation of all approved changes to the configuration of a CI after formal establishment of its configuration identification.
- **Configuration Identification:** The process of selecting CIs for a system and recording their functional and physical characteristics in technical documentation.
- **Configuration Item (CI):** An aggregation of hardware, software, or both, that is designated for configuration management and treated as a single entity in the configuration management process.
- **Configuration Management (CM):** A discipline applying technical and administrative direction and surveillance to identify and document the functional and physical characteristics of a CI, control changes to those characteristics, record and report change processing and implementation status, and verify compliance with specified requirements.
- **Configuration Status Accounting (CSA):** The recording and reporting of information needed to manage a configuration effectively. This information includes a listing of the approved configuration identification, the status of proposed changes to the configuration, and the implementation status of approved changes.
- **Version Control System (VCS):** A software tool that helps manage changes to source code and other files over time.

## 2. SCM Management and Organization

### 2.1. Roles and Responsibilities
- **Configuration Manager (or designated lead):** Overall responsibility for SCMP execution, SCM tool administration, process facilitation, CM training, audit coordination, and reporting on CM status.
- **Engineering Leads (Architecture, Software, Hardware, Test, Safety, Security):** Defining CIs within their respective domains, ensuring their teams adhere to CM procedures, participating in CCB reviews, and approving baselines for their areas of responsibility.
- **Developers/Engineers:** Identifying potential CIs, adhering to version control practices (branching, merging, committing), creating change requests for modifications to baselined items, ensuring their work products are correctly identified and versioned, and participating in reviews and audits.
- **Quality Assurance (QA):** Auditing CM processes and baselines, verifying CI integrity and completeness against specifications, participating in CCB.
- **Change Control Board (CCB):** Reviewing and approving/rejecting Change Requests (CRs) that affect baselined CIs. Composition to include Project Manager (Chair), CM Manager, relevant Engineering Leads, QA Lead, Safety Officer, Security Officer.

### 2.2. SCM Tools
- **Version Control System (VCS):** Git. Repository structure, access control, and branching/merging strategies will be defined in this SCMP (see Section 3.2).
- **Build System:** Cargo (for Rust components) and potentially custom scripts for overall system builds and packaging. Build configurations and scripts will be version controlled CIs.
- **Issue/Defect Tracking System:** [To be Specified, e.g., GitLab Issues, JIRA]. This system will be used to track defects and link them to CIs and Change Requests.
- **Change Request Management System:** [To be Specified, may be part of Issue Tracking or a separate system]. Used to manage the workflow of CRs.
- **Repository Hosting:** [To be Specified, e.g., GitLab, GitHub, self-hosted server].

### 2.3. SCM Training
A plan will be developed to provide initial and ongoing training to all relevant project personnel on the SCM tools, processes, and their specific responsibilities as defined in this SCMP.

## 3. SCM Activities

### 3.1. Configuration Identification
#### 3.1.1. Identifying Configuration Items (CIs)
CIs will include all items necessary to define, produce, test, and support gRiTOS. This encompasses:
- Project planning documents (including this SCMP and other PLM/process documents).
- Requirements specifications (system, software, hardware, safety, security).
- Architecture and design documents (system, software, hardware).
- Source code (for all custom-developed software and firmware).
- Build scripts and environment configuration files.
- Test documentation (plans, specifications, procedures, data, reports).
- Hardware definition files (schematics, BoMs - if custom hardware is developed).
- Key COTS software/libraries (with specific versions).
- Toolchain components (compilers, linkers, debuggers - with specific versions).
- Safety and security artifacts (e.g., Hazard Analysis, TARA, Safety Case).
- User and developer documentation.
- Release packages.
Engineering leads are responsible for identifying and proposing CIs from their domains at project initiation and as new items are developed. CIs will be formally documented, often in a CI list or within the CM system.

#### 3.1.2. Naming Conventions for CIs
A systematic and unique naming convention will be established for all CIs and their versions. This will include elements such as project identifier, CI type, name, and version. (Specific convention TBD).

#### 3.1.3. Baselines
Baselines represent a formally reviewed and agreed-upon state of a collection of CIs at a specific point in time. Key baselines for gRiTOS will include:
- **Functional Baseline:** Established after system-level requirements are approved.
- **Allocated Baseline:** Established after system architecture and major component requirements are approved.
- **Developmental Baseline(s):** Established at key points during development (e.g., completion of major features, end of Agile sprints for components, internal releases).
- **Product Baseline:** Established before formal system verification and validation testing, representing a complete product configuration.
- **Release Baseline:** Established for each official release of gRiTOS to customers/users.
Procedures for creating, reviewing (including QA and relevant engineering leads), and approving baselines will be defined. All baselines will be recorded and versioned.

### 3.2. Version Control
#### 3.2.1. Branching Strategy
A branching strategy (e.g., Gitflow-based with `main`, `develop`, `feature/*`, `release/*`, `hotfix/*` branches) will be adopted to manage parallel development, feature integration, release stabilization, and defect correction. Specific rules for branch creation, commit messages, code reviews prior to merging (e.g., via Merge/Pull Requests), and branch protection will be defined and enforced.

#### 3.2.2. Tagging/Labeling
All baselines and official software/firmware releases will be marked with unique, immutable tags in the VCS (e.g., `v1.0.0`, `safety_baseline_rev2`). Tagging conventions will be defined.

#### 3.2.3. Access Control & Permissions
Repository access rights (read, write, merge, admin) will be defined based on roles to protect the integrity of the codebase.

#### 3.2.4. Workspace Management
Guidelines for individual developer workspaces (e.g., regular synchronization with `develop` branch, managing local changes) will be provided to minimize integration issues.

### 3.3. Build Management
#### 3.3.1. Build Process
The build process will be automated using the selected Build System (Cargo, scripts) and integrated with the CI/CD pipeline. Builds will fetch version-controlled source code and precisely versioned toolchain components to ensure consistency.

#### 3.3.2. Build Identification
Each build will be assigned a unique identifier (e.g., incorporating timestamp, VCS commit ID, CI/CD job number) that links it to the specific versions of CIs used.

#### 3.3.3. Reproducibility
All official builds intended for formal testing or release must be reproducible. This requires strict CM of all inputs: source code, build scripts, libraries, compiler versions, and the build environment itself.

#### 3.3.4. Build Archival
Official builds (binaries, logs, associated artifacts) will be archived in a designated, secure location with their corresponding build identification and CI list.

### 3.4. Configuration Control (Interface with Change Management)
All changes to established baselines must follow the formal Change Management Process detailed in `./Change Management Process.md`. This ensures that changes are properly requested, analyzed for impact (including on safety, security, and regression potential by engineering teams), approved by the CCB, implemented in a controlled manner, verified, and documented.

### 3.5. Configuration Status Accounting (CSA)
#### 3.5.1. Information to be Tracked
The status of all CIs and their versions, status of all Change Requests, history of baselines, build reports, and configuration audit findings will be tracked.

#### 3.5.2. Reporting
CSA reports (e.g., baseline content lists, change log reports for a release, CI traceability reports, audit status) will be generated periodically or on demand for project management, engineering teams, and QA.

### 3.6. Configuration Audits
#### 3.6.1. Types of Audits
- **Functional Configuration Audit (FCA):** To verify that a CI's actual performance and functional characteristics match those specified in its requirements and design documentation. Engineering teams will support FCAs by providing test evidence and demonstrations.
- **Physical Configuration Audit (PCA):** To verify that all items in a baseline are present, correctly versioned, and that the accompanying documentation accurately describes the CIs. Engineering teams will provide necessary data for PCAs.
#### 3.6.2. Audit Schedule and Process
Audits will be conducted by QA or independent auditors before major baselines (e.g., Product Baseline) and releases. The process will involve planning, execution, reporting findings, and tracking corrective actions.
#### 3.6.3. Discrepancy Resolution
A formal process will be used for documenting, tracking, and resolving discrepancies found during configuration audits. This will involve engineering teams for technical corrections.

## 4. SCM Resources and Schedules

### 4.1. Tools and Infrastructure
(Summary of tools from Section 2.2 and any other necessary infrastructure).

### 4.2. Personnel
(Summary of roles and responsibilities from Section 2.1).

### 4.3. Schedule of SCM Activities
Major CM activities (e.g., establishment of key baselines, major audits) will be aligned with the overall project schedule, PLM phase gates, and V-Model milestones.

## 5. SCMP Maintenance
This SCMP will be reviewed at least annually, or in response to significant project changes, process improvement needs, or audit findings. Updates to this plan will follow the project's Change Management Process.
