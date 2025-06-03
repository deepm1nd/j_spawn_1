# HANDOFF NOTES for MPS Framework Development Task

This file is intended for use by Jules AI instances working on the iterative development and refinement of the Master Prompt Segment (MPS) framework. All canonical framework files are now located under `/prompts/` (for AI-usable content sources) and `/framework_dev_docs/` (for guides, examples, and these meta-development files).

Each Jules instance concluding a work session on this task should append notes below, detailing:
- User feedback/requests addressed during its session.
- A summary of changes made to the MPS framework documents.
- The state in which deliverables were left (e.g., specific commits, branches).
- Any pending items or suggestions for the next Jules instance.

---
**Session Summary - 2023-11-02**
**Instance:** Jules (this instance)
**Key Actions:**
-   MAJOR RESTRUCTURING: Consolidated all MPS framework development from previous `/mps_01/`, `/mps_02/` and root `/prompts/util/` directories into a new canonical structure:
    -   `/prompts/` now holds source texts for AI-usable assets (`Master_Prompt_Segment.txt`, `iep/Base_IEP.txt`, `add_ons/*`, `util/*`, `tasks/00_task_launch_plan.md` placeholder).
    -   `/framework_dev_docs/` now holds all guides, examples, and meta-development files for the framework.
-   The canonical `prompts/Master_Prompt_Segment.txt` has been updated to the latest design (effectively v0.3.2). This version includes 8 core features:
    1.  Internal `[[USER PATH CONFIGURATION]]` block.
    2.  Internal `[[USER_ADDON_SELECTION]]` block (user uses `[x] addon_file.txt`).
    3.  Planning AI reads Base IEP & Addons from user-provided paths in target repo (e.g., `/prompts/iep/Base_IEP.txt`, `/prompts/add_ons/`).
    4.  Correct guidance for Planning AI's output to its *own* `prompts/tasks/` dir.
    5.  Automated Dependency Linkage in TLP.
    6.  Estimated Time/Complexity in TLP.
    7.  Dynamic Risk Assessment directive (IEP v0.2 supports this).
    8.  Knowledge Capture in `_next_steps.md`.
    9.  Automated Sanity Checks by Planning AI.
    10. Planning AI Confidence Score reporting.
    11. Visual Plan Overview in TLP.
-   `prompts/iep/Base_IEP.txt` content is v0.2 (with Risk Assessment example).
-   All guides and examples in `/framework_dev_docs/` updated to reflect new paths and MPS functionality.
-   Old `/mps_01/` and `/mps_02/` directories were deleted.
-   All changes committed to new branch `feat/canonical-mps-framework-v1.0`.
**State of Deliverables:** Canonical framework v1.0 is established. Ready for user to prepare target repo for gritos test using these files, or for further feature design on these canonical files.
**Next Steps (User):** Prepare target repo for gritos test by:
    1. Creating a main spawn prompt (e.g., `target_repo/prompts/p_gritos_spawn.md`) using `framework_dev_docs/guides/p_gritos_dev_plan_spawn_EXAMPLE.md` as a guide and embedding content from `prompts/Master_Prompt_Segment.txt`.
    2. Editing `[[USER PATH CONFIGURATION]]` and `[[USER_ADDON_SELECTION]]` blocks within their spawn prompt's MPS section.
    3. Copying content from `prompts/iep/Base_IEP.txt` to `target_repo/prompts/iep/Base_IEP.txt`.
    4. Copying content from `prompts/add_ons/*` to `target_repo/prompts/add_ons/*`.
    5. Ensuring gritos input files are in target repo.
    6. Launching new Jules (Planning AI) with `"/prompts/p_gritos_spawn.md is the prompt..."`.
**Next Steps (This Jules instance/session):** Await user action or conclude session.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions:**
- Modularized `prompts/Master_Prompt_Segment.txt` by extracting its core planning instructions.
- Created `prompts/iep/Core_Planning_Instructions.txt` to house these extracted instructions.
- Updated `prompts/Master_Prompt_Segment.txt` to use an `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive to load the core instructions.
- Updated `framework_dev_docs/guides/MPS_Usage_Guide.md` to reflect these structural changes and provide updated instructions for users on preparing their target repositories.
**State of Deliverables:** Modularization of `Master_Prompt_Segment.txt` is complete. The `MPS_Usage_Guide.md` is updated. Framework is ready for testing of this new structure or for further refinement.
**Suggestions for Next Steps/Instance:**
- Test the new modular MPS structure by having a Planning AI use it for a sample project.
- Review the clarity of the `[[INCLUDE ...]]` directive and its processing requirements for the Planning AI.
- Consider if other sections of `Master_Prompt_Segment.txt` (if any more are added later) or other core prompt files could benefit from similar modularization.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions:**
- Completed a significant refactor to make the core task spawning/project planning functionality optional and add-on driven.
- **Created `prompts/add_ons/task_spawning_addon.txt`**: This new add-on now houses the detailed instructions for generating a main development plan, Task Launch Plan (TLP), and individual task prompts. This logic was moved from `prompts/iep/Core_Planning_Instructions.txt`.
- **Updated `prompts/iep/Core_Planning_Instructions.txt`**: This file now focuses on foundational framework logic: parsing user configurations (`[[USER PATH CONFIGURATION]]`, `[[USER_ADDON_SELECTION]]`), managing the add-on list, and implementing conditional logic to execute `task_spawning_addon.txt` (or other primary add-ons) if selected. It also handles utility outputs like `Information_Exchange_Protocol.md`.
- **Modified `prompts/Master_Prompt_Segment.txt`**: Added `task_spawning_addon.txt` to the `[[USER_ADDON_SELECTION]]` block as a user-selectable option, clarifying its role.
- **Updated `prompts/add_ons/available_addons_manifest.md`**: Added `task_spawning_addon.txt` with a description of its function and importance.
- **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`**: Extensively revised the guide to explain the new optional task spawning mechanism, how to enable it, and the AI's behavior with and without `task_spawning_addon.txt` selected.
**State of Deliverables:** The MPS framework now features optional, add-on-driven task spawning, significantly increasing its modularity and flexibility. Core logic for this is in place, and documentation has been updated. Ready for testing of various add-on combinations.
**Suggestions for Next Steps/Instance:**
- Thoroughly test the new conditional logic with `task_spawning_addon.txt` selected and not selected.
- Test with combinations of `task_spawning_addon.txt` and other utility add-ons.
- Evaluate if the instructions in `Core_Planning_Instructions.txt` regarding behavior *without* `task_spawning_addon.txt` (and no other primary add-on) are sufficiently clear and lead to sensible AI actions.
- Consider if any further logic from `Core_Planning_Instructions.txt` can be either minimized or moved to more specialized utility add-ons.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions:**
- Addressed user queries regarding add-on interactions, focusing on ensuring `task_spawning_addon.txt` is not inherited by Task AIs and clarifying add-on ordering for users.
- **Refined `prompts/iep/Core_Planning_Instructions.txt` (Section I.B)**: Modified the add-on parsing logic to create two distinct collections:
    - `All_Selected_Addons_Content_Map`: For Planning AI execution of all selected add-ons.
    - `Inheritable_Addons_Content_Ordered_List`: A filtered, ordered list of add-on *contents* (excluding `task_spawning_addon.txt`) specifically for Task AI prompt inheritance. This ensures `task_spawning_addon.txt`'s logic is not passed to Task AIs.
- **Updated `prompts/Master_Prompt_Segment.txt`**: Revised the `[[USER_ADDON_SELECTION]]` block to:
    - Recommend listing `task_spawning_addon.txt` last for user clarity if it's selected.
    - Add comments explaining that the order of other (inheritable) add-ons is preserved when appended to task prompts by this add-on.
- **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`**:
    - Documented that `task_spawning_addon.txt` itself is not inherited by Task AI prompts.
    - Clarified the significance of the listing order for inheritable add-ons in `[[USER_ADDON_SELECTION]]` and the recommendation for placing `task_spawning_addon.txt` last.
- Confirmed these changes align with user expectations for clarity and safe add-on processing.
**State of Deliverables:** Add-on processing logic and user guidance have been refined. The system is safer regarding add-on inheritance by Task AIs, and documentation provides clearer instructions on add-on ordering. Framework is robust and ready for submission or further advanced testing.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions:**
This session focused on introducing a new "process" type add-on, `build_product_specs_process.txt`, and significantly enhancing the core framework's ability to manage different types of primary directive add-ons.

*   **`build_product_specs_process.txt` Add-on Development:**
    *   Conceptualized and created the new add-on `prompts/add_ons/build_product_specs_process.txt` to guide an AI in generating comprehensive product specification documents.
    *   Defined necessary user configuration variables in `prompts/Master_Prompt_Segment.txt` for this process:
        *   `PRODUCT_NAME`
        *   `PRODUCT_SPECS_INPUT_DOCS_PATH`
        *   `PRODUCT_SPECS_GUIDANCE_DOCS_PATH`
        *   `PRODUCT_SPECS_OUTPUT_PATH`
        *   `PRODUCT_SPECS_PRIMARY_INPUT_FILE` (optional, for heading-based research focus)
        *   `PRODUCT_SPECS_REF_DEPTH` (optional, 0-2, for controlling internal document reference following)
    *   Detailed the multi-phase logic within `build_product_specs_process.txt` covering initialization, input ingestion, guided deep research (including primary document heading focus and configurable reference depth), decision making, and structured output generation (product spec, requirements list, optional architectural spec).
    *   Updated `prompts/add_ons/available_addons_manifest.md` to include `build_product_specs_process.txt` with its type, purpose, inputs, and outputs.
    *   Updated `framework_dev_docs/guides/MPS_Usage_Guide.md` to document the new configuration variables and explain their use with the `build_product_specs_process.txt` add-on.

*   **`Core_Planning_Instructions.txt` Framework Refinement:**
    *   To robustly support diverse primary add-ons (like the new `build_product_specs_process.txt` alongside `task_spawning_addon.txt`), the add-on handling logic in `prompts/iep/Core_Planning_Instructions.txt` was enhanced:
        *   Introduced `Known_Primary_Directive_Addons`: A defined list of add-on filenames recognized as capable of being the main directive for a session.
        *   Implemented logic to identify an `Active_Primary_Addon_Filename`: The first add-on selected by the user that matches an entry in `Known_Primary_Directive_Addons` takes precedence.
        *   Added handling for scenarios where multiple known primary add-ons are selected, ensuring only the first one (by user order) is active and a warning/note is made.
        *   Generalized the non-inheritance mechanism: The `Inheritable_Addons_Content_Ordered_List` (for appending to sub-prompts) now correctly excludes the content of whatever add-on is currently the `Active_Primary_Addon_Filename`, not just `task_spawning_addon.txt` by its hardcoded name. This makes the filtering dynamic.
        *   Updated commit message instructions to reflect which primary add-on was active.

**State of Deliverables:**
The new `build_product_specs_process.txt` add-on is created, documented, and integrated with new configuration options. The core MPS framework's add-on handling is now significantly more robust, extensible to multiple primary directive add-ons, and clearly defines precedence and inheritance rules. All relevant documentation (`MPS_Usage_Guide.md`, `available_addons_manifest.md`, `Master_Prompt_Segment.txt`) has been updated. The system is ready for submission and testing of these new capabilities.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions:**
This session focused on fully implementing the "Level 0 Deep Research" methodology within the `build_product_specs_process.txt` add-on, including integration of previously defined codebase review preferences.

*   **`build_product_specs_process.txt` "Level 0 Deep Research" Implementation:**
    *   **New User Configurations:** Added `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` (options: "complex", "cursory", "focused"; default "cursory") and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` (optional keywords for "focused" review) to `prompts/Master_Prompt_Segment.txt`.
    *   **Documentation:** Updated `framework_dev_docs/guides/MPS_Usage_Guide.md` to document these new configuration variables for the "Build Product Specs" process.
    *   **Add-on Logic Refinement (`prompts/add_ons/build_product_specs_process.txt`):**
        *   **Phase 1 (Initialization):** Ensured retrieval and validation of the new codebase review variables.
        *   **Phase 2 (Input Ingestion):** Clarified identification of the 'Primary Input Document'.
        *   **New Sub-Phase 3.A (Overall Primary Input Document Review):** Added an initial high-level review of the Primary Input Document to identify broad themes.
        *   **Restructured Sub-Phase 3.B (Per-Heading Deep Dive Cycle):** This is now the core of the research process. For each major heading in the Primary Input Document, the AI performs a 5-step analysis:
            1.  Heading Context Analysis.
            2.  Supporting Input Documents Analysis (contextual to the current heading).
            3.  References & Codebase Analysis:
                *   Document-to-document references honor `PRODUCT_SPECS_REF_DEPTH`.
                *   Codebase references are analyzed based on `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS`.
                *   **"Level 0 Code Analysis ONLY"** rule strictly enforced (AI does not follow internal code references like imports from fetched code text).
            4.  Consolidated Research Execution (for the current heading, using all gathered information).
            5.  Iterative Content Contribution (drafting content for output documents relevant to the current heading).
        *   **Revised Phase 4 (Decision Making):** Now explicitly follows the per-heading cycle, focusing on finalizing document structures based on iteratively drafted content.
        *   **Revised Phase 5 (Output Generation):** Changed to "Output Document Assembly and Finalization," emphasizing assembly and review of the iteratively drafted content.

**State of Deliverables:**
The `build_product_specs_process.txt` add-on now implements a detailed "Level 0 Deep Research" methodology with per-heading analysis. This includes configurable codebase review preferences and a strict "Level 0" approach to code analysis. All associated configurations in `Master_Prompt_Segment.txt` and documentation in `MPS_Usage_Guide.md` have been updated. This "Phase A" of the `build_product_specs_process.txt` development is complete and ready for submission and testing before considering any potential "Phase B" (multi-pass iterative deepening) refinements.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions for Enhanced Iterative Deepening (0-3 Levels) - "Phase B (Revised)":**
This session focused on expanding and refining the iterative deepening capabilities of the `build_product_specs_process.txt` add-on to support four distinct levels (0-3) with specific content generation goals for each.

*   **Updated `prompts/Master_Prompt_Segment.txt`:**
    *   Modified the `PRODUCT_SPECS_ITERATION_DEPTH` variable within `[[USER PATH CONFIGURATION]]` to accept a range of 0-3.
    *   Updated its comment to describe the new purpose for each level:
        *   `0`: Outline Generation (all heading levels 3-4 deep, title, 1 para key ideas per heading).
        *   `1`: Basic Content (Bachelor's level, ~3 paras per original D0 heading, with concepts, examples). Builds on D0.
        *   `2`: Expert Elaboration (~1 page per original D0 heading, deeper analysis, more nuanced). Builds on D1.
        *   `3`: AI Unique Insights (Extensive elaboration, ~3 pages per original D0 heading, novel ideas, critical analysis). Builds on D2.
    *   Confirmed `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` remains correctly defined as required for depths 1, 2, and 3.

*   **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`:**
    *   **Variable Documentation:** The description for `PRODUCT_SPECS_ITERATION_DEPTH` was updated to reflect the new 0-3 range and the detailed definitions for each level.
    *   **Workflow Section ("Using Iterative Deepening..."):**
        *   The step-by-step guide was revised to incorporate all four iteration levels (0-3).
        *   Descriptions for Iterations 0, 1, and 2 were updated to match the new content goals.
        *   Iteration 3 (AI Unique Insights) was added with instructions on setting variables and expected outcomes (`*_D3.md` files).
        *   Clarified user responsibility for managing output folders across all iterations (D0, D1, D2, D3).

*   **Extensively Refined `prompts/add_ons/build_product_specs_process.txt`:**
    *   **Phase 1 (Initialization):** Updated `PRODUCT_SPECS_ITERATION_DEPTH` validation to [0, 3] (default 0). Added strict validation for `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` (must be provided and accessible if depth > 0, otherwise critical error and halt). Logic to determine output file suffix (`_D0` to `_D3`) added.
    *   **Phase 2 (Input Processing):** Made conditional. Depth 0 processes original inputs. Depths 1-3 primarily process previous iteration's outputs, using original inputs as reference.
    *   **Phase 3 (Content Generation):** Implemented distinct conditional logic for each `PRODUCT_SPECS_ITERATION_DEPTH` (0-3):
        *   **Depth 0 ("Outline Generation"):** Uses the "Level 0 Deep Research" per-heading methodology. Output per heading is strictly title and a single paragraph of key ideas.
        *   **Depth 1 ("Basic Content"):** Reads D0 outputs. Expands each D0 key idea paragraph to ~3 paragraphs (Bachelor's level detail).
        *   **Depth 2 ("Expert Elaboration"):** Reads D1 outputs. Expands each original major heading's section to ~1 page of expert-level content.
        *   **Depth 3 ("AI Unique Insights"):** Reads D2 outputs. Expands each original major heading's section to ~3 pages with novel AI-driven insights.
    *   Included general instructions for iterations 1-3 on building upon previous work and maintaining consistency.
    *   **Phase 4 (Decision Making) & Phase 5 (Output Assembly):** Adapted to support the iterative workflow, ensuring correct document structures are defined (primarily at D0) and content is assembled with appropriate depth suffixes for filenames.

**State of Deliverables:**
The `build_product_specs_process.txt` add-on now fully supports a 0-3 level iterative deepening workflow with clearly defined goals and logic for each depth. All associated configurations in `Master_Prompt_Segment.txt` and documentation in `MPS_Usage_Guide.md` have been updated to reflect these significant enhancements. This "Phase B (Revised)" of the `build_product_specs_process.txt` development, focusing on iterative deepening, is complete. The system is ready for submission and extensive testing of this multi-stage process.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions for "Folder-per-App" Architecture Refactoring:**
This session focused on restructuring the MPS framework components (add-ons, utils) into a "folder-per-app" architecture to enhance modularity, organization, and extensibility.

*   **Core Logic Update (`prompts/iep/Core_Planning_Instructions.txt`):**
    *   Revised Section I.B to implement new discovery and loading logic for add-on "apps". The AI now scans for subdirectories within `User_Path_Addons_Dir`. The selected `addon_app_name` (folder name) is used to find the primary instruction file at `[User_Path_Addons_Dir]/[addon_app_name]/[addon_app_name].txt`.
    *   Updated `Known_Primary_Directive_Addon_Apps` to list app/folder names (e.g., "task_spawning_addon").
    *   Added new Section I.D for discovering and loading "util apps" from `User_Path_Utils_Dir` using the same `[util_app_name]/[util_app_name].txt` pattern, storing them in `All_Available_Utils_Content_Map`.
    *   Ensured error handling for missing primary instruction files within app folders.
    *   Adjusted internal variable names (e.g., `Active_Primary_Addon_AppName`) and commit message details to use app names.

*   **Component Migration:**
    *   All existing add-ons (`task_spawning_addon.txt`, `task_resumption_addon.txt`, `build_product_specs_process.txt`) were migrated from flat files in `prompts/add_ons/` to their respective subdirectories (e.g., `prompts/add_ons/task_spawning_addon/task_spawning_addon.txt`).
    *   The existing utility (`pre_flight_check_user_plan.txt`) was migrated from `prompts/util/` to `prompts/util/pre_flight_check_user_plan/pre_flight_check_user_plan.txt`.
    *   The migration process involved reading original content, creating the new file in the subdirectory (which also created the directory), and then deleting the original flat file.

*   **New IPC Directory:**
    *   Created the `prompts/ipc/` directory and added a `.gitkeep` file to ensure it's tracked, in anticipation of future IPC components using this folder-per-app structure.

*   **Documentation Updates:**
    *   `prompts/add_ons/available_addons_manifest.md`: Updated to refer to add-ons by their app/folder names and to explain the new structure (e.g., "primary instruction file: `app_name/app_name.txt`").
    *   `framework_dev_docs/guides/MPS_Usage_Guide.md`: Comprehensively updated to:
        *   Explain the "folder-per-app" architecture for all components.
        *   Clarify that `User_Path_Addons_Dir`, `User_Path_Utils_Dir`, and `User_Path_IPC_Dir` in `[[USER PATH CONFIGURATION]]` point to parent directories for component subfolders.
        *   Guide users to select add-ons by their app/folder name in `[[USER_ADDON_SELECTION]]`.
        *   Provide instructions for developers on creating new components under this architecture.
        *   Ensured consistent terminology (e.g., "add-on app") throughout the guide.

**State of Deliverables:**
The MPS framework has been successfully refactored to a "folder-per-app" architecture for its components. Core instruction loading logic, all existing add-ons and utils, and all key documentation (`Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`) have been updated to support and reflect this new structure. The framework is now significantly more modular, organized, and extensible for future component development. Ready for submission and testing.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Actions: Configuration System Refactoring (Fixed Paths, App-Specific Configs, USER_config.txt Standard)**
This session executed a major refactoring of the MPS configuration system, moving away from a global `[[USER PATH CONFIGURATION]]` block in `Master_Prompt_Segment.txt` towards a more modular and app-centric approach.

*   **Architectural Changes & Core Logic (`prompts/iep/Core_Planning_Instructions.txt`):**
    *   **Fixed Global System Paths:** Implemented for core component discovery. Base directories for Add-ons (`prompts/add_ons/`), Utils (`prompts/util/`), Base IEP (`prompts/iep/`), and IPC (`prompts/ipc/`) are now hardcoded in `Core_Planning_Instructions.txt`, simplifying user setup. These paths are no longer read from `Master_Prompt_Segment.txt`.
    *   **App-Specific Configuration Block Discovery:** `Core_Planning_Instructions.txt` was updated to scan the main user spawn prompt for app-specific configuration blocks demarcated by `[[USER_CONFIG_FOR_appName]]...[[END_USER_CONFIG_FOR_appName]]`. The raw string content of such blocks is captured and passed to the respective add-on app.

*   **`Master_Prompt_Segment.txt` Streamlining (v0.4.0):**
    *   The entire `[[USER PATH CONFIGURATION]]` block was removed.
    *   Comments in `[[USER_ADDON_SELECTION]]` were updated to guide users to use app names (folder names) and to refer to app-specific configuration methods (i.e., the `[[USER_CONFIG_FOR_appName]]` blocks or `USER_appName_CONFIG.txt` files).
    *   Version updated to `v0.4.0` to reflect these structural changes.

*   **`USER_appName_CONFIG.txt` Standard Formalized:**
    *   Defined a standard format for `USER_appName_CONFIG.txt` files, which serve as templates and persistent storage for app parameters within each app's folder (e.g., `prompts/add_ons/appName/USER_appName_CONFIG.txt`).
    *   **Standard Includes:**
        *   Overall `[[USER_CONFIG_FOR_appName]]...[[END_USER_CONFIG_FOR_appName]]` delimiters.
        *   Per-parameter metadata: `# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`.
        *   Per-parameter description and default: `# Description: ... Default: [template_default_value]`.
        *   Parameter value line: `PARAM_NAME: [USER_VALUE_START] [current_value] [USER_VALUE_END]`.
    *   An illustrative `USER_exampleApp_CONFIG.txt` was defined as part of the standard.

*   **Implementation in `build_product_specs_process` App:**
    *   Created `prompts/add_ons/build_product_specs_process/USER_build_product_specs_process_CONFIG.txt` adhering to the new standard, defining all its parameters (including `PRODUCT_NAME`, `PRODUCT_SPECS_*` paths, iteration settings, codebase review settings) with appropriate metadata.
    *   Refined `prompts/add_ons/build_product_specs_process/build_product_specs_process.txt` (v0.4) to:
        *   Remove old logic for reading parameters from the global `[[USER PATH CONFIGURATION]]`.
        *   Implement the new parameter resolution logic:
            1.  Prioritize values from the `[[USER_CONFIG_FOR_build_product_specs_process]]` block in the main spawn prompt.
            2.  Fall back to user-edited values in its `USER_..._CONFIG.txt` file.
            3.  Fall back to "AcceptedDefault" values from its `USER_..._CONFIG.txt` file.
            4.  For "Required" parameters with "PlaceholderDefault" values that are still unresolved, instruct Jules to request input from the user via chat.
        *   The AI updates the on-disk `USER_build_product_specs_process_CONFIG.txt` with values obtained via chat.
        *   The AI outputs the complete, updated `[[USER_CONFIG_FOR_build_product_specs_process]]` block for the user to copy to their main spawn prompt if changes occurred via chat or if the block was initially missing/incomplete.
        *   Added final validation to HALT if any `Required` parameters remain unset after all resolution attempts.

*   **Documentation Update (`framework_dev_docs/guides/MPS_Usage_Guide.md`):**
    *   Comprehensively updated to reflect the removal of `[[USER PATH CONFIGURATION]]` for global paths.
    *   Explained the fixed system paths for component discovery.
    *   Detailed the new app-specific configuration system: the role and format of `USER_appName_CONFIG.txt` files, the `[[USER_CONFIG_FOR_appName]]` blocks in the main prompt, the user workflow for configuration, and the AI's parameter resolution precedence including AI-assisted updates.

**State of Deliverables:**
The MPS framework's configuration system has been thoroughly refactored. Global component paths are now fixed. App-specific parameters are managed via a robust system of `USER_appName_CONFIG.txt` template/storage files and `[[USER_CONFIG_FOR_appName]]` override blocks in the main user prompt, featuring AI-assisted updates. The `build_product_specs_process` add-on fully implements this new configuration paradigm. All core documentation (`Master_Prompt_Segment.txt`, `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`) is updated. The framework is significantly more modular, user-friendly in its configuration, and extensible. Ready for submission and testing.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Accomplishments This Session (Finalizing Configuration System & Preparing for "Concept Task" Handoff):**
This session concluded the major refactoring of the MPS framework's configuration system and component architecture. It also prepared for the next phase of work focusing on the "Concept task."

*   **Configuration System Refactoring (Completion & Documentation):**
    *   **Standard for `USER_appName_CONFIG.txt`:** Formalized the standard for these files, including app-specific delimiters (`[[USER_CONFIG_FOR_appName]]`), and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), descriptions, defaults, and `[USER_VALUE_START]...[USER_VALUE_END]` markers. An illustrative example (`USER_exampleApp_CONFIG.txt`) was created as part of this standard definition.
    *   **Applied Standard:** The `build_product_specs_process` add-on's `USER_build_product_specs_process_CONFIG.txt` was updated to fully adhere to this new standard, with all its parameters classified.
    *   **Refined App Logic (`build_product_specs_process.txt` v0.4):** Its parameter resolution logic in Phase 1 was enhanced to parse the new metadata from `USER_..._CONFIG.txt`. The AI-assisted update mechanism (asking user via chat) now correctly triggers only for "Required" parameters with "PlaceholderDefault" values that haven't been overridden. A final validation step ensures all "Required" parameters have valid values before proceeding, halting with an error if not.
    *   **Streamlined `Master_Prompt_Segment.txt` (v0.4.0):** The global `[[USER PATH CONFIGURATION]]` block was definitively removed. Instructions in `[[USER_ADDON_SELECTION]]` were updated to guide users on app-specific configurations via `[[USER_CONFIG_FOR_appName]]` blocks in their main spawn prompt.
    *   **Consolidated `MPS_Usage_Guide.md`:** The guide was thoroughly reviewed and updated to provide a comprehensive and consistent explanation of the new configuration system. This includes: fixed global system paths, the role and format of `USER_appName_CONFIG.txt` files, the user workflow for app configuration (using main prompt blocks or editing `USER_...CONFIG.txt`), the AI's parameter resolution precedence, and how AI-assisted updates work. The "folder-per-app" architecture is also clearly documented.

*   **Next Area of Work (User Directed - For Next Jules Instance):**
    *   The user has directed focus towards refining an existing "Concept task."
    *   This will involve analyzing the "Concept task" and splitting it into approximately three sub-tasks.
    *   A key aspect of this work will be to use the "Concept task" sub-tasks as a practical use case for **exploring and implementing extensions to the 'add-on, util, and process' component concepts** within the MPS framework.
    *   The next Jules instance is tasked with initiating this by requesting detailed requirements from the user regarding the "Concept task" (current state, materials possibly in `Inputs/concept/inputs`, ideas for sub-tasks) and their initial thoughts on evolving the component model.

**State of Deliverables:**
The comprehensive refactoring of the MPS configuration system is complete. This includes the "folder-per-app" architecture, fixed system paths for component discovery, and the new app-specific configuration mechanism using `USER_appName_CONFIG.txt` files and `[[USER_CONFIG_FOR_appName]]` blocks, featuring AI-assisted updates. All core prompts and documentation (`Master_Prompt_Segment.txt`, `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`, and relevant add-ons like `build_product_specs_process`) have been updated to reflect these changes. The framework is significantly more modular, user-friendly, and robust in its configuration management. No new code or prompt changes were made in this specific summarization step. The system is ready for handoff to the next Jules instance to begin work on the "Concept task" and component model evolution.
---
---
**Feedback Entry Date:** 2025-06-02
**Source of Feedback:** User (new directive for next area of work following completion of major configuration system and architectural refactoring).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment) and related component architecture (folder-per-app, `USER_appName_CONFIG.txt` standard, etc.).
**Context/Scenario:** Planning the next development cycle for the MPS framework after successfully implementing a new configuration system and "folder-per-app" architecture.
**Observation/Issue:**
    1.  An existing internal "Concept task" (details to be provided by the user to the next Jules instance, potentially located in `Inputs/concept/inputs`) is currently monolithic and needs to be broken down into more manageable sub-tasks (approximately three).
    2.  The process of decomposing and implementing these "Concept task" sub-tasks is expected to reveal opportunities or requirements for evolving the framework's current component definitions (add-on, util, process) or potentially introducing new component types or interaction paradigms.
**Suggestion (For Next Jules Instance):**
The next Jules instance should:
    1.  **Gather Detailed Requirements for "Concept Task":** Actively engage with the user to obtain a detailed understanding of the current "Concept task," its goals, any existing materials (potentially in `Inputs/concept/inputs`).
    2.  Collaborate with the user to define the three (approx.) sub-tasks for the "Concept task."
    3.  In parallel, analyze how the requirements of these sub-tasks might necessitate extensions to the existing component concepts (add-on, util, process) or the introduction of new component types/paradigms within the MPS framework.
    4.  Plan and implement both the "Concept task" restructuring and the identified framework component extensions, using the former as a practical test case for the latter.
---
**Session Summary - 2025-06-03**
**Instance:** Jules
**User Feedback/Requests Addressed:**
- User directed a new phase of MPS development focusing on two major initiatives:
    1.  Abstracting the existing `build_product_specs_process` add-on into a more generic `create_research_report` add-on.
    2.  Introducing a new "promptApp" concept to allow users to define and execute multi-stage workflows composed of underlying MPS components.
- Clarified requirements for `create_research_report` parameters, including separate controls for `ITERATIVE_OUTPUT_DEPTH` (D0-D3 style) and `REFERENCE_FOLLOWING_DEPTH` (document link traversal).
- Confirmed that plain text input formats (.txt, .md, web text) are the initial target for document processing.
- Decided that sequential tasks within a `promptApp` will be handled by user re-invocation of new AI instances for each task, simplifying initial framework orchestration.

**Summary of Approved Plan:**
The overall plan involves:
1.  Developing the `create_research_report` add-on (now complete).
2.  Defining the "promptApp" structure and manifest file format.
3.  Updating `Core_Planning_Instructions.txt` and `Master_Prompt_Segment.txt` to support `promptApp` discovery, selection, and execution.
4.  Creating an initial structural example of a `promptApp` (`CompliancePLM`).
5.  Retiring the `build_product_specs_process` add-on files.
6.  Updating all documentation.
7.  Testing the new functionalities.

**Summary of Changes Made This Session (to implement Step 1 of the plan):**
-   **Created `prompts/add_ons/create_research_report/` directory.**
-   **Created `prompts/add_ons/create_research_report/USER_create_research_report_CONFIG.txt`:**
    *   Defined parameters: `PROPOSED_TABLE_OF_CONTENTS`, `GUIDELINES_DOC_PATHS`, `ADDITIONAL_WEB_LINKS_FILE`, `EXEMPLAR_DOCS`, `GENERAL_INPUT_DOCS`, `PRIMARY_INPUT_DOC_PATH`, `ITERATIVE_OUTPUT_DEPTH`, `REFERENCE_FOLLOWING_DEPTH`, `PREVIOUS_ITERATION_OUTPUT_PATH`, `REPORT_BASENAME`, `REPORT_OUTPUT_PATH`.
    *   Included full metadata (Requirement, DefaultType, Description, Default) for each.
-   **Created `prompts/add_ons/create_research_report/create_research_report.txt`:**
    *   Adapted core logic from the former `build_product_specs_process.txt`.
    *   Updated to use the new parameter set for generic report generation.
    *   Removed product-specification-specific and detailed codebase review logic.
    *   Maintained the 6-phase structure and parameter resolution mechanisms.
-   **Updated `prompts/add_ons/available_addons_manifest.md`:**
    *   Added entry for `create_research_report`.
    *   Removed entry for `build_product_specs_process`.

**State of Deliverables:**
- The `create_research_report` add-on (v0.1) is developed and its configuration file is in place.
- The add-on manifest is updated.
- The `build_product_specs_process` add-on is effectively replaced by this new add-on (physical file deletion is pending a later plan step).
- Plan step 1 is complete.

**Next Steps (This Jules instance/session):**
- Proceed to Plan Step 2: "Define 'promptApp' Structure and Manifest." This involves designing the manifest file format that will define how `promptApps` (like the `CompliancePLM` example) are structured and how their tasks map to MPS components.
---
**Session Summary - 2025-06-03 (Continued)**
**Instance:** Jules
**User Feedback/Requests Addressed (Post Initial 2025-06-03 Summary):**
- User confirmed a critical change in component resolution strategy for `promptApps`: the search order should be "app-specific first" (flat file, then folder style within app), followed by global components (add-ons, then utils). This was a change from the initially implemented "globals first" approach.

**Summary of Approved Plan (Revised Portion):**
The plan was revised to implement and document this new search order:
1.  Revise `Core_Planning_Instructions.txt` for the new "app-specific first" search order. (Completed)
2.  Update `MPS_Usage_Guide.md` to reflect this new search order. (Completed)
3.  Create test assets and conceptually outline tests for the new search order. (Completed)
4.  Update `HANDOFF_NOTES.md` and submit. (This step)

**Summary of Changes Made This Session (Implementing Revised Plan):**
-   **Modified `prompts/iep/Core_Planning_Instructions.txt`:**
    *   Updated the component resolution logic in Section II.A.1.a.viii to implement the "app-specific first" search order:
        1.  App-Specific (flat file): `prompts/apps/[Selected_PromptAppName]/[componentName].txt`
        2.  App-Specific (folder style): `prompts/apps/[Selected_PromptAppName]/[componentName]/[componentName].txt`
        3.  Global Add-on: `prompts/add_ons/[componentName]/[componentName].txt`
        4.  Global Util: `prompts/util/[componentName]/[componentName].txt`
-   **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`:**
    *   Modified Section 3.B.2.i ("Component Resolution Search Order") and Section 4 ("Developing New promptApps") to accurately document the new "app-specific first" search order.
-   **Created Test Assets for Search Order Verification:**
    *   Global add-on: `prompts/add_ons/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   App-specific component (folder style): `prompts/apps/CompliancePLM/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   Updated `prompts/add_ons/available_addons_manifest.md` for the global test component.
    *   Updated `prompts/apps/CompliancePLM/CompliancePLM_manifest.json` to include a task "Test Component Resolution" that calls `test_resolver_component`.

**State of Deliverables:**
- Core framework logic (`Core_Planning_Instructions.txt`) now correctly implements "app-specific first" component resolution for `promptApps`.
- Documentation (`MPS_Usage_Guide.md`) accurately reflects this new search order.
- Test assets are in place to verify this specific functionality.
- The broader `promptApp` and `create_research_report` add-on implementation (from previous steps in this session) is complete.
- The system is ready for user testing of the `promptApp` functionality, especially the component search order.

**Next Steps (User):**
- Thoroughly test the `promptApp` system, focusing on:
    - Invoking the "Test Component Resolution" task within `CompliancePLM` to ensure the app-specific `test_resolver_component` is used.
    - Invoking tasks that use global components (like "Product Specification" using `create_research_report` in `CompliancePLM`) to ensure they are correctly found.
    - Testing the iteration flow and re-invocation prompts for iterable tasks.
---

## MPS Performance Feedback Log

This section logs specific feedback on the performance, clarity, and effectiveness of the Master Prompt Segment (MPS) versions and the artifacts they help generate.

---
**Feedback Entry - 2023-11-02**
**Source of Feedback:** Initial setup of this feedback log section during MPS feature design.
**MPS Version Referenced:** Relevant for `prompts/Master_Prompt_Segment.txt` (v0.3.2 and future versions).
**Context/Scenario:** Establishing the feedback mechanism as per design discussions for feature "Feedback Loop for MPS Refinement".
**Observation/Issue:** This log is intended to capture issues like:
    - Ambiguities in any version of `Master_Prompt_Segment.md` that lead to incorrect or suboptimal behavior by a Planning AI.
    - Problems encountered by Task AIs due to the structure or content of prompts generated based on an MPS version.
    - User difficulties in understanding or using the MPS, its generated `00_task_launch_plan.md`, or other artifacts.
    - Success stories where a particular MPS instruction proved very effective.
**Suggestion for MPS Refinement (if any):** N/A
---
*(Future entries will be appended below this line)*
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request to Jules instance for refactoring).
**MPS Version Referenced:** `prompts/Master_Prompt_Segment.txt` (approx. v0.3.2, prior to modularization) and the new modularized version (post this session's changes).
**Context/Scenario:** User identified that the `Master_Prompt_Segment.txt` was becoming very long and dense, making it difficult to read, manage, and maintain.
**Observation/Issue:** The monolithic nature of `Master_Prompt_Segment.txt` was impacting usability and the ease of making targeted changes to core planning instructions without affecting user-configurable sections.
**Suggestion for MPS Refinement (Implemented):** To improve readability and maintainability, the core planning AI instructions were extracted from `Master_Prompt_Segment.txt` and placed into a new, dedicated file: `prompts/iep/Core_Planning_Instructions.txt`. The `Master_Prompt_Segment.txt` now includes these instructions using an `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive. This separation is intended to make both the user-facing configuration parts of the MPS and the core instructional logic easier to manage.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request and illustrative example: `[ ] task_spawning_addon.txt` to Jules instance).
**MPS Version Referenced:** The modularized MPS version (where `Core_Planning_Instructions.txt` was already separate) and the current version implementing optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User desired to further enhance the framework's modularity by making the entire project planning and task generation process an optional component, selectable by the user, rather than an inherent, non-optional function of the MPS.
**Observation/Issue:** While `Core_Planning_Instructions.txt` was already separated, it still implicitly mandated the task spawning workflow. The framework lacked the flexibility to be used for other purposes without invoking this primary planning behavior.
**Suggestion for MPS Refinement (Implemented):** The user's suggestion to make task spawning an optional add-on was implemented.
    - The core logic for project planning, TLP generation, and task prompt creation was moved from `prompts/iep/Core_Planning_Instructions.txt` to a new file: `prompts/add_ons/task_spawning_addon.txt`.
    - `prompts/iep/Core_Planning_Instructions.txt` was updated to include conditional logic. It now checks if `task_spawning_addon.txt` is selected by the user in `[[USER_ADDON_SELECTION]]` (within `Master_Prompt_Segment.txt`).
    - If selected, the `task_spawning_addon.txt` is executed.
    - If not selected, the AI is instructed to acknowledge this, skip default plan generation, and process any other selected add-ons.
    - This change makes the MPS framework significantly more flexible, allowing users to engage the Planning AI for potentially different primary functions by selecting different combinations of add-ons, or by using it with only utility add-ons.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifying questions and specific directive regarding `task_spawning_addon.txt` inheritance and add-on ordering).
**MPS Version Referenced:** The MPS version with optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User inquired about how multiple add-ons interact, particularly how `task_spawning_addon.txt` would affect other add-ons, and then specifically requested that `task_spawning_addon.txt` should not be inherited by the Task AIs it generates. User also sought clarity on the importance of add-on ordering in the selection block.
**Observation/Issue:**
    1. The previous add-on processing logic could have resulted in `task_spawning_addon.txt` (intended for the Planning AI) being appended to Task AI prompts, leading to overly verbose prompts and potential for unintended behavior.
    2. The impact of add-on order in `[[USER_ADDON_SELECTION]]` on the final content of Task AI prompts was not explicitly documented, potentially leading to user confusion.
**Suggestion for MPS Refinement (Implemented):**
    - **Non-inheritance of `task_spawning_addon.txt`**: Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B) to create a filtered list (`Inheritable_Addons_Content_Ordered_List`) of add-on contents for Task AI prompt inheritance, which explicitly excludes the content of `task_spawning_addon.txt`. This ensures the Planning AI uses `task_spawning_addon.txt` for its process, but Task AIs receive cleaner, more focused prompts.
    - **Clarified Add-on Ordering**: Updated `prompts/Master_Prompt_Segment.txt` (comments in `[[USER_ADDON_SELECTION]]`) and `framework_dev_docs/guides/MPS_Usage_Guide.md` to explain that the order of inheritable add-ons is preserved when `task_spawning_addon.txt` appends them to task prompts. Also recommended listing `task_spawning_addon.txt` last for visual clarity if used.
    - These refinements improve the safety and predictability of add-on interactions and provide users with better control and understanding of how their selections affect Task AI prompts.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (initial idea for process automation, then specific refinements for primary input and reference depth for product specs).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** User expressed a need to use the MPS framework for more than just task spawning, specifically for a structured process to generate product specification documents from various inputs. Later refinements included requests for more control over the research phase of this process.
**Observation/Issue:** The initial MPS framework was primarily geared towards task spawning. A structured approach was needed to define and execute other complex, multi-step AI-driven processes. For the product specs process,
        *   Added `PRODUCT_SPECS_PRIMARY_INPUT_FILE`: An optional path to a specific document. The AI is instructed to focus its research by analyzing at least every numbered heading in this document. If not provided, the AI selects the most comprehensive input document for this role.
        *   Added `PRODUCT_SPECS_REF_DEPTH`: An optional integer (0-2, default 1) to control how many levels deep the AI will follow internal references within the provided document set.
    These enhancements make the product specification generation process more guided and configurable.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** Jules (proactive refinement during development of `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** The introduction of `build_product_specs_process.txt` as a new "primary directive" add-on (similar in role to `task_spawning_addon.txt` but with a different function) highlighted the need to generalize how the core framework selects and manages such add-ons.
**Observation/Issue:** The existing logic in `prompts/iep/Core_Planning_Instructions.txt` was somewhat hardcoded around `task_spawning_addon.txt` for determining the main execution path and for non-inheritance of add-on content into sub-prompts. This would not scale well if many different primary add-ons were developed.
**Suggestion for MPS Refinement (Implemented):**
    - Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B and II) to:
        - Define a list of `Known_Primary_Directive_Addons`.
        - Dynamically identify an `Active_Primary_Addon_Filename` by checking the user's selection against this known list, prioritizing the first one selected by the user if multiple are chosen. A warning is logged if multiple primary add-ons are selected.
        - Ensure that the `Inheritable_Addons_Content_Ordered_List` (used for appending to sub-prompts) correctly excludes the content of the currently `Active_Primary_Addon_Filename`, not just `task_spawning_addon.txt`.
    - This makes the core framework more robust and extensible, allowing new primary add-ons to be integrated more easily without requiring significant changes to the core conditional logic each time. It also handles user selection of multiple primary add-ons more gracefully.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (specific feedback on research methodology, codebase interaction rules like "level 0 only for codebases," and codebase review preferences for the `build_product_specs_process.txt` add-on).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on.
**Context/Scenario:** User provided detailed requirements to refine the initial `build_product_specs_process.txt` add-on to achieve a more structured, in-depth, and controllable research and content generation process, particularly focusing on how the AI should interact with a primary document and linked codebases.
**Observation/Issue:** The initial version of `build_product_specs_process.txt` had a more general research phase. The user required a more granular, heading-based research approach for the Primary Input Document, specific rules for how the AI should analyze linked codebases (including different levels of review depth/focus), and a strict "level 0 only" rule for code analysis to prevent unintended deep dives into code.
**Suggestion for MPS Refinement (Implemented):**
The `prompts/add_ons/build_product_specs_process.txt` was significantly updated to incorporate the "Level 0 Deep Research" methodology as per user requirements:
    - **New Configurations:** Introduced `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` in `Master_Prompt_Segment.txt` and documented them in `MPS_Usage_Guide.md`.
    - **Per-Heading Research Cycle:** The add-on's Phase 3 was restructured. It now includes an initial high-level review of the Primary Input Document. Then, for each major heading in that document, it performs a 5-step analysis: Heading Context Analysis, Supporting Docs Analysis, References & Codebase Analysis, Consolidated Research for the heading, and Iterative Content Contribution for that heading.
    - **Codebase Review Integration:** The "References & Codebase Analysis" step now explicitly uses the `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` ("complex", "cursory", "focused") and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` to guide its interaction with linked code.
    - **"Level 0 Code Analysis ONLY":** A strict rule was added, instructing the AI not to follow internal code references (like imports or function calls to other files) from any fetched code text. Its analysis is confined to the directly retrieved code content.
    - **Iterative Content Drafting:** Content for output documents is now drafted iteratively during the per-heading analysis and then assembled and finalized in later phases.
    This refined process provides a more systematic and controllable approach for the AI when generating product specifications.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifications and new requirements for iterative deepening for `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on incorporating "Level 0 Deep Research".
**Context/Scenario:** Expanding the iterative capabilities of `build_product_specs_process.txt` from an initial 0-2 level concept to a more detailed 0-3 level process with specific content goals for each level.
**Observation/Issue:** The initial iterative deepening plan (0-2 levels) for `build_product_specs_process.txt` needed more specific content goals for each level and an additional level for AI-driven insights to meet user's evolving requirements for content depth and sophistication.
**Suggestion for MPS Refinement (Implemented):**
The iterative deepening feature of `build_product_specs_process.txt` was expanded to support 0-3 levels, and associated configurations and documentation were updated:
    - **Configuration Updates (`Master_Prompt_Segment.txt`):** `PRODUCT_SPECS_ITERATION_DEPTH` comment updated to reflect the 0-3 range and specific goals:
        - `0`: Outline Generation (headings with single key idea paragraphs).
        - `1`: Basic Content (Bachelor's level, ~3 paras per D0 heading).
        - `2`: Expert Elaboration (~1 page per original D0 heading).
        - `3`: AI Unique Insights (~3 pages per original D0 heading, novel ideas).
    - **Add-on Logic (`build_product_specs_process.txt`):**
        - Phase 1 (Initialization) now validates depth (0-3) and `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` (required for D1-D3, critical error if missing). Output file suffixes `_D0` to `_D3` are determined.
        - Phase 2 (Input Processing) is conditional: D0 uses original inputs; D1-D3 use previous iteration's outputs as primary, with original inputs as reference.
        - Phase 3 (Content Generation) has distinct logic for each depth (0-3), detailing how to build upon previous work (for D1-D3) or generate outlines (D0) according to the specified content goals for each level.
    - **Documentation (`MPS_Usage_Guide.md`):** Updated to detail the 0-3 levels, their specific purposes, and the user workflow for managing iterative runs and output folders.
    This provides a more granular, powerful, and well-defined iterative workflow for the `build_product_specs_process.txt` add-on.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (vision for enhanced component modularity via "folder-per-app" architecture).
**MPS Version Referenced:** Current version with multiple add-ons and utils.
**Context/Scenario:** As the number of components (add-ons, utils, and potentially IPC handlers) grows, a flat file structure in their respective directories (`prompts/add_ons/`, `prompts/util/`) becomes less organized and limits the ability for components to have their own supporting files or sub-frameworks.
**Observation/Issue:** The previous flat-file structure for add-ons and utils was not ideal for scalability or for components that might require multiple associated files (e.g., templates, data snippets, complex sub-instructions).
**Suggestion for MPS Refinement (Implemented):**
The framework was refactored to a "folder-per-app" architecture for all components (add-ons, utils, and anticipated IPC handlers):
    - **Core Logic (`prompts/iep/Core_Planning_Instructions.txt`):** Updated to discover and load components based on a new directory structure.
        - It now expects component-specific parent paths like `User_Path_Addons_Dir`, `User_Path_Utils_Dir`, `User_Path_IPC_Dir` to be defined in `[[USER PATH CONFIGURATION]]`.
        - For each component "app" (e.g., `my_addon_app`), it looks for a subdirectory named `my_addon_app` within the relevant parent path.
        - The primary instruction file for the app must be located at `[parent_path]/my_addon_app/my_addon_app.txt`.
        - Internal references (e.g., `Known_Primary_Directive_Addon_Apps`, `Active_Primary_Addon_AppName`) now use these app/folder names.
    - **Component Migration:** All existing add-ons (`task_spawning_addon`, `task_resumption_addon`, `build_product_specs_process`) and utils (`pre_flight_check_user_plan`) were moved into their respective new subdirectories (e.g., `prompts/add_ons/task_spawning_addon/task_spawning_addon.txt`).
    - **New `prompts/ipc/` directory:** Created with a `.gitkeep` file for future IPC components.
    - **Documentation Updates:**
        - `prompts/add_ons/available_addons_manifest.md`: Updated to list add-ons by their app/folder name and describe the new structure.
        - `framework_dev_docs/guides/MPS_Usage_Guide.md`: Comprehensively updated to explain the "folder-per-app" architecture, how users should set up paths, how components are selected, and how developers should structure new components.
    This architectural change significantly improves modularity, organization, and the potential for more complex, self-contained components within the MPS framework.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (desire to simplify MPS, new way to handle app-specific data, AI assistance for missing/default configs, specific format for detecting edits).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment and Core Planning Instructions).
**Context/Scenario:** This feedback entry summarizes a series of user requests and design discussions aimed at a comprehensive overhaul of the MPS framework's configuration system and a significant enhancement of the `build_product_specs_process` add-on's iterative capabilities.
**Observation/Issue:**
    1.  The global `[[USER PATH CONFIGURATION]]` in `Master_Prompt_Segment.txt` was becoming unwieldy; app-specific configs were mixed with global ones; parameter precedence and user edit detection were not formalized.
    2.  A more structured and discoverable way to manage parameters for individual add-on apps was needed, including clear defaults and user override mechanisms.
    3.  Users needed assistance in setting mandatory parameters, especially those with placeholder defaults.
**Suggestion for MPS Refinement (Implemented):**
A comprehensive refactoring of the configuration system was performed:
    1.  **Fixed Global System Paths:** Core component discovery paths (for add-ons, utils, IEP, IPC) are now hardcoded in `Core_Planning_Instructions.txt`, simplifying user setup.
    2.  **`[[USER PATH CONFIGURATION]]` Removed from `Master_Prompt_Segment.txt`:** This block was deleted. General output paths (like `Main Iteration Folder`) and other process-specific parameters are now intended to be managed as parameters within the configuration system of the relevant app.
    3.  **App-Specific Configuration System:**
        *   **`USER_appName_CONFIG.txt` Standard:** Defined a standard format for these template/storage files within each app's folder. This includes `[[USER_CONFIG_FOR_appName]]` delimiters, and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), description with default (`# Description: ... Default: ...`), and the value line (`PARAM_NAME: [USER_VALUE_START]value[USER_VALUE_END]`).
        *   **Main Prompt Overrides:** Users can copy the content of a `USER_appName_CONFIG.txt` into their main spawn prompt to provide run-specific overrides.
        *   **Parameter Resolution Precedence:** Implemented in app logic (demonstrated in `build_product_specs_process.txt`): Main prompt block > User-edited `USER_appName_CONFIG.txt` > AcceptedDefault in `USER_appName_CONFIG.txt`.
        *   **AI-Assisted Updates:** If a `Required` parameter with a `PlaceholderDefault` remains unresolved, the app instructs the AI (Jules) to request the value from the user via chat. The AI then updates the on-disk `USER_appName_CONFIG.txt` with the new value and outputs the complete updated config block for the user to transfer to their main spawn prompt.
    4.  **Documentation:** All changes were reflected in `Master_Prompt_Segment.txt` (now v0.4.0), `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, and relevant add-on files (e.g., `build_product_specs_process/USER_build_product_specs_process_CONFIG.txt` and its primary instruction file).
    This new system offers improved modularity, clarity in parameter management, and a better user experience through AI-assisted configuration.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Accomplishments This Session (Finalizing Configuration System & Preparing for "Concept Task" Handoff):**
This session concluded the major refactoring of the MPS framework's configuration system and component architecture. It also prepared for the next phase of work focusing on the "Concept task."

*   **Configuration System Refactoring (Completion & Documentation):**
    *   **Standard for `USER_appName_CONFIG.txt`:** Formalized the standard for these files, including app-specific delimiters (`[[USER_CONFIG_FOR_appName]]`), and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), descriptions, defaults, and `[USER_VALUE_START]...[USER_VALUE_END]` markers. An illustrative example (`USER_exampleApp_CONFIG.txt`) was created as part of this standard definition.
    *   **Applied Standard:** The `build_product_specs_process` add-on's `USER_build_product_specs_process_CONFIG.txt` was updated to fully adhere to this new standard, with all its parameters classified.
    *   **Refined App Logic (`build_product_specs_process.txt` v0.4):** Its parameter resolution logic in Phase 1 was enhanced to parse the new metadata from `USER_..._CONFIG.txt`. The AI-assisted update mechanism (asking user via chat) now correctly triggers only for "Required" parameters with "PlaceholderDefault" values that haven't been overridden. A final validation step ensures all "Required" parameters have valid values before proceeding, halting with an error if not.
    *   **Streamlined `Master_Prompt_Segment.txt` (v0.4.0):** The global `[[USER PATH CONFIGURATION]]` block was definitively removed. Instructions in `[[USER_ADDON_SELECTION]]` were updated to guide users on app-specific configurations via `[[USER_CONFIG_FOR_appName]]` blocks in their main spawn prompt.
    *   **Consolidated `MPS_Usage_Guide.md`:** The guide was thoroughly reviewed and updated to provide a comprehensive and consistent explanation of the new configuration system. This includes: fixed global system paths, the role and format of `USER_appName_CONFIG.txt` files, the user workflow for app configuration (using main prompt blocks or editing `USER_...CONFIG.txt`), the AI's parameter resolution precedence, and how AI-assisted updates work. The "folder-per-app" architecture is also clearly documented.

*   **Next Area of Work (User Directed - For Next Jules Instance):**
    *   The user has directed focus towards refining an existing "Concept task."
    *   This will involve analyzing the "Concept task" and splitting it into approximately three sub-tasks.
    *   A key aspect of this work will be to use the "Concept task" sub-tasks as a practical use case for **exploring and implementing extensions to the 'add-on, util, and process' component concepts** within the MPS framework.
    *   The next Jules instance is tasked with initiating this by requesting detailed requirements from the user regarding the "Concept task" (current state, materials possibly in `Inputs/concept/inputs`, ideas for sub-tasks) and their initial thoughts on evolving the component model.

**State of Deliverables:**
The comprehensive refactoring of the MPS configuration system is complete. This includes the "folder-per-app" architecture, fixed system paths for component discovery, and the new app-specific configuration mechanism using `USER_appName_CONFIG.txt` files and `[[USER_CONFIG_FOR_appName]]` blocks, featuring AI-assisted updates. All core prompts and documentation (`Master_Prompt_Segment.txt`, `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`, and relevant add-ons like `build_product_specs_process`) have been updated to reflect these changes. The framework is significantly more modular, user-friendly, and robust in its configuration management. No new code or prompt changes were made in this specific summarization step. The system is ready for handoff to the next Jules instance to begin work on the "Concept task" and component model evolution.
---
---
**Feedback Entry Date:** 2025-06-02
**Source of Feedback:** User (new directive for next area of work following completion of major configuration system and architectural refactoring).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment) and related component architecture (folder-per-app, `USER_appName_CONFIG.txt` standard, etc.).
**Context/Scenario:** Planning the next development cycle for the MPS framework after successfully implementing a new configuration system and "folder-per-app" architecture.
**Observation/Issue:**
    1.  An existing internal "Concept task" (details to be provided by the user to the next Jules instance, potentially located in `Inputs/concept/inputs`) is currently monolithic and needs to be broken down into more manageable sub-tasks (approximately three).
    2.  The process of decomposing and implementing these "Concept task" sub-tasks is expected to reveal opportunities or requirements for evolving the framework's current component definitions (add-on, util, process) or potentially introducing new component types or interaction paradigms.
**Suggestion (For Next Jules Instance):**
The next Jules instance should:
    1.  **Gather Detailed Requirements for "Concept Task":** Actively engage with the user to obtain a detailed understanding of the current "Concept task," its goals, any existing materials (potentially in `Inputs/concept/inputs`).
    2.  Collaborate with the user to define the three (approx.) sub-tasks for the "Concept task."
    3.  In parallel, analyze how the requirements of these sub-tasks might necessitate extensions to the existing component concepts (add-on, util, process) or the introduction of new component types/paradigms within the MPS framework.
    4.  Plan and implement both the "Concept task" restructuring and the identified framework component extensions, using the former as a practical test case for the latter.
---
**Session Summary - 2025-06-03**
**Instance:** Jules
**User Feedback/Requests Addressed:**
- User directed a new phase of MPS development focusing on two major initiatives:
    1.  Abstracting the existing `build_product_specs_process` add-on into a more generic `create_research_report` add-on.
    2.  Introducing a new "promptApp" concept to allow users to define and execute multi-stage workflows composed of underlying MPS components.
- Clarified requirements for `create_research_report` parameters, including separate controls for `ITERATIVE_OUTPUT_DEPTH` (D0-D3 style) and `REFERENCE_FOLLOWING_DEPTH` (document link traversal).
- Confirmed that plain text input formats (.txt, .md, web text) are the initial target for document processing.
- Decided that sequential tasks within a `promptApp` will be handled by user re-invocation of new AI instances for each task, simplifying initial framework orchestration.

**Summary of Approved Plan:**
The overall plan involves:
1.  Developing the `create_research_report` add-on (now complete).
2.  Defining the "promptApp" structure and manifest file format.
3.  Updating `Core_Planning_Instructions.txt` and `Master_Prompt_Segment.txt` to support `promptApp` discovery, selection, and execution.
4.  Creating an initial structural example of a `promptApp` (`CompliancePLM`).
5.  Retiring the `build_product_specs_process` add-on files.
6.  Updating all documentation.
7.  Testing the new functionalities.

**Summary of Changes Made This Session (to implement Step 1 of the plan):**
-   **Created `prompts/add_ons/create_research_report/` directory.**
-   **Created `prompts/add_ons/create_research_report/USER_create_research_report_CONFIG.txt`:**
    *   Defined parameters: `PROPOSED_TABLE_OF_CONTENTS`, `GUIDELINES_DOC_PATHS`, `ADDITIONAL_WEB_LINKS_FILE`, `EXEMPLAR_DOCS`, `GENERAL_INPUT_DOCS`, `PRIMARY_INPUT_DOC_PATH`, `ITERATIVE_OUTPUT_DEPTH`, `REFERENCE_FOLLOWING_DEPTH`, `PREVIOUS_ITERATION_OUTPUT_PATH`, `REPORT_BASENAME`, `REPORT_OUTPUT_PATH`.
    *   Included full metadata (Requirement, DefaultType, Description, Default) for each.
-   **Created `prompts/add_ons/create_research_report/create_research_report.txt`:**
    *   Adapted core logic from the former `build_product_specs_process.txt`.
    *   Updated to use the new parameter set for generic report generation.
    *   Removed product-specification-specific and detailed codebase review logic.
    *   Maintained the 6-phase structure and parameter resolution mechanisms.
-   **Updated `prompts/add_ons/available_addons_manifest.md`:**
    *   Added entry for `create_research_report`.
    *   Removed entry for `build_product_specs_process`.

**State of Deliverables:**
- The `create_research_report` add-on (v0.1) is developed and its configuration file is in place.
- The add-on manifest is updated.
- The `build_product_specs_process` add-on is effectively replaced by this new add-on (physical file deletion is pending a later plan step).
- Plan step 1 is complete.

**Next Steps (This Jules instance/session):**
- Proceed to Plan Step 2: "Define 'promptApp' Structure and Manifest." This involves designing the manifest file format that will define how `promptApps` (like the `CompliancePLM` example) are structured and how their tasks map to MPS components.
---
**Session Summary - 2025-06-03 (Continued)**
**Instance:** Jules
**User Feedback/Requests Addressed (Post Initial 2025-06-03 Summary):**
- User confirmed a critical change in component resolution strategy for `promptApps`: the search order should be "app-specific first" (flat file, then folder style within app), followed by global components (add-ons, then utils). This was a change from the initially implemented "globals first" approach.

**Summary of Approved Plan (Revised Portion):**
The plan was revised to implement and document this new search order:
1.  Revise `Core_Planning_Instructions.txt` for the new "app-specific first" search order. (Completed)
2.  Update `MPS_Usage_Guide.md` to reflect this new search order. (Completed)
3.  Create test assets and conceptually outline tests for the new search order. (Completed)
4.  Update `HANDOFF_NOTES.md` and submit. (This step)

**Summary of Changes Made This Session (Implementing Revised Plan):**
-   **Modified `prompts/iep/Core_Planning_Instructions.txt`:**
    *   Updated the component resolution logic in Section II.A.1.a.viii to implement the "app-specific first" search order:
        1.  App-Specific (flat file): `prompts/apps/[Selected_PromptAppName]/[componentName].txt`
        2.  App-Specific (folder style): `prompts/apps/[Selected_PromptAppName]/[componentName]/[componentName].txt`
        3.  Global Add-on: `prompts/add_ons/[componentName]/[componentName].txt`
        4.  Global Util: `prompts/util/[componentName]/[componentName].txt`
-   **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`:**
    *   Modified Section 3.B.2.i ("Component Resolution Search Order") and Section 4 ("Developing New promptApps") to accurately document the new "app-specific first" search order.
-   **Created Test Assets for Search Order Verification:**
    *   Global add-on: `prompts/add_ons/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   App-specific component (folder style): `prompts/apps/CompliancePLM/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   Updated `prompts/add_ons/available_addons_manifest.md` for the global test component.
    *   Updated `prompts/apps/CompliancePLM/CompliancePLM_manifest.json` to include a task "Test Component Resolution" that calls `test_resolver_component`.

**State of Deliverables:**
- Core framework logic (`Core_Planning_Instructions.txt`) now correctly implements "app-specific first" component resolution for `promptApps`.
- Documentation (`MPS_Usage_Guide.md`) accurately reflects this new search order.
- Test assets are in place to verify this specific functionality.
- The broader `promptApp` and `create_research_report` add-on implementation (from previous steps in this session) is complete.
- The system is ready for user testing of the `promptApp` functionality, especially the component search order.

**Next Steps (User):**
- Thoroughly test the `promptApp` system, focusing on:
    - Invoking the "Test Component Resolution" task within `CompliancePLM` to ensure the app-specific `test_resolver_component` is used.
    - Invoking tasks that use global components (like "Product Specification" using `create_research_report` in `CompliancePLM`) to ensure they are correctly found.
    - Testing the iteration flow and re-invocation prompts for iterable tasks.
---

## MPS Performance Feedback Log

This section logs specific feedback on the performance, clarity, and effectiveness of the Master Prompt Segment (MPS) versions and the artifacts they help generate.

---
**Feedback Entry - 2023-11-02**
**Source of Feedback:** Initial setup of this feedback log section during MPS feature design.
**MPS Version Referenced:** Relevant for `prompts/Master_Prompt_Segment.txt` (v0.3.2 and future versions).
**Context/Scenario:** Establishing the feedback mechanism as per design discussions for feature "Feedback Loop for MPS Refinement".
**Observation/Issue:** This log is intended to capture issues like:
    - Ambiguities in any version of `Master_Prompt_Segment.md` that lead to incorrect or suboptimal behavior by a Planning AI.
    - Problems encountered by Task AIs due to the structure or content of prompts generated based on an MPS version.
    - User difficulties in understanding or using the MPS, its generated `00_task_launch_plan.md`, or other artifacts.
    - Success stories where a particular MPS instruction proved very effective.
**Suggestion for MPS Refinement (if any):** N/A
---
*(Future entries will be appended below this line)*
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request to Jules instance for refactoring).
**MPS Version Referenced:** `prompts/Master_Prompt_Segment.txt` (approx. v0.3.2, prior to modularization) and the new modularized version (post this session's changes).
**Context/Scenario:** User identified that the `Master_Prompt_Segment.txt` was becoming very long and dense, making it difficult to read, manage, and maintain.
**Observation/Issue:** The monolithic nature of `Master_Prompt_Segment.txt` was impacting usability and the ease of making targeted changes to core planning instructions without affecting user-configurable sections.
**Suggestion for MPS Refinement (Implemented):** To improve readability and maintainability, the core planning AI instructions were extracted from `Master_Prompt_Segment.txt` and placed into a new, dedicated file: `prompts/iep/Core_Planning_Instructions.txt`. The `Master_Prompt_Segment.txt` now includes these instructions using an `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive. This separation is intended to make both the user-facing configuration parts of the MPS and the core instructional logic easier to manage.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request and illustrative example: `[ ] task_spawning_addon.txt` to Jules instance).
**MPS Version Referenced:** The modularized MPS version (where `Core_Planning_Instructions.txt` was already separate) and the current version implementing optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User desired to further enhance the framework's modularity by making the entire project planning and task generation process an optional component, selectable by the user, rather than an inherent, non-optional function of the MPS.
**Observation/Issue:** While `Core_Planning_Instructions.txt` was already separated, it still implicitly mandated the task spawning workflow. The framework lacked the flexibility to be used for other purposes without invoking this primary planning behavior.
**Suggestion for MPS Refinement (Implemented):** The user's suggestion to make task spawning an optional add-on was implemented.
    - The core logic for project planning, TLP generation, and task prompt creation was moved from `prompts/iep/Core_Planning_Instructions.txt` to a new file: `prompts/add_ons/task_spawning_addon.txt`.
    - `prompts/iep/Core_Planning_Instructions.txt` was updated to include conditional logic. It now checks if `task_spawning_addon.txt` is selected by the user in `[[USER_ADDON_SELECTION]]` (within `Master_Prompt_Segment.txt`).
    - If selected, the `task_spawning_addon.txt` is executed.
    - If not selected, the AI is instructed to acknowledge this, skip default plan generation, and process any other selected add-ons.
    - This change makes the MPS framework significantly more flexible, allowing users to engage the Planning AI for potentially different primary functions by selecting different combinations of add-ons, or by using it with only utility add-ons.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifying questions and specific directive regarding `task_spawning_addon.txt` inheritance and add-on ordering).
**MPS Version Referenced:** The MPS version with optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User inquired about how multiple add-ons interact, particularly how `task_spawning_addon.txt` would affect other add-ons, and then specifically requested that `task_spawning_addon.txt` should not be inherited by the Task AIs it generates. User also sought clarity on the importance of add-on ordering in the selection block.
**Observation/Issue:**
    1. The previous add-on processing logic could have resulted in `task_spawning_addon.txt` (intended for the Planning AI) being appended to Task AI prompts, leading to overly verbose prompts and potential for unintended behavior.
    2. The impact of add-on order in `[[USER_ADDON_SELECTION]]` on the final content of Task AI prompts was not explicitly documented, potentially leading to user confusion.
**Suggestion for MPS Refinement (Implemented):**
    - **Non-inheritance of `task_spawning_addon.txt`**: Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B) to create a filtered list (`Inheritable_Addons_Content_Ordered_List`) of add-on contents for Task AI prompt inheritance, which explicitly excludes the content of `task_spawning_addon.txt`. This ensures the Planning AI uses `task_spawning_addon.txt` for its process, but Task AIs receive cleaner, more focused prompts.
    - **Clarified Add-on Ordering**: Updated `prompts/Master_Prompt_Segment.txt` (comments in `[[USER_ADDON_SELECTION]]`) and `framework_dev_docs/guides/MPS_Usage_Guide.md` to explain that the order of inheritable add-ons is preserved when `task_spawning_addon.txt` appends them to task prompts. Also recommended listing `task_spawning_addon.txt` last for visual clarity if used.
    - These refinements improve the safety and predictability of add-on interactions and provide users with better control and understanding of how their selections affect Task AI prompts.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (initial idea for process automation, then specific refinements for primary input and reference depth for product specs).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** User expressed a need to use the MPS framework for more than just task spawning, specifically for a structured process to generate product specification documents from various inputs. Later refinements included requests for more control over the research phase of this process.
**Observation/Issue:** The initial MPS framework was primarily geared towards task spawning. A structured approach was needed to define and execute other complex, multi-step AI-driven processes. For the product specs process,
        *   Added `PRODUCT_SPECS_PRIMARY_INPUT_FILE`: An optional path to a specific document. The AI is instructed to focus its research by analyzing at least every numbered heading in this document. If not provided, the AI selects the most comprehensive input document for this role.
        *   Added `PRODUCT_SPECS_REF_DEPTH`: An optional integer (0-2, default 1) to control how many levels deep the AI will follow internal references within the provided document set.
    These enhancements make the product specification generation process more guided and configurable.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** Jules (proactive refinement during development of `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** The introduction of `build_product_specs_process.txt` as a new "primary directive" add-on (similar in role to `task_spawning_addon.txt` but with a different function) highlighted the need to generalize how the core framework selects and manages such add-ons.
**Observation/Issue:** The existing logic in `prompts/iep/Core_Planning_Instructions.txt` was somewhat hardcoded around `task_spawning_addon.txt` for determining the main execution path and for non-inheritance of add-on content into sub-prompts. This would not scale well if many different primary add-ons were developed.
**Suggestion for MPS Refinement (Implemented):**
    - Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B and II) to:
        - Define a list of `Known_Primary_Directive_Addons`.
        - Dynamically identify an `Active_Primary_Addon_Filename` by checking the user's selection against this known list, prioritizing the first one selected by the user if multiple are chosen. A warning is logged if multiple primary add-ons are selected.
        - Ensure that the `Inheritable_Addons_Content_Ordered_List` (used for appending to sub-prompts) correctly excludes the content of the currently `Active_Primary_Addon_Filename`, not just `task_spawning_addon.txt`.
    - This makes the core framework more robust and extensible, allowing new primary add-ons to be integrated more easily without requiring significant changes to the core conditional logic each time. It also handles user selection of multiple primary add-ons more gracefully.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (specific feedback on research methodology, codebase interaction rules like "level 0 only for codebases," and codebase review preferences for the `build_product_specs_process.txt` add-on).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on.
**Context/Scenario:** User provided detailed requirements to refine the initial `build_product_specs_process.txt` add-on to achieve a more structured, in-depth, and controllable research and content generation process, particularly focusing on how the AI should interact with a primary document and linked codebases.
**Observation/Issue:** The initial version of `build_product_specs_process.txt` had a more general research phase. The user required a more granular, heading-based research approach for the Primary Input Document, specific rules for how the AI should analyze linked codebases (including different levels of review depth/focus), and a strict "level 0 only" rule for code analysis to prevent unintended deep dives into code.
**Suggestion for MPS Refinement (Implemented):**
The `prompts/add_ons/build_product_specs_process.txt` was significantly updated to incorporate the "Level 0 Deep Research" methodology as per user requirements:
    - **New Configurations:** Introduced `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` in `Master_Prompt_Segment.txt` and documented them in `MPS_Usage_Guide.md`.
    - **Per-Heading Research Cycle:** The add-on's Phase 3 was restructured. It now includes an initial high-level review of the Primary Input Document. Then, for each major heading in that document, it performs a 5-step analysis: Heading Context Analysis, Supporting Docs Analysis, References & Codebase Analysis, Consolidated Research for the heading, and Iterative Content Contribution for that heading.
    - **Codebase Review Integration:** The "References & Codebase Analysis" step now explicitly uses the `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` ("complex", "cursory", "focused") and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` to guide its interaction with linked code.
    - **"Level 0 Code Analysis ONLY":** A strict rule was added, instructing the AI not to follow internal code references (like imports or function calls to other files) from any fetched code text. Its analysis is confined to the directly retrieved code content.
    - **Iterative Content Drafting:** Content for output documents is now drafted iteratively during the per-heading analysis and then assembled and finalized in later phases.
    This refined process provides a more systematic and controllable approach for the AI when generating product specifications.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifications and new requirements for iterative deepening for `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on incorporating "Level 0 Deep Research".
**Context/Scenario:** Expanding the iterative capabilities of `build_product_specs_process.txt` from an initial 0-2 level concept to a more detailed 0-3 level process with specific content goals for each level.
**Observation/Issue:** The initial iterative deepening plan (0-2 levels) for `build_product_specs_process.txt` needed more specific content goals for each level and an additional level for AI-driven insights to meet user's evolving requirements for content depth and sophistication.
**Suggestion for MPS Refinement (Implemented):**
The iterative deepening feature of `build_product_specs_process.txt` was expanded to support 0-3 levels, and associated configurations and documentation were updated:
    - **Configuration Updates (`Master_Prompt_Segment.txt`):** `PRODUCT_SPECS_ITERATION_DEPTH` comment updated to reflect the 0-3 range and specific goals:
        - `0`: Outline Generation (headings with single key idea paragraphs).
        - `1`: Basic Content (Bachelor's level, ~3 paras per D0 heading).
        - `2`: Expert Elaboration (~1 page per original D0 heading).
        - `3`: AI Unique Insights (~3 pages per original D0 heading, novel ideas).
    - **Add-on Logic (`build_product_specs_process.txt`):**
        - Phase 1 (Initialization) now validates depth (0-3) and `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` (required for D1-D3, critical error if missing). Output file suffixes `_D0` to `_D3` are determined.
        - Phase 2 (Input Processing) is conditional: D0 uses original inputs; D1-D3 use previous iteration's outputs as primary, with original inputs as reference.
        - Phase 3 (Content Generation) has distinct logic for each depth (0-3), detailing how to build upon previous work (for D1-D3) or generate outlines (D0) according to the specified content goals for each level.
    - **Documentation (`MPS_Usage_Guide.md`):** Updated to detail the 0-3 levels, their specific purposes, and the user workflow for managing iterative runs and output folders.
    This provides a more granular, powerful, and well-defined iterative workflow for the `build_product_specs_process.txt` add-on.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (vision for enhanced component modularity via "folder-per-app" architecture).
**MPS Version Referenced:** Current version with multiple add-ons and utils.
**Context/Scenario:** As the number of components (add-ons, utils, and potentially IPC handlers) grows, a flat file structure in their respective directories (`prompts/add_ons/`, `prompts/util/`) becomes less organized and limits the ability for components to have their own supporting files or sub-frameworks.
**Observation/Issue:** The previous flat-file structure for add-ons and utils was not ideal for scalability or for components that might require multiple associated files (e.g., templates, data snippets, complex sub-instructions).
**Suggestion for MPS Refinement (Implemented):**
The framework was refactored to a "folder-per-app" architecture for all components (add-ons, utils, and anticipated IPC handlers):
    - **Core Logic (`prompts/iep/Core_Planning_Instructions.txt`):** Updated to discover and load components based on a new directory structure.
        - It now expects component-specific parent paths like `User_Path_Addons_Dir`, `User_Path_Utils_Dir`, `User_Path_IPC_Dir` to be defined in `[[USER PATH CONFIGURATION]]`.
        - For each component "app" (e.g., `my_addon_app`), it looks for a subdirectory named `my_addon_app` within the relevant parent path.
        - The primary instruction file for the app must be located at `[parent_path]/my_addon_app/my_addon_app.txt`.
        - Internal references (e.g., `Known_Primary_Directive_Addon_Apps`, `Active_Primary_Addon_AppName`) now use these app/folder names.
    - **Component Migration:** All existing add-ons (`task_spawning_addon`, `task_resumption_addon`, `build_product_specs_process`) and utils (`pre_flight_check_user_plan`) were moved into their respective new subdirectories (e.g., `prompts/add_ons/task_spawning_addon/task_spawning_addon.txt`).
    - **New `prompts/ipc/` directory:** Created with a `.gitkeep` file for future IPC components.
    - **Documentation Updates:**
        - `prompts/add_ons/available_addons_manifest.md`: Updated to list add-ons by their app/folder name and describe the new structure.
        - `framework_dev_docs/guides/MPS_Usage_Guide.md`: Comprehensively updated to explain the "folder-per-app" architecture, how users should set up paths, how components are selected, and how developers should structure new components.
    This architectural change significantly improves modularity, organization, and the potential for more complex, self-contained components within the MPS framework.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (desire to simplify MPS, new way to handle app-specific data, AI assistance for missing/default configs, specific format for detecting edits).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment and Core Planning Instructions).
**Context/Scenario:** This feedback entry summarizes a series of user requests and design discussions aimed at a comprehensive overhaul of the MPS framework's configuration system and a significant enhancement of the `build_product_specs_process` add-on's iterative capabilities.
**Observation/Issue:**
    1.  The global `[[USER PATH CONFIGURATION]]` in `Master_Prompt_Segment.txt` was becoming unwieldy; app-specific configs were mixed with global ones; parameter precedence and user edit detection were not formalized.
    2.  A more structured and discoverable way to manage parameters for individual add-on apps was needed, including clear defaults and user override mechanisms.
    3.  Users needed assistance in setting mandatory parameters, especially those with placeholder defaults.
**Suggestion for MPS Refinement (Implemented):**
A comprehensive refactoring of the configuration system was performed:
    1.  **Fixed Global System Paths:** Core component discovery paths (for add-ons, utils, IEP, IPC) are now hardcoded in `Core_Planning_Instructions.txt`, simplifying user setup.
    2.  **`[[USER PATH CONFIGURATION]]` Removed from `Master_Prompt_Segment.txt`:** This block was deleted. General output paths (like `Main Iteration Folder`) and other process-specific parameters are now intended to be managed as parameters within the configuration system of the relevant app.
    3.  **App-Specific Configuration System:**
        *   **`USER_appName_CONFIG.txt` Standard:** Defined a standard format for these template/storage files within each app's folder. This includes `[[USER_CONFIG_FOR_appName]]` delimiters, and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), description with default (`# Description: ... Default: ...`), and the value line (`PARAM_NAME: [USER_VALUE_START]value[USER_VALUE_END]`).
        *   **Main Prompt Overrides:** Users can copy the content of a `USER_appName_CONFIG.txt` into their main spawn prompt to provide run-specific overrides.
        *   **Parameter Resolution Precedence:** Implemented in app logic (demonstrated in `build_product_specs_process.txt`): Main prompt block > User-edited `USER_appName_CONFIG.txt` > AcceptedDefault in `USER_appName_CONFIG.txt`.
        *   **AI-Assisted Updates:** If a `Required` parameter with a `PlaceholderDefault` remains unresolved, the app instructs the AI (Jules) to request the value from the user via chat. The AI then updates the on-disk `USER_appName_CONFIG.txt` with the new value and outputs the complete updated config block for the user to transfer to their main spawn prompt.
    4.  **Documentation:** All changes were reflected in `Master_Prompt_Segment.txt` (now v0.4.0), `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, and relevant add-on files (e.g., `build_product_specs_process/USER_build_product_specs_process_CONFIG.txt` and its primary instruction file).
    This new system offers improved modularity, clarity in parameter management, and a better user experience through AI-assisted configuration.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Accomplishments This Session (Finalizing Configuration System & Preparing for "Concept Task" Handoff):**
This session concluded the major refactoring of the MPS framework's configuration system and component architecture. It also prepared for the next phase of work focusing on the "Concept task."

*   **Configuration System Refactoring (Completion & Documentation):**
    *   **Standard for `USER_appName_CONFIG.txt`:** Formalized the standard for these files, including app-specific delimiters (`[[USER_CONFIG_FOR_appName]]`), and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), descriptions, defaults, and `[USER_VALUE_START]...[USER_VALUE_END]` markers. An illustrative example (`USER_exampleApp_CONFIG.txt`) was created as part of this standard definition.
    *   **Applied Standard:** The `build_product_specs_process` add-on's `USER_build_product_specs_process_CONFIG.txt` was updated to fully adhere to this new standard, with all its parameters classified.
    *   **Refined App Logic (`build_product_specs_process.txt` v0.4):** Its parameter resolution logic in Phase 1 was enhanced to parse the new metadata from `USER_..._CONFIG.txt`. The AI-assisted update mechanism (asking user via chat) now correctly triggers only for "Required" parameters with "PlaceholderDefault" values that haven't been overridden. A final validation step ensures all "Required" parameters have valid values before proceeding, halting with an error if not.
    *   **Streamlined `Master_Prompt_Segment.txt` (v0.4.0):** The global `[[USER PATH CONFIGURATION]]` block was definitively removed. Instructions in `[[USER_ADDON_SELECTION]]` were updated to guide users on app-specific configurations via `[[USER_CONFIG_FOR_appName]]` blocks in their main spawn prompt.
    *   **Consolidated `MPS_Usage_Guide.md`:** The guide was thoroughly reviewed and updated to provide a comprehensive and consistent explanation of the new configuration system. This includes: fixed global system paths, the role and format of `USER_appName_CONFIG.txt` files, the user workflow for app configuration (using main prompt blocks or editing `USER_...CONFIG.txt`), the AI's parameter resolution precedence, and how AI-assisted updates work. The "folder-per-app" architecture is also clearly documented.

*   **Next Area of Work (User Directed - For Next Jules Instance):**
    *   The user has directed focus towards refining an existing "Concept task."
    *   This will involve analyzing the "Concept task" and splitting it into approximately three sub-tasks.
    *   A key aspect of this work will be to use the "Concept task" sub-tasks as a practical use case for **exploring and implementing extensions to the 'add-on, util, and process' component concepts** within the MPS framework.
    *   The next Jules instance is tasked with initiating this by requesting detailed requirements from the user regarding the "Concept task" (current state, materials possibly in `Inputs/concept/inputs`, ideas for sub-tasks) and their initial thoughts on evolving the component model.

**State of Deliverables:**
The comprehensive refactoring of the MPS configuration system is complete. This includes the "folder-per-app" architecture, fixed system paths for component discovery, and the new app-specific configuration mechanism using `USER_appName_CONFIG.txt` files and `[[USER_CONFIG_FOR_appName]]` blocks, featuring AI-assisted updates. All core prompts and documentation (`Master_Prompt_Segment.txt`, `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`, and relevant add-ons like `build_product_specs_process`) have been updated to reflect these changes. The framework is significantly more modular, user-friendly, and robust in its configuration management. No new code or prompt changes were made in this specific summarization step. The system is ready for handoff to the next Jules instance to begin work on the "Concept task" and component model evolution.
---
---
**Feedback Entry Date:** 2025-06-02
**Source of Feedback:** User (new directive for next area of work following completion of major configuration system and architectural refactoring).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment) and related component architecture (folder-per-app, `USER_appName_CONFIG.txt` standard, etc.).
**Context/Scenario:** Planning the next development cycle for the MPS framework after successfully implementing a new configuration system and "folder-per-app" architecture.
**Observation/Issue:**
    1.  An existing internal "Concept task" (details to be provided by the user to the next Jules instance, potentially located in `Inputs/concept/inputs`) is currently monolithic and needs to be broken down into more manageable sub-tasks (approximately three).
    2.  The process of decomposing and implementing these "Concept task" sub-tasks is expected to reveal opportunities or requirements for evolving the framework's current component definitions (add-on, util, process) or potentially introducing new component types or interaction paradigms.
**Suggestion (For Next Jules Instance):**
The next Jules instance should:
    1.  **Gather Detailed Requirements for "Concept Task":** Actively engage with the user to obtain a detailed understanding of the current "Concept task," its goals, any existing materials (potentially in `Inputs/concept/inputs`).
    2.  Collaborate with the user to define the three (approx.) sub-tasks for the "Concept task."
    3.  In parallel, analyze how the requirements of these sub-tasks might necessitate extensions to the existing component concepts (add-on, util, process) or the introduction of new component types/paradigms within the MPS framework.
    4.  Plan and implement both the "Concept task" restructuring and the identified framework component extensions, using the former as a practical test case for the latter.
---
**Session Summary - 2025-06-03**
**Instance:** Jules
**User Feedback/Requests Addressed:**
- User directed a new phase of MPS development focusing on two major initiatives:
    1.  Abstracting the existing `build_product_specs_process` add-on into a more generic `create_research_report` add-on.
    2.  Introducing a new "promptApp" concept to allow users to define and execute multi-stage workflows composed of underlying MPS components.
- Clarified requirements for `create_research_report` parameters, including separate controls for `ITERATIVE_OUTPUT_DEPTH` (D0-D3 style) and `REFERENCE_FOLLOWING_DEPTH` (document link traversal).
- Confirmed that plain text input formats (.txt, .md, web text) are the initial target for document processing.
- Decided that sequential tasks within a `promptApp` will be handled by user re-invocation of new AI instances for each task, simplifying initial framework orchestration.

**Summary of Approved Plan:**
The overall plan involves:
1.  Developing the `create_research_report` add-on (now complete).
2.  Defining the "promptApp" structure and manifest file format.
3.  Updating `Core_Planning_Instructions.txt` and `Master_Prompt_Segment.txt` to support `promptApp` discovery, selection, and execution.
4.  Creating an initial structural example of a `promptApp` (`CompliancePLM`).
5.  Retiring the `build_product_specs_process` add-on files.
6.  Updating all documentation.
7.  Testing the new functionalities.

**Summary of Changes Made This Session (to implement Step 1 of the plan):**
-   **Created `prompts/add_ons/create_research_report/` directory.**
-   **Created `prompts/add_ons/create_research_report/USER_create_research_report_CONFIG.txt`:**
    *   Defined parameters: `PROPOSED_TABLE_OF_CONTENTS`, `GUIDELINES_DOC_PATHS`, `ADDITIONAL_WEB_LINKS_FILE`, `EXEMPLAR_DOCS`, `GENERAL_INPUT_DOCS`, `PRIMARY_INPUT_DOC_PATH`, `ITERATIVE_OUTPUT_DEPTH`, `REFERENCE_FOLLOWING_DEPTH`, `PREVIOUS_ITERATION_OUTPUT_PATH`, `REPORT_BASENAME`, `REPORT_OUTPUT_PATH`.
    *   Included full metadata (Requirement, DefaultType, Description, Default) for each.
-   **Created `prompts/add_ons/create_research_report/create_research_report.txt`:**
    *   Adapted core logic from the former `build_product_specs_process.txt`.
    *   Updated to use the new parameter set for generic report generation.
    *   Removed product-specification-specific and detailed codebase review logic.
    *   Maintained the 6-phase structure and parameter resolution mechanisms.
-   **Updated `prompts/add_ons/available_addons_manifest.md`:**
    *   Added entry for `create_research_report`.
    *   Removed entry for `build_product_specs_process`.

**State of Deliverables:**
- The `create_research_report` add-on (v0.1) is developed and its configuration file is in place.
- The add-on manifest is updated.
- The `build_product_specs_process` add-on is effectively replaced by this new add-on (physical file deletion is pending a later plan step).
- Plan step 1 is complete.

**Next Steps (This Jules instance/session):**
- Proceed to Plan Step 2: "Define 'promptApp' Structure and Manifest." This involves designing the manifest file format that will define how `promptApps` (like the `CompliancePLM` example) are structured and how their tasks map to MPS components.
---
**Session Summary - 2025-06-03 (Continued)**
**Instance:** Jules
**User Feedback/Requests Addressed (Post Initial 2025-06-03 Summary):**
- User confirmed a critical change in component resolution strategy for `promptApps`: the search order should be "app-specific first" (flat file, then folder style within app), followed by global components (add-ons, then utils). This was a change from the initially implemented "globals first" approach.

**Summary of Approved Plan (Revised Portion):**
The plan was revised to implement and document this new search order:
1.  Revise `Core_Planning_Instructions.txt` for the new "app-specific first" search order. (Completed)
2.  Update `MPS_Usage_Guide.md` to reflect this new search order. (Completed)
3.  Create test assets and conceptually outline tests for the new search order. (Completed)
4.  Update `HANDOFF_NOTES.md` and submit. (This step)

**Summary of Changes Made This Session (Implementing Revised Plan):**
-   **Modified `prompts/iep/Core_Planning_Instructions.txt`:**
    *   Updated the component resolution logic in Section II.A.1.a.viii to implement the "app-specific first" search order:
        1.  App-Specific (flat file): `prompts/apps/[Selected_PromptAppName]/[componentName].txt`
        2.  App-Specific (folder style): `prompts/apps/[Selected_PromptAppName]/[componentName]/[componentName].txt`
        3.  Global Add-on: `prompts/add_ons/[componentName]/[componentName].txt`
        4.  Global Util: `prompts/util/[componentName]/[componentName].txt`
-   **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`:**
    *   Modified Section 3.B.2.i ("Component Resolution Search Order") and Section 4 ("Developing New promptApps") to accurately document the new "app-specific first" search order.
-   **Created Test Assets for Search Order Verification:**
    *   Global add-on: `prompts/add_ons/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   App-specific component (folder style): `prompts/apps/CompliancePLM/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   Updated `prompts/add_ons/available_addons_manifest.md` for the global test component.
    *   Updated `prompts/apps/CompliancePLM/CompliancePLM_manifest.json` to include a task "Test Component Resolution" that calls `test_resolver_component`.

**State of Deliverables:**
- Core framework logic (`Core_Planning_Instructions.txt`) now correctly implements "app-specific first" component resolution for `promptApps`.
- Documentation (`MPS_Usage_Guide.md`) accurately reflects this new search order.
- Test assets are in place to verify this specific functionality.
- The broader `promptApp` and `create_research_report` add-on implementation (from previous steps in this session) is complete.
- The system is ready for user testing of the `promptApp` functionality, especially the component search order.

**Next Steps (User):**
- Thoroughly test the `promptApp` system, focusing on:
    - Invoking the "Test Component Resolution" task within `CompliancePLM` to ensure the app-specific `test_resolver_component` is used.
    - Invoking tasks that use global components (like "Product Specification" using `create_research_report` in `CompliancePLM`) to ensure they are correctly found.
    - Testing the iteration flow and re-invocation prompts for iterable tasks.
---

## MPS Performance Feedback Log

This section logs specific feedback on the performance, clarity, and effectiveness of the Master Prompt Segment (MPS) versions and the artifacts they help generate.

---
**Feedback Entry - 2023-11-02**
**Source of Feedback:** Initial setup of this feedback log section during MPS feature design.
**MPS Version Referenced:** Relevant for `prompts/Master_Prompt_Segment.txt` (v0.3.2 and future versions).
**Context/Scenario:** Establishing the feedback mechanism as per design discussions for feature "Feedback Loop for MPS Refinement".
**Observation/Issue:** This log is intended to capture issues like:
    - Ambiguities in any version of `Master_Prompt_Segment.md` that lead to incorrect or suboptimal behavior by a Planning AI.
    - Problems encountered by Task AIs due to the structure or content of prompts generated based on an MPS version.
    - User difficulties in understanding or using the MPS, its generated `00_task_launch_plan.md`, or other artifacts.
    - Success stories where a particular MPS instruction proved very effective.
**Suggestion for MPS Refinement (if any):** N/A
---
*(Future entries will be appended below this line)*
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request to Jules instance for refactoring).
**MPS Version Referenced:** `prompts/Master_Prompt_Segment.txt` (approx. v0.3.2, prior to modularization) and the new modularized version (post this session's changes).
**Context/Scenario:** User identified that the `Master_Prompt_Segment.txt` was becoming very long and dense, making it difficult to read, manage, and maintain.
**Observation/Issue:** The monolithic nature of `Master_Prompt_Segment.txt` was impacting usability and the ease of making targeted changes to core planning instructions without affecting user-configurable sections.
**Suggestion for MPS Refinement (Implemented):** To improve readability and maintainability, the core planning AI instructions were extracted from `Master_Prompt_Segment.txt` and placed into a new, dedicated file: `prompts/iep/Core_Planning_Instructions.txt`. The `Master_Prompt_Segment.txt` now includes these instructions using an `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive. This separation is intended to make both the user-facing configuration parts of the MPS and the core instructional logic easier to manage.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request and illustrative example: `[ ] task_spawning_addon.txt` to Jules instance).
**MPS Version Referenced:** The modularized MPS version (where `Core_Planning_Instructions.txt` was already separate) and the current version implementing optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User desired to further enhance the framework's modularity by making the entire project planning and task generation process an optional component, selectable by the user, rather than an inherent, non-optional function of the MPS.
**Observation/Issue:** While `Core_Planning_Instructions.txt` was already separated, it still implicitly mandated the task spawning workflow. The framework lacked the flexibility to be used for other purposes without invoking this primary planning behavior.
**Suggestion for MPS Refinement (Implemented):** The user's suggestion to make task spawning an optional add-on was implemented.
    - The core logic for project planning, TLP generation, and task prompt creation was moved from `prompts/iep/Core_Planning_Instructions.txt` to a new file: `prompts/add_ons/task_spawning_addon.txt`.
    - `prompts/iep/Core_Planning_Instructions.txt` was updated to include conditional logic. It now checks if `task_spawning_addon.txt` is selected by the user in `[[USER_ADDON_SELECTION]]` (within `Master_Prompt_Segment.txt`).
    - If selected, the `task_spawning_addon.txt` is executed.
    - If not selected, the AI is instructed to acknowledge this, skip default plan generation, and process any other selected add-ons.
    - This change makes the MPS framework significantly more flexible, allowing users to engage the Planning AI for potentially different primary functions by selecting different combinations of add-ons, or by using it with only utility add-ons.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifying questions and specific directive regarding `task_spawning_addon.txt` inheritance and add-on ordering).
**MPS Version Referenced:** The MPS version with optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User inquired about how multiple add-ons interact, particularly how `task_spawning_addon.txt` would affect other add-ons, and then specifically requested that `task_spawning_addon.txt` should not be inherited by the Task AIs it generates. User also sought clarity on the importance of add-on ordering in the selection block.
**Observation/Issue:**
    1. The previous add-on processing logic could have resulted in `task_spawning_addon.txt` (intended for the Planning AI) being appended to Task AI prompts, leading to overly verbose prompts and potential for unintended behavior.
    2. The impact of add-on order in `[[USER_ADDON_SELECTION]]` on the final content of Task AI prompts was not explicitly documented, potentially leading to user confusion.
**Suggestion for MPS Refinement (Implemented):**
    - **Non-inheritance of `task_spawning_addon.txt`**: Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B) to create a filtered list (`Inheritable_Addons_Content_Ordered_List`) of add-on contents for Task AI prompt inheritance, which explicitly excludes the content of `task_spawning_addon.txt`. This ensures the Planning AI uses `task_spawning_addon.txt` for its process, but Task AIs receive cleaner, more focused prompts.
    - **Clarified Add-on Ordering**: Updated `prompts/Master_Prompt_Segment.txt` (comments in `[[USER_ADDON_SELECTION]]`) and `framework_dev_docs/guides/MPS_Usage_Guide.md` to explain that the order of inheritable add-ons is preserved when `task_spawning_addon.txt` appends them to task prompts. Also recommended listing `task_spawning_addon.txt` last for visual clarity if used.
    - These refinements improve the safety and predictability of add-on interactions and provide users with better control and understanding of how their selections affect Task AI prompts.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (initial idea for process automation, then specific refinements for primary input and reference depth for product specs).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** User expressed a need to use the MPS framework for more than just task spawning, specifically for a structured process to generate product specification documents from various inputs. Later refinements included requests for more control over the research phase of this process.
**Observation/Issue:** The initial MPS framework was primarily geared towards task spawning. A structured approach was needed to define and execute other complex, multi-step AI-driven processes. For the product specs process,
        *   Added `PRODUCT_SPECS_PRIMARY_INPUT_FILE`: An optional path to a specific document. The AI is instructed to focus its research by analyzing at least every numbered heading in this document. If not provided, the AI selects the most comprehensive input document for this role.
        *   Added `PRODUCT_SPECS_REF_DEPTH`: An optional integer (0-2, default 1) to control how many levels deep the AI will follow internal references within the provided document set.
    These enhancements make the product specification generation process more guided and configurable.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** Jules (proactive refinement during development of `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** The introduction of `build_product_specs_process.txt` as a new "primary directive" add-on (similar in role to `task_spawning_addon.txt` but with a different function) highlighted the need to generalize how the core framework selects and manages such add-ons.
**Observation/Issue:** The existing logic in `prompts/iep/Core_Planning_Instructions.txt` was somewhat hardcoded around `task_spawning_addon.txt` for determining the main execution path and for non-inheritance of add-on content into sub-prompts. This would not scale well if many different primary add-ons were developed.
**Suggestion for MPS Refinement (Implemented):**
    - Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B and II) to:
        - Define a list of `Known_Primary_Directive_Addons`.
        - Dynamically identify an `Active_Primary_Addon_Filename` by checking the user's selection against this known list, prioritizing the first one selected by the user if multiple are chosen. A warning is logged if multiple primary add-ons are selected.
        - Ensure that the `Inheritable_Addons_Content_Ordered_List` (used for appending to sub-prompts) correctly excludes the content of the currently `Active_Primary_Addon_Filename`, not just `task_spawning_addon.txt`.
    - This makes the core framework more robust and extensible, allowing new primary add-ons to be integrated more easily without requiring significant changes to the core conditional logic each time. It also handles user selection of multiple primary add-ons more gracefully.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (specific feedback on research methodology, codebase interaction rules like "level 0 only for codebases," and codebase review preferences for the `build_product_specs_process.txt` add-on).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on.
**Context/Scenario:** User provided detailed requirements to refine the initial `build_product_specs_process.txt` add-on to achieve a more structured, in-depth, and controllable research and content generation process, particularly focusing on how the AI should interact with a primary document and linked codebases.
**Observation/Issue:** The initial version of `build_product_specs_process.txt` had a more general research phase. The user required a more granular, heading-based research approach for the Primary Input Document, specific rules for how the AI should analyze linked codebases (including different levels of review depth/focus), and a strict "level 0 only" rule for code analysis to prevent unintended deep dives into code.
**Suggestion for MPS Refinement (Implemented):**
The `prompts/add_ons/build_product_specs_process.txt` was significantly updated to incorporate the "Level 0 Deep Research" methodology as per user requirements:
    - **New Configurations:** Introduced `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` in `Master_Prompt_Segment.txt` and documented them in `MPS_Usage_Guide.md`.
    - **Per-Heading Research Cycle:** The add-on's Phase 3 was restructured. It now includes an initial high-level review of the Primary Input Document. Then, for each major heading in that document, it performs a 5-step analysis: Heading Context Analysis, Supporting Docs Analysis, References & Codebase Analysis, Consolidated Research for the heading, and Iterative Content Contribution for that heading.
    - **Codebase Review Integration:** The "References & Codebase Analysis" step now explicitly uses the `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` ("complex", "cursory", "focused") and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` to guide its interaction with linked code.
    - **"Level 0 Code Analysis ONLY":** A strict rule was added, instructing the AI not to follow internal code references (like imports or function calls to other files) from any fetched code text. Its analysis is confined to the directly retrieved code content.
    - **Iterative Content Drafting:** Content for output documents is now drafted iteratively during the per-heading analysis and then assembled and finalized in later phases.
    This refined process provides a more systematic and controllable approach for the AI when generating product specifications.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifications and new requirements for iterative deepening for `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on incorporating "Level 0 Deep Research".
**Context/Scenario:** Expanding the iterative capabilities of `build_product_specs_process.txt` from an initial 0-2 level concept to a more detailed 0-3 level process with specific content goals for each level.
**Observation/Issue:** The initial iterative deepening plan (0-2 levels) for `build_product_specs_process.txt` needed more specific content goals for each level and an additional level for AI-driven insights to meet user's evolving requirements for content depth and sophistication.
**Suggestion for MPS Refinement (Implemented):**
The iterative deepening feature of `build_product_specs_process.txt` was expanded to support 0-3 levels, and associated configurations and documentation were updated:
    - **Configuration Updates (`Master_Prompt_Segment.txt`):** `PRODUCT_SPECS_ITERATION_DEPTH` comment updated to reflect the 0-3 range and specific goals:
        - `0`: Outline Generation (headings with single key idea paragraphs).
        - `1`: Basic Content (Bachelor's level, ~3 paras per D0 heading).
        - `2`: Expert Elaboration (~1 page per original D0 heading).
        - `3`: AI Unique Insights (~3 pages per original D0 heading, novel ideas).
    - **Add-on Logic (`build_product_specs_process.txt`):**
        - Phase 1 (Initialization) now validates depth (0-3) and `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` (required for D1-D3, critical error if missing). Output file suffixes `_D0` to `_D3` are determined.
        - Phase 2 (Input Processing) is conditional: D0 uses original inputs; D1-D3 use previous iteration's outputs as primary, with original inputs as reference.
        - Phase 3 (Content Generation) has distinct logic for each depth (0-3), detailing how to build upon previous work (for D1-D3) or generate outlines (D0) according to the specified content goals for each level.
    - **Documentation (`MPS_Usage_Guide.md`):** Updated to detail the 0-3 levels, their specific purposes, and the user workflow for managing iterative runs and output folders.
    This provides a more granular, powerful, and well-defined iterative workflow for the `build_product_specs_process.txt` add-on.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (vision for enhanced component modularity via "folder-per-app" architecture).
**MPS Version Referenced:** Current version with multiple add-ons and utils.
**Context/Scenario:** As the number of components (add-ons, utils, and potentially IPC handlers) grows, a flat file structure in their respective directories (`prompts/add_ons/`, `prompts/util/`) becomes less organized and limits the ability for components to have their own supporting files or sub-frameworks.
**Observation/Issue:** The previous flat-file structure for add-ons and utils was not ideal for scalability or for components that might require multiple associated files (e.g., templates, data snippets, complex sub-instructions).
**Suggestion for MPS Refinement (Implemented):**
The framework was refactored to a "folder-per-app" architecture for all components (add-ons, utils, and anticipated IPC handlers):
    - **Core Logic (`prompts/iep/Core_Planning_Instructions.txt`):** Updated to discover and load components based on a new directory structure.
        - It now expects component-specific parent paths like `User_Path_Addons_Dir`, `User_Path_Utils_Dir`, `User_Path_IPC_Dir` to be defined in `[[USER PATH CONFIGURATION]]`.
        - For each component "app" (e.g., `my_addon_app`), it looks for a subdirectory named `my_addon_app` within the relevant parent path.
        - The primary instruction file for the app must be located at `[parent_path]/my_addon_app/my_addon_app.txt`.
        - Internal references (e.g., `Known_Primary_Directive_Addon_Apps`, `Active_Primary_Addon_AppName`) now use these app/folder names.
    - **Component Migration:** All existing add-ons (`task_spawning_addon`, `task_resumption_addon`, `build_product_specs_process`) and utils (`pre_flight_check_user_plan`) were moved into their respective new subdirectories (e.g., `prompts/add_ons/task_spawning_addon/task_spawning_addon.txt`).
    - **New `prompts/ipc/` directory:** Created with a `.gitkeep` file for future IPC components.
    - **Documentation Updates:**
        - `prompts/add_ons/available_addons_manifest.md`: Updated to list add-ons by their app/folder name and describe the new structure.
        - `framework_dev_docs/guides/MPS_Usage_Guide.md`: Comprehensively updated to explain the "folder-per-app" architecture, how users should set up paths, how components are selected, and how developers should structure new components.
    This architectural change significantly improves modularity, organization, and the potential for more complex, self-contained components within the MPS framework.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (desire to simplify MPS, new way to handle app-specific data, AI assistance for missing/default configs, specific format for detecting edits).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment and Core Planning Instructions).
**Context/Scenario:** This feedback entry summarizes a series of user requests and design discussions aimed at a comprehensive overhaul of the MPS framework's configuration system and a significant enhancement of the `build_product_specs_process` add-on's iterative capabilities.
**Observation/Issue:**
    1.  The global `[[USER PATH CONFIGURATION]]` in `Master_Prompt_Segment.txt` was becoming unwieldy; app-specific configs were mixed with global ones; parameter precedence and user edit detection were not formalized.
    2.  A more structured and discoverable way to manage parameters for individual add-on apps was needed, including clear defaults and user override mechanisms.
    3.  Users needed assistance in setting mandatory parameters, especially those with placeholder defaults.
**Suggestion for MPS Refinement (Implemented):**
A comprehensive refactoring of the configuration system was performed:
    1.  **Fixed Global System Paths:** Core component discovery paths (for add-ons, utils, IEP, IPC) are now hardcoded in `Core_Planning_Instructions.txt`, simplifying user setup.
    2.  **`[[USER PATH CONFIGURATION]]` Removed from `Master_Prompt_Segment.txt`:** This block was deleted. General output paths (like `Main Iteration Folder`) and other process-specific parameters are now intended to be managed as parameters within the configuration system of the relevant app.
    3.  **App-Specific Configuration System:**
        *   **`USER_appName_CONFIG.txt` Standard:** Defined a standard format for these template/storage files within each app's folder. This includes `[[USER_CONFIG_FOR_appName]]` delimiters, and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), description with default (`# Description: ... Default: ...`), and the value line (`PARAM_NAME: [USER_VALUE_START]value[USER_VALUE_END]`).
        *   **Main Prompt Overrides:** Users can copy the content of a `USER_appName_CONFIG.txt` into their main spawn prompt to provide run-specific overrides.
        *   **Parameter Resolution Precedence:** Implemented in app logic (demonstrated in `build_product_specs_process.txt`): Main prompt block > User-edited `USER_appName_CONFIG.txt` > AcceptedDefault in `USER_appName_CONFIG.txt`.
        *   **AI-Assisted Updates:** If a `Required` parameter with a `PlaceholderDefault` remains unresolved, the app instructs the AI (Jules) to request the value from the user via chat. The AI then updates the on-disk `USER_appName_CONFIG.txt` with the new value and outputs the complete updated config block for the user to transfer to their main spawn prompt.
    4.  **Documentation:** All changes were reflected in `Master_Prompt_Segment.txt` (now v0.4.0), `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, and relevant add-on files (e.g., `build_product_specs_process/USER_build_product_specs_process_CONFIG.txt` and its primary instruction file).
    This new system offers improved modularity, clarity in parameter management, and a better user experience through AI-assisted configuration.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Accomplishments This Session (Finalizing Configuration System & Preparing for "Concept Task" Handoff):**
This session concluded the major refactoring of the MPS framework's configuration system and component architecture. It also prepared for the next phase of work focusing on the "Concept task."

*   **Configuration System Refactoring (Completion & Documentation):**
    *   **Standard for `USER_appName_CONFIG.txt`:** Formalized the standard for these files, including app-specific delimiters (`[[USER_CONFIG_FOR_appName]]`), and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), descriptions, defaults, and `[USER_VALUE_START]...[USER_VALUE_END]` markers. An illustrative example (`USER_exampleApp_CONFIG.txt`) was created as part of this standard definition.
    *   **Applied Standard:** The `build_product_specs_process` add-on's `USER_build_product_specs_process_CONFIG.txt` was updated to fully adhere to this new standard, with all its parameters classified.
    *   **Refined App Logic (`build_product_specs_process.txt` v0.4):** Its parameter resolution logic in Phase 1 was enhanced to parse the new metadata from `USER_..._CONFIG.txt`. The AI-assisted update mechanism (asking user via chat) now correctly triggers only for "Required" parameters with "PlaceholderDefault" values that haven't been overridden. A final validation step ensures all "Required" parameters have valid values before proceeding, halting with an error if not.
    *   **Streamlined `Master_Prompt_Segment.txt` (v0.4.0):** The global `[[USER PATH CONFIGURATION]]` block was definitively removed. Instructions in `[[USER_ADDON_SELECTION]]` were updated to guide users on app-specific configurations via `[[USER_CONFIG_FOR_appName]]` blocks in their main spawn prompt.
    *   **Consolidated `MPS_Usage_Guide.md`:** The guide was thoroughly reviewed and updated to provide a comprehensive and consistent explanation of the new configuration system. This includes: fixed global system paths, the role and format of `USER_appName_CONFIG.txt` files, the user workflow for app configuration (using main prompt blocks or editing `USER_...CONFIG.txt`), the AI's parameter resolution precedence, and how AI-assisted updates work. The "folder-per-app" architecture is also clearly documented.

*   **Next Area of Work (User Directed - For Next Jules Instance):**
    *   The user has directed focus towards refining an existing "Concept task."
    *   This will involve analyzing the "Concept task" and splitting it into approximately three sub-tasks.
    *   A key aspect of this work will be to use the "Concept task" sub-tasks as a practical use case for **exploring and implementing extensions to the 'add-on, util, and process' component concepts** within the MPS framework.
    *   The next Jules instance is tasked with initiating this by requesting detailed requirements from the user regarding the "Concept task" (current state, materials possibly in `Inputs/concept/inputs`, ideas for sub-tasks) and their initial thoughts on evolving the component model.

**State of Deliverables:**
The comprehensive refactoring of the MPS configuration system is complete. This includes the "folder-per-app" architecture, fixed system paths for component discovery, and the new app-specific configuration mechanism using `USER_appName_CONFIG.txt` files and `[[USER_CONFIG_FOR_appName]]` blocks, featuring AI-assisted updates. All core prompts and documentation (`Master_Prompt_Segment.txt`, `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`, and relevant add-ons like `build_product_specs_process`) have been updated to reflect these changes. The framework is significantly more modular, user-friendly, and robust in its configuration management. No new code or prompt changes were made in this specific summarization step. The system is ready for handoff to the next Jules instance to begin work on the "Concept task" and component model evolution.
---
---
**Feedback Entry Date:** 2025-06-02
**Source of Feedback:** User (new directive for next area of work following completion of major configuration system and architectural refactoring).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment) and related component architecture (folder-per-app, `USER_appName_CONFIG.txt` standard, etc.).
**Context/Scenario:** Planning the next development cycle for the MPS framework after successfully implementing a new configuration system and "folder-per-app" architecture.
**Observation/Issue:**
    1.  An existing internal "Concept task" (details to be provided by the user to the next Jules instance, potentially located in `Inputs/concept/inputs`) is currently monolithic and needs to be broken down into more manageable sub-tasks (approximately three).
    2.  The process of decomposing and implementing these "Concept task" sub-tasks is expected to reveal opportunities or requirements for evolving the framework's current component definitions (add-on, util, process) or potentially introducing new component types or interaction paradigms.
**Suggestion (For Next Jules Instance):**
The next Jules instance should:
    1.  **Gather Detailed Requirements for "Concept Task":** Actively engage with the user to obtain a detailed understanding of the current "Concept task," its goals, any existing materials (potentially in `Inputs/concept/inputs`).
    2.  Collaborate with the user to define the three (approx.) sub-tasks for the "Concept task."
    3.  In parallel, analyze how the requirements of these sub-tasks might necessitate extensions to the existing component concepts (add-on, util, process) or the introduction of new component types/paradigms within the MPS framework.
    4.  Plan and implement both the "Concept task" restructuring and the identified framework component extensions, using the former as a practical test case for the latter.
---
**Session Summary - 2025-06-03**
**Instance:** Jules
**User Feedback/Requests Addressed:**
- User directed a new phase of MPS development focusing on two major initiatives:
    1.  Abstracting the existing `build_product_specs_process` add-on into a more generic `create_research_report` add-on.
    2.  Introducing a new "promptApp" concept to allow users to define and execute multi-stage workflows composed of underlying MPS components.
- Clarified requirements for `create_research_report` parameters, including separate controls for `ITERATIVE_OUTPUT_DEPTH` (D0-D3 style) and `REFERENCE_FOLLOWING_DEPTH` (document link traversal).
- Confirmed that plain text input formats (.txt, .md, web text) are the initial target for document processing.
- Decided that sequential tasks within a `promptApp` will be handled by user re-invocation of new AI instances for each task, simplifying initial framework orchestration.

**Summary of Approved Plan:**
The overall plan involves:
1.  Developing the `create_research_report` add-on (now complete).
2.  Defining the "promptApp" structure and manifest file format.
3.  Updating `Core_Planning_Instructions.txt` and `Master_Prompt_Segment.txt` to support `promptApp` discovery, selection, and execution.
4.  Creating an initial structural example of a `promptApp` (`CompliancePLM`).
5.  Retiring the `build_product_specs_process` add-on files.
6.  Updating all documentation.
7.  Testing the new functionalities.

**Summary of Changes Made This Session (to implement Step 1 of the plan):**
-   **Created `prompts/add_ons/create_research_report/` directory.**
-   **Created `prompts/add_ons/create_research_report/USER_create_research_report_CONFIG.txt`:**
    *   Defined parameters: `PROPOSED_TABLE_OF_CONTENTS`, `GUIDELINES_DOC_PATHS`, `ADDITIONAL_WEB_LINKS_FILE`, `EXEMPLAR_DOCS`, `GENERAL_INPUT_DOCS`, `PRIMARY_INPUT_DOC_PATH`, `ITERATIVE_OUTPUT_DEPTH`, `REFERENCE_FOLLOWING_DEPTH`, `PREVIOUS_ITERATION_OUTPUT_PATH`, `REPORT_BASENAME`, `REPORT_OUTPUT_PATH`.
    *   Included full metadata (Requirement, DefaultType, Description, Default) for each.
-   **Created `prompts/add_ons/create_research_report/create_research_report.txt`:**
    *   Adapted core logic from the former `build_product_specs_process.txt`.
    *   Updated to use the new parameter set for generic report generation.
    *   Removed product-specification-specific and detailed codebase review logic.
    *   Maintained the 6-phase structure and parameter resolution mechanisms.
-   **Updated `prompts/add_ons/available_addons_manifest.md`:**
    *   Added entry for `create_research_report`.
    *   Removed entry for `build_product_specs_process`.

**State of Deliverables:**
- The `create_research_report` add-on (v0.1) is developed and its configuration file is in place.
- The add-on manifest is updated.
- The `build_product_specs_process` add-on is effectively replaced by this new add-on (physical file deletion is pending a later plan step).
- Plan step 1 is complete.

**Next Steps (This Jules instance/session):**
- Proceed to Plan Step 2: "Define 'promptApp' Structure and Manifest." This involves designing the manifest file format that will define how `promptApps` (like the `CompliancePLM` example) are structured and how their tasks map to MPS components.
---
**Session Summary - 2025-06-03 (Continued)**
**Instance:** Jules
**User Feedback/Requests Addressed (Post Initial 2025-06-03 Summary):**
- User confirmed a critical change in component resolution strategy for `promptApps`: the search order should be "app-specific first" (flat file, then folder style within app), followed by global components (add-ons, then utils). This was a change from the initially implemented "globals first" approach.

**Summary of Approved Plan (Revised Portion):**
The plan was revised to implement and document this new search order:
1.  Revise `Core_Planning_Instructions.txt` for the new "app-specific first" search order. (Completed)
2.  Update `MPS_Usage_Guide.md` to reflect this new search order. (Completed)
3.  Create test assets and conceptually outline tests for the new search order. (Completed)
4.  Update `HANDOFF_NOTES.md` and submit. (This step)

**Summary of Changes Made This Session (Implementing Revised Plan):**
-   **Modified `prompts/iep/Core_Planning_Instructions.txt`:**
    *   Updated the component resolution logic in Section II.A.1.a.viii to implement the "app-specific first" search order:
        1.  App-Specific (flat file): `prompts/apps/[Selected_PromptAppName]/[componentName].txt`
        2.  App-Specific (folder style): `prompts/apps/[Selected_PromptAppName]/[componentName]/[componentName].txt`
        3.  Global Add-on: `prompts/add_ons/[componentName]/[componentName].txt`
        4.  Global Util: `prompts/util/[componentName]/[componentName].txt`
-   **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`:**
    *   Modified Section 3.B.2.i ("Component Resolution Search Order") and Section 4 ("Developing New promptApps") to accurately document the new "app-specific first" search order.
-   **Created Test Assets for Search Order Verification:**
    *   Global add-on: `prompts/add_ons/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   App-specific component (folder style): `prompts/apps/CompliancePLM/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   Updated `prompts/add_ons/available_addons_manifest.md` for the global test component.
    *   Updated `prompts/apps/CompliancePLM/CompliancePLM_manifest.json` to include a task "Test Component Resolution" that calls `test_resolver_component`.

**State of Deliverables:**
- Core framework logic (`Core_Planning_Instructions.txt`) now correctly implements "app-specific first" component resolution for `promptApps`.
- Documentation (`MPS_Usage_Guide.md`) accurately reflects this new search order.
- Test assets are in place to verify this specific functionality.
- The broader `promptApp` and `create_research_report` add-on implementation (from previous steps in this session) is complete.
- The system is ready for user testing of the `promptApp` functionality, especially the component search order.

**Next Steps (User):**
- Thoroughly test the `promptApp` system, focusing on:
    - Invoking the "Test Component Resolution" task within `CompliancePLM` to ensure the app-specific `test_resolver_component` is used.
    - Invoking tasks that use global components (like "Product Specification" using `create_research_report` in `CompliancePLM`) to ensure they are correctly found.
    - Testing the iteration flow and re-invocation prompts for iterable tasks.
---

## MPS Performance Feedback Log

This section logs specific feedback on the performance, clarity, and effectiveness of the Master Prompt Segment (MPS) versions and the artifacts they help generate.

---
**Feedback Entry - 2023-11-02**
**Source of Feedback:** Initial setup of this feedback log section during MPS feature design.
**MPS Version Referenced:** Relevant for `prompts/Master_Prompt_Segment.txt` (v0.3.2 and future versions).
**Context/Scenario:** Establishing the feedback mechanism as per design discussions for feature "Feedback Loop for MPS Refinement".
**Observation/Issue:** This log is intended to capture issues like:
    - Ambiguities in any version of `Master_Prompt_Segment.md` that lead to incorrect or suboptimal behavior by a Planning AI.
    - Problems encountered by Task AIs due to the structure or content of prompts generated based on an MPS version.
    - User difficulties in understanding or using the MPS, its generated `00_task_launch_plan.md`, or other artifacts.
    - Success stories where a particular MPS instruction proved very effective.
**Suggestion for MPS Refinement (if any):** N/A
---
*(Future entries will be appended below this line)*
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request to Jules instance for refactoring).
**MPS Version Referenced:** `prompts/Master_Prompt_Segment.txt` (approx. v0.3.2, prior to modularization) and the new modularized version (post this session's changes).
**Context/Scenario:** User identified that the `Master_Prompt_Segment.txt` was becoming very long and dense, making it difficult to read, manage, and maintain.
**Observation/Issue:** The monolithic nature of `Master_Prompt_Segment.txt` was impacting usability and the ease of making targeted changes to core planning instructions without affecting user-configurable sections.
**Suggestion for MPS Refinement (Implemented):** To improve readability and maintainability, the core planning AI instructions were extracted from `Master_Prompt_Segment.txt` and placed into a new, dedicated file: `prompts/iep/Core_Planning_Instructions.txt`. The `Master_Prompt_Segment.txt` now includes these instructions using an `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive. This separation is intended to make both the user-facing configuration parts of the MPS and the core instructional logic easier to manage.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request and illustrative example: `[ ] task_spawning_addon.txt` to Jules instance).
**MPS Version Referenced:** The modularized MPS version (where `Core_Planning_Instructions.txt` was already separate) and the current version implementing optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User desired to further enhance the framework's modularity by making the entire project planning and task generation process an optional component, selectable by the user, rather than an inherent, non-optional function of the MPS.
**Observation/Issue:** While `Core_Planning_Instructions.txt` was already separated, it still implicitly mandated the task spawning workflow. The framework lacked the flexibility to be used for other purposes without invoking this primary planning behavior.
**Suggestion for MPS Refinement (Implemented):** The user's suggestion to make task spawning an optional add-on was implemented.
    - The core logic for project planning, TLP generation, and task prompt creation was moved from `prompts/iep/Core_Planning_Instructions.txt` to a new file: `prompts/add_ons/task_spawning_addon.txt`.
    - `prompts/iep/Core_Planning_Instructions.txt` was updated to include conditional logic. It now checks if `task_spawning_addon.txt` is selected by the user in `[[USER_ADDON_SELECTION]]` (within `Master_Prompt_Segment.txt`).
    - If selected, the `task_spawning_addon.txt` is executed.
    - If not selected, the AI is instructed to acknowledge this, skip default plan generation, and process any other selected add-ons.
    - This change makes the MPS framework significantly more flexible, allowing users to engage the Planning AI for potentially different primary functions by selecting different combinations of add-ons, or by using it with only utility add-ons.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifying questions and specific directive regarding `task_spawning_addon.txt` inheritance and add-on ordering).
**MPS Version Referenced:** The MPS version with optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User inquired about how multiple add-ons interact, particularly how `task_spawning_addon.txt` would affect other add-ons, and then specifically requested that `task_spawning_addon.txt` should not be inherited by the Task AIs it generates. User also sought clarity on the importance of add-on ordering in the selection block.
**Observation/Issue:**
    1. The previous add-on processing logic could have resulted in `task_spawning_addon.txt` (intended for the Planning AI) being appended to Task AI prompts, leading to overly verbose prompts and potential for unintended behavior.
    2. The impact of add-on order in `[[USER_ADDON_SELECTION]]` on the final content of Task AI prompts was not explicitly documented, potentially leading to user confusion.
**Suggestion for MPS Refinement (Implemented):**
    - **Non-inheritance of `task_spawning_addon.txt`**: Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B) to create a filtered list (`Inheritable_Addons_Content_Ordered_List`) of add-on contents for Task AI prompt inheritance, which explicitly excludes the content of `task_spawning_addon.txt`. This ensures the Planning AI uses `task_spawning_addon.txt` for its process, but Task AIs receive cleaner, more focused prompts.
    - **Clarified Add-on Ordering**: Updated `prompts/Master_Prompt_Segment.txt` (comments in `[[USER_ADDON_SELECTION]]`) and `framework_dev_docs/guides/MPS_Usage_Guide.md` to explain that the order of inheritable add-ons is preserved when `task_spawning_addon.txt` appends them to task prompts. Also recommended listing `task_spawning_addon.txt` last for visual clarity if used.
    - These refinements improve the safety and predictability of add-on interactions and provide users with better control and understanding of how their selections affect Task AI prompts.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (initial idea for process automation, then specific refinements for primary input and reference depth for product specs).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** User expressed a need to use the MPS framework for more than just task spawning, specifically for a structured process to generate product specification documents from various inputs. Later refinements included requests for more control over the research phase of this process.
**Observation/Issue:** The initial MPS framework was primarily geared towards task spawning. A structured approach was needed to define and execute other complex, multi-step AI-driven processes. For the product specs process,
        *   Added `PRODUCT_SPECS_PRIMARY_INPUT_FILE`: An optional path to a specific document. The AI is instructed to focus its research by analyzing at least every numbered heading in this document. If not provided, the AI selects the most comprehensive input document for this role.
        *   Added `PRODUCT_SPECS_REF_DEPTH`: An optional integer (0-2, default 1) to control how many levels deep the AI will follow internal references within the provided document set.
    These enhancements make the product specification generation process more guided and configurable.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** Jules (proactive refinement during development of `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** The introduction of `build_product_specs_process.txt` as a new "primary directive" add-on (similar in role to `task_spawning_addon.txt` but with a different function) highlighted the need to generalize how the core framework selects and manages such add-ons.
**Observation/Issue:** The existing logic in `prompts/iep/Core_Planning_Instructions.txt` was somewhat hardcoded around `task_spawning_addon.txt` for determining the main execution path and for non-inheritance of add-on content into sub-prompts. This would not scale well if many different primary add-ons were developed.
**Suggestion for MPS Refinement (Implemented):**
    - Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B and II) to:
        - Define a list of `Known_Primary_Directive_Addons`.
        - Dynamically identify an `Active_Primary_Addon_Filename` by checking the user's selection against this known list, prioritizing the first one selected by the user if multiple are chosen. A warning is logged if multiple primary add-ons are selected.
        - Ensure that the `Inheritable_Addons_Content_Ordered_List` (used for appending to sub-prompts) correctly excludes the content of the currently `Active_Primary_Addon_Filename`, not just `task_spawning_addon.txt`.
    - This makes the core framework more robust and extensible, allowing new primary add-ons to be integrated more easily without requiring significant changes to the core conditional logic each time. It also handles user selection of multiple primary add-ons more gracefully.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (specific feedback on research methodology, codebase interaction rules like "level 0 only for codebases," and codebase review preferences for the `build_product_specs_process.txt` add-on).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on.
**Context/Scenario:** User provided detailed requirements to refine the initial `build_product_specs_process.txt` add-on to achieve a more structured, in-depth, and controllable research and content generation process, particularly focusing on how the AI should interact with a primary document and linked codebases.
**Observation/Issue:** The initial version of `build_product_specs_process.txt` had a more general research phase. The user required a more granular, heading-based research approach for the Primary Input Document, specific rules for how the AI should analyze linked codebases (including different levels of review depth/focus), and a strict "level 0 only" rule for code analysis to prevent unintended deep dives into code.
**Suggestion for MPS Refinement (Implemented):**
The `prompts/add_ons/build_product_specs_process.txt` was significantly updated to incorporate the "Level 0 Deep Research" methodology as per user requirements:
    - **New Configurations:** Introduced `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` in `Master_Prompt_Segment.txt` and documented them in `MPS_Usage_Guide.md`.
    - **Per-Heading Research Cycle:** The add-on's Phase 3 was restructured. It now includes an initial high-level review of the Primary Input Document. Then, for each major heading in that document, it performs a 5-step analysis: Heading Context Analysis, Supporting Docs Analysis, References & Codebase Analysis, Consolidated Research for the heading, and Iterative Content Contribution for that heading.
    - **Codebase Review Integration:** The "References & Codebase Analysis" step now explicitly uses the `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` ("complex", "cursory", "focused") and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` to guide its interaction with linked code.
    - **"Level 0 Code Analysis ONLY":** A strict rule was added, instructing the AI not to follow internal code references (like imports or function calls to other files) from any fetched code text. Its analysis is confined to the directly retrieved code content.
    - **Iterative Content Drafting:** Content for output documents is now drafted iteratively during the per-heading analysis and then assembled and finalized in later phases.
    This refined process provides a more systematic and controllable approach for the AI when generating product specifications.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifications and new requirements for iterative deepening for `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on incorporating "Level 0 Deep Research".
**Context/Scenario:** Expanding the iterative capabilities of `build_product_specs_process.txt` from an initial 0-2 level concept to a more detailed 0-3 level process with specific content goals for each level.
**Observation/Issue:** The initial iterative deepening plan (0-2 levels) for `build_product_specs_process.txt` needed more specific content goals for each level and an additional level for AI-driven insights to meet user's evolving requirements for content depth and sophistication.
**Suggestion for MPS Refinement (Implemented):**
The iterative deepening feature of `build_product_specs_process.txt` was expanded to support 0-3 levels, and associated configurations and documentation were updated:
    - **Configuration Updates (`Master_Prompt_Segment.txt`):** `PRODUCT_SPECS_ITERATION_DEPTH` comment updated to reflect the 0-3 range and specific goals:
        - `0`: Outline Generation (headings with single key idea paragraphs).
        - `1`: Basic Content (Bachelor's level, ~3 paras per D0 heading).
        - `2`: Expert Elaboration (~1 page per original D0 heading).
        - `3`: AI Unique Insights (~3 pages per original D0 heading, novel ideas).
    - **Add-on Logic (`build_product_specs_process.txt`):**
        - Phase 1 (Initialization) now validates depth (0-3) and `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` (required for D1-D3, critical error if missing). Output file suffixes `_D0` to `_D3` are determined.
        - Phase 2 (Input Processing) is conditional: D0 uses original inputs; D1-D3 use previous iteration's outputs as primary, with original inputs as reference.
        - Phase 3 (Content Generation) has distinct logic for each depth (0-3), detailing how to build upon previous work (for D1-D3) or generate outlines (D0) according to the specified content goals for each level.
    - **Documentation (`MPS_Usage_Guide.md`):** Updated to detail the 0-3 levels, their specific purposes, and the user workflow for managing iterative runs and output folders.
    This provides a more granular, powerful, and well-defined iterative workflow for the `build_product_specs_process.txt` add-on.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (vision for enhanced component modularity via "folder-per-app" architecture).
**MPS Version Referenced:** Current version with multiple add-ons and utils.
**Context/Scenario:** As the number of components (add-ons, utils, and potentially IPC handlers) grows, a flat file structure in their respective directories (`prompts/add_ons/`, `prompts/util/`) becomes less organized and limits the ability for components to have their own supporting files or sub-frameworks.
**Observation/Issue:** The previous flat-file structure for add-ons and utils was not ideal for scalability or for components that might require multiple associated files (e.g., templates, data snippets, complex sub-instructions).
**Suggestion for MPS Refinement (Implemented):**
The framework was refactored to a "folder-per-app" architecture for all components (add-ons, utils, and anticipated IPC handlers):
    - **Core Logic (`prompts/iep/Core_Planning_Instructions.txt`):** Updated to discover and load components based on a new directory structure.
        - It now expects component-specific parent paths like `User_Path_Addons_Dir`, `User_Path_Utils_Dir`, `User_Path_IPC_Dir` to be defined in `[[USER PATH CONFIGURATION]]`.
        - For each component "app" (e.g., `my_addon_app`), it looks for a subdirectory named `my_addon_app` within the relevant parent path.
        - The primary instruction file for the app must be located at `[parent_path]/my_addon_app/my_addon_app.txt`.
        - Internal references (e.g., `Known_Primary_Directive_Addon_Apps`, `Active_Primary_Addon_AppName`) now use these app/folder names.
    - **Component Migration:** All existing add-ons (`task_spawning_addon`, `task_resumption_addon`, `build_product_specs_process`) and utils (`pre_flight_check_user_plan`) were moved into their respective new subdirectories (e.g., `prompts/add_ons/task_spawning_addon/task_spawning_addon.txt`).
    - **New `prompts/ipc/` directory:** Created with a `.gitkeep` file for future IPC components.
    - **Documentation Updates:**
        - `prompts/add_ons/available_addons_manifest.md`: Updated to list add-ons by their app/folder name and describe the new structure.
        - `framework_dev_docs/guides/MPS_Usage_Guide.md`: Comprehensively updated to explain the "folder-per-app" architecture, how users should set up paths, how components are selected, and how developers should structure new components.
    This architectural change significantly improves modularity, organization, and the potential for more complex, self-contained components within the MPS framework.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (desire to simplify MPS, new way to handle app-specific data, AI assistance for missing/default configs, specific format for detecting edits).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment and Core Planning Instructions).
**Context/Scenario:** This feedback entry summarizes a series of user requests and design discussions aimed at a comprehensive overhaul of the MPS framework's configuration system and a significant enhancement of the `build_product_specs_process` add-on's iterative capabilities.
**Observation/Issue:**
    1.  The global `[[USER PATH CONFIGURATION]]` in `Master_Prompt_Segment.txt` was becoming unwieldy; app-specific configs were mixed with global ones; parameter precedence and user edit detection were not formalized.
    2.  A more structured and discoverable way to manage parameters for individual add-on apps was needed, including clear defaults and user override mechanisms.
    3.  Users needed assistance in setting mandatory parameters, especially those with placeholder defaults.
**Suggestion for MPS Refinement (Implemented):**
A comprehensive refactoring of the configuration system was performed:
    1.  **Fixed Global System Paths:** Core component discovery paths (for add-ons, utils, IEP, IPC) are now hardcoded in `Core_Planning_Instructions.txt`, simplifying user setup.
    2.  **`[[USER PATH CONFIGURATION]]` Removed from `Master_Prompt_Segment.txt`:** This block was deleted. General output paths (like `Main Iteration Folder`) and other process-specific parameters are now intended to be managed as parameters within the configuration system of the relevant app.
    3.  **App-Specific Configuration System:**
        *   **`USER_appName_CONFIG.txt` Standard:** Defined a standard format for these template/storage files within each app's folder. This includes `[[USER_CONFIG_FOR_appName]]` delimiters, and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), description with default (`# Description: ... Default: ...`), and the value line (`PARAM_NAME: [USER_VALUE_START]value[USER_VALUE_END]`).
        *   **Main Prompt Overrides:** Users can copy the content of a `USER_appName_CONFIG.txt` into their main spawn prompt to provide run-specific overrides.
        *   **Parameter Resolution Precedence:** Implemented in app logic (demonstrated in `build_product_specs_process.txt`): Main prompt block > User-edited `USER_appName_CONFIG.txt` > AcceptedDefault in `USER_appName_CONFIG.txt`.
        *   **AI-Assisted Updates:** If a `Required` parameter with a `PlaceholderDefault` remains unresolved, the app instructs the AI (Jules) to request the value from the user via chat. The AI then updates the on-disk `USER_appName_CONFIG.txt` with the new value and outputs the complete updated config block for the user to transfer to their main spawn prompt.
    4.  **Documentation:** All changes were reflected in `Master_Prompt_Segment.txt` (now v0.4.0), `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, and relevant add-on files (e.g., `build_product_specs_process/USER_build_product_specs_process_CONFIG.txt` and its primary instruction file).
    This new system offers improved modularity, clarity in parameter management, and a better user experience through AI-assisted configuration.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Accomplishments This Session (Finalizing Configuration System & Preparing for "Concept Task" Handoff):**
This session concluded the major refactoring of the MPS framework's configuration system and component architecture. It also prepared for the next phase of work focusing on the "Concept task."

*   **Configuration System Refactoring (Completion & Documentation):**
    *   **Standard for `USER_appName_CONFIG.txt`:** Formalized the standard for these files, including app-specific delimiters (`[[USER_CONFIG_FOR_appName]]`), and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), descriptions, defaults, and `[USER_VALUE_START]...[USER_VALUE_END]` markers. An illustrative example (`USER_exampleApp_CONFIG.txt`) was created as part of this standard definition.
    *   **Applied Standard:** The `build_product_specs_process` add-on's `USER_build_product_specs_process_CONFIG.txt` was updated to fully adhere to this new standard, with all its parameters classified.
    *   **Refined App Logic (`build_product_specs_process.txt` v0.4):** Its parameter resolution logic in Phase 1 was enhanced to parse the new metadata from `USER_..._CONFIG.txt`. The AI-assisted update mechanism (asking user via chat) now correctly triggers only for "Required" parameters with "PlaceholderDefault" values that haven't been overridden. A final validation step ensures all "Required" parameters have valid values before proceeding, halting with an error if not.
    *   **Streamlined `Master_Prompt_Segment.txt` (v0.4.0):** The global `[[USER PATH CONFIGURATION]]` block was definitively removed. Instructions in `[[USER_ADDON_SELECTION]]` were updated to guide users on app-specific configurations via `[[USER_CONFIG_FOR_appName]]` blocks in their main spawn prompt.
    *   **Consolidated `MPS_Usage_Guide.md`:** The guide was thoroughly reviewed and updated to provide a comprehensive and consistent explanation of the new configuration system. This includes: fixed global system paths, the role and format of `USER_appName_CONFIG.txt` files, the user workflow for app configuration (using main prompt blocks or editing `USER_...CONFIG.txt`), the AI's parameter resolution precedence, and how AI-assisted updates work. The "folder-per-app" architecture is also clearly documented.

*   **Next Area of Work (User Directed - For Next Jules Instance):**
    *   The user has directed focus towards refining an existing "Concept task."
    *   This will involve analyzing the "Concept task" and splitting it into approximately three sub-tasks.
    *   A key aspect of this work will be to use the "Concept task" sub-tasks as a practical use case for **exploring and implementing extensions to the 'add-on, util, and process' component concepts** within the MPS framework.
    *   The next Jules instance is tasked with initiating this by requesting detailed requirements from the user regarding the "Concept task" (current state, materials possibly in `Inputs/concept/inputs`, ideas for sub-tasks) and their initial thoughts on evolving the component model.

**State of Deliverables:**
The comprehensive refactoring of the MPS configuration system is complete. This includes the "folder-per-app" architecture, fixed system paths for component discovery, and the new app-specific configuration mechanism using `USER_appName_CONFIG.txt` files and `[[USER_CONFIG_FOR_appName]]` blocks, featuring AI-assisted updates. All core prompts and documentation (`Master_Prompt_Segment.txt`, `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`, and relevant add-ons like `build_product_specs_process`) have been updated to reflect these changes. The framework is significantly more modular, user-friendly, and robust in its configuration management. No new code or prompt changes were made in this specific summarization step. The system is ready for handoff to the next Jules instance to begin work on the "Concept task" and component model evolution.
---
---
**Feedback Entry Date:** 2025-06-02
**Source of Feedback:** User (new directive for next area of work following completion of major configuration system and architectural refactoring).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment) and related component architecture (folder-per-app, `USER_appName_CONFIG.txt` standard, etc.).
**Context/Scenario:** Planning the next development cycle for the MPS framework after successfully implementing a new configuration system and "folder-per-app" architecture.
**Observation/Issue:**
    1.  An existing internal "Concept task" (details to be provided by the user to the next Jules instance, potentially located in `Inputs/concept/inputs`) is currently monolithic and needs to be broken down into more manageable sub-tasks (approximately three).
    2.  The process of decomposing and implementing these "Concept task" sub-tasks is expected to reveal opportunities or requirements for evolving the framework's current component definitions (add-on, util, process) or potentially introducing new component types or interaction paradigms.
**Suggestion (For Next Jules Instance):**
The next Jules instance should:
    1.  **Gather Detailed Requirements for "Concept Task":** Actively engage with the user to obtain a detailed understanding of the current "Concept task," its goals, any existing materials (potentially in `Inputs/concept/inputs`).
    2.  Collaborate with the user to define the three (approx.) sub-tasks for the "Concept task."
    3.  In parallel, analyze how the requirements of these sub-tasks might necessitate extensions to the existing component concepts (add-on, util, process) or the introduction of new component types/paradigms within the MPS framework.
    4.  Plan and implement both the "Concept task" restructuring and the identified framework component extensions, using the former as a practical test case for the latter.
---
**Session Summary - 2025-06-03**
**Instance:** Jules
**User Feedback/Requests Addressed:**
- User directed a new phase of MPS development focusing on two major initiatives:
    1.  Abstracting the existing `build_product_specs_process` add-on into a more generic `create_research_report` add-on.
    2.  Introducing a new "promptApp" concept to allow users to define and execute multi-stage workflows composed of underlying MPS components.
- Clarified requirements for `create_research_report` parameters, including separate controls for `ITERATIVE_OUTPUT_DEPTH` (D0-D3 style) and `REFERENCE_FOLLOWING_DEPTH` (document link traversal).
- Confirmed that plain text input formats (.txt, .md, web text) are the initial target for document processing.
- Decided that sequential tasks within a `promptApp` will be handled by user re-invocation of new AI instances for each task, simplifying initial framework orchestration.

**Summary of Approved Plan:**
The overall plan involves:
1.  Developing the `create_research_report` add-on (now complete).
2.  Defining the "promptApp" structure and manifest file format.
3.  Updating `Core_Planning_Instructions.txt` and `Master_Prompt_Segment.txt` to support `promptApp` discovery, selection, and execution.
4.  Creating an initial structural example of a `promptApp` (`CompliancePLM`).
5.  Retiring the `build_product_specs_process` add-on files.
6.  Updating all documentation.
7.  Testing the new functionalities.

**Summary of Changes Made This Session (to implement Step 1 of the plan):**
-   **Created `prompts/add_ons/create_research_report/` directory.**
-   **Created `prompts/add_ons/create_research_report/USER_create_research_report_CONFIG.txt`:**
    *   Defined parameters: `PROPOSED_TABLE_OF_CONTENTS`, `GUIDELINES_DOC_PATHS`, `ADDITIONAL_WEB_LINKS_FILE`, `EXEMPLAR_DOCS`, `GENERAL_INPUT_DOCS`, `PRIMARY_INPUT_DOC_PATH`, `ITERATIVE_OUTPUT_DEPTH`, `REFERENCE_FOLLOWING_DEPTH`, `PREVIOUS_ITERATION_OUTPUT_PATH`, `REPORT_BASENAME`, `REPORT_OUTPUT_PATH`.
    *   Included full metadata (Requirement, DefaultType, Description, Default) for each.
-   **Created `prompts/add_ons/create_research_report/create_research_report.txt`:**
    *   Adapted core logic from the former `build_product_specs_process.txt`.
    *   Updated to use the new parameter set for generic report generation.
    *   Removed product-specification-specific and detailed codebase review logic.
    *   Maintained the 6-phase structure and parameter resolution mechanisms.
-   **Updated `prompts/add_ons/available_addons_manifest.md`:**
    *   Added entry for `create_research_report`.
    *   Removed entry for `build_product_specs_process`.

**State of Deliverables:**
- The `create_research_report` add-on (v0.1) is developed and its configuration file is in place.
- The add-on manifest is updated.
- The `build_product_specs_process` add-on is effectively replaced by this new add-on (physical file deletion is pending a later plan step).
- Plan step 1 is complete.

**Next Steps (This Jules instance/session):**
- Proceed to Plan Step 2: "Define 'promptApp' Structure and Manifest." This involves designing the manifest file format that will define how `promptApps` (like the `CompliancePLM` example) are structured and how their tasks map to MPS components.
---
**Session Summary - 2025-06-03 (Continued)**
**Instance:** Jules
**User Feedback/Requests Addressed (Post Initial 2025-06-03 Summary):**
- User confirmed a critical change in component resolution strategy for `promptApps`: the search order should be "app-specific first" (flat file, then folder style within app), followed by global components (add-ons, then utils). This was a change from the initially implemented "globals first" approach.

**Summary of Approved Plan (Revised Portion):**
The plan was revised to implement and document this new search order:
1.  Revise `Core_Planning_Instructions.txt` for the new "app-specific first" search order. (Completed)
2.  Update `MPS_Usage_Guide.md` to reflect this new search order. (Completed)
3.  Create test assets and conceptually outline tests for the new search order. (Completed)
4.  Update `HANDOFF_NOTES.md` and submit. (This step)

**Summary of Changes Made This Session (Implementing Revised Plan):**
-   **Modified `prompts/iep/Core_Planning_Instructions.txt`:**
    *   Updated the component resolution logic in Section II.A.1.a.viii to implement the "app-specific first" search order:
        1.  App-Specific (flat file): `prompts/apps/[Selected_PromptAppName]/[componentName].txt`
        2.  App-Specific (folder style): `prompts/apps/[Selected_PromptAppName]/[componentName]/[componentName].txt`
        3.  Global Add-on: `prompts/add_ons/[componentName]/[componentName].txt`
        4.  Global Util: `prompts/util/[componentName]/[componentName].txt`
-   **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`:**
    *   Modified Section 3.B.2.i ("Component Resolution Search Order") and Section 4 ("Developing New promptApps") to accurately document the new "app-specific first" search order.
-   **Created Test Assets for Search Order Verification:**
    *   Global add-on: `prompts/add_ons/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   App-specific component (folder style): `prompts/apps/CompliancePLM/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   Updated `prompts/add_ons/available_addons_manifest.md` for the global test component.
    *   Updated `prompts/apps/CompliancePLM/CompliancePLM_manifest.json` to include a task "Test Component Resolution" that calls `test_resolver_component`.

**State of Deliverables:**
- Core framework logic (`Core_Planning_Instructions.txt`) now correctly implements "app-specific first" component resolution for `promptApps`.
- Documentation (`MPS_Usage_Guide.md`) accurately reflects this new search order.
- Test assets are in place to verify this specific functionality.
- The broader `promptApp` and `create_research_report` add-on implementation (from previous steps in this session) is complete.
- The system is ready for user testing of the `promptApp` functionality, especially the component search order.

**Next Steps (User):**
- Thoroughly test the `promptApp` system, focusing on:
    - Invoking the "Test Component Resolution" task within `CompliancePLM` to ensure the app-specific `test_resolver_component` is used.
    - Invoking tasks that use global components (like "Product Specification" using `create_research_report` in `CompliancePLM`) to ensure they are correctly found.
    - Testing the iteration flow and re-invocation prompts for iterable tasks.
---

## MPS Performance Feedback Log

This section logs specific feedback on the performance, clarity, and effectiveness of the Master Prompt Segment (MPS) versions and the artifacts they help generate.

---
**Feedback Entry - 2023-11-02**
**Source of Feedback:** Initial setup of this feedback log section during MPS feature design.
**MPS Version Referenced:** Relevant for `prompts/Master_Prompt_Segment.txt` (v0.3.2 and future versions).
**Context/Scenario:** Establishing the feedback mechanism as per design discussions for feature "Feedback Loop for MPS Refinement".
**Observation/Issue:** This log is intended to capture issues like:
    - Ambiguities in any version of `Master_Prompt_Segment.md` that lead to incorrect or suboptimal behavior by a Planning AI.
    - Problems encountered by Task AIs due to the structure or content of prompts generated based on an MPS version.
    - User difficulties in understanding or using the MPS, its generated `00_task_launch_plan.md`, or other artifacts.
    - Success stories where a particular MPS instruction proved very effective.
**Suggestion for MPS Refinement (if any):** N/A
---
*(Future entries will be appended below this line)*
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request to Jules instance for refactoring).
**MPS Version Referenced:** `prompts/Master_Prompt_Segment.txt` (approx. v0.3.2, prior to modularization) and the new modularized version (post this session's changes).
**Context/Scenario:** User identified that the `Master_Prompt_Segment.txt` was becoming very long and dense, making it difficult to read, manage, and maintain.
**Observation/Issue:** The monolithic nature of `Master_Prompt_Segment.txt` was impacting usability and the ease of making targeted changes to core planning instructions without affecting user-configurable sections.
**Suggestion for MPS Refinement (Implemented):** To improve readability and maintainability, the core planning AI instructions were extracted from `Master_Prompt_Segment.txt` and placed into a new, dedicated file: `prompts/iep/Core_Planning_Instructions.txt`. The `Master_Prompt_Segment.txt` now includes these instructions using an `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive. This separation is intended to make both the user-facing configuration parts of the MPS and the core instructional logic easier to manage.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request and illustrative example: `[ ] task_spawning_addon.txt` to Jules instance).
**MPS Version Referenced:** The modularized MPS version (where `Core_Planning_Instructions.txt` was already separate) and the current version implementing optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User desired to further enhance the framework's modularity by making the entire project planning and task generation process an optional component, selectable by the user, rather than an inherent, non-optional function of the MPS.
**Observation/Issue:** While `Core_Planning_Instructions.txt` was already separated, it still implicitly mandated the task spawning workflow. The framework lacked the flexibility to be used for other purposes without invoking this primary planning behavior.
**Suggestion for MPS Refinement (Implemented):** The user's suggestion to make task spawning an optional add-on was implemented.
    - The core logic for project planning, TLP generation, and task prompt creation was moved from `prompts/iep/Core_Planning_Instructions.txt` to a new file: `prompts/add_ons/task_spawning_addon.txt`.
    - `prompts/iep/Core_Planning_Instructions.txt` was updated to include conditional logic. It now checks if `task_spawning_addon.txt` is selected by the user in `[[USER_ADDON_SELECTION]]` (within `Master_Prompt_Segment.txt`).
    - If selected, the `task_spawning_addon.txt` is executed.
    - If not selected, the AI is instructed to acknowledge this, skip default plan generation, and process any other selected add-ons.
    - This change makes the MPS framework significantly more flexible, allowing users to engage the Planning AI for potentially different primary functions by selecting different combinations of add-ons, or by using it with only utility add-ons.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifying questions and specific directive regarding `task_spawning_addon.txt` inheritance and add-on ordering).
**MPS Version Referenced:** The MPS version with optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User inquired about how multiple add-ons interact, particularly how `task_spawning_addon.txt` would affect other add-ons, and then specifically requested that `task_spawning_addon.txt` should not be inherited by the Task AIs it generates. User also sought clarity on the importance of add-on ordering in the selection block.
**Observation/Issue:**
    1. The previous add-on processing logic could have resulted in `task_spawning_addon.txt` (intended for the Planning AI) being appended to Task AI prompts, leading to overly verbose prompts and potential for unintended behavior.
    2. The impact of add-on order in `[[USER_ADDON_SELECTION]]` on the final content of Task AI prompts was not explicitly documented, potentially leading to user confusion.
**Suggestion for MPS Refinement (Implemented):**
    - **Non-inheritance of `task_spawning_addon.txt`**: Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B) to create a filtered list (`Inheritable_Addons_Content_Ordered_List`) of add-on contents for Task AI prompt inheritance, which explicitly excludes the content of `task_spawning_addon.txt`. This ensures the Planning AI uses `task_spawning_addon.txt` for its process, but Task AIs receive cleaner, more focused prompts.
    - **Clarified Add-on Ordering**: Updated `prompts/Master_Prompt_Segment.txt` (comments in `[[USER_ADDON_SELECTION]]`) and `framework_dev_docs/guides/MPS_Usage_Guide.md` to explain that the order of inheritable add-ons is preserved when `task_spawning_addon.txt` appends them to task prompts. Also recommended listing `task_spawning_addon.txt` last for visual clarity if used.
    - These refinements improve the safety and predictability of add-on interactions and provide users with better control and understanding of how their selections affect Task AI prompts.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (initial idea for process automation, then specific refinements for primary input and reference depth for product specs).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** User expressed a need to use the MPS framework for more than just task spawning, specifically for a structured process to generate product specification documents from various inputs. Later refinements included requests for more control over the research phase of this process.
**Observation/Issue:** The initial MPS framework was primarily geared towards task spawning. A structured approach was needed to define and execute other complex, multi-step AI-driven processes. For the product specs process,
        *   Added `PRODUCT_SPECS_PRIMARY_INPUT_FILE`: An optional path to a specific document. The AI is instructed to focus its research by analyzing at least every numbered heading in this document. If not provided, the AI selects the most comprehensive input document for this role.
        *   Added `PRODUCT_SPECS_REF_DEPTH`: An optional integer (0-2, default 1) to control how many levels deep the AI will follow internal references within the provided document set.
    These enhancements make the product specification generation process more guided and configurable.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** Jules (proactive refinement during development of `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** The introduction of `build_product_specs_process.txt` as a new "primary directive" add-on (similar in role to `task_spawning_addon.txt` but with a different function) highlighted the need to generalize how the core framework selects and manages such add-ons.
**Observation/Issue:** The existing logic in `prompts/iep/Core_Planning_Instructions.txt` was somewhat hardcoded around `task_spawning_addon.txt` for determining the main execution path and for non-inheritance of add-on content into sub-prompts. This would not scale well if many different primary add-ons were developed.
**Suggestion for MPS Refinement (Implemented):**
    - Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B and II) to:
        - Define a list of `Known_Primary_Directive_Addons`.
        - Dynamically identify an `Active_Primary_Addon_Filename` by checking the user's selection against this known list, prioritizing the first one selected by the user if multiple are chosen. A warning is logged if multiple primary add-ons are selected.
        - Ensure that the `Inheritable_Addons_Content_Ordered_List` (used for appending to sub-prompts) correctly excludes the content of the currently `Active_Primary_Addon_Filename`, not just `task_spawning_addon.txt`.
    - This makes the core framework more robust and extensible, allowing new primary add-ons to be integrated more easily without requiring significant changes to the core conditional logic each time. It also handles user selection of multiple primary add-ons more gracefully.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (specific feedback on research methodology, codebase interaction rules like "level 0 only for codebases," and codebase review preferences for the `build_product_specs_process.txt` add-on).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on.
**Context/Scenario:** User provided detailed requirements to refine the initial `build_product_specs_process.txt` add-on to achieve a more structured, in-depth, and controllable research and content generation process, particularly focusing on how the AI should interact with a primary document and linked codebases.
**Observation/Issue:** The initial version of `build_product_specs_process.txt` had a more general research phase. The user required a more granular, heading-based research approach for the Primary Input Document, specific rules for how the AI should analyze linked codebases (including different levels of review depth/focus), and a strict "level 0 only" rule for code analysis to prevent unintended deep dives into code.
**Suggestion for MPS Refinement (Implemented):**
The `prompts/add_ons/build_product_specs_process.txt` was significantly updated to incorporate the "Level 0 Deep Research" methodology as per user requirements:
    - **New Configurations:** Introduced `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` in `Master_Prompt_Segment.txt` and documented them in `MPS_Usage_Guide.md`.
    - **Per-Heading Research Cycle:** The add-on's Phase 3 was restructured. It now includes an initial high-level review of the Primary Input Document. Then, for each major heading in that document, it performs a 5-step analysis: Heading Context Analysis, Supporting Docs Analysis, References & Codebase Analysis, Consolidated Research for the heading, and Iterative Content Contribution for that heading.
    - **Codebase Review Integration:** The "References & Codebase Analysis" step now explicitly uses the `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` ("complex", "cursory", "focused") and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` to guide its interaction with linked code.
    - **"Level 0 Code Analysis ONLY":** A strict rule was added, instructing the AI not to follow internal code references (like imports or function calls to other files) from any fetched code text. Its analysis is confined to the directly retrieved code content.
    - **Iterative Content Drafting:** Content for output documents is now drafted iteratively during the per-heading analysis and then assembled and finalized in later phases.
    This refined process provides a more systematic and controllable approach for the AI when generating product specifications.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifications and new requirements for iterative deepening for `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on incorporating "Level 0 Deep Research".
**Context/Scenario:** Expanding the iterative capabilities of `build_product_specs_process.txt` from an initial 0-2 level concept to a more detailed 0-3 level process with specific content goals for each level.
**Observation/Issue:** The initial iterative deepening plan (0-2 levels) for `build_product_specs_process.txt` needed more specific content goals for each level and an additional level for AI-driven insights to meet user's evolving requirements for content depth and sophistication.
**Suggestion for MPS Refinement (Implemented):**
The iterative deepening feature of `build_product_specs_process.txt` was expanded to support 0-3 levels, and associated configurations and documentation were updated:
    - **Configuration Updates (`Master_Prompt_Segment.txt`):** `PRODUCT_SPECS_ITERATION_DEPTH` comment updated to reflect the 0-3 range and specific goals:
        - `0`: Outline Generation (headings with single key idea paragraphs).
        - `1`: Basic Content (Bachelor's level, ~3 paras per D0 heading).
        - `2`: Expert Elaboration (~1 page per original D0 heading).
        - `3`: AI Unique Insights (~3 pages per original D0 heading, novel ideas).
    - **Add-on Logic (`build_product_specs_process.txt`):**
        - Phase 1 (Initialization) now validates depth (0-3) and `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` (required for D1-D3, critical error if missing). Output file suffixes `_D0` to `_D3` are determined.
        - Phase 2 (Input Processing) is conditional: D0 uses original inputs; D1-D3 use previous iteration's outputs as primary, with original inputs as reference.
        - Phase 3 (Content Generation) has distinct logic for each depth (0-3), detailing how to build upon previous work (for D1-D3) or generate outlines (D0) according to the specified content goals for each level.
    - **Documentation (`MPS_Usage_Guide.md`):** Updated to detail the 0-3 levels, their specific purposes, and the user workflow for managing iterative runs and output folders.
    This provides a more granular, powerful, and well-defined iterative workflow for the `build_product_specs_process.txt` add-on.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (vision for enhanced component modularity via "folder-per-app" architecture).
**MPS Version Referenced:** Current version with multiple add-ons and utils.
**Context/Scenario:** As the number of components (add-ons, utils, and potentially IPC handlers) grows, a flat file structure in their respective directories (`prompts/add_ons/`, `prompts/util/`) becomes less organized and limits the ability for components to have their own supporting files or sub-frameworks.
**Observation/Issue:** The previous flat-file structure for add-ons and utils was not ideal for scalability or for components that might require multiple associated files (e.g., templates, data snippets, complex sub-instructions).
**Suggestion for MPS Refinement (Implemented):**
The framework was refactored to a "folder-per-app" architecture for all components (add-ons, utils, and anticipated IPC handlers):
    - **Core Logic (`prompts/iep/Core_Planning_Instructions.txt`):** Updated to discover and load components based on a new directory structure.
        - It now expects component-specific parent paths like `User_Path_Addons_Dir`, `User_Path_Utils_Dir`, `User_Path_IPC_Dir` to be defined in `[[USER PATH CONFIGURATION]]`.
        - For each component "app" (e.g., `my_addon_app`), it looks for a subdirectory named `my_addon_app` within the relevant parent path.
        - The primary instruction file for the app must be located at `[parent_path]/my_addon_app/my_addon_app.txt`.
        - Internal references (e.g., `Known_Primary_Directive_Addon_Apps`, `Active_Primary_Addon_AppName`) now use these app/folder names.
    - **Component Migration:** All existing add-ons (`task_spawning_addon`, `task_resumption_addon`, `build_product_specs_process`) and utils (`pre_flight_check_user_plan`) were moved into their respective new subdirectories (e.g., `prompts/add_ons/task_spawning_addon/task_spawning_addon.txt`).
    - **New `prompts/ipc/` directory:** Created with a `.gitkeep` file for future IPC components.
    - **Documentation Updates:**
        - `prompts/add_ons/available_addons_manifest.md`: Updated to list add-ons by their app/folder name and describe the new structure.
        - `framework_dev_docs/guides/MPS_Usage_Guide.md`: Comprehensively updated to explain the "folder-per-app" architecture, how users should set up paths, how components are selected, and how developers should structure new components.
    This architectural change significantly improves modularity, organization, and the potential for more complex, self-contained components within the MPS framework.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (desire to simplify MPS, new way to handle app-specific data, AI assistance for missing/default configs, specific format for detecting edits).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment and Core Planning Instructions).
**Context/Scenario:** This feedback entry summarizes a series of user requests and design discussions aimed at a comprehensive overhaul of the MPS framework's configuration system and a significant enhancement of the `build_product_specs_process` add-on's iterative capabilities.
**Observation/Issue:**
    1.  The global `[[USER PATH CONFIGURATION]]` in `Master_Prompt_Segment.txt` was becoming unwieldy; app-specific configs were mixed with global ones; parameter precedence and user edit detection were not formalized.
    2.  A more structured and discoverable way to manage parameters for individual add-on apps was needed, including clear defaults and user override mechanisms.
    3.  Users needed assistance in setting mandatory parameters, especially those with placeholder defaults.
**Suggestion for MPS Refinement (Implemented):**
A comprehensive refactoring of the configuration system was performed:
    1.  **Fixed Global System Paths:** Core component discovery paths (for add-ons, utils, IEP, IPC) are now hardcoded in `Core_Planning_Instructions.txt`, simplifying user setup.
    2.  **`[[USER PATH CONFIGURATION]]` Removed from `Master_Prompt_Segment.txt`:** This block was deleted. General output paths (like `Main Iteration Folder`) and other process-specific parameters are now intended to be managed as parameters within the configuration system of the relevant app.
    3.  **App-Specific Configuration System:**
        *   **`USER_appName_CONFIG.txt` Standard:** Defined a standard format for these template/storage files within each app's folder. This includes `[[USER_CONFIG_FOR_appName]]` delimiters, and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), description with default (`# Description: ... Default: ...`), and the value line (`PARAM_NAME: [USER_VALUE_START]value[USER_VALUE_END]`).
        *   **Main Prompt Overrides:** Users can copy the content of a `USER_appName_CONFIG.txt` into their main spawn prompt to provide run-specific overrides.
        *   **Parameter Resolution Precedence:** Implemented in app logic (demonstrated in `build_product_specs_process.txt`): Main prompt block > User-edited `USER_appName_CONFIG.txt` > AcceptedDefault in `USER_appName_CONFIG.txt`.
        *   **AI-Assisted Updates:** If a `Required` parameter with a `PlaceholderDefault` remains unresolved, the app instructs the AI (Jules) to request the value from the user via chat. The AI then updates the on-disk `USER_appName_CONFIG.txt` with the new value and outputs the complete updated config block for the user to transfer to their main spawn prompt.
    4.  **Documentation:** All changes were reflected in `Master_Prompt_Segment.txt` (now v0.4.0), `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, and relevant add-on files (e.g., `build_product_specs_process/USER_build_product_specs_process_CONFIG.txt` and its primary instruction file).
    This new system offers improved modularity, clarity in parameter management, and a better user experience through AI-assisted configuration.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Accomplishments This Session (Finalizing Configuration System & Preparing for "Concept Task" Handoff):**
This session concluded the major refactoring of the MPS framework's configuration system and component architecture. It also prepared for the next phase of work focusing on the "Concept task."

*   **Configuration System Refactoring (Completion & Documentation):**
    *   **Standard for `USER_appName_CONFIG.txt`:** Formalized the standard for these files, including app-specific delimiters (`[[USER_CONFIG_FOR_appName]]`), and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), descriptions, defaults, and `[USER_VALUE_START]...[USER_VALUE_END]` markers. An illustrative example (`USER_exampleApp_CONFIG.txt`) was created as part of this standard definition.
    *   **Applied Standard:** The `build_product_specs_process` add-on's `USER_build_product_specs_process_CONFIG.txt` was updated to fully adhere to this new standard, with all its parameters classified.
    *   **Refined App Logic (`build_product_specs_process.txt` v0.4):** Its parameter resolution logic in Phase 1 was enhanced to parse the new metadata from `USER_..._CONFIG.txt`. The AI-assisted update mechanism (asking user via chat) now correctly triggers only for "Required" parameters with "PlaceholderDefault" values that haven't been overridden. A final validation step ensures all "Required" parameters have valid values before proceeding, halting with an error if not.
    *   **Streamlined `Master_Prompt_Segment.txt` (v0.4.0):** The global `[[USER PATH CONFIGURATION]]` block was definitively removed. Instructions in `[[USER_ADDON_SELECTION]]` were updated to guide users on app-specific configurations via `[[USER_CONFIG_FOR_appName]]` blocks in their main spawn prompt.
    *   **Consolidated `MPS_Usage_Guide.md`:** The guide was thoroughly reviewed and updated to provide a comprehensive and consistent explanation of the new configuration system. This includes: fixed global system paths, the role and format of `USER_appName_CONFIG.txt` files, the user workflow for app configuration (using main prompt blocks or editing `USER_...CONFIG.txt`), the AI's parameter resolution precedence, and how AI-assisted updates work. The "folder-per-app" architecture is also clearly documented.

*   **Next Area of Work (User Directed - For Next Jules Instance):**
    *   The user has directed focus towards refining an existing "Concept task."
    *   This will involve analyzing the "Concept task" and splitting it into approximately three sub-tasks.
    *   A key aspect of this work will be to use the "Concept task" sub-tasks as a practical use case for **exploring and implementing extensions to the 'add-on, util, and process' component concepts** within the MPS framework.
    *   The next Jules instance is tasked with initiating this by requesting detailed requirements from the user regarding the "Concept task" (current state, materials possibly in `Inputs/concept/inputs`, ideas for sub-tasks) and their initial thoughts on evolving the component model.

**State of Deliverables:**
The comprehensive refactoring of the MPS configuration system is complete. This includes the "folder-per-app" architecture, fixed system paths for component discovery, and the new app-specific configuration mechanism using `USER_appName_CONFIG.txt` files and `[[USER_CONFIG_FOR_appName]]` blocks, featuring AI-assisted updates. All core prompts and documentation (`Master_Prompt_Segment.txt`, `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`, and relevant add-ons like `build_product_specs_process`) have been updated to reflect these changes. The framework is significantly more modular, user-friendly, and robust in its configuration management. No new code or prompt changes were made in this specific summarization step. The system is ready for handoff to the next Jules instance to begin work on the "Concept task" and component model evolution.
---
---
**Feedback Entry Date:** 2025-06-02
**Source of Feedback:** User (new directive for next area of work following completion of major configuration system and architectural refactoring).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment) and related component architecture (folder-per-app, `USER_appName_CONFIG.txt` standard, etc.).
**Context/Scenario:** Planning the next development cycle for the MPS framework after successfully implementing a new configuration system and "folder-per-app" architecture.
**Observation/Issue:**
    1.  An existing internal "Concept task" (details to be provided by the user to the next Jules instance, potentially located in `Inputs/concept/inputs`) is currently monolithic and needs to be broken down into more manageable sub-tasks (approximately three).
    2.  The process of decomposing and implementing these "Concept task" sub-tasks is expected to reveal opportunities or requirements for evolving the framework's current component definitions (add-on, util, process) or potentially introducing new component types or interaction paradigms.
**Suggestion (For Next Jules Instance):**
The next Jules instance should:
    1.  **Gather Detailed Requirements for "Concept Task":** Actively engage with the user to obtain a detailed understanding of the current "Concept task," its goals, any existing materials (potentially in `Inputs/concept/inputs`).
    2.  Collaborate with the user to define the three (approx.) sub-tasks for the "Concept task."
    3.  In parallel, analyze how the requirements of these sub-tasks might necessitate extensions to the existing component concepts (add-on, util, process) or the introduction of new component types/paradigms within the MPS framework.
    4.  Plan and implement both the "Concept task" restructuring and the identified framework component extensions, using the former as a practical test case for the latter.
---
**Session Summary - 2025-06-03**
**Instance:** Jules
**User Feedback/Requests Addressed:**
- User directed a new phase of MPS development focusing on two major initiatives:
    1.  Abstracting the existing `build_product_specs_process` add-on into a more generic `create_research_report` add-on.
    2.  Introducing a new "promptApp" concept to allow users to define and execute multi-stage workflows composed of underlying MPS components.
- Clarified requirements for `create_research_report` parameters, including separate controls for `ITERATIVE_OUTPUT_DEPTH` (D0-D3 style) and `REFERENCE_FOLLOWING_DEPTH` (document link traversal).
- Confirmed that plain text input formats (.txt, .md, web text) are the initial target for document processing.
- Decided that sequential tasks within a `promptApp` will be handled by user re-invocation of new AI instances for each task, simplifying initial framework orchestration.

**Summary of Approved Plan:**
The overall plan involves:
1.  Developing the `create_research_report` add-on (now complete).
2.  Defining the "promptApp" structure and manifest file format.
3.  Updating `Core_Planning_Instructions.txt` and `Master_Prompt_Segment.txt` to support `promptApp` discovery, selection, and execution.
4.  Creating an initial structural example of a `promptApp` (`CompliancePLM`).
5.  Retiring the `build_product_specs_process` add-on files.
6.  Updating all documentation.
7.  Testing the new functionalities.

**Summary of Changes Made This Session (to implement Step 1 of the plan):**
-   **Created `prompts/add_ons/create_research_report/` directory.**
-   **Created `prompts/add_ons/create_research_report/USER_create_research_report_CONFIG.txt`:**
    *   Defined parameters: `PROPOSED_TABLE_OF_CONTENTS`, `GUIDELINES_DOC_PATHS`, `ADDITIONAL_WEB_LINKS_FILE`, `EXEMPLAR_DOCS`, `GENERAL_INPUT_DOCS`, `PRIMARY_INPUT_DOC_PATH`, `ITERATIVE_OUTPUT_DEPTH`, `REFERENCE_FOLLOWING_DEPTH`, `PREVIOUS_ITERATION_OUTPUT_PATH`, `REPORT_BASENAME`, `REPORT_OUTPUT_PATH`.
    *   Included full metadata (Requirement, DefaultType, Description, Default) for each.
-   **Created `prompts/add_ons/create_research_report/create_research_report.txt`:**
    *   Adapted core logic from the former `build_product_specs_process.txt`.
    *   Updated to use the new parameter set for generic report generation.
    *   Removed product-specification-specific and detailed codebase review logic.
    *   Maintained the 6-phase structure and parameter resolution mechanisms.
-   **Updated `prompts/add_ons/available_addons_manifest.md`:**
    *   Added entry for `create_research_report`.
    *   Removed entry for `build_product_specs_process`.

**State of Deliverables:**
- The `create_research_report` add-on (v0.1) is developed and its configuration file is in place.
- The add-on manifest is updated.
- The `build_product_specs_process` add-on is effectively replaced by this new add-on (physical file deletion is pending a later plan step).
- Plan step 1 is complete.

**Next Steps (This Jules instance/session):**
- Proceed to Plan Step 2: "Define 'promptApp' Structure and Manifest." This involves designing the manifest file format that will define how `promptApps` (like the `CompliancePLM` example) are structured and how their tasks map to MPS components.
---
**Session Summary - 2025-06-03 (Continued)**
**Instance:** Jules
**User Feedback/Requests Addressed (Post Initial 2025-06-03 Summary):**
- User confirmed a critical change in component resolution strategy for `promptApps`: the search order should be "app-specific first" (flat file, then folder style within app), followed by global components (add-ons, then utils). This was a change from the initially implemented "globals first" approach.

**Summary of Approved Plan (Revised Portion):**
The plan was revised to implement and document this new search order:
1.  Revise `Core_Planning_Instructions.txt` for the new "app-specific first" search order. (Completed)
2.  Update `MPS_Usage_Guide.md` to reflect this new search order. (Completed)
3.  Create test assets and conceptually outline tests for the new search order. (Completed)
4.  Update `HANDOFF_NOTES.md` and submit. (This step)

**Summary of Changes Made This Session (Implementing Revised Plan):**
-   **Modified `prompts/iep/Core_Planning_Instructions.txt`:**
    *   Updated the component resolution logic in Section II.A.1.a.viii to implement the "app-specific first" search order:
        1.  App-Specific (flat file): `prompts/apps/[Selected_PromptAppName]/[componentName].txt`
        2.  App-Specific (folder style): `prompts/apps/[Selected_PromptAppName]/[componentName]/[componentName].txt`
        3.  Global Add-on: `prompts/add_ons/[componentName]/[componentName].txt`
        4.  Global Util: `prompts/util/[componentName]/[componentName].txt`
-   **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`:**
    *   Modified Section 3.B.2.i ("Component Resolution Search Order") and Section 4 ("Developing New promptApps") to accurately document the new "app-specific first" search order.
-   **Created Test Assets for Search Order Verification:**
    *   Global add-on: `prompts/add_ons/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   App-specific component (folder style): `prompts/apps/CompliancePLM/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   Updated `prompts/add_ons/available_addons_manifest.md` for the global test component.
    *   Updated `prompts/apps/CompliancePLM/CompliancePLM_manifest.json` to include a task "Test Component Resolution" that calls `test_resolver_component`.

**State of Deliverables:**
- Core framework logic (`Core_Planning_Instructions.txt`) now correctly implements "app-specific first" component resolution for `promptApps`.
- Documentation (`MPS_Usage_Guide.md`) accurately reflects this new search order.
- Test assets are in place to verify this specific functionality.
- The broader `promptApp` and `create_research_report` add-on implementation (from previous steps in this session) is complete.
- The system is ready for user testing of the `promptApp` functionality, especially the component search order.

**Next Steps (User):**
- Thoroughly test the `promptApp` system, focusing on:
    - Invoking the "Test Component Resolution" task within `CompliancePLM` to ensure the app-specific `test_resolver_component` is used.
    - Invoking tasks that use global components (like "Product Specification" using `create_research_report` in `CompliancePLM`) to ensure they are correctly found.
    - Testing the iteration flow and re-invocation prompts for iterable tasks.
---

## MPS Performance Feedback Log

This section logs specific feedback on the performance, clarity, and effectiveness of the Master Prompt Segment (MPS) versions and the artifacts they help generate.

---
**Feedback Entry - 2023-11-02**
**Source of Feedback:** Initial setup of this feedback log section during MPS feature design.
**MPS Version Referenced:** Relevant for `prompts/Master_Prompt_Segment.txt` (v0.3.2 and future versions).
**Context/Scenario:** Establishing the feedback mechanism as per design discussions for feature "Feedback Loop for MPS Refinement".
**Observation/Issue:** This log is intended to capture issues like:
    - Ambiguities in any version of `Master_Prompt_Segment.md` that lead to incorrect or suboptimal behavior by a Planning AI.
    - Problems encountered by Task AIs due to the structure or content of prompts generated based on an MPS version.
    - User difficulties in understanding or using the MPS, its generated `00_task_launch_plan.md`, or other artifacts.
    - Success stories where a particular MPS instruction proved very effective.
**Suggestion for MPS Refinement (if any):** N/A
---
*(Future entries will be appended below this line)*
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request to Jules instance for refactoring).
**MPS Version Referenced:** `prompts/Master_Prompt_Segment.txt` (approx. v0.3.2, prior to modularization) and the new modularized version (post this session's changes).
**Context/Scenario:** User identified that the `Master_Prompt_Segment.txt` was becoming very long and dense, making it difficult to read, manage, and maintain.
**Observation/Issue:** The monolithic nature of `Master_Prompt_Segment.txt` was impacting usability and the ease of making targeted changes to core planning instructions without affecting user-configurable sections.
**Suggestion for MPS Refinement (Implemented):** To improve readability and maintainability, the core planning AI instructions were extracted from `Master_Prompt_Segment.txt` and placed into a new, dedicated file: `prompts/iep/Core_Planning_Instructions.txt`. The `Master_Prompt_Segment.txt` now includes these instructions using an `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive. This separation is intended to make both the user-facing configuration parts of the MPS and the core instructional logic easier to manage.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request and illustrative example: `[ ] task_spawning_addon.txt` to Jules instance).
**MPS Version Referenced:** The modularized MPS version (where `Core_Planning_Instructions.txt` was already separate) and the current version implementing optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User desired to further enhance the framework's modularity by making the entire project planning and task generation process an optional component, selectable by the user, rather than an inherent, non-optional function of the MPS.
**Observation/Issue:** While `Core_Planning_Instructions.txt` was already separated, it still implicitly mandated the task spawning workflow. The framework lacked the flexibility to be used for other purposes without invoking this primary planning behavior.
**Suggestion for MPS Refinement (Implemented):** The user's suggestion to make task spawning an optional add-on was implemented.
    - The core logic for project planning, TLP generation, and task prompt creation was moved from `prompts/iep/Core_Planning_Instructions.txt` to a new file: `prompts/add_ons/task_spawning_addon.txt`.
    - `prompts/iep/Core_Planning_Instructions.txt` was updated to include conditional logic. It now checks if `task_spawning_addon.txt` is selected by the user in `[[USER_ADDON_SELECTION]]` (within `Master_Prompt_Segment.txt`).
    - If selected, the `task_spawning_addon.txt` is executed.
    - If not selected, the AI is instructed to acknowledge this, skip default plan generation, and process any other selected add-ons.
    - This change makes the MPS framework significantly more flexible, allowing users to engage the Planning AI for potentially different primary functions by selecting different combinations of add-ons, or by using it with only utility add-ons.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifying questions and specific directive regarding `task_spawning_addon.txt` inheritance and add-on ordering).
**MPS Version Referenced:** The MPS version with optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User inquired about how multiple add-ons interact, particularly how `task_spawning_addon.txt` would affect other add-ons, and then specifically requested that `task_spawning_addon.txt` should not be inherited by the Task AIs it generates. User also sought clarity on the importance of add-on ordering in the selection block.
**Observation/Issue:**
    1. The previous add-on processing logic could have resulted in `task_spawning_addon.txt` (intended for the Planning AI) being appended to Task AI prompts, leading to overly verbose prompts and potential for unintended behavior.
    2. The impact of add-on order in `[[USER_ADDON_SELECTION]]` on the final content of Task AI prompts was not explicitly documented, potentially leading to user confusion.
**Suggestion for MPS Refinement (Implemented):**
    - **Non-inheritance of `task_spawning_addon.txt`**: Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B) to create a filtered list (`Inheritable_Addons_Content_Ordered_List`) of add-on contents for Task AI prompt inheritance, which explicitly excludes the content of `task_spawning_addon.txt`. This ensures the Planning AI uses `task_spawning_addon.txt` for its process, but Task AIs receive cleaner, more focused prompts.
    - **Clarified Add-on Ordering**: Updated `prompts/Master_Prompt_Segment.txt` (comments in `[[USER_ADDON_SELECTION]]`) and `framework_dev_docs/guides/MPS_Usage_Guide.md` to explain that the order of inheritable add-ons is preserved when `task_spawning_addon.txt` appends them to task prompts. Also recommended listing `task_spawning_addon.txt` last for visual clarity if used.
    - These refinements improve the safety and predictability of add-on interactions and provide users with better control and understanding of how their selections affect Task AI prompts.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (initial idea for process automation, then specific refinements for primary input and reference depth for product specs).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** User expressed a need to use the MPS framework for more than just task spawning, specifically for a structured process to generate product specification documents from various inputs. Later refinements included requests for more control over the research phase of this process.
**Observation/Issue:** The initial MPS framework was primarily geared towards task spawning. A structured approach was needed to define and execute other complex, multi-step AI-driven processes. For the product specs process,
        *   Added `PRODUCT_SPECS_PRIMARY_INPUT_FILE`: An optional path to a specific document. The AI is instructed to focus its research by analyzing at least every numbered heading in this document. If not provided, the AI selects the most comprehensive input document for this role.
        *   Added `PRODUCT_SPECS_REF_DEPTH`: An optional integer (0-2, default 1) to control how many levels deep the AI will follow internal references within the provided document set.
    These enhancements make the product specification generation process more guided and configurable.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** Jules (proactive refinement during development of `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** The introduction of `build_product_specs_process.txt` as a new "primary directive" add-on (similar in role to `task_spawning_addon.txt` but with a different function) highlighted the need to generalize how the core framework selects and manages such add-ons.
**Observation/Issue:** The existing logic in `prompts/iep/Core_Planning_Instructions.txt` was somewhat hardcoded around `task_spawning_addon.txt` for determining the main execution path and for non-inheritance of add-on content into sub-prompts. This would not scale well if many different primary add-ons were developed.
**Suggestion for MPS Refinement (Implemented):**
    - Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B and II) to:
        - Define a list of `Known_Primary_Directive_Addons`.
        - Dynamically identify an `Active_Primary_Addon_Filename` by checking the user's selection against this known list, prioritizing the first one selected by the user if multiple are chosen. A warning is logged if multiple primary add-ons are selected.
        - Ensure that the `Inheritable_Addons_Content_Ordered_List` (used for appending to sub-prompts) correctly excludes the content of the currently `Active_Primary_Addon_Filename`, not just `task_spawning_addon.txt`.
    - This makes the core framework more robust and extensible, allowing new primary add-ons to be integrated more easily without requiring significant changes to the core conditional logic each time. It also handles user selection of multiple primary add-ons more gracefully.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (specific feedback on research methodology, codebase interaction rules like "level 0 only for codebases," and codebase review preferences for the `build_product_specs_process.txt` add-on).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on.
**Context/Scenario:** User provided detailed requirements to refine the initial `build_product_specs_process.txt` add-on to achieve a more structured, in-depth, and controllable research and content generation process, particularly focusing on how the AI should interact with a primary document and linked codebases.
**Observation/Issue:** The initial version of `build_product_specs_process.txt` had a more general research phase. The user required a more granular, heading-based research approach for the Primary Input Document, specific rules for how the AI should analyze linked codebases (including different levels of review depth/focus), and a strict "level 0 only" rule for code analysis to prevent unintended deep dives into code.
**Suggestion for MPS Refinement (Implemented):**
The `prompts/add_ons/build_product_specs_process.txt` was significantly updated to incorporate the "Level 0 Deep Research" methodology as per user requirements:
    - **New Configurations:** Introduced `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` in `Master_Prompt_Segment.txt` and documented them in `MPS_Usage_Guide.md`.
    - **Per-Heading Research Cycle:** The add-on's Phase 3 was restructured. It now includes an initial high-level review of the Primary Input Document. Then, for each major heading in that document, it performs a 5-step analysis: Heading Context Analysis, Supporting Docs Analysis, References & Codebase Analysis, Consolidated Research for the heading, and Iterative Content Contribution for that heading.
    - **Codebase Review Integration:** The "References & Codebase Analysis" step now explicitly uses the `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` ("complex", "cursory", "focused") and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` to guide its interaction with linked code.
    - **"Level 0 Code Analysis ONLY":** A strict rule was added, instructing the AI not to follow internal code references (like imports or function calls to other files) from any fetched code text. Its analysis is confined to the directly retrieved code content.
    - **Iterative Content Drafting:** Content for output documents is now drafted iteratively during the per-heading analysis and then assembled and finalized in later phases.
    This refined process provides a more systematic and controllable approach for the AI when generating product specifications.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifications and new requirements for iterative deepening for `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on incorporating "Level 0 Deep Research".
**Context/Scenario:** Expanding the iterative capabilities of `build_product_specs_process.txt` from an initial 0-2 level concept to a more detailed 0-3 level process with specific content goals for each level.
**Observation/Issue:** The initial iterative deepening plan (0-2 levels) for `build_product_specs_process.txt` needed more specific content goals for each level and an additional level for AI-driven insights to meet user's evolving requirements for content depth and sophistication.
**Suggestion for MPS Refinement (Implemented):**
The iterative deepening feature of `build_product_specs_process.txt` was expanded to support 0-3 levels, and associated configurations and documentation were updated:
    - **Configuration Updates (`Master_Prompt_Segment.txt`):** `PRODUCT_SPECS_ITERATION_DEPTH` comment updated to reflect the 0-3 range and specific goals:
        - `0`: Outline Generation (headings with single key idea paragraphs).
        - `1`: Basic Content (Bachelor's level, ~3 paras per D0 heading).
        - `2`: Expert Elaboration (~1 page per original D0 heading).
        - `3`: AI Unique Insights (~3 pages per original D0 heading, novel ideas).
    - **Add-on Logic (`build_product_specs_process.txt`):**
        - Phase 1 (Initialization) now validates depth (0-3) and `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` (required for D1-D3, critical error if missing). Output file suffixes `_D0` to `_D3` are determined.
        - Phase 2 (Input Processing) is conditional: D0 uses original inputs; D1-D3 use previous iteration's outputs as primary, with original inputs as reference.
        - Phase 3 (Content Generation) has distinct logic for each depth (0-3), detailing how to build upon previous work (for D1-D3) or generate outlines (D0) according to the specified content goals for each level.
    - **Documentation (`MPS_Usage_Guide.md`):** Updated to detail the 0-3 levels, their specific purposes, and the user workflow for managing iterative runs and output folders.
    This provides a more granular, powerful, and well-defined iterative workflow for the `build_product_specs_process.txt` add-on.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (vision for enhanced component modularity via "folder-per-app" architecture).
**MPS Version Referenced:** Current version with multiple add-ons and utils.
**Context/Scenario:** As the number of components (add-ons, utils, and potentially IPC handlers) grows, a flat file structure in their respective directories (`prompts/add_ons/`, `prompts/util/`) becomes less organized and limits the ability for components to have their own supporting files or sub-frameworks.
**Observation/Issue:** The previous flat-file structure for add-ons and utils was not ideal for scalability or for components that might require multiple associated files (e.g., templates, data snippets, complex sub-instructions).
**Suggestion for MPS Refinement (Implemented):**
The framework was refactored to a "folder-per-app" architecture for all components (add-ons, utils, and anticipated IPC handlers):
    - **Core Logic (`prompts/iep/Core_Planning_Instructions.txt`):** Updated to discover and load components based on a new directory structure.
        - It now expects component-specific parent paths like `User_Path_Addons_Dir`, `User_Path_Utils_Dir`, `User_Path_IPC_Dir` to be defined in `[[USER PATH CONFIGURATION]]`.
        - For each component "app" (e.g., `my_addon_app`), it looks for a subdirectory named `my_addon_app` within the relevant parent path.
        - The primary instruction file for the app must be located at `[parent_path]/my_addon_app/my_addon_app.txt`.
        - Internal references (e.g., `Known_Primary_Directive_Addon_Apps`, `Active_Primary_Addon_AppName`) now use these app/folder names.
    - **Component Migration:** All existing add-ons (`task_spawning_addon`, `task_resumption_addon`, `build_product_specs_process`) and utils (`pre_flight_check_user_plan`) were moved into their respective new subdirectories (e.g., `prompts/add_ons/task_spawning_addon/task_spawning_addon.txt`).
    - **New `prompts/ipc/` directory:** Created with a `.gitkeep` file for future IPC components.
    - **Documentation Updates:**
        - `prompts/add_ons/available_addons_manifest.md`: Updated to list add-ons by their app/folder name and describe the new structure.
        - `framework_dev_docs/guides/MPS_Usage_Guide.md`: Comprehensively updated to explain the "folder-per-app" architecture, how users should set up paths, how components are selected, and how developers should structure new components.
    This architectural change significantly improves modularity, organization, and the potential for more complex, self-contained components within the MPS framework.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (desire to simplify MPS, new way to handle app-specific data, AI assistance for missing/default configs, specific format for detecting edits).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment and Core Planning Instructions).
**Context/Scenario:** This feedback entry summarizes a series of user requests and design discussions aimed at a comprehensive overhaul of the MPS framework's configuration system and a significant enhancement of the `build_product_specs_process` add-on's iterative capabilities.
**Observation/Issue:**
    1.  The global `[[USER PATH CONFIGURATION]]` in `Master_Prompt_Segment.txt` was becoming unwieldy; app-specific configs were mixed with global ones; parameter precedence and user edit detection were not formalized.
    2.  A more structured and discoverable way to manage parameters for individual add-on apps was needed, including clear defaults and user override mechanisms.
    3.  Users needed assistance in setting mandatory parameters, especially those with placeholder defaults.
**Suggestion for MPS Refinement (Implemented):**
A comprehensive refactoring of the configuration system was performed:
    1.  **Fixed Global System Paths:** Core component discovery paths (for add-ons, utils, IEP, IPC) are now hardcoded in `Core_Planning_Instructions.txt`, simplifying user setup.
    2.  **`[[USER PATH CONFIGURATION]]` Removed from `Master_Prompt_Segment.txt`:** This block was deleted. General output paths (like `Main Iteration Folder`) and other process-specific parameters are now intended to be managed as parameters within the configuration system of the relevant app.
    3.  **App-Specific Configuration System:**
        *   **`USER_appName_CONFIG.txt` Standard:** Defined a standard format for these template/storage files within each app's folder. This includes `[[USER_CONFIG_FOR_appName]]` delimiters, and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), description with default (`# Description: ... Default: ...`), and the value line (`PARAM_NAME: [USER_VALUE_START]value[USER_VALUE_END]`).
        *   **Main Prompt Overrides:** Users can copy the content of a `USER_appName_CONFIG.txt` into their main spawn prompt to provide run-specific overrides.
        *   **Parameter Resolution Precedence:** Implemented in app logic (demonstrated in `build_product_specs_process.txt`): Main prompt block > User-edited `USER_appName_CONFIG.txt` > AcceptedDefault in `USER_appName_CONFIG.txt`.
        *   **AI-Assisted Updates:** If a `Required` parameter with a `PlaceholderDefault` remains unresolved, the app instructs the AI (Jules) to request the value from the user via chat. The AI then updates the on-disk `USER_appName_CONFIG.txt` with the new value and outputs the complete updated config block for the user to transfer to their main spawn prompt.
    4.  **Documentation:** All changes were reflected in `Master_Prompt_Segment.txt` (now v0.4.0), `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, and relevant add-on files (e.g., `build_product_specs_process/USER_build_product_specs_process_CONFIG.txt` and its primary instruction file).
    This new system offers improved modularity, clarity in parameter management, and a better user experience through AI-assisted configuration.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Accomplishments This Session (Finalizing Configuration System & Preparing for "Concept Task" Handoff):**
This session concluded the major refactoring of the MPS framework's configuration system and component architecture. It also prepared for the next phase of work focusing on the "Concept task."

*   **Configuration System Refactoring (Completion & Documentation):**
    *   **Standard for `USER_appName_CONFIG.txt`:** Formalized the standard for these files, including app-specific delimiters (`[[USER_CONFIG_FOR_appName]]`), and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), descriptions, defaults, and `[USER_VALUE_START]...[USER_VALUE_END]` markers. An illustrative example (`USER_exampleApp_CONFIG.txt`) was created as part of this standard definition.
    *   **Applied Standard:** The `build_product_specs_process` add-on's `USER_build_product_specs_process_CONFIG.txt` was updated to fully adhere to this new standard, with all its parameters classified.
    *   **Refined App Logic (`build_product_specs_process.txt` v0.4):** Its parameter resolution logic in Phase 1 was enhanced to parse the new metadata from `USER_..._CONFIG.txt`. The AI-assisted update mechanism (asking user via chat) now correctly triggers only for "Required" parameters with "PlaceholderDefault" values that haven't been overridden. A final validation step ensures all "Required" parameters have valid values before proceeding, halting with an error if not.
    *   **Streamlined `Master_Prompt_Segment.txt` (v0.4.0):** The global `[[USER PATH CONFIGURATION]]` block was definitively removed. Instructions in `[[USER_ADDON_SELECTION]]` were updated to guide users on app-specific configurations via `[[USER_CONFIG_FOR_appName]]` blocks in their main spawn prompt.
    *   **Consolidated `MPS_Usage_Guide.md`:** The guide was thoroughly reviewed and updated to provide a comprehensive and consistent explanation of the new configuration system. This includes: fixed global system paths, the role and format of `USER_appName_CONFIG.txt` files, the user workflow for app configuration (using main prompt blocks or editing `USER_...CONFIG.txt`), the AI's parameter resolution precedence, and how AI-assisted updates work. The "folder-per-app" architecture is also clearly documented.

*   **Next Area of Work (User Directed - For Next Jules Instance):**
    *   The user has directed focus towards refining an existing "Concept task."
    *   This will involve analyzing the "Concept task" and splitting it into approximately three sub-tasks.
    *   A key aspect of this work will be to use the "Concept task" sub-tasks as a practical use case for **exploring and implementing extensions to the 'add-on, util, and process' component concepts** within the MPS framework.
    *   The next Jules instance is tasked with initiating this by requesting detailed requirements from the user regarding the "Concept task" (current state, materials possibly in `Inputs/concept/inputs`, ideas for sub-tasks) and their initial thoughts on evolving the component model.

**State of Deliverables:**
The comprehensive refactoring of the MPS configuration system is complete. This includes the "folder-per-app" architecture, fixed system paths for component discovery, and the new app-specific configuration mechanism using `USER_appName_CONFIG.txt` files and `[[USER_CONFIG_FOR_appName]]` blocks, featuring AI-assisted updates. All core prompts and documentation (`Master_Prompt_Segment.txt`, `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`, and relevant add-ons like `build_product_specs_process`) have been updated to reflect these changes. The framework is significantly more modular, user-friendly, and robust in its configuration management. No new code or prompt changes were made in this specific summarization step. The system is ready for handoff to the next Jules instance to begin work on the "Concept task" and component model evolution.
---
---
**Feedback Entry Date:** 2025-06-02
**Source of Feedback:** User (new directive for next area of work following completion of major configuration system and architectural refactoring).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment) and related component architecture (folder-per-app, `USER_appName_CONFIG.txt` standard, etc.).
**Context/Scenario:** Planning the next development cycle for the MPS framework after successfully implementing a new configuration system and "folder-per-app" architecture.
**Observation/Issue:**
    1.  An existing internal "Concept task" (details to be provided by the user to the next Jules instance, potentially located in `Inputs/concept/inputs`) is currently monolithic and needs to be broken down into more manageable sub-tasks (approximately three).
    2.  The process of decomposing and implementing these "Concept task" sub-tasks is expected to reveal opportunities or requirements for evolving the framework's current component definitions (add-on, util, process) or potentially introducing new component types or interaction paradigms.
**Suggestion (For Next Jules Instance):**
The next Jules instance should:
    1.  **Gather Detailed Requirements for "Concept Task":** Actively engage with the user to obtain a detailed understanding of the current "Concept task," its goals, any existing materials (potentially in `Inputs/concept/inputs`).
    2.  Collaborate with the user to define the three (approx.) sub-tasks for the "Concept task."
    3.  In parallel, analyze how the requirements of these sub-tasks might necessitate extensions to the existing component concepts (add-on, util, process) or the introduction of new component types/paradigms within the MPS framework.
    4.  Plan and implement both the "Concept task" restructuring and the identified framework component extensions, using the former as a practical test case for the latter.
---
**Session Summary - 2025-06-03**
**Instance:** Jules
**User Feedback/Requests Addressed:**
- User directed a new phase of MPS development focusing on two major initiatives:
    1.  Abstracting the existing `build_product_specs_process` add-on into a more generic `create_research_report` add-on.
    2.  Introducing a new "promptApp" concept to allow users to define and execute multi-stage workflows composed of underlying MPS components.
- Clarified requirements for `create_research_report` parameters, including separate controls for `ITERATIVE_OUTPUT_DEPTH` (D0-D3 style) and `REFERENCE_FOLLOWING_DEPTH` (document link traversal).
- Confirmed that plain text input formats (.txt, .md, web text) are the initial target for document processing.
- Decided that sequential tasks within a `promptApp` will be handled by user re-invocation of new AI instances for each task, simplifying initial framework orchestration.

**Summary of Approved Plan:**
The overall plan involves:
1.  Developing the `create_research_report` add-on (now complete).
2.  Defining the "promptApp" structure and manifest file format.
3.  Updating `Core_Planning_Instructions.txt` and `Master_Prompt_Segment.txt` to support `promptApp` discovery, selection, and execution.
4.  Creating an initial structural example of a `promptApp` (`CompliancePLM`).
5.  Retiring the `build_product_specs_process` add-on files.
6.  Updating all documentation.
7.  Testing the new functionalities.

**Summary of Changes Made This Session (to implement Step 1 of the plan):**
-   **Created `prompts/add_ons/create_research_report/` directory.**
-   **Created `prompts/add_ons/create_research_report/USER_create_research_report_CONFIG.txt`:**
    *   Defined parameters: `PROPOSED_TABLE_OF_CONTENTS`, `GUIDELINES_DOC_PATHS`, `ADDITIONAL_WEB_LINKS_FILE`, `EXEMPLAR_DOCS`, `GENERAL_INPUT_DOCS`, `PRIMARY_INPUT_DOC_PATH`, `ITERATIVE_OUTPUT_DEPTH`, `REFERENCE_FOLLOWING_DEPTH`, `PREVIOUS_ITERATION_OUTPUT_PATH`, `REPORT_BASENAME`, `REPORT_OUTPUT_PATH`.
    *   Included full metadata (Requirement, DefaultType, Description, Default) for each.
-   **Created `prompts/add_ons/create_research_report/create_research_report.txt`:**
    *   Adapted core logic from the former `build_product_specs_process.txt`.
    *   Updated to use the new parameter set for generic report generation.
    *   Removed product-specification-specific and detailed codebase review logic.
    *   Maintained the 6-phase structure and parameter resolution mechanisms.
-   **Updated `prompts/add_ons/available_addons_manifest.md`:**
    *   Added entry for `create_research_report`.
    *   Removed entry for `build_product_specs_process`.

**State of Deliverables:**
- The `create_research_report` add-on (v0.1) is developed and its configuration file is in place.
- The add-on manifest is updated.
- The `build_product_specs_process` add-on is effectively replaced by this new add-on (physical file deletion is pending a later plan step).
- Plan step 1 is complete.

**Next Steps (This Jules instance/session):**
- Proceed to Plan Step 2: "Define 'promptApp' Structure and Manifest." This involves designing the manifest file format that will define how `promptApps` (like the `CompliancePLM` example) are structured and how their tasks map to MPS components.
---
**Session Summary - 2025-06-03 (Continued)**
**Instance:** Jules
**User Feedback/Requests Addressed (Post Initial 2025-06-03 Summary):**
- User confirmed a critical change in component resolution strategy for `promptApps`: the search order should be "app-specific first" (flat file, then folder style within app), followed by global components (add-ons, then utils). This was a change from the initially implemented "globals first" approach.

**Summary of Approved Plan (Revised Portion):**
The plan was revised to implement and document this new search order:
1.  Revise `Core_Planning_Instructions.txt` for the new "app-specific first" search order. (Completed)
2.  Update `MPS_Usage_Guide.md` to reflect this new search order. (Completed)
3.  Create test assets and conceptually outline tests for the new search order. (Completed)
4.  Update `HANDOFF_NOTES.md` and submit. (This step)

**Summary of Changes Made This Session (Implementing Revised Plan):**
-   **Modified `prompts/iep/Core_Planning_Instructions.txt`:**
    *   Updated the component resolution logic in Section II.A.1.a.viii to implement the "app-specific first" search order:
        1.  App-Specific (flat file): `prompts/apps/[Selected_PromptAppName]/[componentName].txt`
        2.  App-Specific (folder style): `prompts/apps/[Selected_PromptAppName]/[componentName]/[componentName].txt`
        3.  Global Add-on: `prompts/add_ons/[componentName]/[componentName].txt`
        4.  Global Util: `prompts/util/[componentName]/[componentName].txt`
-   **Updated `framework_dev_docs/guides/MPS_Usage_Guide.md`:**
    *   Modified Section 3.B.2.i ("Component Resolution Search Order") and Section 4 ("Developing New promptApps") to accurately document the new "app-specific first" search order.
-   **Created Test Assets for Search Order Verification:**
    *   Global add-on: `prompts/add_ons/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   App-specific component (folder style): `prompts/apps/CompliancePLM/test_resolver_component/` (with `test_resolver_component.txt` and `USER_...CONFIG.txt`).
    *   Updated `prompts/add_ons/available_addons_manifest.md` for the global test component.
    *   Updated `prompts/apps/CompliancePLM/CompliancePLM_manifest.json` to include a task "Test Component Resolution" that calls `test_resolver_component`.

**State of Deliverables:**
- Core framework logic (`Core_Planning_Instructions.txt`) now correctly implements "app-specific first" component resolution for `promptApps`.
- Documentation (`MPS_Usage_Guide.md`) accurately reflects this new search order.
- Test assets are in place to verify this specific functionality.
- The broader `promptApp` and `create_research_report` add-on implementation (from previous steps in this session) is complete.
- The system is ready for user testing of the `promptApp` functionality, especially the component search order.

**Next Steps (User):**
- Thoroughly test the `promptApp` system, focusing on:
    - Invoking the "Test Component Resolution" task within `CompliancePLM` to ensure the app-specific `test_resolver_component` is used.
    - Invoking tasks that use global components (like "Product Specification" using `create_research_report` in `CompliancePLM`) to ensure they are correctly found.
    - Testing the iteration flow and re-invocation prompts for iterable tasks.
---

## MPS Performance Feedback Log

This section logs specific feedback on the performance, clarity, and effectiveness of the Master Prompt Segment (MPS) versions and the artifacts they help generate.

---
**Feedback Entry - 2023-11-02**
**Source of Feedback:** Initial setup of this feedback log section during MPS feature design.
**MPS Version Referenced:** Relevant for `prompts/Master_Prompt_Segment.txt` (v0.3.2 and future versions).
**Context/Scenario:** Establishing the feedback mechanism as per design discussions for feature "Feedback Loop for MPS Refinement".
**Observation/Issue:** This log is intended to capture issues like:
    - Ambiguities in any version of `Master_Prompt_Segment.md` that lead to incorrect or suboptimal behavior by a Planning AI.
    - Problems encountered by Task AIs due to the structure or content of prompts generated based on an MPS version.
    - User difficulties in understanding or using the MPS, its generated `00_task_launch_plan.md`, or other artifacts.
    - Success stories where a particular MPS instruction proved very effective.
**Suggestion for MPS Refinement (if any):** N/A
---
*(Future entries will be appended below this line)*
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request to Jules instance for refactoring).
**MPS Version Referenced:** `prompts/Master_Prompt_Segment.txt` (approx. v0.3.2, prior to modularization) and the new modularized version (post this session's changes).
**Context/Scenario:** User identified that the `Master_Prompt_Segment.txt` was becoming very long and dense, making it difficult to read, manage, and maintain.
**Observation/Issue:** The monolithic nature of `Master_Prompt_Segment.txt` was impacting usability and the ease of making targeted changes to core planning instructions without affecting user-configurable sections.
**Suggestion for MPS Refinement (Implemented):** To improve readability and maintainability, the core planning AI instructions were extracted from `Master_Prompt_Segment.txt` and placed into a new, dedicated file: `prompts/iep/Core_Planning_Instructions.txt`. The `Master_Prompt_Segment.txt` now includes these instructions using an `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive. This separation is intended to make both the user-facing configuration parts of the MPS and the core instructional logic easier to manage.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (via direct request and illustrative example: `[ ] task_spawning_addon.txt` to Jules instance).
**MPS Version Referenced:** The modularized MPS version (where `Core_Planning_Instructions.txt` was already separate) and the current version implementing optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User desired to further enhance the framework's modularity by making the entire project planning and task generation process an optional component, selectable by the user, rather than an inherent, non-optional function of the MPS.
**Observation/Issue:** While `Core_Planning_Instructions.txt` was already separated, it still implicitly mandated the task spawning workflow. The framework lacked the flexibility to be used for other purposes without invoking this primary planning behavior.
**Suggestion for MPS Refinement (Implemented):** The user's suggestion to make task spawning an optional add-on was implemented.
    - The core logic for project planning, TLP generation, and task prompt creation was moved from `prompts/iep/Core_Planning_Instructions.txt` to a new file: `prompts/add_ons/task_spawning_addon.txt`.
    - `prompts/iep/Core_Planning_Instructions.txt` was updated to include conditional logic. It now checks if `task_spawning_addon.txt` is selected by the user in `[[USER_ADDON_SELECTION]]` (within `Master_Prompt_Segment.txt`).
    - If selected, the `task_spawning_addon.txt` is executed.
    - If not selected, the AI is instructed to acknowledge this, skip default plan generation, and process any other selected add-ons.
    - This change makes the MPS framework significantly more flexible, allowing users to engage the Planning AI for potentially different primary functions by selecting different combinations of add-ons, or by using it with only utility add-ons.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifying questions and specific directive regarding `task_spawning_addon.txt` inheritance and add-on ordering).
**MPS Version Referenced:** The MPS version with optional task spawning via `task_spawning_addon.txt`.
**Context/Scenario:** User inquired about how multiple add-ons interact, particularly how `task_spawning_addon.txt` would affect other add-ons, and then specifically requested that `task_spawning_addon.txt` should not be inherited by the Task AIs it generates. User also sought clarity on the importance of add-on ordering in the selection block.
**Observation/Issue:**
    1. The previous add-on processing logic could have resulted in `task_spawning_addon.txt` (intended for the Planning AI) being appended to Task AI prompts, leading to overly verbose prompts and potential for unintended behavior.
    2. The impact of add-on order in `[[USER_ADDON_SELECTION]]` on the final content of Task AI prompts was not explicitly documented, potentially leading to user confusion.
**Suggestion for MPS Refinement (Implemented):**
    - **Non-inheritance of `task_spawning_addon.txt`**: Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B) to create a filtered list (`Inheritable_Addons_Content_Ordered_List`) of add-on contents for Task AI prompt inheritance, which explicitly excludes the content of `task_spawning_addon.txt`. This ensures the Planning AI uses `task_spawning_addon.txt` for its process, but Task AIs receive cleaner, more focused prompts.
    - **Clarified Add-on Ordering**: Updated `prompts/Master_Prompt_Segment.txt` (comments in `[[USER_ADDON_SELECTION]]`) and `framework_dev_docs/guides/MPS_Usage_Guide.md` to explain that the order of inheritable add-ons is preserved when `task_spawning_addon.txt` appends them to task prompts. Also recommended listing `task_spawning_addon.txt` last for visual clarity if used.
    - These refinements improve the safety and predictability of add-on interactions and provide users with better control and understanding of how their selections affect Task AI prompts.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (initial idea for process automation, then specific refinements for primary input and reference depth for product specs).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** User expressed a need to use the MPS framework for more than just task spawning, specifically for a structured process to generate product specification documents from various inputs. Later refinements included requests for more control over the research phase of this process.
**Observation/Issue:** The initial MPS framework was primarily geared towards task spawning. A structured approach was needed to define and execute other complex, multi-step AI-driven processes. For the product specs process,
        *   Added `PRODUCT_SPECS_PRIMARY_INPUT_FILE`: An optional path to a specific document. The AI is instructed to focus its research by analyzing at least every numbered heading in this document. If not provided, the AI selects the most comprehensive input document for this role.
        *   Added `PRODUCT_SPECS_REF_DEPTH`: An optional integer (0-2, default 1) to control how many levels deep the AI will follow internal references within the provided document set.
    These enhancements make the product specification generation process more guided and configurable.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** Jules (proactive refinement during development of `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with extensible primary add-on logic.
**Context/Scenario:** The introduction of `build_product_specs_process.txt` as a new "primary directive" add-on (similar in role to `task_spawning_addon.txt` but with a different function) highlighted the need to generalize how the core framework selects and manages such add-ons.
**Observation/Issue:** The existing logic in `prompts/iep/Core_Planning_Instructions.txt` was somewhat hardcoded around `task_spawning_addon.txt` for determining the main execution path and for non-inheritance of add-on content into sub-prompts. This would not scale well if many different primary add-ons were developed.
**Suggestion for MPS Refinement (Implemented):**
    - Modified `prompts/iep/Core_Planning_Instructions.txt` (Section I.B and II) to:
        - Define a list of `Known_Primary_Directive_Addons`.
        - Dynamically identify an `Active_Primary_Addon_Filename` by checking the user's selection against this known list, prioritizing the first one selected by the user if multiple are chosen. A warning is logged if multiple primary add-ons are selected.
        - Ensure that the `Inheritable_Addons_Content_Ordered_List` (used for appending to sub-prompts) correctly excludes the content of the currently `Active_Primary_Addon_Filename`, not just `task_spawning_addon.txt`.
    - This makes the core framework more robust and extensible, allowing new primary add-ons to be integrated more easily without requiring significant changes to the core conditional logic each time. It also handles user selection of multiple primary add-ons more gracefully.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (specific feedback on research methodology, codebase interaction rules like "level 0 only for codebases," and codebase review preferences for the `build_product_specs_process.txt` add-on).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on.
**Context/Scenario:** User provided detailed requirements to refine the initial `build_product_specs_process.txt` add-on to achieve a more structured, in-depth, and controllable research and content generation process, particularly focusing on how the AI should interact with a primary document and linked codebases.
**Observation/Issue:** The initial version of `build_product_specs_process.txt` had a more general research phase. The user required a more granular, heading-based research approach for the Primary Input Document, specific rules for how the AI should analyze linked codebases (including different levels of review depth/focus), and a strict "level 0 only" rule for code analysis to prevent unintended deep dives into code.
**Suggestion for MPS Refinement (Implemented):**
The `prompts/add_ons/build_product_specs_process.txt` was significantly updated to incorporate the "Level 0 Deep Research" methodology as per user requirements:
    - **New Configurations:** Introduced `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` in `Master_Prompt_Segment.txt` and documented them in `MPS_Usage_Guide.md`.
    - **Per-Heading Research Cycle:** The add-on's Phase 3 was restructured. It now includes an initial high-level review of the Primary Input Document. Then, for each major heading in that document, it performs a 5-step analysis: Heading Context Analysis, Supporting Docs Analysis, References & Codebase Analysis, Consolidated Research for the heading, and Iterative Content Contribution for that heading.
    - **Codebase Review Integration:** The "References & Codebase Analysis" step now explicitly uses the `PRODUCT_SPECS_CODEBASE_REVIEW_PREFERENCE` ("complex", "cursory", "focused") and `PRODUCT_SPECS_CODEBASE_FOCUS_AREAS` to guide its interaction with linked code.
    - **"Level 0 Code Analysis ONLY":** A strict rule was added, instructing the AI not to follow internal code references (like imports or function calls to other files) from any fetched code text. Its analysis is confined to the directly retrieved code content.
    - **Iterative Content Drafting:** Content for output documents is now drafted iteratively during the per-heading analysis and then assembled and finalized in later phases.
    This refined process provides a more systematic and controllable approach for the AI when generating product specifications.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (clarifications and new requirements for iterative deepening for `build_product_specs_process.txt`).
**MPS Version Referenced:** Current version with `build_product_specs_process.txt` add-on incorporating "Level 0 Deep Research".
**Context/Scenario:** Expanding the iterative capabilities of `build_product_specs_process.txt` from an initial 0-2 level concept to a more detailed 0-3 level process with specific content goals for each level.
**Observation/Issue:** The initial iterative deepening plan (0-2 levels) for `build_product_specs_process.txt` needed more specific content goals for each level and an additional level for AI-driven insights to meet user's evolving requirements for content depth and sophistication.
**Suggestion for MPS Refinement (Implemented):**
The iterative deepening feature of `build_product_specs_process.txt` was expanded to support 0-3 levels, and associated configurations and documentation were updated:
    - **Configuration Updates (`Master_Prompt_Segment.txt`):** `PRODUCT_SPECS_ITERATION_DEPTH` comment updated to reflect the 0-3 range and specific goals:
        - `0`: Outline Generation (headings with single key idea paragraphs).
        - `1`: Basic Content (Bachelor's level, ~3 paras per D0 heading).
        - `2`: Expert Elaboration (~1 page per original D0 heading).
        - `3`: AI Unique Insights (~3 pages per original D0 heading, novel ideas).
    - **Add-on Logic (`build_product_specs_process.txt`):**
        - Phase 1 (Initialization) now validates depth (0-3) and `PRODUCT_SPECS_PREVIOUS_ITERATION_OUTPUT_PATH` (required for D1-D3, critical error if missing). Output file suffixes `_D0` to `_D3` are determined.
        - Phase 2 (Input Processing) is conditional: D0 uses original inputs; D1-D3 use previous iteration's outputs as primary, with original inputs as reference.
        - Phase 3 (Content Generation) has distinct logic for each depth (0-3), detailing how to build upon previous work (for D1-D3) or generate outlines (D0) according to the specified content goals for each level.
    - **Documentation (`MPS_Usage_Guide.md`):** Updated to detail the 0-3 levels, their specific purposes, and the user workflow for managing iterative runs and output folders.
    This provides a more granular, powerful, and well-defined iterative workflow for the `build_product_specs_process.txt` add-on.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (vision for enhanced component modularity via "folder-per-app" architecture).
**MPS Version Referenced:** Current version with multiple add-ons and utils.
**Context/Scenario:** As the number of components (add-ons, utils, and potentially IPC handlers) grows, a flat file structure in their respective directories (`prompts/add_ons/`, `prompts/util/`) becomes less organized and limits the ability for components to have their own supporting files or sub-frameworks.
**Observation/Issue:** The previous flat-file structure for add-ons and utils was not ideal for scalability or for components that might require multiple associated files (e.g., templates, data snippets, complex sub-instructions).
**Suggestion for MPS Refinement (Implemented):**
The framework was refactored to a "folder-per-app" architecture for all components (add-ons, utils, and anticipated IPC handlers):
    - **Core Logic (`prompts/iep/Core_Planning_Instructions.txt`):** Updated to discover and load components based on a new directory structure.
        - It now expects component-specific parent paths like `User_Path_Addons_Dir`, `User_Path_Utils_Dir`, `User_Path_IPC_Dir` to be defined in `[[USER PATH CONFIGURATION]]`.
        - For each component "app" (e.g., `my_addon_app`), it looks for a subdirectory named `my_addon_app` within the relevant parent path.
        - The primary instruction file for the app must be located at `[parent_path]/my_addon_app/my_addon_app.txt`.
        - Internal references (e.g., `Known_Primary_Directive_Addon_Apps`, `Active_Primary_Addon_AppName`) now use these app/folder names.
    - **Component Migration:** All existing add-ons (`task_spawning_addon`, `task_resumption_addon`, `build_product_specs_process`) and utils (`pre_flight_check_user_plan`) were moved into their respective new subdirectories (e.g., `prompts/add_ons/task_spawning_addon/task_spawning_addon.txt`).
    - **New `prompts/ipc/` directory:** Created with a `.gitkeep` file for future IPC components.
    - **Documentation Updates:**
        - `prompts/add_ons/available_addons_manifest.md`: Updated to list add-ons by their app/folder name and describe the new structure.
        - `framework_dev_docs/guides/MPS_Usage_Guide.md`: Comprehensively updated to explain the "folder-per-app" architecture, how users should set up paths, how components are selected, and how developers should structure new components.
    This architectural change significantly improves modularity, organization, and the potential for more complex, self-contained components within the MPS framework.
---
---
**Feedback Entry Date:** 2023-10-27
**Source of Feedback:** User (desire to simplify MPS, new way to handle app-specific data, AI assistance for missing/default configs, specific format for detecting edits).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment and Core Planning Instructions).
**Context/Scenario:** This feedback entry summarizes a series of user requests and design discussions aimed at a comprehensive overhaul of the MPS framework's configuration system and a significant enhancement of the `build_product_specs_process` add-on's iterative capabilities.
**Observation/Issue:**
    1.  The global `[[USER PATH CONFIGURATION]]` in `Master_Prompt_Segment.txt` was becoming unwieldy; app-specific configs were mixed with global ones; parameter precedence and user edit detection were not formalized.
    2.  A more structured and discoverable way to manage parameters for individual add-on apps was needed, including clear defaults and user override mechanisms.
    3.  Users needed assistance in setting mandatory parameters, especially those with placeholder defaults.
**Suggestion for MPS Refinement (Implemented):**
A comprehensive refactoring of the configuration system was performed:
    1.  **Fixed Global System Paths:** Core component discovery paths (for add-ons, utils, IEP, IPC) are now hardcoded in `Core_Planning_Instructions.txt`, simplifying user setup.
    2.  **`[[USER PATH CONFIGURATION]]` Removed from `Master_Prompt_Segment.txt`:** This block was deleted. General output paths (like `Main Iteration Folder`) and other process-specific parameters are now intended to be managed as parameters within the configuration system of the relevant app.
    3.  **App-Specific Configuration System:**
        *   **`USER_appName_CONFIG.txt` Standard:** Defined a standard format for these template/storage files within each app's folder. This includes `[[USER_CONFIG_FOR_appName]]` delimiters, and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), description with default (`# Description: ... Default: ...`), and the value line (`PARAM_NAME: [USER_VALUE_START]value[USER_VALUE_END]`).
        *   **Main Prompt Overrides:** Users can copy the content of a `USER_appName_CONFIG.txt` into their main spawn prompt to provide run-specific overrides.
        *   **Parameter Resolution Precedence:** Implemented in app logic (demonstrated in `build_product_specs_process.txt`): Main prompt block > User-edited `USER_appName_CONFIG.txt` > AcceptedDefault in `USER_appName_CONFIG.txt`.
        *   **AI-Assisted Updates:** If a `Required` parameter with a `PlaceholderDefault` remains unresolved, the app instructs the AI (Jules) to request the value from the user via chat. The AI then updates the on-disk `USER_appName_CONFIG.txt` with the new value and outputs the complete updated config block for the user to transfer to their main spawn prompt.
    4.  **Documentation:** All changes were reflected in `Master_Prompt_Segment.txt` (now v0.4.0), `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, and relevant add-on files (e.g., `build_product_specs_process/USER_build_product_specs_process_CONFIG.txt` and its primary instruction file).
    This new system offers improved modularity, clarity in parameter management, and a better user experience through AI-assisted configuration.
---
---
**Session Summary - 2023-10-27**
**Instance:** Jules
**Key Accomplishments This Session (Finalizing Configuration System & Preparing for "Concept Task" Handoff):**
This session concluded the major refactoring of the MPS framework's configuration system and component architecture. It also prepared for the next phase of work focusing on the "Concept task."

*   **Configuration System Refactoring (Completion & Documentation):**
    *   **Standard for `USER_appName_CONFIG.txt`:** Formalized the standard for these files, including app-specific delimiters (`[[USER_CONFIG_FOR_appName]]`), and per-parameter metadata (`# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`), descriptions, defaults, and `[USER_VALUE_START]...[USER_VALUE_END]` markers. An illustrative example (`USER_exampleApp_CONFIG.txt`) was created as part of this standard definition.
    *   **Applied Standard:** The `build_product_specs_process` add-on's `USER_build_product_specs_process_CONFIG.txt` was updated to fully adhere to this new standard, with all its parameters classified.
    *   **Refined App Logic (`build_product_specs_process.txt` v0.4):** Its parameter resolution logic in Phase 1 was enhanced to parse the new metadata from `USER_..._CONFIG.txt`. The AI-assisted update mechanism (asking user via chat) now correctly triggers only for "Required" parameters with "PlaceholderDefault" values that haven't been overridden. A final validation step ensures all "Required" parameters have valid values before proceeding, halting with an error if not.
    *   **Streamlined `Master_Prompt_Segment.txt` (v0.4.0):** The global `[[USER PATH CONFIGURATION]]` block was definitively removed. Instructions in `[[USER_ADDON_SELECTION]]` were updated to guide users on app-specific configurations via `[[USER_CONFIG_FOR_appName]]` blocks in their main spawn prompt.
    *   **Consolidated `MPS_Usage_Guide.md`:** The guide was thoroughly reviewed and updated to provide a comprehensive and consistent explanation of the new configuration system. This includes: fixed global system paths, the role and format of `USER_appName_CONFIG.txt` files, the user workflow for app configuration (using main prompt blocks or editing `USER_...CONFIG.txt`), the AI's parameter resolution precedence, and how AI-assisted updates work. The "folder-per-app" architecture is also clearly documented.

*   **Next Area of Work (User Directed - For Next Jules Instance):**
    *   The user has directed focus towards refining an existing "Concept task."
    *   This will involve analyzing the "Concept task" and splitting it into approximately three sub-tasks.
    *   A key aspect of this work will be to use the "Concept task" sub-tasks as a practical use case for **exploring and implementing extensions to the 'add-on, util, and process' component concepts** within the MPS framework.
    *   The next Jules instance is tasked with initiating this by requesting detailed requirements from the user regarding the "Concept task" (current state, materials possibly in `Inputs/concept/inputs`, ideas for sub-tasks) and their initial thoughts on evolving the component model.

**State of Deliverables:**
The comprehensive refactoring of the MPS configuration system is complete. This includes the "folder-per-app" architecture, fixed system paths for component discovery, and the new app-specific configuration mechanism using `USER_appName_CONFIG.txt` files and `[[USER_CONFIG_FOR_appName]]` blocks, featuring AI-assisted updates. All core prompts and documentation (`Master_Prompt_Segment.txt`, `Core_Planning_Instructions.txt`, `MPS_Usage_Guide.md`, `available_addons_manifest.md`, and relevant add-ons like `build_product_specs_process`) have been updated to reflect these changes. The framework is significantly more modular, user-friendly, and robust in its configuration management. No new code or prompt changes were made in this specific summarization step. The system is ready for handoff to the next Jules instance to begin work on the "Concept task" and component model evolution.
---
---
**Feedback Entry Date:** 2025-06-02
**Source of Feedback:** User (new directive for next area of work following completion of major configuration system and architectural refactoring).
**MPS Version Referenced:** v0.4.0 (Master Prompt Segment) and related component architecture (folder-per-app, `USER_appName_CONFIG.txt` standard, etc.).
**Context/Scenario:** Planning the next development cycle for the MPS framework after successfully implementing a new configuration system and "folder-per-app" architecture.
**Observation/Issue:**
    1.  An existing internal "Concept task" (details to be provided by the user to the next Jules instance, potentially located in `Inputs/concept/inputs`) is currently monolithic and needs to be broken down into more manageable sub-tasks (approximately three).
    2.  The process of decomposing and implementing these "Concept task" sub-tasks is expected to reveal opportunities or requirements for evolving the framework's current component definitions (add-on, util, process) or potentially introducing new component types or interaction paradigms.
**Suggestion (For Next Jules Instance):**
The next Jules instance should:
    1.  **Gather Detailed Requirements for "Concept Task":** Actively engage with the user to obtain a detailed understanding of the current "Concept task," its goals, any existing materials (potentially in `Inputs/concept/inputs`).
    2.  Collaborate with the user to define the three (approx.) sub-tasks for the "Concept task."
    3.  In parallel, analyze how the requirements of these sub-tasks might necessitate extensions to the existing component concepts (add-on, util, process) or the introduction of new component types/paradigms within the MPS framework.
    4.  Plan and implement both the "Concept task" restructuring and the identified framework component extensions, using the former as a practical test case for the latter.
---
**Session Summary - 2025-06-03**
**Instance:** Jules
**User Feedback/Requests Addressed:**
- User directed a new phase of MPS development focusing on two major initiatives:
    1.  Abstracting the existing `build_product_specs_process` add-on into a more generic `create_research_report` add-on.
    2.  Introducing a new "promptApp" concept to allow users to define and execute multi-stage workflows composed of underlying MPS components.
- Clarified requirements for `create_research_report` parameters, including separate controls for `ITERATIVE_OUTPUT_DEPTH` (D0-D3 style) and `REFERENCE_FOLLOWING_DEPTH` (document link traversal).
- Confirmed that plain text input formats (.txt, .md, web text) are the initial target for document processing.
- Decided that sequential tasks within a `promptApp` will be handled by user re-invocation of new AI instances for each task, simplifying initial framework orchestration.

**Summary of Approved Plan:**
The overall plan involves:
1.  Developing the `create_research_report` add-on (now complete).
2.  Defining the "promptApp" structure and manifest file format.
3.  Updating `Core_Planning_Instructions.txt` and `Master_Prompt_Segment.txt` to support `promptApp` discovery, selection, and execution.
4.  Creating an initial structural example of a `promptApp` (`CompliancePLM`).
5.  Retiring the `build_product_specs_process` add-on files.
6.  Updating all documentation.
7.  Testing the new functionalities.

**Summary of Changes Made This Session (to implement Step 1 of the plan):**
-   **Created `prompts/add_ons/create_research_report/` directory.**
-   **Created `prompts/add_ons/create_research_report/USER_create_research_report_CONFIG.txt`:**
    *   Defined parameters: `PROPOSED_TABLE_OF_CONTENTS`, `GUIDELINES_DOC_PATHS`, `ADDITIONAL_WEB_LINKS_FILE`, `EXEMPLAR_DOCS`, `GENERAL_INPUT_DOCS`, `PRIMARY_INPUT_DOC_PATH`, `ITERATIVE_OUTPUT_DEPTH`, `REFERENCE_FOLLOWING_DEPTH`, `PREVIOUS_ITERATION_OUTPUT_PATH`, `REPORT_BASENAME`, `REPORT_OUTPUT_PATH`.
    *   Included full metadata (Requirement, DefaultType, Description, Default) for each.
-   **Created `prompts/add_ons/create_research_report/create_research_report.txt`:**
    *   Adapted core logic from the former `build_product_specs_process.txt`.
    *   Updated to use the new parameter set for generic report generation.
    *   Removed product-specification-specific and detailed codebase review logic.
    *   Maintained the 6-phase structure and parameter resolution mechanisms.
-   **Updated `prompts/add_ons/available_addons_manifest.md`:**
    *   Added entry for `create_research_report`.
    *   Removed entry for `build_product_specs_process`.

**State of Deliverables:**
- The `create_research_report` add-on (v0.1) is developed and its configuration file is in place.
- The add-on manifest is updated.
- The `build_product_specs_process` add-on is effectively replaced by this new add-on (physical file deletion is pending a later plan step).
- Plan step 1 is complete.

**Next Steps (This Jules instance/session):**
- Proceed to Plan Step 2: "Define 'promptApp' Structure and Manifest." This involves designing the manifest file format that will define how `promptApps` (like the `CompliancePLM` example) are structured and how their tasks map to MPS components
