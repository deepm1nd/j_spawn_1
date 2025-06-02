[[USER PATH CONFIGURATION]]
# Instructions for User: Edit the paths below before using this MPS.
# - Main Iteration Folder: Where the Planning AI will place all its outputs for the target project.
#   Use '.' for the repository root, or a relative path like 'project_X_outputs'.
# - Prompts Folder: Subdirectory for generated task prompts, TLP, IEP. Relative to Main Iteration Folder.
#   Example: 'prompts/tasks' if Main Iteration Folder is '.'.
# - Submodule Plan Destination: Directory for main project plan (e.g., gritos_dev_plan.md) and Task AI _dev_plan.md files. Relative to Main Iteration Folder.
# - Inter-AI Communication Folder: Subdirectory for IPC files. Typically nested within 'Prompts Folder'.
# - User-Specified Task Output Base Path: The root path where Task AIs will place their primary deliverables (e.g., code). Relative to repo root.

Main Iteration Folder: .
Prompts Folder: prompts/tasks
Submodule Plan Destination: plan
Inter-AI Communication Folder: prompts/tasks/ipc
User-Specified Task Output Base Path: dev/default_task_outputs
[[END USER PATH CONFIGURATION]]

[[USER_ADDON_SELECTION]]
# Instructions for user:
# - List desired add-on filenames below, one per line.
# - Mark selection with '[x]' (case-insensitive for 'x').
# - Add-ons must exist in the '/prompts/add_ons/' directory of this repository (i.e., the repo where the Planning AI runs).
# - Ensure exact filenames, including extensions (e.g., .txt).
# - Add-ons will be appended to task prompts in the order listed here.

[x] task_resumption_addon.txt
# [ ] example_coding_standards_addon.txt
# [ ] example_security_checklist_addon.txt
[[END USER_ADDON_SELECTION]]

--------------------------------------------------------------------------------------------------------------
[[START OF MASTER PROMPT SEGMENT (v0.3.2 - Canonical Paths, Internal Configs, 8 Features): AI Project Planning & Task Generation Instructions]]

ATTENTION PLANNING AI: Your primary function is to interpret the user's project request (provided by the user *before* this Master Prompt Segment begins) and then to follow the instructions within this segment to generate a comprehensive, phased execution plan. This involves creating detailed task prompts for subsequent AI instances ("Task AIs") and all necessary supporting documentation. Adhere strictly to the following instructions and output requirements.

**I. USER-SPECIFIED PATHS, CONFIGURATIONS & ADD-ON SELECTIONS (Parse these first from the top of THIS MPS text):**

A. **Parse User Path Configurations:**
    You **must** parse the `[[USER PATH CONFIGURATION]]` block found at the very beginning of *this Master Prompt Segment*. Use the paths specified there by the user for generating *your outputs*.
    *   If `Main Iteration Folder` is specified as `.` (dot), this signifies the repository root (where you are operating). All other relative paths specified in that configuration block (like for 'Prompts Folder' or 'Submodule Plan Destination') are then considered directly under this repository root (e.g., if `Prompts Folder` is `prompts/tasks`, it means `<repo_root>/prompts/tasks/`).
    *   If `Main Iteration Folder` specifies a directory name (e.g., `my_project_outputs`), then other paths from the config block are typically nested within it (e.g., `my_project_outputs/prompts/tasks/`).
    *   If any path (other than `Main Iteration Folder`) is missing from the `[[USER PATH CONFIGURATION]]` block, you may use a logical default *relative to the determined Main Iteration Folder*, but state this assumption clearly in your `00_task_launch_plan.md`.
    All paths you generate in task prompts and plans must be constructed clearly, typically relative to the repository root, and reflect these user-specified locations. When resolving path placeholders like `[Actual Path to Prompts Folder]`, if `Main Iteration Folder` was `.` and `Prompts Folder` was `prompts/tasks`, then `[Actual Path to Prompts Folder]` resolves to `prompts/tasks`.

B. **Parse User Add-on Selections:**
    1.  Scan the `[[USER_ADDON_SELECTION]]` block found near the beginning of *this Master Prompt Segment* (typically just after the `[[USER PATH CONFIGURATION]]` block).
    2.  If this block is found, parse each line within it.
    3.  Identify lines that begin with `[x]` (case-insensitive for 'x'). For these lines, extract the subsequent filename (e.g., `my_addon.txt`). Trim any leading/trailing whitespace from the filename.
    4.  Compile an ordered list of these selected add-on filenames. The order should match their appearance in the selection block.
    5.  For each filename in your compiled list, the path to find the add-on text file (which the user should have provided in their repository) will be `/prompts/add_ons/[addon_filename.txt]` (this path is relative to the repository root where you are operating; it is in the repo). You will need to read the content of these files.
    6.  If the `[[USER_ADDON_SELECTION]]` block is not found, or if no add-ons are marked with `[x]`, proceed without applying any optional add-ons.
    7.  If an add-on file selected by the user is not found at its expected path (e.g., `/prompts/add_ons/non_existent_addon.txt`), you **must** log a clear warning at the top of the `00_task_launch_plan.md` file you generate (after the Confidence Score and Visual Overview sections), listing which selected add-ons could not be found. Then, proceed with the planning using only the add-ons that were successfully located and read. Store the successfully read add-on contents for use in Section III.B.

C. **Input Definition Files (Base IEP):**
    You will also need to read certain definition files provided by the user. Specifically, the Base IEP content is expected at `/prompts/iep/Base_IEP.txt` (this path is relative to the repository root where you are invoked; it is in the repo). This will be referenced in Section III.C.1.

**II. CORE PLANNING & TASK DEFINITION DIRECTIVES:**
1.  **Understand User's Project:** Thoroughly analyze the user's project description (the part of the prompt *before* this MPS text), including any referenced input documents and exemplar plans. Perform research if indicated. Your primary deliverable is often a main development plan document (e.g., `project_dev_plan.md` as per user's request); your task prompts will break this main plan into executable pieces.
2.  **Phased Structure:** Decompose the project into 2-5 logical phases.
3.  **Task Granularity & Parallelism:** Define tasks within each phase to be as granular as possible while remaining meaningful, designed for maximum parallel execution.
4.  **Merge Conflict Mitigation Strategy (HIGHEST PRIORITY):**
    *   Your **highest priority** is to eliminate or severe mitigation of potential merge conflicts.
    *   Actively identify tasks that might conflict (write to same file, or read file modified by concurrent task).
    *   If conflict identified:
        *   First, try to redesign tasks for separate files/parts. Task AIs should primarily create *new* files in their designated output directories (derived from `User-Specified Task Output Base Path`).
        *   If redesign fails, **must** serialize these tasks (different phases, or explicitly sequenced in TLP using 'Dependencies' field, see III.C.2). Task prompts must state these dependencies (e.g., "Note: This task depends on Task X (`[Task_X_Prompt_Filename]`). Ensure its deliverables are available...").
    *   If a task *must* modify existing shared code (in `User-Specified Task Output Base Path`), its prompt must be *extremely specific* about changes and AI must be vigilant with IEP `Owned-Files-Modules`.
5.  **File Naming Conventions:**
    *   Main Development Plan: As specified by user (e.g., `gritos_dev_plan.md`), in "Submodule Plan Destination".
    *   Task Prompts: `p<Phase#_t<Task#_<brief_description_lowercase_underscores>.txt`.
    *   Task Launch Plan: `00_task_launch_plan.md`.
    *   Submodule Dev Plans (by Task AIs): `<prompt_filename_base>_dev_plan.md`.
    *   Next Steps Docs (by Task AIs): `<prompt_filename_base>_next_steps.md`.
6.  **Task Effort Estimation (Heuristic):**
    *   For each task, estimate effort: S (Small), M (Medium), L (Large), XL (Extra Large).
    *   Base on work amount, components, complexity, unknowns.
    *   Aim for S or M. If L, ensure well-defined. **Avoid XL; decompose further.** If unavoidable, flag in TLP and suggest user review.
7.  **Planning Confidence Assessment & Reporting:**
    *   After generating the complete plan, self-assess your confidence: **High, Medium, or Low.**
    *   Base on: clarity of user request, task decomposition success (S/M/L sizes, avoiding XL), number of serializations, information completeness.
    *   Report score and rationale: 1. At top of `00_task_launch_plan.md`. 2. In your final commit message (`[PLANNING_CONFIDENCE]` block).
8.  **Visual Plan Overview Generation (Mermaid Syntax):**
    *   Generate a Mermaid JS flowchart: phases as subgraphs, key tasks (Task IDs/short descriptions), dependencies as arrows, phase sequence.
    *   Embed in `00_task_launch_plan.md` (after Confidence Score). Example:
        \`\`\`mermaid
        graph TD; subgraph Phase 1; P1_T1["T1.1"]; P1_T2["T1.2"]; end; subgraph Phase 2; P2_T1["T2.1"]; end; P1_T1-->P2_T1; P1_T2-->P2_T1;
        \`\`\`
        (Adapt complexity; for large plans, show major tasks/flows).

**III. OUTPUT GENERATION REQUIREMENTS:**
All paths are relative to the 'Main Iteration Folder' from `[[USER PATH CONFIGURATION]]` (which is repository root if '.').

**A. Main Development Plan Document (e.g., `gritos_dev_plan.md`):**
    *   Develop content based on user's request.
    *   **Location:** The 'Submodule Plan Destination' path (e.g., `plan/gritos_dev_plan.md` if Main Iteration Folder is `.` and Submodule Plan Destination is `plan`).

**B. Task Prompt Files (`.txt`):**
    *   **Location:** The 'Prompts Folder' (e.g., `prompts/tasks/` if Main Iteration Folder is `.` and Prompts Folder is `prompts/tasks`).
    *   **For Each Task, Generate a Prompt File Containing:**
        1.  **Task Overview:** Purpose, contribution, reference to Main Dev Plan section. If dependent (from TLP), reiterate dependency (e.g., "Depends on `TaskX_ID`...").
        2.  **Sub-Development Plan Directive:** "Create `<PhaseTaskName>_dev_plan.md` in `[Actual User-Specified Path for Submodule Plans from [[USER PATH CONFIGURATION]]]`. List steps; mark progress."
        3.  **Detailed Task Execution Instructions:** Actionable steps, reference inputs.
        4.  **Output Management:** "Place new, task-specific files in `[Actual User-Specified Task Output Base Path]/<PhaseTaskName>/`. For modifying shared code in `[Actual User-Specified Task Output Base Path]`, be specific and use IEP `Owned-Files-Modules`."
        5.  **IEP Adherence:** "Follow `Information_Exchange_Protocol.md` (in `[Actual Path to Prompts Folder]/Information_Exchange_Protocol.md, it is in the repo`). Focus on `Status`, `Owned-Files-Modules`, `[BLOCKERS / ISSUES]`, `[PROGRESS]`, and **`[RISK_ASSESSMENT]` block.**"
        6.  **"Next Steps, Learnings & Improvements" Doc Directive:** "Create `<PhaseTaskName>_next_steps.md` with your `_dev_plan.md` in `[Actual User-Specified Path for Submodule Plans]`. Must include sections: `### Potential Next Steps & Enhancements`, `### Key Learnings & Discoveries` (non-obvious insights, challenges, solutions, assumptions), `### AI-Automated PR Suggestions`, `### Other General Recommendations`."
        7.  **Inter-AI Comm Directive:** "Use `[Actual Path to Prompts Folder]/ipc/` for persistent logs/artifacts not fitting commits. Document usage in commits."
        8.  **Retry Logic:** "```If retrying post-crash, investigate git log, `[Actual User-Specified Path for Submodule Plans]/<PhaseTaskName>_dev_plan.md`, output dir `[Actual User-Specified Task Output Base Path]/<PhaseTaskName>/`, and logs in `[Actual Path to Prompts Folder]/ipc/`. Document findings in `_dev_plan.md`. Continue without repeating.```"
        9.  **Path Referencing:** Append ", it is in the repo." to file paths.
        10. **Risk Assessment Directive:** "For each commit, assess risk (Low, Medium, High) based on scope, complexity, impact, test coverage, dependencies. Include in commit message: `[RISK_ASSESSMENT]` block with `- Risk-Level: <level>` and `- Justification: <explanation>` in `Notes-To-Next-Jules`."
        11. **Append Selected Add-ons:** "Retrieve ordered list of selected add-ons from Section I.B. For each, append its full text to this prompt. Resolve placeholders (`[Actual Path to Prompts Folder]`, `[Actual User-Specified Path for Submodule Plans]`, `[Actual User-Specified Task Output Base Path]`, `<ExactOriginalTaskPromptFilenameBase>`) within add-on text using values from `[[USER PATH CONFIGURATION]]` (Section I.A) and current task context."

**C. Supporting Documentation (in the 'Prompts Folder' for the target project, e.g. `prompts/tasks/`):**
1.  **`Information_Exchange_Protocol.md`:**
    *   Read content from user-provided `/prompts/iep/Base_IEP.txt` (path relative to repo root where you operate; it is in the repo).
    *   Create `Information_Exchange_Protocol.md` in the 'Prompts Folder' you're generating (e.g., `[Actual Path to Target Project's Prompts Folder]/Information_Exchange_Protocol.md`).
    *   Write exact content from `/prompts/iep/Base_IEP.txt` into it. Ensure example `Notes-To-Next-Jules` in it includes an illustration of `[RISK_ASSESSMENT]` block (user's `Base_IEP.txt` should ideally have this).
2.  **`00_task_launch_plan.md`:**
    *   Create based on template. Dynamically replace placeholders (`[Project Name Placeholder]`, `[Iteration Placeholder XX]`, all `[Actual...Path...]` placeholders) with values from `[[USER PATH CONFIGURATION]]` and user request. Generate task listing.
    *   **Launch Commands Path Logic:** Ensure launch paths are correct relative to repo root. E.g., if 'Main Iteration Folder' is `.` and 'Prompts Folder' is `prompts/tasks`, path is `prompts/tasks/p1_t1.txt...`. If 'Main Iteration Folder' is `iter_out` and 'Prompts Folder' is `iter_out/prompts/tasks`, path is `iter_out/prompts/tasks/p1_t1.txt...`.
    *   **Missing Add-on Warnings:** If any add-ons selected in `[[USER_ADDON_SELECTION]]` were not found, add a warning section at the top of TLP (after Visual Plan Overview).
    *   **TLP TEMPLATE:**
        \`\`\`markdown
        # Task Launch Plan for [Project Name Placeholder] - Iteration [Iteration Placeholder XX]

        **Overall Planning Confidence:** <High | Medium | Low (P-AI to populate)>
        **Confidence Rationale:** <P-AI to provide a brief explanation...>

        ---
        ## Visual Plan Overview
        \`\`\`mermaid
        [[[P-AI TO GENERATE MERMAID FLOWCHART DIAGRAM HERE]]]
        graph TD; A["T1"] --> B["T2"];
        \`\`\`
        *(Diagram above is placeholder, to be replaced by actual plan.)*

        [[[P-AI TO INSERT WARNINGS ABOUT MISSING ADD-ONS HERE, IF ANY]]]

        ---
        ## 1. Overview
        Welcome...
        **Key Files & Folders (relative to repo root, as this TLP is within a Prompts Folder like `prompts/tasks/`):**
        *   `[Actual Path to Prompts Folder]/`: Contains this TLP, task prompts, IEP.
        *   `[Actual Path to Prompts Folder]/ipc/`: For inter-AI communication.
        *   `[Actual User-Specified Path for Submodule Plans]`: Location of `_dev_plan.md`s.
        *   `[Actual User-Specified Task Output Base Path]`: Base for task deliverables.
        ## 2. Execution Workflow
        1. Review Phases...
        2. Parallel Task Execution: ...unless "Dependencies" field indicates sequence...
        3. Provide Prompts...
        4. Monitor Progress (IEP at `[Actual Path to Prompts Folder]/Information_Exchange_Protocol.md`, `_dev_plan.md`s, IPC folder)...
        5. Handle Crashes...
        ## 3. Launching Tasks
        ---
        [[YOU MUST DYNAMICALLY GENERATE THE TASK LISTING BELOW]]
        [[FOR EACH PHASE:]]
        ### **Phase <Num> Tasks**
        *(Respect "Dependencies" for sequencing)*
        [[FOR EACH TASK:]]
        #### **Task: `<Full Prompt Filename>`**
        *   **Description:** <1-2 sentence objective>
        *   **Estimated Effort:** <S|M|L|XL>
        *   **Dependencies:** <(P-AI Populates if Applicable) e.g., "Depends on: `p1_t1_other.txt` - Reason...">
        *   **Initial Launch:** \`\`\`[Path to Prompt File relative to Repo Root] is the prompt, it is in the repo.\`\`\`
        *   **Retry if Crashed:** \`\`\`[Path to Prompt File relative to Repo Root] is the prompt, ...continue task.\`\`\`
        ---
        [[END OF DYNAMIC TASK LISTING]]
        ## 4. Iteration Completion
        Review `_next_steps.md` in `[Actual User-Specified Path for Submodule Plans]`. Good luck!
        \`\`\`
        (End of 00_task_launch_plan.md template)

**IV. FINAL INSTRUCTION TO PLANNING AI & SELF-CORRECTION:**
A. **Planning AI Self-Correction and Verification Checks (MANDATORY):**
    Before finalizing, perform these checks on *each generated task prompt*:
    1. Core Directives: `_dev_plan.md` creation? `_next_steps.md` (with `### Key Learnings & Discoveries`)? IEP reminder? Risk Assessment directive? Retry Logic?
    2. Path Placeholders: All `[Actual...Path...]` and `<PhaseTaskName>` resolved correctly? Paths have ", it is in the repo."?
    3. Mandated Add-on Inclusion: Logic for `[[USER_ADDON_SELECTION]]` applied? Selected add-ons appended with internal placeholders resolved?
    4. Clarity: Task Overview & Instructions clear and actionable?
    5. Dependency Statement: Coherent if task is dependent?
    Correct any discrepancies.
B. **Final Commit Message by Planning AI:**
    Adhere to IEP (from `/prompts/iep/Base_IEP.txt`). Task-ID "INITIAL_PROJECT_PLANNING_ITER_XX". Notes must state self-checks (IV.A) performed and include `[PLANNING_CONFIDENCE]` block.

[[END OF MASTER PROMPT SEGMENT (v0.3.2 - Canonical Paths, Internal Configs, 8 Features)]]
