[[USER PATH CONFIGURATION]]
Main Iteration Folder: /gritos_mps_test_01/
Submodule Plan Destination: /gritos_mps_test_01/plan/
Prompts Folder: /gritos_mps_test_01/prompts/
Inter-AI Communication Folder: /gritos_mps_test_01/prompts/ipc/
User-Specified Task Output Base Path: /dev/src/kernel/
[[END USER PATH CONFIGURATION]]

gritos Dev Plan (a003) - With gritos_product_specification.md and gritos_product_requirements_full.md as the primary source of information, develop a highly detailed development plan for this called gritos_dev_plan.md (the Plan).

Suggested Steps

Review documents in the concept/research folder of the repository as an input Review the documents in the docs folder as guidance
You may review other documents in the plan folder but they should only be used to enhance or improve a task, activity, or end product.
Perform deep research on modern OS architectures and specifically the OSes listed in the OS Kernal Comparison document. Include codebases referenced in the input documents in your research.
The /concept/inputs folder contains an exemplar development plan "grit_dev_plan.md" (and its subplans). Use it as the standard for the expected style, level of detail, and implementation guidance expected in the gritos_dev_plan.md document.

Present a plan to create gritos_dev_plan.md and highly detailed supporting subplans to the same level of detail and quality as the files in concept/inputs. Try to develop the plan such that it can be broken down into 2-5 phases with multiple tasks in each phase. The goal is to divide the tasks within each Phase such that they can be developed in parallel by multiple Jules instances.
--------------------------------------------------------------------------------------------------------------
[[START OF MASTER PROMPT SEGMENT: AI Project Planning & Task Generation Instructions]]

ATTENTION PLANNING AI: Your primary function is to interpret the user's project request (provided immediately preceding this segment) and generate a comprehensive, phased execution plan. This involves creating detailed task prompts for subsequent AI instances ("Task AIs") and all necessary supporting documentation. Adhere strictly to the following instructions and output requirements.

**I. USER-SPECIFIED PATHS & DEFAULTS:**
The user may provide paths for:
    A. Destination for submodule development plans (`_dev_plan.md`, `_next_steps.md`).
    B. Folder for generated task prompts and launch plans (defaults to `prompts/` within the main iteration folder).
    C. Folder for inter-AI communication/documentation (defaults to `prompts/ipc/` within the chosen prompts folder).
    D. The main iteration folder (defaults to `/mps_XX/` at the repository root, where XX is the current iteration number, e.g., `01`).
    E. A User-Specified Task Output Base Path (where Task AIs should place their primary deliverables like code).

You **must** parse the `[[USER PATH CONFIGURATION]]` block at the beginning of this overall prompt. Use the paths specified there. If any path is missing from the block, you may use a logical default but state this assumption clearly in your `00_task_launch_plan.md`. All paths you generate in prompts and plans must be constructed clearly, typically relative to the repository root, and reflect these user-specified locations.

**II. CORE PLANNING & TASK DEFINITION DIRECTIVES:**

1.  **Understand User's Project:** Thoroughly analyze the user's project description, including any referenced input documents (specifications, research, requirements) and exemplar plans for style and detail. Perform research if indicated. The primary deliverable you will be creating based on the user's request is often a main development plan document (e.g., `gritos_dev_plan.md`); your subsequent task prompts will break this main plan into executable pieces.
2.  **Phased Structure:** Decompose the project into 2-5 logical phases. Tasks within a phase should be executable in parallel; phases themselves are sequential.
3.  **Task Granularity & Parallelism:** Define tasks within each phase to be as granular as possible while remaining meaningful. Design these tasks for maximum parallel execution by different Task AIs.
4.  **Merge Conflict Mitigation Strategy (HIGHEST PRIORITY):**
    *   Your **highest priority** during task decomposition is the elimination or severe mitigation of
potential merge conflicts.
    *   Actively identify any tasks that might write to the same file(s) or where one task might read a file that a concurrent task in the same phase modifies.
    *   If such a potential conflict is identified:
        *   First, attempt to redesign the tasks to operate on entirely separate files or distinct, non-overlapping parts of shared files. Task AIs should primarily create *new* files in their designated output directories (see `User-Specified Task Output Base Path`).
        *   If redesign is not feasible to completely avoid the conflict, you **must** serialize these tasks. This means placing them in different phases, or if they must remain within the same conceptual phase due to logical grouping, ensure your generated `00_task_launch_plan.md` clearly indicates a specific sequential order for these conflicting tasks *within* that phase, and the task prompts reflect this dependency.
        *   Task prompts for serialized tasks must clearly state their dependencies on the completion of preceding tasks.
    *   If a task *must* modify existing shared code (as per the `User-Specified Task Output Base Path`), the task prompt you generate for that task must be *extremely specific* about the exact functions, classes, or sections of code to be modified, the nature of the changes, and any assumptions about the state of that shared code. Also, instruct the Task AI to be extra vigilant in using the `Owned-Files-Modules` field in the Information Exchange Protocol (IEP) for such shared resources.
5.  **File Naming Conventions:**
    *   Main Development Plan (if you are creating/drafting its content): As specified by user (e.g., `gritos_dev_plan.md`), place in the "Submodule Plan Destination" path.
    *   Task Prompts: `p<Phase#_t<Task#_<brief_description_lowercase_underscores>.txt` (e.g., `p1_t1_database_schema.txt`).
    *   Task Launch Plan: `00_task_launch_plan.md`.
    *   Submodule Dev Plans (created by Task AIs): `<prompt_filename_base>_dev_plan.md` (e.g., `p1_t1_database_schema_dev_plan.md`).
    *   Next Steps Docs (created by Task AIs): `<prompt_filename_base>_next_steps.md`.

**III. OUTPUT GENERATION REQUIREMENTS:**

All file paths below are relative to the "Main Iteration Folder" you identified from the `[[USER PATH CONFIGURATION]]` block.

**A. Main Development Plan Document (e.g., `gritos_dev_plan.md`):**
    *   Based on the user's request, you will develop the content for this plan.
    *   **Location:** Place this file in the "Submodule Plan Destination" path, inside the "Main Iteration Folder". For example, if "Main Iteration Folder" is `/gritos_mps_test_01/` and "Submodule Plan Destination" is `/gritos_mps_test_01/plan/`, then the file is `/gritos_mps_test_01/plan/gritos_dev_plan.md`.

**B. Task Prompt Files (`.txt`):**
    *   **Location:** Create in the designated "Prompts Folder" (e.g., `/gritos_mps_test_01/prompts/`).
    *   **For Each Task Defined in your Main Development Plan, Generate a Prompt File Containing:**
        1.  **Task Overview:** Purpose of this specific task, its contribution to the current phase and overall project. Reference the relevant section of the Main Development Plan document you created.
        2.  **Sub-Development Plan Directive:**
            *   "You must first create a detailed sub-development plan for this task. Save it as `<PhaseTaskName>_dev_plan.md` in the user-specified submodule plan destination folder: `[Actual User-Specified Path for Submodule Plans from [[USER PATH CONFIGURATION]]]`. This plan must list your detailed steps. Mark your progress in this file (e.g., using markdown checkboxes: `[ ] Step 1`, `[x] Step 2`)."
        3.  **Detailed Task Execution Instructions:** Clear, actionable steps to complete the task, referencing any necessary input documents (e.g., sections from the Main Development Plan, product specifications).
        4.  **Output Management:**
            *   "Place all new files, code, or artifacts created *specifically for this task* and not intended as direct modifications to existing shared project code into a dedicated task-specific subdirectory within the `User-Specified Task Output Base Path`. For this task, your primary output subdirectory should be `[Actual User-Specified Task Output Base Path]/<PhaseTaskName>/`. If this task involves modifying existing shared project code directly within `[Actual User-Specified Task Output Base Path]`, refer to the specific instructions above regarding those modifications and be meticulous with IEP commit messages and the `Owned-Files-Modules` field." (Ensure `<PhaseTaskName>` is specific to the task).
        5.  **Information Exchange Protocol (IEP) Adherence:**
            *   "You **must** strictly adhere to the `Information_Exchange_Protocol.md` (located in `[Actual Path to Prompts Folder]/Information_Exchange_Protocol.md, it is in the repo`) for all your commit messages. Pay close attention to `Status`, `Owned-Files-Modules`, `[BLOCKERS / ISSUES]`, and `[PROGRESS]` sections."
        6.  **"Next Steps & Improvements" Document Directive:**
            *   "After completing your primary task, create a document named `<PhaseTaskName>_next_steps.md` in the same location as your `_dev_plan.md` (`[Actual User-Specified Path for Submodule Plans]`). This document must include:
                *   Potential next steps, enhancements, or optimizations for the content/code you developed.
                *   A dedicated section titled 'AI-Automated PR Suggestions': Your thoughts on how a Pull Request for your work could be best formulated for AI-assisted creation/review.
                *   A section titled 'Other Recommendations': Any general recommendations for improving the overall project or development process."
        7.  **Inter-AI Communication Directive:**
            *   "For persistent communication, detailed logs, or shared artifacts relevant to other AI tasks that don't fit commit messages, use the designated inter-AI communication folder: `[Actual Path to Prompts Folder]/ipc/`. Document any such usage in your commit messages."
        8.  **Retry Logic (Verbatim Text):**
            *   "```If you are starting this task and a previous AI instance may have already worked on it and crashed, your first step is to thoroughly investigate and determine what that instance completed. Check version control (git log), the file system (especially in `[Actual User-Specified Path for Submodule Plans]/<PhaseTaskName>_dev_plan.md` and your task output directory `[Actual User-Specified Task Output Base Path]/<PhaseTaskName>/`), and any logs in `[Actual Path to Prompts Folder]/ipc/`. Document your findings in your `_dev_plan.md`. Then, proceed to complete the task fully from where the previous instance left off. Do not repeat successfully completed work.```" (Ensure `<PhaseTaskName>` is correctly substituted).
        9.  **Path Referencing:** When providing file paths to the Task AI, append ", it is in the repo." to aid discovery.

**C. Supporting Documentation (in the designated "Prompts Folder"):**

1.  **`Information_Exchange_Protocol.md`:**
    *   Create this file. Its content **must be** (verbatim):
        ```markdown
        # Information Exchange Protocol (IEP) for Commit Messages
        ## 1. Purpose
        This protocol defines a standardized format for Git commit messages. Its objectives are to:
        - Ensure clarity, traceability, and accountability for all changes.
        - Facilitate effective asynchronous collaboration among all contributors (human or AI).
        - Enable robust progress tracking at both task and sub-task levels.
        - Provide structured data to support automated processes, including AI-assisted Pull Request (PR) generation and review.
        - Aid in managing dependencies and proactively identifying potential integration issues or conflicts.
        - Offer a clear channel for communicating blockers, issues, or specific information to relevant parties.

        ## 2. Commit Message Format
        Each commit message **must** adhere to the following structure. All fields are mandatory unless explicitly stated as optional.
        \`\`\`
        Task-ID: <Unique identifier for the task, typically the prompt filename, e.g., p1_t1_setup_database.txt>
        Sub-Task-ID: <Identifier for a specific sub-task from the task's _dev_plan.md; "N/A" if not applicable or if the commit addresses the task generally>
        Phase: <Project Phase number, e.g., P1, P2, P3, as defined in 00_task_launch_plan.md>
        Status: <Current status of the Sub-Task-ID, or Task-ID if Sub-Task-ID is N/A. This reflects the state *after* this commit is applied.>
          Allowed values:
            - Not-Started: Initial commit, task/sub-task definition, planning, or setup.
            - In-Progress: Actively working on implementation; partial completion.
            - Blocked: Work on this task/sub-task cannot proceed. (Must be explained in [BLOCKERS / ISSUES])
            - In-Review: Implementation of this part is complete and awaiting review (peer or automated).
            - Completed: Implementation of this part is complete and has passed self-review/initial testing.
            - Tested: Implementation is complete, reviewed, and all specified tests (unit, integration) have passed.
        Owned-Files-Modules: <(Optional, but HIGHLY RECOMMENDED, especially for shared resources) Comma-separated list of primary files, directories, or modules this commit creates, significantly modifies, or claims ownership of for the scope of this task. Helps in early detection of potential merge conflicts.>
          Example: \`Owned-Files-Modules: src/auth/service.py, new_file:src/user/models.py, config/app_settings.json\`
        Depends-On: <(Optional) Task-ID(s) or specific Commit_SHA(s) if this work is directly dependent on other unmerged or incomplete work; "N/A" otherwise.>
          Example: \`Depends-On: p1_t2_database_schema.txt\`
        Files-Modified:
          - path/to/modified_or_created_file_1.ext: Brief, clear description of the change in this file.
          - path/to/another_file.ext: Brief, clear description of the change in this file.
          - ... (List all significant files. For extensive changes in many files within a directory, a summary like \`src/core_utils/*: Refactored logging functions\` is acceptable, but list key individual files if possible.)
        Summary: <A concise, imperative summary of the commit's primary purpose (max 72 characters). E.g., "Add user registration endpoint and initial validation">

        Notes-To-Next-Jules:
        <This section is multi-line and provides the detailed narrative of the commit. It's crucial for context, hand-offs, and reviews.>

        [PROGRESS]
        - Current step in \`_dev_plan.md\`: <Reference the specific step name or number from this task's _dev_plan.md, e.g., "Step 3.2: Implement password hashing">
        - Percentage complete (approximate for this Sub-Task-ID or Task-ID if N/A): <e.g., 75%>
        - Key accomplishments in THIS commit:
          - Implemented X feature.
          - Refactored Y module for clarity.
          - Added Z unit tests covering new logic.

        [BLOCKERS / ISSUES]
        - Current blockers: <Describe any impediments preventing further progress. State "None" if there are no blockers.>
        - Potential issues or risks identified: <Describe any new concerns, potential future problems, or areas of technical debt introduced.>
        - Questions for other tasks or project lead: <Pose specific questions if input or clarification is needed from others. Tag tasks if possible, e.g., "Question for p2_t1_api_gateway.txt: ...">

        [USER_REQUEST_INFO]
        - <(If applicable) Describe how this commit addresses any specific user requests or feedback that were relayed to this task. Otherwise, state "N/A".>

        [NOTES_FOR_PR_AUTOMATION]
        - Key changes to highlight in a Pull Request: <Bulleted list of the most significant changes from a user, reviewer, or product perspective.>
          - Example: "- Added new \`/auth/register\` API endpoint."
          - Example: "- Integrated Prometheus metrics for XYZ service."
        - Test evidence location/commands: <Link to test results, CI build, or specify commands to run tests verifying these changes.>
          - Example: "All unit tests pass. See CI build #123."
          - Example: "Run \`npm test --filter=authService\`"

        <Any other general notes, observations, design rationale, assumptions made, or specific instructions for the next Jules instance, human reviewer, or integrator.>
        \`\`\`

        ## 3. Field Descriptions and Guidelines (Summary)
        *   **`Task-ID`**: Identifies the main task.
        *   **`Sub-Task-ID`**: Granular step within the task.
        *   **`Phase`**: Project phase for context.
        *   **`Status`**: Critical for progress tracking; reflects state *after* commit.
        *   **`Owned-Files-Modules`**: Key for merge conflict awareness.
        *   **`Depends-On`**: Highlights dependencies.
        *   **`Files-Modified`**: Quick overview of changed files.
        *   **`Summary`**: Concise purpose of the commit.
        *   **`Notes-To-Next-Jules`**: The detailed body, with structured sections for progress, blockers, user requests, PR notes, and general observations.
        ## 4. Best Practices for Usage
        - **Atomic Commits:** Strive for small, logically atomic commits.
        - **Commit Frequently:** Don't wait too long to commit changes.
        - **Accuracy is Key:** Ensure all fields accurately reflect the work.
        - **Clarity in Notes:** Be explicit and clear in `Notes-To-Next-Jules`.
        - **Review Before Commit:** Briefly review your commit message against this protocol.
        ```
        (End of Information_Exchange_Protocol.md content)

2.  **`00_task_launch_plan.md`:**
    *   Create this file. Its content **must be based on the following template.** You **must** dynamically replace placeholders like `[Project Name Placeholder]`, `[Iteration Placeholder XX]`, `[Actual Main Iteration Folder Path]`, `[Actual Path to Prompts Folder]`, `[Actual User-Specified Path for Submodule Plans]`, and `[Actual User-Specified Task Output Base Path]` with the actual values derived from the user's request and the `[[USER PATH CONFIGURATION]]` block. You must also dynamically generate the task listing.
        ```markdown
        # Task Launch Plan for [Project Name Placeholder] - Iteration [Iteration Placeholder XX]

        ## 1. Overview
        Welcome to the Task Launch Plan for **[Project Name Placeholder]**, Iteration **[Iteration Placeholder XX]**! This document provides all the necessary information to launch and manage the development tasks generated for this project.
        The project has been decomposed into several phases. Each phase contains one or more tasks designed by the Planning AI. Tasks within the same phase can often be executed in parallel by different AI instances (e.g., Jules instances) *unless a specific order is indicated below due to dependencies*.

        **Key Files & Folders (relative to this iteration's root folder, e.g., `[Actual Main Iteration Folder Path]/`):**
        *   `[Actual Path to Prompts Folder]/`: Contains this launch plan, all individual task prompt files (`.txt`), and the Information Exchange Protocol.
        *   `[Actual Path to Prompts Folder]/ipc/`: A dedicated space for inter-AI instance communication.
        *   `[Actual User-Specified Path for Submodule Plans]`: The location Task AIs will store their `_dev_plan.md` and `_next_steps.md` files.
        *   `[Actual User-Specified Task Output Base Path]`: The base directory where Task AIs will place their primary deliverables (e.g., code). Task-specific subdirectories will be created within this path.

        ## 2. Execution Workflow
        1.  **Review Phases:** Understand the sequential nature of phases. All tasks in a preceding phase should be completed and verified before starting tasks in a subsequent phase.
        2.  **Parallel Task Execution:** Tasks listed *within the same phase* below can be assigned to different AI instances simultaneously, *unless a specific execution order is noted for particular tasks due to dependencies (e.g., to avoid merge conflicts)*.
        3.  **Provide Prompts to AI Instances:** For each task, copy the "Initial Launch" command and provide it to an AI instance.
        4.  **Monitor Progress:**
            *   Regularly check commit messages (see `[Actual Path to Prompts Folder]/Information_Exchange_Protocol.md, it is in the repo`).
            *   Review `_dev_plan.md` files in `[Actual User-Specified Path for Submodule Plans]`.
            *   Check `[Actual Path to Prompts Folder]/ipc/` for inter-AI communications.
        5.  **Handle Crashes:** If an AI instance crashes, use the "Retry if Crashed" command for that task.

        ## 3. Launching Tasks
        Below are the tasks, organized by phase.
        ---
        [[YOU MUST DYNAMICALLY GENERATE THE TASK LISTING BELOW BASED ON THE PROMPT FILES YOU CREATED]]
        [[FOR EACH PHASE:]]
        ### **Phase <PhaseNumber> Tasks**
        [[IF ANY TASKS IN THIS PHASE ARE SERIALIZED, ADD A NOTE HERE EXPLAINING THE SEQUENCE]]

        [[FOR EACH TASK IN THE PHASE:]]
        #### **Task: `<Full Prompt Filename e.g., p1_t1_setup_core_framework.txt>`**
        *   **Description:** <A brief, 1-2 sentence user-friendly description of this task's objective, derived from your Main Development Plan.>
        *   **Initial Launch:**
            \`\`\`
            [Actual Main Iteration Folder Path]/[Actual Path to Prompts Folder relative to Main Iteration Folder]/<Full Prompt Filename> is the prompt, it is in the repo.
            \`\`\`
        *   **Retry if Crashed:**
            \`\`\`
            [Actual Main Iteration Folder Path]/[Actual Path to Prompts Folder relative to Main Iteration Folder]/<Full Prompt Filename> is the prompt, it is in the repo, but a previous Jules instance crashed. Your task is to determine what the previous instance was able to complete before crashing, and to continue and complete the task.
            \`\`\`
        ---
        [[END OF DYNAMICALLY GENERATED TASK LISTING]]

        ## 4. Iteration Completion
        Once all tasks in all phases are reported as `Tested` or `Completed` satisfactorily, this project iteration can be considered complete. Review `_next_steps.md` documents in `[Actual User-Specified Path for Submodule Plans]`.
        Good luck with project **[Project Name Placeholder]**!
        ```
        (End of 00_task_launch_plan.md template)

**IV. FINAL INSTRUCTION TO PLANNING AI:**
Your own final commit message for completing *this planning and generation task* must also adhere to the `Information_Exchange_Protocol.md` you've just defined, as much as is applicable. Use a relevant Task-ID like "INITIAL_PROJECT_PLANNING_ITER_XX" (replace XX with the iteration number).

**V. TASK RESUMPTION ADD-ON INCORPORATION (MANDATORY):**
For every task prompt you generate as per Section III.B above, you **must append** the complete and unmodified content of the "Task Resumption Add-on". This add-on's text is specified in `mps_01/add_ons/task_resumption_addon.txt` in the repository where this MPS is located (you should assume this MPS file itself is at `mps_01/Master_Prompt_Segment.txt` to help you locate the add-on relative to it, or rely on the absolute path if you can determine it). You are responsible for retrieving this add-on text and including it in each generated task prompt. Ensure all path placeholders within the add-on (like `[Actual Path to Prompts Folder]`, `[Actual User-Specified Path for Submodule Plans]`, `[Actual User-Specified Task Output Base Path]`, `<ExactOriginalTaskPromptFilenameBase>`) are correctly resolved by you when you embed the add-on into the final task prompts.

[[END OF MASTER PROMPT SEGMENT]]
