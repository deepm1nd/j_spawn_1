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
    *   The main wrapper for the Planning AI's instructions (currently v0.5.0). You will copy its *content* into your main spawn prompt file (e.g., `p_my_project_spawn.md`) within your target repository.
    *   It now includes `[[USER_APP_SELECTION]]` for choosing a promptApp, `[[USER_ADDON_SELECTION]]` for choosing standalone add-on apps, placeholders for `[[USER_promptAPP_NAME_CONFIGURATION]]` examples, and an `[[INCLUDE]]` directive for `Core_Planning_Instructions.txt`.

*   **`/prompts/iep/Core_Planning_Instructions.txt` (Framework Source):**
    *   Contains foundational instructions for the Planning AI. It dictates component discovery (promptApps, add-ons, utils using fixed paths), loading of primary instruction files, parsing of user selections, discovery of app-specific configuration blocks, and conditional execution logic for promptApps or standalone add-ons.
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

**B. Create Your Main Spawn Prompt File (e.g., `target_repo/prompts/p_my_project_spawn.md`):**

   A recommended order for blocks within your main spawn prompt file is:
    1.  **Your Specific Project Request.** This is the high-level goal for the AI.
    2.  **(If using a promptApp) The `[[USER_promptAPP_NAME_CONFIGURATION]]` block** for the selected promptApp (e.g., `[[USER_CompliancePLM_App_CONFIGURATION]]`). This is where you select which tasks within the promptApp to run.
    3.  **Component-Specific Configuration Blocks (`[[USER_CONFIG_FOR_componentName]]`)**: For any components (whether run standalone via add-on selection, or as part of a selected promptApp task) that need parameter overrides from their defaults.
    4.  **The Full Content of `/prompts/Master_Prompt_Segment.txt` (Framework Source):** This includes `[[USER_APP_SELECTION]]`, `[[USER_ADDON_SELECTION]]`, examples for promptApp configurations, and the `[[INCLUDE]]` directive for `Core_Planning_Instructions.txt`. You **must edit** the selection blocks within this pasted content.

        **3.B.1. Understanding Path Configurations in the New System**
        Path management relies on:
        *   **Fixed System Paths:** The Planning AI, guided by `Core_Planning_Instructions.txt`, uses fixed relative paths (from the repository root) to discover core components (promptApps in `prompts/apps/`, add-ons in `prompts/add_ons/`, etc.). These are internal to the framework.
        *   **Component-Specific Path Parameters:** Paths that control where a component reads its specific inputs or writes its outputs (e.g., `PRIMARY_INPUT_DOC_PATH`, `REPORT_OUTPUT_PATH` for the `create_research_report` component) are defined as parameters *within that component's own configuration system*. You manage these via component-specific configuration blocks.

        **3.B.2. Configuring and Running MPS Workflows**
        You can direct the MPS framework in two primary ways: by selecting a `promptApp` for a predefined workflow, or by selecting one or more standalone add-on apps if no `promptApp` is chosen.

        *   **i. Selecting and Configuring a `promptApp` (Primary Workflow Method):**
            *   **Selection (`[[USER_APP_SELECTION]]` Block):**
                *   This block is inside the `Master_Prompt_Segment.txt` content you copied into your main spawn prompt.
                *   Select **at most one** promptApp by its folder name (e.g., `[x] CompliancePLM_App`). The Planning AI will look for the app's manifest at `prompts/apps/[AppName]/[AppName]_manifest.json`.
            *   **Task Configuration (`[[USER_promptAPP_NAME_CONFIGURATION]]` Block):**
                *   You **must** create this block in your main spawn prompt if you selected a promptApp. Name it after the selected app (e.g., `[[USER_CompliancePLM_App_CONFIGURATION]] ... [[END_USER_CompliancePLM_App_CONFIGURATION]]`).
                *   Inside this block, you will list phases and tasks based on the selected promptApp's manifest. Mark the tasks you want the AI to execute for the current session with `[x]`. Example:
                    ```
                    [[USER_CompliancePLM_App_CONFIGURATION]]
                    # [Concept Phase]
                    #   [ ] Architectural Specification
                    #   [x] Product Specification
                    #   [ ] Product Requirements
                    # ... other phases and tasks ...
                    [[END_USER_CompliancePLM_App_CONFIGURATION]]
                    ```
                *   Refer to `framework_dev_docs/guides/PromptApp_Manifest_Guide.md` for how promptApp manifests are structured.
            *   **Underlying Component Configuration:** Each task checked `[x]` in your `[[USER_promptAPP_NAME_CONFIGURATION]]` block maps to an underlying MPS component (e.g., `create_research_report`). This component might require its own parameters to be set.
                *   These parameters are resolved using the precedence:
                    1.  `[[USER_CONFIG_FOR_componentName]]` block in your main spawn prompt (highest).
                    2.  User-edited values in the component's `USER_componentName_CONFIG.txt` file.
                    3.  `defaultConfigOverrides` specified in the `promptApp` manifest for that specific task.
                    4.  Accepted defaults from the component's `USER_componentName_CONFIG.txt`.
                    5.  AI-assisted user query if a required parameter with a placeholder default is still unresolved.
                *   So, if a task's `defaultConfigOverrides` in the manifest are not sufficient, or if the component has other required parameters not covered by those overrides, you'll need to provide a standard `[[USER_CONFIG_FOR_componentName]]` block for that underlying component.
            *   **Component Resolution Search Order:** When a `promptApp` task in its manifest specifies a `componentName`, the MPS framework will attempt to locate and load the component by searching in the following prioritized order:
                1.  **App-Specific (Flat File Style):** `prompts/apps/[SelectedAppName]/[componentName].txt` (A component file directly within the selected promptApp's root folder).
                2.  **App-Specific (Folder Style):** `prompts/apps/[SelectedAppName]/[componentName]/[componentName].txt` (A component structured within its own sub-folder inside the selected promptApp's root folder; its `USER_..._CONFIG.txt` would also be in this sub-folder).
                3.  **Global Add-on:** `prompts/add_ons/[componentName]/[componentName].txt`
                4.  **Global Util:** `prompts/util/[componentName]/[componentName].txt`
                The first component found in this sequence is used. This allows `promptApps` to bundle their own specific versions of components or define unique components that take precedence over global ones if named identically.
            *   **Iteration and Sequential Execution:**
                *   If a task in the `promptApp` manifest is marked `isIterable: true`, the underlying component is expected to prompt you to "repeat this task or move on" upon its completion.
                *   If you choose "move on," the AI session will HALT. The AI will instruct you to update your `[[USER_promptAPP_NAME_CONFIGURATION]]` block in your main spawn prompt to select the next desired task from the `promptApp`'s workflow. You will then start a new AI session with the updated prompt. This is how you progress through a `promptApp`'s sequence of tasks.

        *   **ii. Selecting and Configuring Standalone Add-on Apps (if no promptApp is selected):**
            This is the fallback method if you are not using a `promptApp`.
            *   **Selection (`[[USER_ADDON_SELECTION]]` Block):**
                *   Located within the `Master_Prompt_Segment.txt` content.
                *   List the **folder name** of the add-on(s) you want to use (e.g., `[x] create_research_report`). The AI looks for `prompts/add_ons/[addonName]/[addonName].txt`.
                *   **Order & Primary Add-on:** If using a primary add-on like `task_spawning_addon`, list it last. Other selected "inheritable" add-ons (e.g., `task_resumption_addon`) will have their content appended to generated task prompts in the order listed.
            *   **Parameter Configuration (`USER_componentName_CONFIG.txt` and `[[USER_CONFIG_FOR_componentName]]` blocks):**
                *   Configurable add-ons manage parameters via their `USER_componentName_CONFIG.txt` file (template/storage) and optional `[[USER_CONFIG_FOR_componentName]]` override blocks in your main spawn prompt.
                *   **`USER_componentName_CONFIG.txt` File:** Found in the component's folder (e.g., `prompts/add_ons/create_research_report/USER_create_research_report_CONFIG.txt`). It defines parameters, defaults, requirements, and types.
                *   **User Workflow:**
                    1.  **Discover Parameters:** Check `available_addons_manifest.md` or the component's `USER_..._CONFIG.txt`.
                    2.  **Provide Overrides (Recommended):** Copy the content of the relevant `USER_..._CONFIG.txt` into your main spawn prompt as a `[[USER_CONFIG_FOR_componentName]]` block and edit values.
                    3.  **Persistent Edits:** Alternatively, edit the `USER_..._CONFIG.txt` directly in your target repo.
                *   **Parameter Resolution Precedence (for standalone components):**
                    1.  Value from `[[USER_CONFIG_FOR_componentName]]` block in the main spawn prompt.
                    2.  User-edited value in `USER_componentName_CONFIG.txt`.
                    3.  Accepted default value from `# Default:` in `USER_componentName_CONFIG.txt`.
                    4.  **AI-Assisted Update:** For `Required | PlaceholderDefault` parameters still unresolved, the AI will ask for input, update the on-disk `USER_..._CONFIG.txt`, and prompt you to copy the updated block to your main prompt.
                *   **Error Handling:** Required parameters not resolved will cause the component to halt.

**D. Invoke the Planning AI:** (Content as before)

**E. Understanding the Planning AI's Process:**
The Planning AI will then:
1.  Read your spawn prompt.
2.  Process Core Instructions (`/prompts/iep/Core_Planning_Instructions.txt`):
    *   Discover available promptApp manifests, add-on apps, and util apps from their fixed system paths.
    *   Parse `[[USER_APP_SELECTION]]` to identify if a `promptApp` is selected.
    *   If a `promptApp` is selected:
        *   Parse its `[[USER_AppName_CONFIGURATION]]` block to determine selected tasks.
        *   Proceed to execute the first selected task, loading the specified component and its configurations (considering manifest overrides, `USER_..._CONFIG.txt`, and main prompt blocks).
    *   If no `promptApp` is selected:
        *   Parse `[[USER_ADDON_SELECTION]]` to identify selected add-on app names.
        *   For each selected add-on, load its instructions and discover its `[[USER_CONFIG_FOR_addonName]]` block from the main prompt.
        *   Execute the primary add-on or other selected add-ons.
3.  Read Supporting Files as needed.

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

This modular approach, combined with flexible configuration and workflow orchestration via promptApps, allows for a powerful and adaptable MPS framework.
