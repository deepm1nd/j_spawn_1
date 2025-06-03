# MPS Usage Guide (for Canonical Framework using `prompts/Master_Prompt_Segment.txt`)

## 1. Introduction
This guide explains how to use the canonical `Master_Prompt_Segment.txt` (located at `/prompts/Master_Prompt_Segment.txt` in this framework's development repository) to instruct a Planning AI. The `Master_Prompt_Segment.txt` (currently v0.4.0) has been significantly refactored to enhance modularity and clarity in how configurations are handled.

Key architectural features include:
*   **Core Instructions (`/prompts/iep/Core_Planning_Instructions.txt`):** Contains the foundational logic for the Planning AI, including component discovery, loading, and parameter processing.
*   **"Folder-per-App" Architecture:** Components like Add-ons and Utils are self-contained within their own subdirectories (e.g., `prompts/add_ons/app_name/`), each with a primary `app_name.txt` instruction file and any supporting files, including a `USER_appName_CONFIG.txt` template for its parameters.
*   **Fixed System Paths:** Base paths for discovering framework components (add-ons, utils, IEP, IPC) are now hardcoded within `Core_Planning_Instructions.txt` (e.g., `prompts/add_ons/`, `prompts/util/`), simplifying initial setup.
*   **App-Specific Configurations:** User-configurable parameters for add-on apps are managed via:
    1.  Template/persistent storage files: `USER_appName_CONFIG.txt` within each app's folder.
    2.  Run-specific overrides: `[[USER_CONFIG_FOR_appName]]...[[END_USER_CONFIG_FOR_appName]]` blocks directly in the user's main spawn prompt.
*   **Optional Primary Functionality:** Core features like project planning (`task_spawning_addon` app) or product specification generation (`build_product_specs_process` app) are encapsulated in optional add-on apps, selected by the user.

This guide details how to set up your project and interact with this refined MPS framework.

## 2. Core Framework Components & User Preparations

This section outlines the key pieces of the MPS framework and what you, the user, will typically prepare or copy into your target repository.

*   **`/prompts/Master_Prompt_Segment.txt` (Framework Source):**
    *   The main wrapper for the Planning AI's instructions. You will copy its *content* into your main spawn prompt file (e.g., `p_my_project_spawn.md`) within your target repository.
    *   It primarily contains the `[[USER_ADDON_SELECTION]]` block for choosing add-on apps and an `[[INCLUDE]]` directive for `Core_Planning_Instructions.txt`.
    *   **Note:** The global `[[USER PATH CONFIGURATION]]` block has been removed from `Master_Prompt_Segment.txt` as of v0.4.0. Path configurations for general outputs (like `Main Iteration Folder`) and some process-specific inputs are now managed as parameters within app-specific configuration blocks (see Section 3.B.2.ii).

*   **`/prompts/iep/Core_Planning_Instructions.txt` (Framework Source):**
    *   Contains foundational instructions for the Planning AI. It dictates component discovery (using fixed paths like `prompts/add_ons/`, `prompts/util/`), loading of app primary instruction files (`app_name/app_name.txt`), parsing of `[[USER_ADDON_SELECTION]]`, discovery of app-specific configuration blocks from the main prompt, and conditional execution logic.
    *   **Action Required:** You **must copy** this file to `target_repo/prompts/iep/Core_Planning_Instructions.txt`.

*   **`/prompts/iep/Base_IEP.txt` (Framework Source):**
    *   Template for the Information Exchange Protocol.
    *   **Action Required:** Copy to `target_repo/prompts/iep/Base_IEP.txt`.

*   **Component "Apps" (Add-ons, Utils - Framework Source):**
    *   Located in fixed base directories within the framework: `prompts/add_ons/` and `prompts/util/`.
    *   Each app resides in its own subdirectory (e.g., `prompts/add_ons/task_spawning_addon/`).
    *   Each app folder must contain a primary instruction file named `[app_name]/[app_name].txt`.
    *   Configurable apps **must** also contain a `USER_appName_CONFIG.txt` file defining their parameters, defaults, and metadata (see Section 3.B.2.ii for details on this file's format and usage).
    *   **Action Required:** Copy the entire folder of each desired add-on or util app from the framework (e.g., `/prompts/add_ons/task_spawning_addon/`) to the corresponding location in your target repository (e.g., `target_repo/prompts/add_ons/task_spawning_addon/`).
    *   Refer to `/prompts/add_ons/available_addons_manifest.md` for details on available add-on apps and their configurable parameters.

*   **Your Project-Specific Request:** The initial text in your main spawn prompt file that describes your project goal.

*   **App-Specific Configuration Blocks (User Created in Main Spawn Prompt):**
    *   For add-ons that require configuration, you will typically copy parameter templates from their `USER_appName_CONFIG.txt` file and embed them as `[[USER_CONFIG_FOR_appName]]...[[END_USER_CONFIG_FOR_appName]]` blocks in your main spawn prompt. This is detailed in Section 3.B.2.ii.

## 3. Setting up Your Target Repository & Crafting the Main Spawn Prompt

**A. Prepare Your Target Repository:**
1.  Ensure all your project-specific input documents are present (if required by the chosen add-on apps).
2.  Create directory structure in your target repo:
    *   `prompts/`
    *   `prompts/iep/`
    *   `prompts/add_ons/`
    *   `prompts/util/`
    *   `prompts/ipc/` (create this directory, add a `.gitkeep` if empty)
3.  Copy core files:
    *   Framework's `/prompts/iep/Core_Planning_Instructions.txt` to `target_repo/prompts/iep/Core_Planning_Instructions.txt`.
    *   Framework's `/prompts/iep/Base_IEP.txt` to `target_repo/prompts/iep/Base_IEP.txt`.
4.  Copy desired Component App Folders:
    *   For each add-on app (e.g., `task_spawning_addon`, `build_product_specs_process`), copy its entire folder from the framework's `prompts/add_ons/` to `target_repo/prompts/add_ons/`. This includes the `app_name.txt` and the `USER_appName_CONFIG.txt`.
    *   For each util app (e.g., `pre_flight_check_user_plan`), copy its entire folder from `prompts/util/` to `target_repo/prompts/util/`.

**B. Create Your Main Spawn Prompt File (e.g., `target_repo/prompts/p_my_project_spawn.md`):**

   This file must contain, in order:
    1.  **Your Specific Project Request.**
    2.  **(Highly Recommended) App-Specific Configuration Blocks:** For any selected add-on app that has configurable parameters, insert its configuration block here. See Section 3.B.2.ii for details.
    3.  **The Full Content of `/prompts/Master_Prompt_Segment.txt` (Framework Source):** This includes the `[[USER_ADDON_SELECTION]]` block (which you **must** edit) and the `[[INCLUDE]]` directive for `Core_Planning_Instructions.txt`.

        **3.B.1. Understanding Path Configurations in the New System**
        The old global `[[USER PATH CONFIGURATION]]` block in `Master_Prompt_Segment.txt` has been removed. Path management is now handled as follows:
        *   **Fixed System Paths:** The Planning AI, guided by `Core_Planning_Instructions.txt`, uses fixed relative paths (from the repository root) to discover core components:
            *   Add-on Apps Directory: `prompts/add_ons/`
            *   Utility Apps Directory: `prompts/util/`
            *   Base IEP File: `prompts/iep/Base_IEP.txt`
            *   IPC Directory: `prompts/ipc/` (for future IPC components and general IPC file storage by apps).
            These paths are internal to the framework and not set by the user in the main spawn prompt.
        *   **App-Specific Path Parameters:** Paths that control where an app reads its specific inputs or writes its outputs (e.g., `PRODUCT_SPECS_INPUT_DOCS_PATH`, `PRODUCT_SPECS_OUTPUT_PATH` for the `build_product_specs_process` app, or `Main Iteration Folder`, `Prompts Folder` for `task_spawning_addon`) are now defined as parameters *within that app's own configuration system*. You manage these via the app-specific configuration blocks, as described next.

        **3.B.2. Configuring Add-on Apps**
        There are two key parts to using and configuring add-on apps:

        *   **i. Selecting Add-on Apps (`[[USER_ADDON_SELECTION]]` Block):**
            *   This block is inside the `Master_Prompt_Segment.txt` content you copied.
            *   List the **folder name** of the add-on app you want to use (e.g., `[x] task_spawning_addon`). The Planning AI will then look for the app's primary instruction file at `prompts/add_ons/[app_name]/[app_name].txt` in your target repository.
            *   **Order Matters:** If a primary add-on like `task_spawning_addon` is selected, the primary instruction file content of other selected "inheritable" add-on apps (e.g., `task_resumption_addon`) will be appended to the generated task prompts in the order you list them here.
            *   **Primary Add-on Placement:** For clarity, it's often best to list the main orchestrating or process add-on (like `task_spawning_addon` or `build_product_specs_process`) last in your selection.
            *   Example:
                ```
                [[USER_ADDON_SELECTION]]
                [x] task_resumption_addon
                [x] build_product_specs_process
                [[END_USER_ADDON_SELECTION]]
                ```

        *   **ii. Providing Parameters for Add-on Apps (App-Specific Config Blocks & `USER_appName_CONFIG.txt` Files):**
            Configurable add-on apps manage their parameters through a combination of a template/storage file (`USER_appName_CONFIG.txt`) and an optional override block in your main spawn prompt.
            *   **`USER_appName_CONFIG.txt` File:**
                *   **Location & Purpose:** Each configurable app in the framework (e.g., `build_product_specs_process`) provides a `USER_appName_CONFIG.txt` file within its folder (e.g., `prompts/add_ons/build_product_specs_process/USER_build_product_specs_process_CONFIG.txt`). This file serves two roles:
                    1.  A **template** defining all its parameters, their descriptions, default values, requirement status (`Required` or `Optional`), and default type (`AcceptedDefault` or `PlaceholderDefault`).
                    2.  A place where **persistent user edits or AI-assisted updates** (values obtained via chat) are stored for that app within your target repository.
                *   **Format:** The content is wrapped in `[[USER_CONFIG_FOR_appName]]...[[END_USER_CONFIG_FOR_appName]]` delimiters. Each parameter has metadata comments and a value line:
                    ```
                    # Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]
                    # Description: [Full description]. Default: [template_default_value]
                    PARAM_NAME: [USER_VALUE_START] [current_or_default_value] [USER_VALUE_END]
                    ```
            *   **User Workflow for Configuration:**
                1.  **Discover Parameters:** Check the `available_addons_manifest.md` or inspect the app's `USER_appName_CONFIG.txt` to understand its parameters.
                2.  **Provide Run-Specific Overrides (Recommended):** Copy the *entire content* of the relevant `USER_appName_CONFIG.txt` (including the `[[USER_CONFIG_FOR_appName]]...` delimiters) into your main spawn prompt file (typically placed just after your project request and before the `[[MASTER_PROMPT_SEGMENT]]` section). Then, edit the values between the `[USER_VALUE_START]` and `[USER_VALUE_END]` markers for any parameters you want to set specifically for that run.
                3.  **Persistent Edits (Alternative):** You can also directly edit the values within the `[USER_VALUE_START]...[USER_VALUE_END]` markers in the `USER_appName_CONFIG.txt` file within the app's folder in your target repository. These will then act as your new defaults if no override block is found in the main spawn prompt.
            *   **AI Parameter Resolution Precedence:** The Planning AI (specifically, each add-on app when it initializes, guided by `Core_Planning_Instructions.txt`) determines the final value for each parameter in the following order:
                1.  Value from the `[[USER_CONFIG_FOR_appName]]` block in the main spawn prompt (highest precedence).
                2.  If not in the main prompt block or if the value there is a placeholder: The value in the `USER_appName_CONFIG.txt` file *if it appears user-edited* (i.e., different from its commented `# Default:` value and not a placeholder itself).
                3.  If still unresolved: The `template_default_value` from the `# Default:` comment in `USER_appName_CONFIG.txt`, but *only if* its `# DefaultType:` is `AcceptedDefault`.
                4.  **AI-Assisted Update:** If a parameter is marked `# Requirement: Required | DefaultType: PlaceholderDefault` and its value remains a placeholder or empty after the above steps, the active add-on app will instruct Jules (the AI you are interacting with) to ask you for the value via chat.
                    *   The value you provide via chat is used for the current run.
                    *   The AI will then **update the `USER_appName_CONFIG.txt` file on disk** with this new value.
                    *   The AI will also output the complete, updated `[[USER_CONFIG_FOR_appName]]` block and instruct you to copy it into your main spawn prompt to ensure consistency for future runs.
            *   **Error Handling:** If a `Required` parameter cannot be resolved to a valid, non-placeholder value (e.g., user doesn't provide input when asked), the add-on app will report an error and halt.

**C. Using Iterative Deepening for `build_product_specs_process`** (Content as before, ensure parameter names like `PRODUCT_SPECS_OUTPUT_PATH` are understood in the context of app-specific configs now.)

**D. Invoke the Planning AI:** (Content as before)

**E. Understanding the Planning AI's Process:**
The Planning AI will then:
1.  **Read your spawn prompt.**
2.  **Process Core Instructions (`/prompts/iep/Core_Planning_Instructions.txt`):**
    *   Discover available util apps from the fixed `prompts/util/` path.
    *   Parse `[[USER_ADDON_SELECTION]]` to identify selected add-on app names.
    *   For each selected add-on app:
        *   Load its primary instruction file (e.g., `prompts/add_ons/app_name/app_name.txt`).
        *   Scan your main spawn prompt for its corresponding `[[USER_CONFIG_FOR_app_name]]...` block and extract its raw content (this raw content is passed to the app).
    *   Prepare a filtered list of "inheritable" add-on app content.
3.  **Conditional Add-on Execution:** (Logic as before, but understanding that each primary add-on app now receives its specific config block content for internal parsing and resolution as described in 3.B.2.ii).
4.  **Read Supporting Files:** (Content as before).

---
## 4. Developing New MPS Components (Folder-per-App Architecture)
(Content as before, but ensure it explicitly mentions creating a `USER_appName_CONFIG.txt` file for any configurable parameters, structured as per the standard defined in 3.B.2.ii.)

If you are developing new components (add-ons, utils, etc.) for the MPS framework, adhere to the "folder-per-app" architecture:
1.  **Create a Dedicated Subdirectory:** (e.g., `prompts/add_ons/my_new_addon/`)
2.  **Create the Primary Instruction File:** (e.g., `prompts/add_ons/my_new_addon/my_new_addon.txt`)
3.  **Create `USER_appName_CONFIG.txt` (for configurable components):**
    *   If your component requires user-configurable parameters, **you must** create a `USER_my_new_addon_CONFIG.txt` file within its directory.
    *   Structure this file according to the standard defined in this guide (Section 3.B.2.ii), including `[[USER_CONFIG_FOR_my_new_addon]]...[[END_USER_CONFIG_FOR_my_new_addon]]` delimiters. For each parameter, include:
        *   `# Requirement: [Required|Optional] | DefaultType: [Accepted|Placeholder]`
        *   `# Description: [Full description]. Default: [template_default_value]`
        *   `PARAM_NAME: [USER_VALUE_START] [current_or_default_value] [USER_VALUE_END]`
4.  **Include Supporting Files:** Place all other necessary files within this app folder.
5.  **Manifest (for Add-ons):** Document new add-on apps in `prompts/add_ons/available_addons_manifest.md`.
---

This modular approach, combined with flexible configuration, allows for a powerful and adaptable MPS framework.
