# Promptu Framework Usage Guide

## 1. Introduction
This guide explains how to use the Promptu Framework, focusing on its core configuration files: `pre_promptu.txt` and `post_promptu.txt`. The framework (based on the former MPS Framework, now refactored) enhances modularity, clarity, and workflow orchestration.

**Key Architectural Features:**
*   **Core Instructions (`/promptu/core/core_planning_instructions.txt`):** Contains the foundational logic for the Planning AI, including component discovery, loading, and parameter processing. This file is included by `post_promptu.txt`.
*   **Pre-Processing Utilities (`pre_promptu.txt`):** An optional file for selecting and configuring utilities (e.g., `promptimizer`) to run *before* the main project request in `post_promptu.txt`. Useful for preparatory tasks.
*   **Main Project Processing (`post_promptu.txt`):** The primary user-configured file. Defines project goal, selects promptApps or standalone add-ons, and configures parameters.
*   **"Folder-per-App" Architecture:** Components (PromptApps, Add-ons, Utils) are in subdirectories (e.g., `promptu/apps/AppName/`, `promptu/add_ons/addonName/`, `promptu/util/utilName/`). Utilities can also be single files (e.g. `promptu/util/util_name.txt`). Each component has primary instruction files and `user_component_name_config.txt` templates if configurable.
*   **PromptApp Workflow Orchestration:** Define, select, and execute multi-stage workflows (promptApps). Each promptApp has a manifest (`app_name_manifest.json`).
*   **Fixed System Paths:** Component discovery paths are hardcoded in `core_planning_instructions.txt` (e.g., `promptu/apps/`, `promptu/add_ons/`, `promptu/util/`).
*   **Utility Discovery:** Utilities are found in `promptu/util/` either as `utility_name.txt` or `utility_name/utility_name.txt`. If both exist, the subdirectory version is prioritized.
*   **Component-Specific Configurations:** Managed via:
    1.  `user_component_name_config.txt` (template/persistent storage).
    2.  `[[USER_CONFIG_FOR_componentName]]` blocks in `post_promptu.txt` (run-specific overrides).
    3.  `defaultConfigOverrides` in promptApp manifest (task-specific overrides).
*   **Optional Primary Functionality:** The primary mode of operation can be a selected promptApp (configured in `post_promptu.txt`). Alternatively, standalone add-ons can define the AI's main task.

This guide details how to set up your project and interact with the Promptu Framework.

## 2. Core Framework Components & User Preparations

This section outlines key pieces of the Promptu Framework and what you, the user, will typically prepare or copy.

*   **`/promptu/pre_promptu.txt` (Framework Source - Optional):**
    *   This **template** allows you to run utilities *before* your main project request in `post_promptu.txt` is processed.
    *   Copy its content to `target_repo/promptu/pre_promptu.txt`.
    *   Configure by selecting utilities in the `[[USER_UTIL_SELECTION]]` block and providing any necessary `[[USER_CONFIG_FOR_utilityName]]` blocks.
    *   To use it, you first invoke the AI with a directive pointing to this file (e.g., `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/pre_promptu.txt]]`). The AI processes the selected utilities. Then, for your main project, you'll make a separate call pointing to `post_promptu.txt`.

*   **`/promptu/post_promptu.txt` (Framework Source - Main):**
    *   This file serves as the **template** for your main project instruction file (formerly `Master_Prompt_Segment.txt`).
    *   Copy its content to `target_repo/promptu/my_project_post_promptu.txt` (or similar).
    *   Add your high-level project request at the very beginning of this new file.
    *   Edit user configuration blocks (`[[USER_APP_SELECTION]]`, `[[USER_ADDON_SELECTION]]`, `[[USER_FRAMEWORK_SETTINGS]]`, etc.).
    *   It includes `core_planning_instructions.txt` to handle the logic.

*   **`/promptu/core/core_planning_instructions.txt` (Framework Source):**
    *   Contains foundational instructions for the Planning AI for processing `post_promptu.txt`. It dictates component discovery, loading, parsing user selections, and execution logic.
    *   **Action Required:** Copy to `target_repo/promptu/core/core_planning_instructions.txt`.

*   **`promptApp` Definitions (`promptu/apps/`)**
    *   `promptu/apps/` is the fixed system path for promptApp definitions.
    *   Each promptApp is in its own subdirectory (e.g., `promptu/apps/CompliancePLM/`).
    *   Core is its manifest file, `[app_name]_manifest.json` (e.g., `compliance_plm_manifest.json`). Refer to `framework_dev_docs/guides/prompt_app_manifest_guide.md`.
    *   App-specific components can be in `promptu/apps/AppName/components/`.
    *   **Action Required:** Copy desired `promptApp` directories (e.g., `framework_source/promptu/apps/CompliancePLM/`) to `target_repo/promptu/apps/CompliancePLM/`.

*   **`/promptu/core/base_iep.txt` (Framework Source):**
    *   Template for the Information Exchange Protocol.
    *   **Action Required:** Copy to `target_repo/promptu/core/base_iep.txt`.

*   **Component "Apps" (Add-ons, Utils - Framework Source):**
    *   Located in fixed base directories: `promptu/add_ons/` and `promptu/util/`.
    *   Add-ons are in subdirectories (e.g., `promptu/add_ons/create_research_report/`). Utilities can be in subdirectories (`promptu/util/some_util/some_util.txt`) or direct files (`promptu/util/another_util.txt`).
    *   Subdirectory components have a primary instruction file `[componentName]/[componentName].txt`. Direct util files are themselves the instruction file.
    *   Configurable components use a `user_component_name_config.txt` file (e.g., `user_create_research_report_config.txt`).
    *   **Action Required:** Copy desired components from `promptu/add_ons/` or `promptu/util/` to your target repository.
    *   Refer to `promptu/add_ons/available_add_ons_manifest.md` for available add-ons.

*   **Your Project-Specific Request:** The initial text in your main `post_promptu.txt` file that describes your project goal.

*   **App-Specific Configuration Blocks (User Created in `post_promptu.txt`):**
    *   For components that require configuration, you will embed `[[USER_CONFIG_FOR_componentName]]...[[END_USER_CONFIG_FOR_componentName]]` blocks in your `post_promptu.txt` file. This is detailed in Section 3.

## 3. Setting up Your Target Repository & Crafting Instruction Files

**A. Prepare Your Target Repository:**
1.  Ensure project-specific input documents are present.
2.  Create directory structure in your target repo (if not already existing):
    *   `promptu/` (root for these components)
    *   `promptu/apps/`
    *   `promptu/core/`
    *   `promptu/add_ons/`
    *   `promptu/util/`
    *   `promptu/ipc/` (add `.gitkeep` if empty)
3.  Copy core files:
    *   Framework's `/promptu/core/core_planning_instructions.txt` to `target_repo/promptu/core/core_planning_instructions.txt`.
    *   Framework's `/promptu/core/base_iep.txt` to `target_repo/promptu/core/base_iep.txt`.
    *   Framework's `/promptu/pre_promptu.txt` (template) to `target_repo/promptu/pre_promptu.txt` (if using).
    *   Framework's `/promptu/post_promptu.txt` (template) to `target_repo/promptu/your_project_post_promptu.txt`.
4.  Copy desired `promptApp` folders (e.g., `CompliancePLM`) to `target_repo/promptu/apps/`.
5.  Copy desired Add-ons (entire folders) to `target_repo/promptu/add_ons/`.
6.  Copy desired Utils (folders or `.txt` files) to `target_repo/promptu/util/`.

**B. Preparing Instruction Files & Invoking the Promptu Framework**

You'll typically prepare `pre_promptu.txt` (optional) and your project-specific version of `post_promptu.txt`.

    **Step 1: Prepare `pre_promptu.txt` (Optional, e.g., `target_repo/promptu/pre_promptu.txt`)**
    *   Use for pre-processing tasks. Copy from the framework's `/promptu/pre_promptu.txt` template.
    *   Edit `[[USER_UTIL_SELECTION]]` to select utilities.
    *   Add `[[USER_CONFIG_FOR_utilityName]]` blocks if needed.
    *   Invoke with: `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/pre_promptu.txt]]`
    *   This is a separate AI call from your main project request.

    **Step 2: Prepare Main Project Instruction File (e.g., `target_repo/promptu/my_project_post_promptu.txt`)**

    This is your copy of the `post_promptu.txt` template.
    1.  **Add Project Request:** At the **top**, add your project request, ideally wrapped in `[[USER_PROJECT_REQUEST]]` and `[[END_USER_PROJECT_REQUEST]]` tags.
        ```markdown
        [[USER_PROJECT_REQUEST]]
        My project is to design a modular hydroponics system.
        [[END_USER_PROJECT_REQUEST]]
        ```
    3.  **Edit Configuration Blocks:** Within the template content, edit:
        *   `[[USER_APP_SELECTION]]`
        *   `[[USER_ADDON_SELECTION]]`
        *   `[[USER_FRAMEWORK_SETTINGS]]` (e.g., for `Full_Handoff_Notes_Logging_Enabled`)
        *   `[[USER_promptAPP_NAME_CONFIGURATION]]` (if promptApp selected)
        *   `[[USER_CONFIG_FOR_componentName]]` blocks for parameter overrides.

    **Step 3: Invoke Main Framework**
    *   Use directive: `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/my_project_post_promptu.txt]]`

    **AI Processing of `post_promptu.txt`:**
    1.  Fetches your file.
    2.  Pre-directive text (if any) is wrapped in `[[USER_PROJECT_REQUEST]]` and prepended.
    3.  `core_planning_instructions.txt` (included by `post_promptu.txt`) processes the combined content, extracts the project goal, and handles component selections/configurations.

        **3.B.1. Path Configurations**
        *   **Fixed System Paths:** Used by `core_planning_instructions.txt` for component discovery (e.g., `promptu/apps/`).
        *   **Component-Specific Paths:** Managed via parameters in `user_..._config.txt` files, overridden by `[[USER_CONFIG_FOR_componentName]]` blocks in your `post_promptu.txt`.

        **3.B.2. Configuring Workflows (in `post_promptu.txt`)**

        *   **i. `promptApp` Workflow:**
            *   **Selection (`[[USER_APP_SELECTION]]`):** E.g., `[x] CompliancePLM_App`. AI seeks `promptu/apps/[AppName]/[app_name]_manifest.json`.
            *   **Task Config (`[[USER_promptAPP_NAME_CONFIGURATION]]`):** Check `[x]` for tasks. See `prompt_app_manifest_guide.md`.
            *   **Component Config:** Parameter precedence:
                    1.  `[[USER_CONFIG_FOR_componentName]]` in `post_promptu.txt`.
                    2.  User-edits in component's `user_..._config.txt`.
                    3.  `defaultConfigOverrides` in promptApp manifest.
                    4.  Defaults in `user_..._config.txt`.
                    5.  AI query if needed.
            *   **Component Search:** App-local first, then `promptu/add_ons/`, then `promptu/util/`.
            *   **Iteration:** Update `post_promptu.txt` and re-invoke for next task.

        *   **ii. Standalone Add-ons (if no promptApp):**
            *   **Selection (`[[USER_ADDON_SELECTION]]`):** E.g., `[x] create_research_report`. AI seeks `promptu/add_ons/[addonName]/[addonName].txt`.
            *   **Parameter Config:** Similar precedence as promptApp components.
                *   Configure e.g., `task_spawning_addon` via `[[USER_CONFIG_FOR_task_spawning_addon]]` or by editing `promptu/add_ons/task_spawning_addon/user_task_spawning_addon_config.txt`.

**D. Invoking the Planning AI:** (Covered by 3.B Steps 1 & 3)

**E. Understanding the Planning AI's Process (for `post_promptu.txt`):**
The Planning AI, upon receiving `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/your_post_promptu_file.txt]]`:
1.  Prepares content as described in 3.B Step 3.
2.  Processes `core_planning_instructions.txt` (included by `post_promptu.txt`):
    *   Extracts `User_High_Level_Project_Goal`.
    *   Discovers components (promptApps, add-ons, utils, noting the dual discovery for utils).
    *   Parses `[[USER_APP_SELECTION]]`, `[[USER_FRAMEWORK_SETTINGS]]`.
    *   If `promptApp` selected: Parses `[[USER_promptAppName_CONFIGURATION]]` and executes tasks.
    *   If no `promptApp`: Parses `[[USER_ADDON_SELECTION]]` and executes add-ons.
3.  Reads `base_iep.txt` etc., as needed.

    **3.C. Directly Invoking Utilities**

    The `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM ...]]` directive can directly invoke utility components.
    While `pre_promptu.txt` is often better for this, direct invocation is an option:
    `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/util/utility_name.txt]]` (or `/promptu/util/utility_name/utility_name.txt`)
    Follow this with the content the utility should process.

    **Example: `promptimizer` direct call (less common than via `pre_promptu.txt`):**
    `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/util/promptimizer/promptimizer.txt]]`
    `My project idea: universal translator for squirrel languages.`
    The AI loads `promptimizer.txt`, prepends its content to your idea, then processes.

---
## 4. Developing New Promptu Components

Guidance for extending the Promptu Framework.

*   **Developing New `promptApps`**
    1.  Create `promptu/apps/MyNewWorkflow/`.
    2.  Add `my_new_workflow_manifest.json` (see `prompt_app_manifest_guide.md`).
    3.  Store app-specific components in `promptu/apps/MyNewWorkflow/components/` or as flat files in the app's root. Update manifest `componentName` accordingly.

*   **Developing Add-ons or Utilities**
    1.  **Location:**
        *   Add-ons: `promptu/add_ons/my_new_addon/`
        *   Utils: `promptu/util/my_new_util/` or `promptu/util/my_new_util.txt`.
    2.  **Primary Instruction File:** For folder-based components, this is `[name].txt` inside the folder. For direct utils, the file itself is the instruction.
    3.  **Configuration File (`user_component_name_config.txt`):** If configurable, create this file (e.g., `user_my_new_addon_config.txt`).
    4.  **Supporting Files:** Place in component's folder if applicable.
    5.  **Add-on Manifest:** Document new add-ons in `promptu/add_ons/available_add_ons_manifest.md`.
---

## 5. Framework Operational Notes

Notes on logging, history, and file management.

**A. Handoff Notes and Archival (`framework_dev_docs/meta/handoff_notes.md` & `framework_dev_docs/meta/archive/handoff_notes/`)**

The Promptu framework employs an automated system for managing and archiving handoff notes to ensure stability and maintain a comprehensive history.

*   **Automated Archival Process:**
    *   **At Session Start (Fallback for Previous Session):** If `framework_dev_docs/meta/handoff_notes.md` contains content from a previous, unterminated session, the AI will automatically:
        1.  Create a new timestamped archive file (e.g., `handoff_notes_YYYYMMDDHHMMSS.md`) in `framework_dev_docs/meta/archive/handoff_notes/`. (The AI will create these directories if they don't exist in the workspace).
        2.  Copy the stale content from `handoff_notes.md` to this new archive file.
        3.  Empty `handoff_notes.md` in the workspace.
    *   **At Normal Session End (User-Requested Termination):** Before appending the current session's summary, the AI will:
        1.  Create a new timestamped archive file in `framework_dev_docs/meta/archive/handoff_notes/`.
        2.  Copy any content from the current `handoff_notes.md` (which would typically be notes from the start of the session if it's a resumed session, or empty if it's a clean start) to this new archive file.
        3.  Empty `handoff_notes.md` in the workspace.
    *   The AI will then append its summary for the *current* session to the now-empty `framework_dev_docs/meta/handoff_notes.md`. This means `handoff_notes.md` should ideally only contain the notes from the most recent session when a new session begins.

*   **Historical Context:** The AI is instructed to read all files from `framework_dev_docs/meta/archive/handoff_notes/*.*` at the start of a session to gain historical context.

*   **Verbosity Control:** You can still control the detail level of the AI's session summary appended to `handoff_notes.md` using the `[[USER_FRAMEWORK_SETTINGS]]` block in `post_promptu.txt`:
    *   `[ ] Full_Handoff_Notes_Logging_Enabled`:
        *   `[x]` (Checked): Detailed session summaries.
        *   `[ ]` (Unchecked - default): Concise summaries.

*   **"Performance Feedback Log" Status:** This specific section was removed from active `handoff_notes.md` updates. The archival process maintains all historical data within the timestamped files.

**B. Legacy Handoff Notes Archive File**

*   An older, potentially very large, manual archive file might exist at `framework_dev_docs/meta/handoff_notes_full_archive_20250602.md`.
*   The AI (Jules) is instructed by `core_planning_instructions.txt` **not to read this specific legacy archive file** during its normal context gathering from the new `framework_dev_docs/meta/archive/handoff_notes/` directory.
*   Access to this legacy file should only occur if deemed critical for a very specific historical request and with explicit user permission.
*   The primary source for ongoing and future historical handoff notes is the directory: `framework_dev_docs/meta/archive/handoff_notes/`.

**C. File Operations Guideline ("File Operations Restriction")**

The Promptu framework includes a specific guideline on how the AI (Jules) handles file system operations:

*   **Allowed Operations (Workspace Preparation for Commit):**
    *   Jules **IS ALLOWED** to perform **create, read, and modify** operations on files directly within its workspace. These actions are considered necessary for preparing content that will be included in a commit.

*   **Restricted Operations (User Approval Needed):**
    *   Jules **MUST AVOID** direct `copy` and `move` file system commands/operations without explicit user approval for each specific instance.
    *   Direct deletion of files by Jules also requires user confirmation for each instance.

*   **Emulating `copy`:** To replicate content to a new location (achieve a copy-like effect), Jules will:
    1.  Create a new file in the target location within the workspace.
    2.  Read the full content of the source file.
    3.  Write this content into the newly created file.
    4.  Save the new file in the workspace.

*   **Emulating `move`:** To replicate content to a new location with the intent of removing the source, Jules will:
    1.  Follow the same 'create new, read source, write to new, save new' method as for emulating `copy`.
    2.  If the source file is then intended to be deleted, Jules **MUST recommend** this deletion to the user for their explicit confirmation and action.

*   **Purpose:** This guideline ensures that while Jules can autonomously prepare content and structure for commits, the user retains oversight and control over potentially destructive or complex file system manipulations like direct moves, copies, or deletions.

The Promptu Framework, with its `pre_promptu.txt` and `post_promptu.txt` structure, offers enhanced modularity and flexibility for AI-driven project planning and execution.
