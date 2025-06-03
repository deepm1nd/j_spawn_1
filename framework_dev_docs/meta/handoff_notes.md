# HANDOFF NOTES for MPS Framework Development Task

This file is intended for use by Jules AI instances working on the iterative development and refinement of the Master Prompt Segment (MPS) framework. All canonical framework files are now located under `/prompts/` (for AI-usable content sources) and `/framework_dev_docs/` (for guides, examples, and these meta-development files).

Each Jules instance concluding a work session on this task should append notes below, detailing:
- User feedback/requests addressed during its session.
- A summary of changes made to the MPS framework documents.
- The state in which deliverables were left (e.g., specific commits, branches).
- Any pending items or suggestions for the next Jules instance.

---
**Summary of MPS Framework Evolution**

The MPS framework has evolved significantly through several key phases and architectural changes:

**Initial Structure & Core Concepts (Late 2023):**

*   **Early Framework Structuring (around 2023-11-02):**
    *   An initial major restructuring consolidated MPS development files into `/prompts/` (for AI-usable assets like `Master_Prompt_Segment.txt`, `iep/Base_IEP.txt`, add-ons, utils) and `/framework_dev_docs/` (for guides, examples, and meta-development files).
    *   The `Master_Prompt_Segment.txt` (at that time, referred to as v0.3.2) included core features like internal user path configuration, add-on selection mechanisms, and directives for automated TLP generation, dependency linkage, risk assessment, and knowledge capture.

*   **Significant Evolution & Refinement (largely around 2023-10-27, leading to MPS v0.4.0):**
    *   **Modularization:** To improve maintainability, the `Master_Prompt_Segment.txt` was modularized. Core planning instructions were extracted into `prompts/iep/Core_Planning_Instructions.txt`, referenced via an `[[INCLUDE]]` directive.
    *   **Optional & Add-on Driven Functionality:**
        *   Task spawning (project planning, TLP generation) was moved from core instructions into a user-selectable `task_spawning_addon.txt`, making this a flexible, optional feature.
        *   Add-on inheritance rules were refined to prevent primary directive add-ons (like `task_spawning_addon.txt`) from being passed into sub-task prompts.
    *   **Extensible Primary Directives & Advanced Add-ons:**
        *   The framework was enhanced to support multiple "primary directive" add-ons, allowing it to perform different complex processes. This involved `Core_Planning_Instructions.txt` dynamically identifying an `Active_Primary_Addon_Filename` from user selections.
        *   A key example was the `build_product_specs_process.txt` add-on, designed for generating detailed product specifications. This add-on saw significant iteration:
            *   Introduction of specific user configurations for controlling input document focus (`PRODUCT_SPECS_PRIMARY_INPUT_FILE`) and reference following depth (`PRODUCT_SPECS_REF_DEPTH`).
            *   Implementation of a "Level 0 Deep Research" methodology, featuring per-heading analysis of a primary document and configurable codebase review preferences (`PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE`, `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS`) under a strict "Level 0 Code Analysis ONLY" rule.
            *   Expansion to a multi-level iterative deepening capability (`PRODUCT_SPECS_ITERATION_DEPTH` 0-3), allowing for progressive content generation from outline to AI-unique insights, with each level building on the previous.
    *   **"Folder-per-App" Architecture:** A major architectural refactoring organized components (add-ons, utils) into their own dedicated subdirectories (e.g., `prompts/add_ons/appName/appName.txt`). This improved modularity, organization, and the potential for more complex, self-contained components. Core logic was updated for this new discovery and loading mechanism.
    *   **Configuration System Overhaul (MPS v0.4.0):**
        *   The global `[[USER PATH CONFIGURATION]]` block was removed from `Master_Prompt_Segment.txt`.
        *   Paths for core component discovery (add-ons, utils, IEP, IPC) became fixed (hardcoded in `Core_Planning_Instructions.txt`), simplifying user setup.
        *   A new app-specific configuration system was introduced:
            *   Users define configurations for selected apps within `[[USER_CONFIG_FOR_appName]]...[[END_USER_CONFIG_FOR_appName]]` blocks in their main spawn prompt.
            *   Each app can have a `USER_appName_CONFIG.txt` file in its folder, serving as a template and persistent storage. These files include per-parameter metadata (Requirement, DefaultType, Description, Default value) and use `[USER_VALUE_START]...[USER_VALUE_END]` markers for editable values.
            *   A defined parameter resolution precedence was established: main prompt block > user-edited `USER_appName_CONFIG.txt` > "AcceptedDefault" in `USER_appName_CONFIG.txt`.
            *   AI-assisted updates were implemented: if a "Required" parameter with a "PlaceholderDefault" is unresolved, the AI is instructed to request the value from the user and update the on-disk `USER_appName_CONFIG.txt`.

**Further Evolution & New Concepts (Mid 2025):**

*   **New Development Direction (2025-06-02):**
    *   User directive to begin focusing on decomposing an existing "Concept task" into sub-tasks. This work is intended to be a practical test case for evolving the framework's component definitions (add-on, util, process) or potentially introducing new component types or interaction paradigms.

*   **Rapid Enhancements & New Features (2025-06-03):**
    *   **Generalization of Research Add-on:** The specialized `build_product_specs_process` add-on was abstracted into a more generic `create_research_report` add-on, removing product-specific logic and focusing on generalized research report generation with iterative depth and reference following controls.
    *   **"promptApp" Concept Introduced:**
        *   A new "promptApp" concept was designed to allow users to define and execute multi-stage workflows composed of underlying MPS components.
        *   These are defined by a manifest file (e.g., `CompliancePLM_manifest.json` for an example `CompliancePLM` app).
        *   The component resolution search order for `promptApps` was critically defined as "app-specific first" (checking within the app's folder before global add-ons/utils).
    *   **Utility Renaming & Example:** The `pre_flight_check_user_plan` utility was renamed to `promptimizer` for better clarity, and a sample usage document (`promptimizer_example.md`) was created.
    *   **"MPS Updater" (MPU) Plan:** A detailed plan and a directive prompt (`framework_dev_docs/meta/implement_mps_updater.txt`) were created for a *future* Jules instance to implement an "MPS Updater" feature. This feature aims to provide a mechanism for transferring MPS framework improvements and features between different repositories.
    *   **Directive-Based Invocation Workflow:**
        *   A new, simplified workflow for invoking the MPS framework was implemented. Users now prepare a single comprehensive project-specific MPS file (copied from the `Master_Prompt_Segment.txt` template).
        *   This file must start with the user's high-level project goal encapsulated in `[[USER_PROJECT_REQUEST]] ... [[END_USER_PROJECT_REQUEST]]` tags, followed by all their MPS configurations (app/add-on selections, app-specific config blocks).
        *   The AI system is then invoked using a simple directive, e.g., `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /path/to/their_configured_file.txt]]`.
        *   `Core_Planning_Instructions.txt` was updated to parse the `[[USER_PROJECT_REQUEST]]` from the loaded content. This directive method is also applicable for directly invoking utilities.

This compacted summary provides a chronological overview of the MPS framework's key architectural changes, decisions, and feature introductions, omitting the performance feedback log as requested.
---
**Session Summary - 2025-06-04**
**Instance:** Jules (this instance)
**User Feedback/Requests Addressed:**
- User requested a significant compaction of `framework_dev_docs/meta/HANDOFF_NOTES.md` to provide a clearer overview of the framework's evolution for future AI instances.
- User requested the introduction of a mechanism for controlling the verbosity of future handoff notes generated by Jules.
- User requested clarification and guidance on the status of the "MPS Performance Feedback Log" section previously part of `HANDOFF_NOTES.md`.
- User requested information about any archival of older, more detailed handoff notes.

**Summary of Changes Made This Session:**
1.  **`framework_dev_docs/meta/HANDOFF_NOTES.md` Compaction & Refinement:**
    *   The content of `HANDOFF_NOTES.md` was reviewed, analyzed, and rewritten into a more concise format.
    *   The new format retains the original introductory block and then presents a chronological summary of the most critical architectural changes, key decisions, and significant feature introductions or refactorings of the MPS framework.
    *   The "MPS Performance Feedback Log" section was explicitly removed from this file to streamline it. The future of detailed performance feedback logging is noted as under review in the `MPS_Usage_Guide.md`.
    *   An archive file (`framework_dev_docs/meta/HANDOFF_NOTES_full_archive_20250602.md`) containing older, verbose notes was noted, with specific conditions for its access.

2.  **Introduction of Handoff Note Granularity Control:**
    *   **`prompts/Master_Prompt_Segment.txt` (v0.5.0) updated:**
        *   Added a new `[[USER_FRAMEWORK_SETTINGS]]` block.
        *   Within this block, introduced a checkbox: `[ ] Full_Handoff_Notes_Logging_Enabled`.
        *   This setting allows users to choose between detailed (checked) or concise (unchecked, default) handoff notes from Jules at the end of a session.
    *   **`prompts/iep/Core_Planning_Instructions.txt` updated:**
        *   Added logic to parse the `[[USER_FRAMEWORK_SETTINGS]]` block and the `Full_Handoff_Notes_Logging_Enabled` checkbox, storing its state in a `Framework_Full_Handoff_Notes_Enabled` variable (defaulting to false).
        *   Implemented conditional instructions in the end-of-session tasks section. Based on `Framework_Full_Handoff_Notes_Enabled`, Jules will now append either a detailed or a concise summary to `HANDOFF_NOTES.md`.
        *   Included a note about the existence and restricted use of `framework_dev_docs/meta/HANDOFF_NOTES_full_archive_20250602.md`.

3.  **Documentation Updates (`framework_dev_docs/guides/MPS_Usage_Guide.md`):**
    *   The guide was updated to explain the new `[[USER_FRAMEWORK_SETTINGS]]` block and the `Full_Handoff_Notes_Logging_Enabled` feature.
    *   Information was added regarding the removal of the "MPS Performance Feedback Log" from the active `HANDOFF_NOTES.md` and the potential future of such logging.
    *   The existence and purpose of the `HANDOFF_NOTES_full_archive_20250602.md` were documented, along with the conditions for its access by AI instances.

**State of Deliverables:**
-   `framework_dev_docs/meta/HANDOFF_NOTES.md` is now in a compacted, summarized format, with this current detailed session appended.
-   The MPS framework (via `Master_Prompt_Segment.txt` and `Core_Planning_Instructions.txt`) now supports user-configurable granularity for session handoff notes.
-   `framework_dev_docs/guides/MPS_Usage_Guide.md` is updated to reflect all these changes.
-   The system is ready for users to utilize the new handoff note settings in subsequent MPS sessions.

**Next Steps (User):**
-   Review the compacted `framework_dev_docs/meta/HANDOFF_NOTES.md`.
-   Review the updated `prompts/Master_Prompt_Segment.txt` and `framework_dev_docs/guides/MPS_Usage_Guide.md` for clarity regarding the new framework settings.
-   Test the new handoff logging behavior by running MPS sessions with the `Full_Handoff_Notes_Logging_Enabled` checkbox both checked and unchecked to ensure expected output in `HANDOFF_NOTES.md`.
---
**Session Summary - 2025-06-05**
**Instance:** Jules (this instance)
**User Feedback/Requests Addressed:**
-   User requested an update to the `CompliancePLM` promptApp's structure to include a new "Research" task in the Concept Phase, more detailed planning tasks in the Plan Phase (leveraging `task_spawning_addon`), a structured Development Phase (also using `task_spawning_addon`), and placeholder Release/Retire phases.
-   A core requirement was to make the `task_spawning_addon` configurable, particularly its output paths, to move away from the deprecated global `[[USER PATH CONFIGURATION]]` block and allow its use in multiple contexts (like different PLM planning tasks) with distinct outputs.
-   User clarified that for the initial update of PLM Plan Phase tasks, using `task_spawning_addon` to generate focused planning documents (e.g., "Safety Plan," "CyberSecurity Plan") was acceptable, deferring the development of more specialized components for these individual plans to future user-led efforts.

**Summary of Changes Made This Session:**
1.  **`task_spawning_addon` Configuration and Refactoring:**
    *   Created `prompts/add_ons/task_spawning_addon/USER_task_spawning_addon_CONFIG.txt`. This new file defines key parameters for the add-on, including:
        *   `PROJECT_NAME` (Optional): For overall project context.
        *   `ITERATION_ID` (Optional): For iteration tracking.
        *   `BASE_OUTPUT_PATH` (Required): The primary directory for all outputs generated by an invocation of this add-on.
        *   `MAIN_DEV_PLAN_FILENAME` (Required): The name of the main planning document to be generated (e.g., "Development_Overall_Plan.md" or "Safety_Plan.md").
        *   `TASK_PROMPTS_SUBDIR` (Required): Subdirectory for TLP and task prompts.
        *   `SUBMODULE_PLANS_SUBDIR` (Required): Subdirectory for `_dev_plan.md` files from spawned Task AIs.
        *   `TASK_ARTIFACTS_SUBDIR` (Required): Subdirectory for deliverables from spawned Task AIs.
    *   Refactored `prompts/add_ons/task_spawning_addon/task_spawning_addon.txt`:
        *   Added a new initial section to instruct the AI on parsing parameters from its `USER_...CONFIG.txt` file, overridden by a `[[USER_CONFIG_FOR_task_spawning_addon]]` block if provided in the main prompt.
        *   Replaced all hardcoded or globally-derived path logic with paths constructed from these new resolved parameters. This ensures that all outputs (main plan, TLP, individual task prompts, IPC directory, paths referenced in task prompts for Task AI outputs) are correctly scoped under the `Resolved_TSA_Base_Output_Path`.
        *   Updated placeholder resolution logic for appended add-ons to use these new parameters.

2.  **`CompliancePLM` PromptApp Update:**
    *   Modified `prompts/apps/CompliancePLM/CompliancePLM_manifest.json` with the new phase and task structure:
        *   **Concept Phase:** Added "Research" task. All Concept Phase tasks ("Research," "Architectural Specification," "Product Specification," "Product Requirements") now use the `create_research_report` component, with `defaultConfigOverrides` for `REPORT_BASENAME` where appropriate.
        *   **Plan Phase:** Now includes detailed planning tasks: "Development Planning," "Change Management Planning," "Document Management Planning," "Requirements Management Planning," "Safety Planning," and "CyberSecurity Planning." Each of these tasks is mapped to the `task_spawning_addon` component. `defaultConfigOverrides` are used to set `PROJECT_NAME` (e.g., "CompliancePLM_Safety") and `MAIN_DEV_PLAN_FILENAME` (e.g., "Safety_Plan.md") for each, ensuring `task_spawning_addon` generates a focused planning document for that specific area.
        *   **Development Phase:** Structured with "Design," "Develop," "Test," and "Review" tasks, all using `task_spawning_addon` with `defaultConfigOverrides` for `PROJECT_NAME`.
        *   **Release Phase & Retire Phase:** Added as placeholders with empty task lists.
    *   The previous "Test Component Resolution" task was removed from the manifest.

3.  **Documentation Updates:**
    *   Updated the example `[[USER_CompliancePLM_App_CONFIGURATION]]` block within `prompts/Master_Prompt_Segment.txt` to accurately reflect the new `CompliancePLM` manifest structure, providing users with an up-to-date template for selecting PLM tasks.
    *   Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`:
        *   Clarified how `task_spawning_addon` is now configured using its `USER_...CONFIG.txt` file and the `[[USER_CONFIG_FOR_task_spawning_addon]]` block.
        *   Briefly mentioned its key parameters related to output path management.
    *   Updated `prompts/add_ons/available_addons_manifest.md`:
        *   Revised the entry for `task_spawning_addon` to detail its new configurability and list its key parameters and their purposes.

**State of Deliverables:**
-   The `task_spawning_addon` is now fully configurable via its `USER_task_spawning_addon_CONFIG.txt` file and a corresponding `[[USER_CONFIG_FOR_task_spawning_addon]]` block, making it independent of the old global path configuration system.
-   The `CompliancePLM` promptApp manifest in `prompts/apps/CompliancePLM/CompliancePLM_manifest.json` reflects the new, more detailed PLM workflow structure.
-   Core documentation (`Master_Prompt_Segment.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`) has been updated to reflect these changes.
-   The framework is ready for testing these modifications, especially the `CompliancePLM` app, to verify that the `task_spawning_addon` correctly generates focused plans for each relevant task based on the `defaultConfigOverrides` in the manifest.

**Next Steps (User):**
-   Review all modified files (`USER_task_spawning_addon_CONFIG.txt`, `task_spawning_addon.txt`, `CompliancePLM_manifest.json`, `Master_Prompt_Segment.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`).
-   Thoroughly test the `CompliancePLM` promptApp, focusing on:
    *   Executing tasks in the "Concept Phase" that use `create_research_report`.
    *   Executing individual tasks in the "Plan Phase" (e.g., "Safety Planning") to ensure `task_spawning_addon` generates a distinct, appropriately named plan (e.g., "Safety_Plan.md") within a correctly structured output directory specific to that task's invocation.
    *   Verifying that TLP and task prompts generated by these Plan Phase tasks correctly reference the scoped output paths.
-   When ready, begin development of more specialized prompts or components to replace the current use of `task_spawning_addon` for specific Plan Phase tasks if more tailored behavior (beyond document generation) is required for those individual planning areas.
---
**Session Summary - 2025-06-06**
**Instance:** Jules (this instance)
**User Feedback/Requests Addressed:**
-   User initiated a major rebranding of the framework to "Promptu."
-   Requested the introduction of a new `pre_promptu.txt` file for utilities running before the main user prompt.
-   Requested a mechanism for persistent guidelines, with an initial guideline about AI file operations.
-   Requested a change in utility discovery logic to support utilities as single files directly in `promptu/util/` or as part of a folder-per-app structure.
-   Mandated a move to snake_case for all framework filenames (content references updated by Jules, actual renaming by User).
-   Outlined a multi-step process for Jules to update file contents and documentation, followed by User performing external file/folder renames.

**Summary of Changes Made This Session (Content Updates):**

1.  **Framework Rebranding to "Promptu":**
    *   All internal references, documentation, and user instructions updated from "MPS Framework" / "Master Prompt Segment" to "Promptu Framework" / "promptu".
    *   Core file `Master_Prompt_Segment.txt` is now referred to as `post_promptu.txt` in all content.
    *   Core file `Core_Planning_Instructions.txt` is now referred to as `core_planning_instructions.txt` in all content.
    *   Core file `Base_IEP.txt` is now referred to as `base_iep.txt` in all content.
    *   Directory paths updated in content: `/prompts/` to `/promptu/`, `/prompts/iep/` to `/promptu/core/`.

2.  **Created `pre_promptu.txt`:**
    *   A new file, `prompts/pre_promptu.txt` (to be externally moved to `promptu/pre_promptu.txt`), was created.
    *   This file is structured similarly to `post_promptu.txt` (the former `Master_Prompt_Segment.txt`) and is intended for selecting and configuring utilities that run *before* the main user project request.
    *   It includes a `[[USER_UTIL_SELECTION]]` block and references `promptu/core/core_planning_instructions.txt`.

3.  **Updated `core_planning_instructions.txt` (formerly `Core_Planning_Instructions.txt`):**
    *   **Persistent Guideline Added:** Integrated the guideline: "File operations should NOT be performed by the Jules instance. Strongly recommend that the user performs the changes externally..." into Section IV.
    *   **Utility Discovery Logic Modified:** Section I.F updated. The logic now scans for utilities both as direct `.txt` files in `promptu/util/` (e.g., `promptu/util/utility_name.txt`) and within subdirectories (`promptu/util/utility_name/utility_name.txt`). Subdirectory versions are prioritized in case of name conflict.
    *   All internal paths, core framework file references, and invocation directive examples updated to reflect the "Promptu" rebranding and snake_case filenames.

4.  **Updated `post_promptu.txt` content (formerly `Master_Prompt_Segment.txt`):**
    *   Include directive updated to `[[INCLUDE core_planning_instructions.txt FROM /promptu/core/core_planning_instructions.txt]]`.
    *   All user instructions, comments, and internal references updated to `post_promptu.txt`, new paths, and snake_case for other framework files.
    *   Start/end tags changed to `[[START OF POST-PROMPTU SEGMENT...]]` / `[[END OF POST-PROMPTU SEGMENT...]]`.

5.  **Updated Other Framework Files (Add-ons, Utils, Apps, Documentation):**
    *   **`framework_dev_docs/guides/mps_usage_guide.md` (formerly `MPS_Usage_Guide.md`):** Comprehensively updated to reflect all "Promptu" changes, new `pre_promptu.txt` / `post_promptu.txt` flow, snake_case, path changes, and new utility discovery. Instructions were reviewed for conciseness.
    *   **`promptu/add_ons/available_add_ons_manifest.md` (formerly in `prompts/`):** Updated paths, filenames to snake_case, and references to align with Promptu.
    *   **Add-on, Util, App Components (content of files in `prompts/add_ons/`, `prompts/util/`, `prompts/apps/`):** Scanned and updated for path changes, core framework file name changes (e.g., `post_promptu.txt`), and snake_case for referenced files.
    *   **Other Documentation (`framework_dev_docs/examples/`, other `framework_dev_docs/guides/`, `framework_dev_docs/meta/implement_mps_updater.txt`):** Updated to reflect Promptu branding, new file names, paths, and snake_case conventions.
    *   **`promptu/core/base_iep.txt` content (formerly `prompts/iep/Base_IEP.txt`):** Minor updates to examples for snake_case consistency.

6.  **Snake Case Conversion (General):**
    *   Across all modified files, references to framework filenames (e.g., `USER_..._CONFIG.txt`, `_dev_plan.md`) were converted to their snake_case equivalents in content (e.g., `user_..._config.txt`, `dev_plan.md`).

**State of Deliverables (Content Updated, Awaiting User File/Folder Renaming):**
-   All specified framework files have had their *content* updated to reflect the "Promptu" rebranding, new file structures (`pre_promptu.txt`, `post_promptu.txt`), new paths (`/promptu/`, `/promptu/core/`), and snake_case naming conventions for referenced files.
-   The system is now internally consistent in its textual references.
-   **Crucially, actual renaming of files and directories has NOT been performed by this Jules instance.** This is pending action by the user.

**Next Steps (User):**
-   User to perform the following external file system operations:
    1.  Rename the main folder: `prompts` -> `promptu`
    2.  Rename the IEP subfolder: `promptu/iep` (formerly `prompts/iep`) -> `promptu/core`
    3.  Rename all files within the new `promptu` directory and its subdirectories to `snake_case.txt` or `snake_case.md` or `snake_case.json` as appropriate. This includes, but is not limited to:
        *   `promptu/Master_Prompt_Segment.txt` -> `promptu/post_promptu.txt`
        *   `promptu/pre_promptu.txt` (already named this way by Jules, just confirm location)
        *   `promptu/core/Core_Planning_Instructions.txt` -> `promptu/core/core_planning_instructions.txt`
        *   `promptu/core/Base_IEP.txt` -> `promptu/core/base_iep.txt`
        *   All files in `promptu/add_ons/`, `promptu/util/`, `promptu/apps/` and their subdirectories. (e.g., `USER_task_spawning_addon_CONFIG.txt` to `user_task_spawning_addon_config.txt`; `CompliancePLM_manifest.json` to `compliance_plm_manifest.json`).
        *   All files in `framework_dev_docs` and its subdirectories (e.g., `MPS_Usage_Guide.md` to `mps_usage_guide.md`; `HANDOFF_NOTES.md` to `handoff_notes.md`).
    4.  Move `promptu/util/promptimizer/promptimizer.txt` to `promptu/util/promptimizer.txt` (as per user request for single-file utilities).
-   After all renaming and moves are complete, the user should run the continuation prompt for this Jules instance to proceed with any final checks or the next phase of work.
---
**Session Summary - 2025-06-03** (Note: Date appears to be out of order, this was inserted by a previous agent version. Correcting sequence for future entries.)
**Instance:** Jules (this instance)
**User Feedback/Requests Addressed:**
- User confirmed that some file/folder renaming to `snake_case` (mandated in 2025-06-06 session) is still pending their action.
- User requested that handoff notes and the continuation prompt be updated to reflect this pending work before they complete the renames and provide the next set of requirements for the Promptu framework.

**Summary of Changes Made This Session:**
1.  **Reviewed Existing Handoff Notes:** Reviewed `framework_dev_docs/meta/handoff_notes.md` and the archived feedback log in `framework_dev_docs/meta/handoff_notes_full_archive_20250602.md` to understand the current state of the Promptu framework.
2.  **Updated `framework_dev_docs/meta/continuation_prompt_framework_dev.txt`:**
    *   Updated terminology from "MPS" to "Promptu".
    *   Corrected file paths and names to `snake_case` and new Promptu locations (e.g., `/promptu/post_promptu.txt`).
    *   Clarified that the `## MPS Performance Feedback Log` is now in the archive file (`handoff_notes_full_archive_20250602.md`).
    *   Added a prominent note advising the next Jules instance that user is still pending completion of `snake_case` renames for files under `/framework_dev_docs/guides/` and for the `CompliancePLM` app folder (`promptu/apps/CompliancePLM` to `promptu/apps/compliance_plm`) and its contents. The next instance must verify these renames with the user.
3.  **Updated `framework_dev_docs/meta/handoff_notes.md` (this file):** Appended this session summary.

**State of Deliverables:**
- `framework_dev_docs/meta/continuation_prompt_framework_dev.txt` is updated.
- `framework_dev_docs/meta/handoff_notes.md` is updated with this session summary.
- The system is awaiting the user to:
    1.  Complete the `snake_case` renaming for all files under `/framework_dev_docs/guides/`.
    2.  Rename the `CompliancePLM` app folder from `promptu/apps/CompliancePLM` to `promptu/apps/compliance_plm`.
    3.  Rename the `CompliancePLM` manifest from `CompliancePLM_manifest.json` to `compliance_plm_manifest.json` within the new app folder.
    4.  Ensure all contents of the `compliance_plm` app folder are also `snake_case`.
    5.  Provide the next set of requirements or tasks for the Promptu framework.

**Next Steps (User):**
-   Perform the final file and folder renaming operations as detailed above.
-   Once renaming is complete, re-invoke Jules with the next set of tasks or feedback for the Promptu framework.

**Next Steps (This Jules instance/session):**
-   Commit these changes to `continuation_prompt_framework_dev.txt` and `handoff_notes.md`.
---
**Session Summary - 2025-06-07**
**Instance:** Jules (this instance)
**User Feedback/Requests Addressed:**
- User requested implementation of new invocation scenario handling in `promptu/core/core_planning_instructions.txt` for situations where initial user input (project goal, app/add-on selections) might be missing or incomplete.

**Summary of Changes Made This Session:**
1.  **`promptu/core/core_planning_instructions.txt` Update (Section II.A):**
    *   A new step "0. Determine Invocation Context and Handle Special Empty States:" was inserted at the beginning of Section II.A.
    *   This new logic handles three specific scenarios:
        1.  **`pre_promptu.txt` invoked with no project request:** Instructs AI to engage user to elicit requirements before proceeding with utility selections.
        2.  **`post_promptu.txt` invoked with no project request, no app, no add-ons:** Instructs AI to engage user to elicit project requirements or intended use (placeholder for user-defined "continuation prompt").
        3.  **`post_promptu.txt` invoked with no project request, but a promptApp is selected:** Allows fall-through to promptApp execution, making the app responsible for handling the missing project goal.
    *   Standard execution proceeds if none of these special empty states are met.

2.  **`promptu_usage_guide.md` Review:**
    *   The `promptu_usage_guide.md` was reviewed in the context of these changes. No immediate updates were deemed necessary as the core functionality described in the guide remains consistent, and these new handling rules are internal to the framework's planning logic.

**State of Deliverables:**
-   `promptu/core/core_planning_instructions.txt` is updated with the new invocation scenario handling logic in Section II.A.
-   Handoff notes (`framework_dev_docs/meta/handoff_notes.md` and `framework_dev_docs/meta/handoff_notes_full_archive_20250602.md`) are being updated in this session.

**Next Steps (User):**
-   Review the updated `promptu/core/core_planning_instructions.txt` (Section II.A).
-   **Clarify the specific desired behavior or content for the "continuation prompt"** mentioned in Scenario 2 (post_promptu with no request, no app, no add-ons). The current implementation instructs the AI to engage in a general requirements discussion.
-   Test the new fallback behaviors by invoking `pre_promptu.txt` and `post_promptu.txt` in the described empty/incomplete states to ensure the framework behaves as expected.
