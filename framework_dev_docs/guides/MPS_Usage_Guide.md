# MPS Usage Guide (for Canonical Framework using `prompts/Master_Prompt_Segment.txt`)

## 1. Introduction
This guide explains how to use the canonical `Master_Prompt_Segment.txt` (located at `/prompts/Master_Prompt_Segment.txt` in this framework's development repository) to instruct a Planning AI. The `Master_Prompt_Segment.txt` (currently v0.5.0) has been significantly refactored to enhance modularity, clarity in configurations, and to introduce "promptApp" workflow orchestration.

Key architectural features include:
*   **Core Instructions (`/prompts/iep/Core_Planning_Instructions.txt`):** Contains the foundational logic for the Planning AI, including component discovery, loading, and parameter processing.
*   **"Folder-per-App" Architecture:** Components like PromptApps, Add-ons, and Utils are self-contained within their own subdirectories (e.g., `prompts/apps/AppName/`, `prompts/add_ons/addonName/`), each with primary instruction files and any supporting files, including `USER_componentName_CONFIG.txt` templates for parameters where applicable.
*   **PromptApp Workflow Orchestration:** A new system allowing users to define, select, and execute multi-stage workflows (promptApps) composed of various MPS components. Each promptApp is defined by a manifest file (`AppName_manifest.json`).
*   **Fixed System Paths:** Base paths for discovering framework components (promptApps, add-ons, utils, IEP, IPC) are now hardcoded within `Core_Planning_Instructions.txt` (e.g., `prompts/apps/`, `prompts/add_ons/`, `prompts/util/`), simplifying initial setup.
*   **Component-Specific Configurations:** User-configurable parameters for components (whether run standalone or as part of a promptApp) are managed via:
    1.  Template/persistent storage files: `USER_componentName_CONFIG.txt` within each component's folder (if configurable).
    2.  Run-specific overrides: `[[USER_CONFIG_FOR_componentName]]...[[END_USER_CONFIG_FOR_componentName]]` blocks directly in the user's main spawn prompt.
    3.  PromptApp task-specific overrides: Defined in the `defaultConfigOverrides` section of a task within a promptApp's manifest.
*   **Optional Primary Functionality:** The primary mode of operation can be a selected promptApp. Alternatively, if no promptApp is chosen, standalone add-on apps like `task_spawning_addon` (for project planning) or `create_research_report` (for generating research reports) can be selected to define the AI's main task.

This guide details how to set up your project and interact with this refined MPS framework.

## 2. Core Framework Components & User Preparations

This section outlines the key pieces of the MPS framework and what you, the user, will typically prepare or copy into your target repository.

*   **`/prompts/Master_Prompt_Segment.txt` (Framework Source):**
    *   This file serves as the **template** for your main project instruction file. You will typically copy its content to a new file in your target repository (e.g., `target_repo/prompts/my_project_instructions.txt`), add your high-level project request at the very beginning of this new file, and then edit the user configuration blocks within it (`[[USER_APP_SELECTION]]`, `[[USER_ADDON_SELECTION]]`, `[[USER_FRAMEWORK_SETTINGS]]` etc.). You will then instruct the AI to load and process this file using a directive.
    *   It now includes `[[USER_APP_SELECTION]]` for choosing a promptApp, `[[USER_ADDON_SELECTION]]` for choosing standalone add-on apps, `[[USER_FRAMEWORK_SETTINGS]]` for framework-level configurations, placeholders for `[[USER_promptAPP_NAME_CONFIGURATION]]` examples, and an `[[INCLUDE]]` directive for `Core_Planning_Instructions.txt`.

*   **`/prompts/iep/Core_Planning_Instructions.txt` (Framework Source):**
    *   Contains foundational instructions for the Planning AI. It dictates component discovery (promptApps, add-ons, utils using fixed paths), loading of primary instruction files, parsing of user selections (including framework settings), discovery of app-specific configuration blocks, and conditional execution logic for promptApps or standalone add-ons.
    *   **Action Required:** You **must copy** this file to `target_repo/prompts/iep/Core_Planning_Instructions.txt`.

*   **New Subsection: `promptApp` Definitions (`prompts/apps/`)**
    *   `prompts/apps/` is a new fixed system path where promptApp definitions are stored.
    *   Each promptApp resides in its own subdirectory (e.g., `prompts/apps/CompliancePLM/`).
    *   The core of a promptApp is its manifest file, named `[AppName]_manifest.json` (e.g., `CompliancePLM_manifest.json`), which defines the app's display name, description, phases, and tasks. Each task specifies an underlying MPS component to execute and can include default configuration overrides for that component within the context of the promptApp. Refer to `framework_dev_docs/guides/PromptApp_Manifest_Guide.md` for details.
    *   A promptApp's subdirectory can also contain app-specific components (e.g., specialized utility scripts or unique instruction files for tasks) typically in a subfolder like `prompts/apps/AppName/components/`. The component resolution logic (see Section 3.B.2.i) will search these locations.
    *   **Action Required:** If you intend to use a specific `promptApp` (e.g., `CompliancePLM`), ensure its entire directory (e.g., `framework_source/prompts/apps/CompliancePLM/`) is copied to `target_repo/prompts/apps/CompliancePLM/`.

*   **`/prompts/iep/Base_IEP.txt` (Framework Source):**
    *   Template for the Information Exchange Protocol.
    *   **Action Required:** Copy to `target_repo/prompts/iep/Base_IEP.txt`.

*   **Component "Apps" (Add-ons, Utils - Framework Source):**
    *   Located in fixed base directories: `prompts/add_ons/` and `prompts/util/`.
    *   Each component resides in its own subdirectory (e.g., `prompts/add_ons/create_research_report/`).
    *   Each component folder must contain a primary instruction file named `[componentName]/[componentName].txt`.
    *   Configurable components **must** also contain a `USER_componentName_CONFIG.txt` file defining their parameters, defaults, and metadata.
    *   **Action Required:** Copy the entire folder of each desired add-on or util component from the framework (e.g., `/prompts/add_ons/create_research_report/`) to the corresponding location in your target repository (e.g., `target_repo/prompts/add_ons/create_research_report/`).
    *   Refer to `/prompts/add_ons/available_addons_manifest.md` for details on available add-on apps.

*   **Your Project-Specific Request:** The initial text in your main spawn prompt file that describes your project goal.

*   **App-Specific Configuration Blocks (User Created in Main Spawn Prompt):**
    *   For components (add-ons, utils, or tasks within PromptApps) that require configuration, you will typically copy parameter templates from their `USER_componentName_CONFIG.txt` file and embed them as `[[USER_CONFIG_FOR_componentName]]...[[END_USER_CONFIG_FOR_componentName]]` blocks in your main spawn prompt. This is detailed in Section 3.B.2.

## 3. Setting up Your Target Repository & Crafting the Main Spawn Prompt

**A. Prepare Your Target Repository:**
1.  Ensure all your project-specific input documents are present.
2.  Create directory structure in your target repo:
    *   `prompts/`
    *   `prompts/apps/` (New: for promptApp definitions)
    *   `prompts/iep/`
    *   `prompts/add_ons/`
    *   `prompts/util/`
    *   `prompts/ipc/` (create this directory, add a `.gitkeep` if empty)
3.  Copy core files:
    *   Framework's `/prompts/iep/Core_Planning_Instructions.txt` to `target_repo/prompts/iep/Core_Planning_Instructions.txt`.
    *   Framework's `/prompts/iep/Base_IEP.txt` to `target_repo/prompts/iep/Base_IEP.txt`.
4.  Copy desired `promptApp` folders:
    *   If using a promptApp (e.g., `CompliancePLM`), copy its entire folder from `framework_source/prompts/apps/CompliancePLM/` to `target_repo/prompts/apps/CompliancePLM/`.
5.  Copy desired Component App Folders (Add-ons, Utils):
    *   For each add-on app (e.g., `task_spawning_addon`, `create_research_report`), copy its entire folder from the framework's `prompts/add_ons/` to `target_repo/prompts/add_ons/`.
    *   For each util app, copy its entire folder from `prompts/util/` to `target_repo/prompts/util/`.

**B. Preparing Your Project Instruction File & Invoking the MPS Framework**

The way you interact with the MPS framework has been streamlined. Instead of pasting large blocks of text into a single "spawn prompt" for the AI, you will now:
1.  Prepare a dedicated "Project Instruction File" in your repository.
2.  Instruct the AI to process this file using a simple directive.

    **Step 1: Prepare Your Project Instruction File (e.g., `target_repo/prompts/my_project_instructions.txt`)**

    This file becomes the central place for your project's high-level goal and all MPS configurations.
    1.  **Copy Template:** Start by copying the entire content of the canonical `prompts/Master_Prompt_Segment.txt` (from the MPS framework source) into your new project instruction file (e.g., `target_repo/prompts/my_project_instructions.txt`).
    2.  **Add Your Project Request:** At the **very top** of this new file (before any `[[...]]` blocks from the template), write your detailed, high-level project request. It is **highly recommended** to wrap this request in `[[USER_PROJECT_REQUEST]]` and `[[END_USER_PROJECT_REQUEST]]` tags for robust parsing by `Core_Planning_Instructions.txt` (Section I.A). For example:
        ```markdown
        [[USER_PROJECT_REQUEST]]
        My project is to design and plan for a new automated hydroponics system
        for urban farming, focusing on modularity and water efficiency.
        [[END_USER_PROJECT_REQUEST]]
        ```
    3.  **Edit MPS Configuration Blocks:** Within the content you pasted from `Master_Prompt_Segment.txt` (following your project request), you will find and edit the following blocks:
        *   `[[USER_APP_SELECTION]]`: If you want to run a predefined workflow, select one promptApp here.
        *   `[[USER_ADDON_SELECTION]]`: If not using a promptApp, or if your chosen promptApp allows for supplementary inheritable add-ons, make your selections here.
        *   `[[USER_FRAMEWORK_SETTINGS]]`: This block allows you to control certain framework-level behaviors. Currently, it contains:
            *   `[ ] Full_Handoff_Notes_Logging_Enabled`: Check this box if you want the AI (Jules) to record detailed session summaries in `framework_dev_docs/meta/HANDOFF_NOTES.md`. If unchecked (default), Jules will record concise summaries focusing on major architectural changes, key decisions, and deliverables.
        *   `[[USER_promptAPP_NAME_CONFIGURATION]]` (if a promptApp was selected): You must create or uncomment and edit this block (e.g., `[[USER_CompliancePLM_App_CONFIGURATION]]`) to check `[x]` which tasks from the promptApp's manifest you want to execute. Refer to the examples in `Master_Prompt_Segment.txt` or the `PromptApp_Manifest_Guide.md`.
        *   `[[USER_CONFIG_FOR_componentName]]` blocks: For any component (whether a standalone add-on, or an underlying component of a selected promptApp task) that requires specific parameter overrides, you will create or uncomment and edit its configuration block (e.g., `[[USER_CONFIG_FOR_create_research_report]]`). These blocks provide the parameters for the respective components.

    Your `my_project_instructions.txt` file now contains your project goal and all necessary MPS configurations in one place.

    **Step 2: Invoke the MPS Framework via Directive**

    Once your project instruction file is prepared and saved in your repository, you instruct the Planning AI to use it with a simple directive.
    *   **Recommended Directive:**
        `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /path/to/your/project_instruction_file.txt]]`
    *   **Example:** If your file is at `prompts/my_project_instructions.txt` within your repository, the directive would be:
        `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /prompts/my_project_instructions.txt]]`
    *   You provide this directive as your input to the AI planning system.

    **How the AI Processes the Directive and User Request:**

    When the AI system (e.g., Jules) receives this directive:
    1.  It fetches the content of the file specified in the `FROM` path (e.g., `my_project_instructions.txt`).
    2.  **Internally, it will take any actual textual request that might have preceded the `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM ...]]` directive in your initial interaction with the AI system (if you provided any separate text before the directive itself) and wrap this text with `[[USER_PROJECT_REQUEST]]` and `[[END_USER_PROJECT_REQUEST]]` tags.**
    3.  **This newly formed `[[USER_PROJECT_REQUEST]]` block is then PREPENDED to the content fetched from the file specified in the directive.** (If your project instruction file *also* starts with a `[[USER_PROJECT_REQUEST]]` block, the one from the pre-directive text takes precedence).
    4.  The combined content (i.e., User Request Block from pre-directive text or top of file + Content of your specified MPS file from the `FROM` path) is then processed according to the logic in `prompts/iep/Core_Planning_Instructions.txt` (which is included by `Master_Prompt_Segment.txt`).
    5.  `Core_Planning_Instructions.txt` (Section I.A) is designed to first look for and extract the content within the `[[USER_PROJECT_REQUEST]]...[[END_USER_PROJECT_REQUEST]]` block (now expected at the beginning of the combined content) to determine the `User_High_Level_Project_Goal`. If this block isn't found or is empty, the `User_High_Level_Project_Goal` may be considered undefined, and components will need to rely on other configuration means or seek clarification.

        **3.B.1. Understanding Path Configurations in the New System**
        Path management relies on:
        *   **Fixed System Paths:** The Planning AI, guided by `Core_Planning_Instructions.txt`, uses fixed relative paths (from the repository root) to discover core components (promptApps in `prompts/apps/`, add-ons in `prompts/add_ons/`, etc.). These are internal to the framework.
        *   **Component-Specific Path Parameters:** Paths that control where a component reads its specific inputs or writes its outputs (e.g., `PRIMARY_INPUT_DOC_PATH`, `REPORT_OUTPUT_PATH` for the `create_research_report` component) are defined as parameters *within that component's own configuration system*. You manage these via `[[USER_CONFIG_FOR_componentName]]` blocks within your project instruction file.

        **3.B.2. Configuring and Running MPS Workflows**
        Within your prepared project instruction file, you direct the MPS framework:

        *   **i. Selecting and Configuring a `promptApp` (Primary Workflow Method):**
            *   **Selection (`[[USER_APP_SELECTION]]` Block):**
                *   Select **at most one** promptApp by its folder name (e.g., `[x] CompliancePLM_App`). The AI looks for `prompts/apps/[AppName]/[AppName]_manifest.json`.
            *   **Task Configuration (`[[USER_promptAPP_NAME_CONFIGURATION]]` Block):**
                *   If a promptApp is selected, you **must** provide this block, named after the app (e.g., `[[USER_CompliancePLM_App_CONFIGURATION]]`).
                *   Inside, check `[x]` next to tasks from the app's manifest that you want to run for the current session.
                *   Refer to `framework_dev_docs/guides/PromptApp_Manifest_Guide.md`.
            *   **Underlying Component Configuration:** Each checked task maps to an MPS component. Its parameters are resolved with precedence:
                    1.  `[[USER_CONFIG_FOR_componentName]]` block in your project instruction file (highest).
                    2.  User-edited values in the component's `USER_componentName_CONFIG.txt` file (in `target_repo`).
                    3.  `defaultConfigOverrides` from the `promptApp` manifest for that task.
                    4.  Accepted defaults from the component's `USER_componentName_CONFIG.txt`.
                    5.  AI-assisted user query for required/placeholder parameters.
                *   Provide `[[USER_CONFIG_FOR_componentName]]` blocks if manifest/component defaults are insufficient.
            *   **Component Resolution Search Order (App-Specific First):**
                1.  `prompts/apps/[SelectedAppName]/[componentName].txt`
                2.  `prompts/apps/[SelectedAppName]/[componentName]/[componentName].txt`
                3.  `prompts/add_ons/[componentName]/[componentName].txt`
                4.  `prompts/util/[componentName]/[componentName].txt`
            *   **Iteration and Sequential Execution:** Iterable tasks prompt "repeat or move on." Choosing "move on" halts the AI, requiring you to update the `[[USER_promptAPP_NAME_CONFIGURATION]]` block for the next task and re-invoke the AI with the *same directive pointing to your updated project instruction file*.

        *   **ii. Selecting and Configuring Standalone Add-on Apps (if no promptApp is selected):**
            *   **Selection (`[[USER_ADDON_SELECTION]]` Block):**
                *   List add-on folder names (e.g., `[x] create_research_report`). The AI looks for `prompts/add_ons/[addonName]/[addonName].txt`.
                *   Order matters for inheritable add-ons if a primary add-on like `task_spawning_addon` is used.
            *   **Parameter Configuration:** Parameters for standalone add-ons are resolved with precedence:
                    1.  `[[USER_CONFIG_FOR_componentName]]` block in your project instruction file.
                    2.  User-edited value in its `USER_componentName_CONFIG.txt` (in `target_repo`).
                    3.  Accepted default from its `USER_componentName_CONFIG.txt`.
                    4.  AI-Assisted Update for required/placeholder parameters.

**D. Invoke the Planning AI:** (This section is now covered by 3.B Step 2)

**E. Understanding the Planning AI's Process:**
The Planning AI, upon receiving the `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM ...]]` directive, will:
1.  Fetch and prepare the content of your specified project instruction file, prepending any user request text that preceded the directive (wrapped in `[[USER_PROJECT_REQUEST]]` tags).
2.  Process Core Instructions (from `/prompts/iep/Core_Planning_Instructions.txt` as included by the loaded file's MPS template):
    *   Extract `User_High_Level_Project_Goal` from the `[[USER_PROJECT_REQUEST]]` block (expected at the beginning of the combined content).
    *   Discover available promptApp manifests, add-on apps, and util apps.
    *   Parse `[[USER_APP_SELECTION]]` (from the loaded file).
    *   Parse `[[USER_FRAMEWORK_SETTINGS]]` (from the loaded file) to determine handoff note verbosity.
    *   If a `promptApp` is selected:
        *   Parse its `[[USER_promptAppName_CONFIGURATION]]` block (from the loaded file) to determine selected tasks.
        *   Proceed to execute the first selected task, loading its component and configurations as per defined search order and precedence.
    *   If no `promptApp` is selected:
        *   Parse `[[USER_ADDON_SELECTION]]` (from the loaded file).
        *   Load selected add-ons and discover their `[[USER_CONFIG_FOR_componentName]]` blocks (from the loaded file).
        *   Execute the primary add-on or other selected add-ons.
3.  Read other supporting files (like Base IEP) as needed.

    **3.C. Directly Invoking Utilities with the MPS Directive**

    The `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM ...]]` directive is versatile and can also be used to directly invoke specific utility components, such as the `promptimizer` utility. This is useful for focused tasks that don't require the full promptApp or add-on selection overhead.

    The general usage pattern is:
    ```
    [[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /path/to/utility_file.txt]]
    The text or content that the utility should process goes here, immediately following the directive.
    ```

    **Example: Using the `promptimizer` utility directly:**

    To get feedback on a high-level project idea using the `promptimizer` utility, your interaction with the AI system would be:

    ```
    [[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /prompts/util/promptimizer/promptimizer.txt]]
    My project idea is to create a universal translator for squirrel languages. I think it could be a mobile app and also a web service. I'm not sure about the technical details yet but want to explore the concept.
    ```

    The AI system will then:
    1. Load the content of `/prompts/util/promptimizer/promptimizer.txt`.
    2. Take the text you provided *after* the directive ("My project idea is to create a universal translator...").
    3. Prepend the `promptimizer.txt` content to your project idea text.
    4. Process the combined text. The `promptimizer.txt` instructions will guide the AI to analyze your project idea and provide structured feedback.

    This method is suitable for utilities designed to process text appended directly after their own prompt content. The utility itself doesn't use the `[[USER_PROJECT_REQUEST]]` block mechanism; it typically processes the immediate subsequent text.

---
## 4. Developing New MPS Components

This section is for developers looking to extend the MPS framework.

*   **`Developing New promptApps`**
    *   To create a new workflow, define a `promptApp`:
        1.  Create a new subdirectory in `prompts/apps/` (e.g., `prompts/apps/MyNewWorkflow/`).
        2.  Inside this folder, create an `MyNewWorkflow_manifest.json` file. Structure this manifest according to the `framework_dev_docs/guides/PromptApp_Manifest_Guide.md`. Define phases, tasks, the components each task uses, and any default configuration overrides for those components within the context of your workflow.
        3.  If your `promptApp` requires unique components not available globally, or if you wish to provide an app-specific version of a globally named component, you can create them within the app's folder. The preferred structure is a sub-folder (e.g., `prompts/apps/MyNewWorkflow/my_custom_task/my_custom_task.txt`), or directly as a flat file (e.g., `prompts/apps/MyNewWorkflow/my_other_task.txt`). The component resolution mechanism will search these app-local paths first (flat file then folder style) before checking global add-ons and utils. Ensure your manifest's `componentName` for tasks using these points to them correctly (e.g., `my_custom_task` or `my_other_task`).

*   **Developing Add-on or Utility "Apps" (Folder-per-App Architecture):**
    1.  **Create a Dedicated Subdirectory:** (e.g., `prompts/add_ons/my_new_addon/` or `prompts/util/my_new_util/`)
    2.  **Create the Primary Instruction File:** (e.g., `my_new_addon.txt` inside its folder).
    3.  **Create `USER_componentName_CONFIG.txt` (for configurable components):**
        *   If your component requires user-configurable parameters, you **must** create a `USER_my_new_addon_CONFIG.txt` file within its directory.
        *   Structure this file according to the standard (see Section 3.B.2.ii), including `[[USER_CONFIG_FOR_my_new_addon]]...` delimiters, and parameter metadata (`# Requirement`, `# DefaultType`, `# Description`, `# Default`), and value lines (`PARAM_NAME: [USER_VALUE_START]...`).
    4.  **Include Supporting Files:** Place all other necessary files within this app folder.
    5.  **Manifest (for Add-ons):** Document new add-on apps in `prompts/add_ons/available_addons_manifest.md`.
---

## 5. Framework Operational Notes & Meta Information

This section provides context on some operational aspects of the MPS framework, particularly concerning logging and historical records.

**A. Handoff Notes (`framework_dev_docs/meta/HANDOFF_NOTES.md`) Management**

*   **User-Controlled Verbosity:**
    *   You can now control the level of detail the AI (Jules) records in the `framework_dev_docs/meta/HANDOFF_NOTES.md` file at the end of its operational session.
    *   This is managed via the `[[USER_FRAMEWORK_SETTINGS]]` block in your project-specific MPS instruction file (your copy of `Master_Prompt_Segment.txt`).
    *   Within this block, the `[ ] Full_Handoff_Notes_Logging_Enabled` checkbox determines the logging level:
        *   **Checked (`[x]`):** Jules will append detailed session summaries to `HANDOFF_NOTES.md`, including user feedback addressed, a detailed account of changes, the state of deliverables, and any pending items or suggestions.
        *   **Unchecked (`[ ]` - default):** Jules will append concise summaries, focusing only on major architectural changes, key decisions made, and the final state of key deliverables.
    *   This setting allows you to choose between comprehensive logging for detailed tracking or more streamlined notes that highlight critical developments.

*   **Status of "MPS Performance Feedback Log":**
    *   The distinct "MPS Performance Feedback Log" section, which was previously part of `HANDOFF_NOTES.md`, has been removed from the active `framework_dev_docs/meta/HANDOFF_NOTES.md` file.
    *   This change aims to simplify the primary handoff document and make it more focused on session summaries and critical evolution points.
    *   The future of detailed performance feedback logging is currently under review. It may be reintroduced in a different format, potentially as an optional component or a separate, dedicated logging mechanism, if deemed necessary for ongoing framework refinement.

**B. Handoff Notes Archive**

*   A comprehensive historical archive of older, more verbose handoff notes (accumulated before the introduction of the verbosity control and the compaction of the main `HANDOFF_NOTES.md` file) might exist at:
    `framework_dev_docs/meta/HANDOFF_NOTES_full_archive_20250602.md`.
*   Jules instances are instructed by `Core_Planning_Instructions.txt` **not to read this archive file** as part of their standard operational procedure.
*   Accessing this archive should only be considered if understanding specific historical context is absolutely critical for fulfilling a *current* user request. In such rare cases, Jules must explicitly ask for and receive user permission before attempting to read the archive.
*   The primary reference for ongoing, current handoff information is the (now potentially more concise) `framework_dev_docs/meta/HANDOFF_NOTES.md` file.

This modular approach, combined with flexible configuration and workflow orchestration via promptApps, allows for a powerful and adaptable MPS framework.
