[[USER PATH CONFIGURATION]]
# Instructions for User: Edit the paths below before using this MPS.
# - Main Iteration Folder: Where the Planning AI will place all its outputs for the target project.
#   Use '.' for the repository root, or a relative path like 'project_X_outputs'.
# - Prompts Folder: Subdirectory for generated task prompts, TLP, IEP. Relative to Main Iteration Folder.
# - Submodule Plan Destination: Directory for main project plan (e.g., gritos_dev_plan.md) and Task AI _dev_plan.md files. Relative to Main Iteration Folder.
# - Inter-AI Communication Folder: Subdirectory for IPC files. Typically 'prompts/ipc'. Relative to Main Iteration Folder.
# - User-Specified Task Output Base Path: The root path where Task AIs will place their primary deliverables (e.g., code). Relative to repo root.

Main Iteration Folder: .
Prompts Folder: prompts
Submodule Plan Destination: plan
Inter-AI Communication Folder: prompts/ipc
User-Specified Task Output Base Path: dev/default_task_outputs
[[END USER PATH CONFIGURATION]]

--------------------------------------------------------------------------------------------------------------
[[START OF MASTER PROMPT SEGMENT (v0.3.1 - Path Config Inside MPS, Configurable Add-on Stack, 7 Features): AI Project Planning & Task Generation Instructions]]

ATTENTION PLANNING AI: Your primary function is to interpret the user's project request (provided by the user *before* this Master Prompt Segment begins) and then to follow the instructions within this segment to generate a comprehensive, phased execution plan. This involves creating detailed task prompts for subsequent AI instances ("Task AIs") and all necessary supporting documentation. Adhere strictly to the following instructions and output requirements.

**I. USER-SPECIFIED PATHS, CONFIGURATIONS & ADD-ON SELECTIONS:**
A. **Parse User Path Configurations:**
    You **must** parse the `[[USER PATH CONFIGURATION]]` block found at the very beginning of *this Master Prompt Segment*. Use the paths specified there by the user.
    *   If `Main Iteration Folder` is specified as `.` (dot), this signifies the repository root. All other relative paths specified in that configuration block (like for 'Prompts Folder' or 'Submodule Plan Destination') are then considered directly under this repository root (e.g., `prompts/`, `plan/`).
    *   If `Main Iteration Folder` specifies a directory name (e.g., `my_project_outputs`), then other paths from the config block are typically nested within it (e.g., `my_project_outputs/prompts/`).
    *   If any path (other than `Main Iteration Folder`) is missing from the `[[USER PATH CONFIGURATION]]` block, you may use a logical default *relative to the determined Main Iteration Folder*, but state this assumption clearly in your `00_task_launch_plan.md`.
    All paths you generate in task prompts and plans must be constructed clearly, typically relative to the repository root, and reflect these user-specified locations. When resolving path placeholders like `[Actual Path to Prompts Folder]`, if `Main Iteration Folder` was specified as `.` and `Prompts Folder` was `prompts` (in the config block), then `[Actual Path to Prompts Folder]` resolves to `prompts` (relative to repository root).

    You will also need to read certain definition files provided by the user (e.g., for the Base IEP and any specified Add-ons). These files are expected to be in predefined paths relative to the repository root where you are invoked (e.g., `/prompts/iep/Base_IEP.txt`, `/prompts/add_ons/some_addon.txt`). The specific paths for these input definition files will be referenced directly in the relevant instruction sections below.

B. **Parse User Add-on Selections:**
    1.  Scan the prompt provided by the user (the part *before* this Master Prompt Segment) for a block demarcated by `[[USER_ADDON_SELECTION]]` and `[[END USER_ADDON_SELECTION]]`.
    2.  If this block is found, parse each line within it.
    3.  Identify lines that begin with `[x]` (case-insensitive for 'x'). For these lines, extract the subsequent filename (e.g., `my_addon.txt`). Trim any leading/trailing whitespace from the filename.
    4.  Compile an ordered list of these selected add-on filenames. The order should match their appearance in the selection block.
    5.  For each filename in your compiled list, the path to the add-on text file will be `/prompts/add_ons/[addon_filename.txt]` (relative to the repository root where you are operating; it is in the repo). You will need to read the content of these files.
    6.  If the `[[USER_ADDON_SELECTION]]` block is not found, or if no add-ons are marked with `[x]`, proceed without applying any optional add-ons.
    7.  If an add-on file selected by the user is not found at its expected path (e.g., `/prompts/add_ons/non_existent_addon.txt`), you **must** log a clear warning at the top of the `00_task_launch_plan.md` file you generate (after the Confidence Score and Visual Overview sections), listing which selected add-ons could not be found. Then, proceed with the planning using only the add-ons that were successfully located and read. Store the successfully read add-on contents for use in Section III.B.

**II. CORE PLANNING & TASK DEFINITION DIRECTIVES:**
1.  **Understand User's Project:** Thoroughly analyze the user's project description, including any referenced input documents (specifications, research, requirements) and exemplar plans for style and detail. Perform research if indicated. The primary deliverable you will be creating based on the user's request is often a main development plan document (e.g., `gritos_dev_plan.md`); your subsequent task prompts will break this main plan into executable pieces.
2.  **Phased Structure:** Decompose the project into 2-5 logical phases. Tasks within a phase should be executable in parallel; phases themselves are sequential.
3.  **Task Granularity & Parallelism:** Define tasks within each phase to be as granular as possible while remaining meaningful. Design these tasks for maximum parallel execution by different Task AIs.
4.  **Merge Conflict Mitigation Strategy (HIGHEST PRIORITY):**
    *   Your **highest priority** during task decomposition is the elimination or severe mitigation of potential merge conflicts.
    *   Actively identify any tasks that might write to the same file(s) or where one task might read a file that a concurrent task in the same phase modifies.
    *   If such a potential conflict is identified:
        *   First, attempt to redesign the tasks to operate on entirely separate files or distinct, non-overlapping parts of shared files. Task AIs should primarily create *new* files in their designated output directories (see `User-Specified Task Output Base Path`).
        *   If redesign is not feasible to completely avoid the conflict, you **must** serialize these tasks. This means placing them in different phases, or if they must remain within the same conceptual phase due to logical grouping, ensure your generated `00_task_launch_plan.md` clearly indicates a specific sequential order for these conflicting tasks *within* that phase **by using the 'Dependencies' field (see Section III.C.2) for the dependent tasks**, and the task prompts reflect this dependency.
        *   Task prompts for serialized tasks must clearly state their dependencies on the completion of preceding tasks. **When generating the prompt for a dependent task, include an introductory note, e.g., "Note: This task depends on the successful completion of Task X (see prompt file `[Task_X_Prompt_Filename]`). Ensure its deliverables are available and verified before you begin."**
        *   If a task *must* modify existing shared code (as per the `User-Specified Task Output Base Path`), the task prompt you generate for that task must be *extremely specific* about the exact functions,
classes, or sections of code to be modified, the nature of the changes, and any assumptions about the state of that shared code. Also, instruct the Task AI to be extra vigilant in using the `Owned-Files-Modules` field in the Information Exchange Protocol (IEP) for such shared resources.
5.  **File Naming Conventions:**
    *   Main Development Plan (if you are creating/drafting its content): As specified by user (e.g., `gritos_dev_plan.md`), place in the "Submodule Plan Destination" path.
    *   Task Prompts: `p<Phase#_t<Task#_<brief_description_lowercase_underscores>.txt` (e.g., `p1_t1_database_schema.txt`).
    *   Task Launch Plan: `00_task_launch_plan.md`.
    *   Submodule Dev Plans (created by Task AIs): `<prompt_filename_base>_dev_plan.md` (e.g., `p1_t1_database_schema_dev_plan.md`).
    *   Next Steps Docs (created by Task AIs): `<prompt_filename_base>_next_steps.md`.
6.  **Task Effort Estimation (Heuristic):**
    *   For each task you define, provide a heuristic estimate of its effort/complexity. Use T-shirt sizes: S (Small), M (Medium), L (Large), XL (Extra Large).
    *   Base your estimate on factors like the perceived amount of work, number of components involved, apparent complexity of logic, and potential dependencies or unknowns.
    *   Your goal is to break down work such that most tasks are S or M. If a task seems L, ensure it's well-defined. **Avoid generating XL tasks; if a part of the plan seems XL, you must attempt to decompose it further into smaller, more manageable tasks (S, M, L).** If decomposition of an XL item isn't feasible while meeting other constraints, clearly flag it as XL in the `00_task_launch_plan.md` and suggest user review for that specific item.
7.  **Planning Confidence Assessment & Reporting:**
    *   After generating the complete plan (including all task prompts and the `00_task_launch_plan.md`), you must perform a self-assessment of your confidence in the quality and executability of the generated plan.
    *   Determine a confidence level: **High, Medium, or Low.**
    *   Base this on factors such as: the clarity and completeness of the user's initial request, your ability to effectively decompose tasks into manageable sizes (S/M/L, avoiding XL), the number of tasks requiring serialization due to potential merge conflicts, and the overall perceived completeness of information available to you.
    *   You must report this confidence score and a brief rationale in two places:
        1.  At the very top of the `00_task_launch_plan.md` you generate (see Section III.C.2 template update).
        2.  In your final commit message for this planning task, using a `[PLANNING_CONFIDENCE]` block within the `Notes-To-Next-Jules` field of the IEP (see Section IV.B).
8.  **Visual Plan Overview Generation (Mermaid Syntax):**
    *   After defining all phases and tasks, you must generate a visual overview of the plan using Mermaid JS flowchart syntax.
    *   This diagram should depict:
        *   Phases as distinct groups or subgraphs.
        *   Key tasks (using their Task IDs/short descriptions) within each phase.
        *   Dependencies between tasks (especially serialized tasks) as arrows.
        *   Dependencies between phases (sequential flow).
    *   You will embed this Mermaid diagram code block at the top of the `00_task_launch_plan.md` file (after the Planning Confidence Score section, see Section III.C.2 template update).
    *   Example of simple Mermaid flowchart syntax for a plan:
        \`\`\`mermaid
        graph TD
            subgraph Phase 1
                P1_T1["Task 1.1: Init Setup"]
                P1_T2["Task 1.2: Define Schema"]
            end
            subgraph Phase 2
                P2_T1["Task 2.1: Build Core"]
                P2_T2["Task 2.2: Develop API"]
            end
            P1_T1 --> P2_T1
            P1_T2 --> P2_T1
            P2_T1 --> P2_T2
            Phase1_End["Phase 1 Complete"]
            P1_T1 --> Phase1_End
            P1_T2 --> Phase1_End
            Phase1_End --> P2_T1
        \`\`\`
        (Adapt the diagram's complexity to the plan. For very complex plans, focus on major tasks and phase flows.)

**III. OUTPUT GENERATION REQUIREMENTS:**

All file paths below are relative to the "Main Iteration Folder" identified from the `[[USER PATH CONFIGURATION]]` block (which could be the repository root if `.` was specified).

**A. Main Development Plan Document (e.g., `gritos_dev_plan.md`):**
    *   Based on the user's request, you will develop the content for this plan.
    *   **Location:** Place this file in the "Submodule Plan Destination" path.
        *   Example 1: If 'Main Iteration Folder' is `gritos_mps_test_01` and 'Submodule Plan Destination' is `gritos_mps_test_01/plan`, then the file is `gritos_mps_test_01/plan/gritos_dev_plan.md`.
        *   Example 2: If 'Main Iteration Folder' is `.` and 'Submodule Plan Destination' is `plan`, then the file is `plan/gritos_dev_plan.md` (relative to the repository root).

**B. Task Prompt Files (`.txt`):**
    *   **Location:** Create in the designated "Prompts Folder".
        *   This path is interpreted relative to the 'Main Iteration Folder'. For example, if 'Main Iteration Folder' is `gritos_mps_test_01` and 'Prompts Folder' is `gritos_mps_test_01/prompts`, files go into `gritos_mps_test_01/prompts/`. If 'Main Iteration Folder' is `.` and 'Prompts Folder' is `prompts`, then files go into `prompts/` at the repository root.
    *   **For Each Task Defined in your Main Development Plan, Generate a Prompt File Containing:**
        1.  **Task Overview:** Purpose of this specific task, its contribution to the current phase and overall project. Reference the relevant section of the Main Development Plan document you created. **If this task has dependencies as noted in the `00_task_launch_plan.md` (due to serialization by Planning AI), reiterate them here briefly (e.g., "This task depends on `TaskX_ID` and should only start after its completion.").**
        2.  **Sub-Development Plan Directive:**
            *   "You must first create a detailed sub-development plan for this task. Save it as `<PhaseTaskName>_dev_plan.md` in the user-specified submodule plan destination folder: `[Actual User-Specified Path for Submodule Plans from [[USER PATH CONFIGURATION]]]`. This plan must list your detailed steps. Mark your progress in this file (e.g., using markdown checkboxes: `[ ] Step 1`, `[x] Step 2`)."
        3.  **Detailed Task Execution Instructions:** Clear, actionable steps to complete the task, referencing any necessary input documents (e.g., sections from the Main Development Plan, product specifications).
        4.  **Output Management:**
            *   "Place all new files, code, or artifacts created *specifically for this task* and not intended as direct modifications to existing shared project code into a dedicated task-specific subdirectory within the `User-Specified Task Output Base Path`. For this task, your primary output subdirectory should be `[Actual User-Specified Task Output Base Path]/<PhaseTaskName>/`. If this task involves modifying existing shared project code directly within `[Actual User-Specified Task Output Base Path]`, refer to the specific instructions above regarding those modifications and be meticulous with IEP commit messages and the `Owned-Files-Modules` field." (Ensure `<PhaseTaskName>` is specific to the task).
        5.  **Information Exchange Protocol (IEP) Adherence:**
            *   "You **must** strictly adhere to the `Information_Exchange_Protocol.md` (located in `[Actual Path to Prompts Folder]/Information_Exchange_Protocol.md, it is in the repo`) for all your commit messages. Pay close attention to `Status`, `Owned-Files-Modules`, `[BLOCKERS / ISSUES]`, and `[PROGRESS]` sections. **Additionally, include the `[RISK_ASSESSMENT]` block as detailed below.**"
        6.  **"Next Steps, Learnings & Improvements" Document Directive:**
            *   "After completing your primary task, create a document named `<PhaseTaskName>_next_steps.md` in the same location as your `_dev_plan.md` (`[Actual User-Specified Path for Submodule Plans]`). This document **must include the following distinct sections:**
                *   `### Potential Next Steps & Enhancements`: Specific ideas for improving or building upon the work you just completed (e.g., refactoring, new features for this component).
                *   `### Key Learnings & Discoveries`: Document any non-obvious technical insights, unexpected challenges, clever solutions/workarounds you developed, critical assumptions made, or notable aspects (positive or negative) of the system you interacted with. This is for knowledge sharing.
                *   `### AI-Automated PR Suggestions`: Your thoughts on how a Pull Request for your work could be best formulated for AI-assisted creation/review.
                *   `### Other General Recommendations`: Any broader recommendations for improving the overall project or development process."
        7.  **Inter-AI Communication Directive:**
            *   "For persistent communication, detailed logs, or shared artifacts relevant to other AI tasks that don't fit commit messages, use the designated inter-AI communication folder: `[Actual Path to Prompts Folder]/ipc/`. Document any such usage in your commit messages."
        8.  **Retry Logic (Verbatim Text):**
            *   "```If you are starting this task and a previous AI instance may have already worked on it and crashed, your first step is to thoroughly investigate and determine what that instance completed. Check version control (git log), the file system (especially in `[Actual User-Specified Path for Submodule Plans]/<PhaseTaskName>_dev_plan.md` and your task output directory `[Actual User-Specified Task Output Base Path]/<PhaseTaskName>/`), and any logs in `[Actual Path to Prompts Folder]/ipc/`. Document your findings in your `_dev_plan.md`. Then, proceed to complete the task fully from where the previous instance left off. Do not repeat successfully completed work.```" (Ensure `<PhaseTaskName>` is correctly substituted).
        9.  **Path Referencing:** When providing file paths to the Task AI, append ", it is in the repo." to aid discovery.
        10. **Risk Assessment of Changes Directive:**
            *   "For each commit you make, you must perform a risk assessment of your changes. Determine a risk level (Low, Medium, High) based on factors such as the scope and complexity of your changes, the potential impact on other modules or core functionality, the existing test coverage for the areas you modified, and any new dependencies introduced.
            *   You must include this assessment in your commit message by adding an `[RISK_ASSESSMENT]` block within the `Notes-To-Next-Jules` field of the IEP. This block should include:
                *   `- Risk-Level: <Your assessed Low/Medium/High level>`
                *   `- Justification: <Your brief explanation for choosing this risk level, highlighting key factors.>`"
        11. **Append Selected Add-ons:**
            *   "Retrieve the ordered list of selected add-on contents you compiled and read in Section I.B."
            *   "For each selected add-on, append its full, unmodified text to the end of this current task prompt."
            *   "Ensure add-ons are appended in the same order they were listed in the `[[USER_ADDON_SELECTION]]` block."
            *   "Crucially, before appending each add-on's text, you must resolve any path placeholders within that add-on's text (e.g., `[Actual Path to Prompts Folder]`, `[Actual User-Specified Path for Submodule Plans]`, `[Actual User-Specified Task Output Base Path]`, `<ExactOriginalTaskPromptFilenameBase>`) using the correct values derived from the `[[USER PATH CONFIGURATION]]` block (Section I.A) and the current task's context. This ensures the add-on operates correctly for this specific task."

**C. Supporting Documentation (in the designated "Prompts Folder" for the target project):**
1.  **`Information_Exchange_Protocol.md`:**
    *   Read the content from the user-provided Base IEP file located at `/prompts/iep/Base_IEP.txt` (this path is relative to the repository root where you, the Planning AI, are operating; it is in the repo).
    *   You **must** create a file named `Information_Exchange_Protocol.md` in the 'Prompts Folder' you are generating for the target project (e.g., `[Actual Path to Target Project's Prompts Folder]/Information_Exchange_Protocol.md`).
    *   Write the exact, unmodified content read from `/prompts/iep/Base_IEP.txt` into this new file.
    *   **Ensure the example `Notes-To-Next-Jules` within the IEP content you write includes an illustration of the `[RISK_ASSESSMENT]` block (e.g., by adding it to the existing example or providing a dedicated small example).** This ensures Task AIs see how to format it. (This means the Base_IEP.txt provided by user should ideally already have this example).

2.  **`00_task_launch_plan.md`:**
    *   Create this file. Its content **must be based on the following template.** You **must** dynamically replace placeholders like `[Project Name Placeholder]`, `[Iteration Placeholder XX]`, `[Actual Main Iteration Folder Path]`, `[Actual Path to Prompts Folder]`, `[Actual User-Specified Path for Submodule Plans]`, and `[Actual User-Specified Task Output Base Path]` with the actual values derived from the user's request and the `[[USER PATH CONFIGURATION]]` block. You must also dynamically generate the task listing.
    *   **CRITICAL FOR LAUNCH COMMANDS:** Ensure the launch paths in `00_task_launch_plan.md` are always correctly formed relative to the repository root. For example, if 'Main Iteration Folder' is `.` and 'Prompts Folder' is `prompts`, a launch path will be `prompts/p1_t1_task.txt is the prompt, it is in the repo.`. If 'Main Iteration Folder' is `iter_outputs` and 'Prompts Folder' is `iter_outputs/prompts`, the path will be `iter_outputs/prompts/p1_t1_task.txt is the prompt, it is in the repo.`.
    *   If any selected add-ons were not found (as per Section I.B.7), add a warning section at the top of the TLP, after the Visual Plan Overview.
        ```markdown
        # Task Launch Plan for [Project Name Placeholder] - Iteration [Iteration Placeholder XX]

        **Overall Planning Confidence:** <High | Medium | Low (P-AI to populate)>
        **Confidence Rationale:** <P-AI to provide a brief explanation for its confidence score, highlighting key factors considered during planning.>

        ---
        ## Visual Plan Overview

        \`\`\`mermaid
        [[[P-AI TO GENERATE MERMAID FLOWCHART DIAGRAM HERE]]]
        graph TD
            A["Example Task A"] --> B["Example Task B"];
            B --> C["Example Task C"];
        \`\`\`
        *(The diagram above is a placeholder and will be replaced by the Planning AI with a representation of the actual plan phases, tasks, and dependencies.)*

        [[[P-AI TO INSERT WARNINGS ABOUT MISSING ADD-ONS HERE, IF ANY]]]

        ---
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
        2.  **Parallel Task Execution:** Tasks listed *within the same phase* below can be assigned to different AI instances simultaneously, *unless a **"Dependencies"** field explicitly indicates a required sequential order for particular tasks*. Respect any specified sequential ordering for such dependent tasks.
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
        *(Tasks listed below can generally be run in parallel. However, if a task includes a **"Dependencies"** field, it **must not** be started until all listed prerequisite tasks are complete. Respect any specified sequential ordering for such dependent tasks.)*

        [[FOR EACH TASK IN THE PHASE:]]
        #### **Task: `<Full Prompt Filename e.g., p2_t1_process_data.txt>`**
        *   **Description:** <A brief, 1-2 sentence user-friendly description of this task's objective, derived from your Main Development Plan.>
        *   **Estimated Effort:** <S | M | L | XL (P-AI to populate this. Strive for S or M. Flag XL for user review if unavoidable)>
        *   **Dependencies:** <(P-AI Populates if Applicable) e.g., "Depends on: `p2_t0_fetch_data.txt` - Requires output file `raw_data.csv`." OR "Depends on: `p2_t2_update_config.txt` - Serialized due to shared access to `config.xml`." >
        *   **Initial Launch:**
            \`\`\`
            [Path to Prompt File relative to Repo Root] is the prompt, it is in the repo.
            \`\`\`
        *   **Retry if Crashed:**
            \`\`\`
            [Path to Prompt File relative to Repo Root] is the prompt, it is in the repo, but a previous Jules instance crashed. Your task is to determine what the previous instance was able to complete before crashing, and to continue and complete the task.
            \`\`\`
        ---
        [[END OF DYNAMICALLY GENERATED TASK LISTING]]

        ## 4. Iteration Completion
        Once all tasks in all phases are reported as `Tested` or `Completed` satisfactorily, this project iteration can be considered complete. Review `_next_steps.md` documents in `[Actual User-Specified Path for Submodule Plans]`.
        Good luck with project **[Project Name Placeholder]**!
        ```
        (End of 00_task_launch_plan.md template)

**IV. FINAL INSTRUCTION TO PLANNING AI & SELF-CORRECTION:**
**A. Planning AI Self-Correction and Verification Checks (MANDATORY):**
    (List of checks as in MPS v0.2.2, including for Knowledge Capture, Risk Assessment, and updating check #3 for `[[USER_ADDON_SELECTION]]` logic: "Verify that the logic to parse `[[USER_ADDON_SELECTION]]` and append the *selected* add-ons (with placeholders resolved) has been correctly applied. If `[[USER_ADDON_SELECTION]]` was not present or no add-ons were selected, confirm no optional add-ons were appended unless explicitly mandated elsewhere by this MPS (though currently none are).")
**B. Final Commit Message by Planning AI:**
    (As in MPS v0.2.2 - including Planning Confidence block and stating self-checks performed)

**V. TASK RESUMPTION ADD-ON INCORPORATION:**
    *   **(REMOVED/SUPERSEDED by Section I.B and III.B.11)** This section is removed. All add-ons are now handled by the `[[USER_ADDON_SELECTION]]` mechanism.

[[END OF MASTER PROMPT SEGMENT (v0.3.1 - Path Config Inside MPS, Configurable Add-on Stack, 7 Features)]]
