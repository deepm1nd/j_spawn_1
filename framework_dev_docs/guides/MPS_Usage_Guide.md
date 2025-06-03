# MPS Usage Guide (for Canonical Framework using `prompts/Master_Prompt_Segment.txt`)

## 1. Introduction
This guide explains how to use the canonical `Master_Prompt_Segment.txt` (located at `/prompts/Master_Prompt_Segment.txt` in this framework's development repository) to instruct a Planning AI. The `Master_Prompt_Segment.txt` has been modularized. Its core instructional logic for parsing configurations and managing components resides in `/prompts/iep/Core_Planning_Instructions.txt`. This framework utilizes a **"folder-per-app" architecture** for its components (add-ons, utils, etc.), enhancing modularity and maintainability. Notably, primary functionalities like project planning (`task_spawning_addon` app) or product specification generation (`build_product_specs_process` app) are encapsulated in optional add-on apps. This structure allows the framework to be adapted for various purposes by selecting different combinations of component apps.

## 2. Core Framework Components & User Preparations

This section outlines the key pieces of the MPS framework and what you, the user, will typically prepare or copy into your target repository.

*   **`/prompts/Master_Prompt_Segment.txt` (Framework Source):**
    *   This is the main wrapper for the Planning AI's instructions. You will copy its *content* into your main spawn prompt file within your target repository.
    *   **Action Required:** Critically, you **must edit** the `[[USER PATH CONFIGURATION]]` and `[[USER_ADDON_SELECTION]]` blocks at the top of the MPS content you copied to suit your target project's specific paths and desired add-on apps.

*   **`/prompts/iep/Core_Planning_Instructions.txt` (Framework Source):**
    *   Contains foundational instructions for the Planning AI. It dictates how the AI parses user configurations, discovers and loads component "apps" (add-ons, utils), implements conditional logic for primary functionalities, and generates utility files like `Information_Exchange_Protocol.md`.
    *   **Action Required:** You **must copy** this file (and its content) to `/prompts/iep/Core_Planning_Instructions.txt` in your target repository. It is essential for the framework's operation.

*   **`/prompts/iep/Base_IEP.txt` (Framework Source):**
    *   This is the template for the Information Exchange Protocol.
    *   **Action Required:** Copy this file to `/prompts/iep/Base_IEP.txt` in your target repository.

*   **Component "Apps" (Add-ons, Utils, IPC Handlers - Framework Source):**
    The MPS framework uses a "folder-per-app" architecture for its extensible components. Each component (be it an add-on, a utility, or a future IPC handler) resides in its own subdirectory within a designated base directory (e.g., `prompts/add_ons/` for add-on apps).
    *   **Structure:** For an app named `my_app_name`, its structure would be `[base_directory]/my_app_name/`, and it must contain a primary instruction file named `my_app_name.txt` (i.e., `[base_directory]/my_app_name/my_app_name.txt`). The app's folder can also contain any other supporting files, scripts, data, or sub-frameworks it needs.
    *   **Add-on Apps (e.g., from `/prompts/add_ons/`):** These provide specific functionalities, either as primary directives (like `task_spawning_addon` or `build_product_specs_process`) or as supplementary, inheritable instructions (like `task_resumption_addon`).
        *   **Action Required:** For any add-on app you wish to use, you must copy its entire folder (e.g., copy `/prompts/add_ons/task_spawning_addon/` from the framework repo to `target_repo/prompts/add_ons/task_spawning_addon/`). You select active add-ons by listing their app/folder names in the `[[USER_ADDON_SELECTION]]` block.
    *   **Utility Apps (e.g., from `/prompts/util/`):** These provide reusable utility functions that can be called by other components. They are discovered by the Planning AI if present in the `User_Path_Utils_Dir`.
        *   **Action Required:** Copy the entire folder of any util app you might need (or that a chosen add-on might require) into the corresponding `utils` directory in your target repository (e.g., `target_repo/prompts/util/pre_flight_check_user_plan/`).
    *   Refer to `/prompts/add_ons/available_addons_manifest.md` for a list and descriptions of available add-on apps.

*   **Your Project-Specific Request:** This is the main text describing what you want the AI to do (e.g., "Create a dev plan for project gritos..."). It's the first part of your main spawn prompt file.

## 3. Setting up Your Target Repository & Crafting the Main Spawn Prompt

**A. Prepare Your Target Repository:**
1.  Ensure all your project-specific input documents are present.
2.  Create a `/prompts/` directory at the root of your target repository.
3.  Inside `target_repo/prompts/`, create an `iep/` subdirectory.
    *   Copy `/prompts/iep/Base_IEP.txt` from the framework to `target_repo/prompts/iep/Base_IEP.txt`.
    *   Copy `/prompts/iep/Core_Planning_Instructions.txt` from the framework to `target_repo/prompts/iep/Core_Planning_Instructions.txt`.
4.  Create component base directories in `target_repo/prompts/` as needed:
    *   `target_repo/prompts/add_ons/` (for add-on apps)
    *   `target_repo/prompts/util/` (for utility apps)
    *   `target_repo/prompts/ipc/` (for IPC handlers - this directory should exist, even if initially empty, perhaps with a `.gitkeep` file)
5.  Copy desired component app folders from the framework's component directories (e.g., `/prompts/add_ons/`, `/prompts/util/`) into the corresponding directories in your target repository. For example:
    *   To use the task spawning add-on, copy the entire `prompts/add_ons/task_spawning_addon/` folder from the framework to `target_repo/prompts/add_ons/task_spawning_addon/`.
    *   To use the pre-flight check utility, copy `prompts/util/pre_flight_check_user_plan/` to `target_repo/prompts/util/pre_flight_check_user_plan/`.

**B. Create Your Main Spawn Prompt File in Target Repository:**
   (e.g., `target_repo/prompts/p_your_project_spawn.txt`)

   This file must contain, in order:
    1.  **Your Specific Project Request.**
    2.  **The Full Content of `/prompts/Master_Prompt_Segment.txt` (from this framework repo), with its top `[[USER PATH CONFIGURATION]]` and `[[USER_ADDON_SELECTION]]` blocks EDITED BY YOU.**

        **3.B.1. Configuring `[[USER PATH CONFIGURATION]]`**
        This block defines key paths and variables.
        *   **Core Framework Paths:** These tell the Planning AI where to find component categories.
            *   `User_Path_Addons_Dir`: (Default: `prompts/add_ons/`) Base directory where add-on app subfolders are located.
            *   `User_Path_Utils_Dir`: (Default: `prompts/util/`) Base directory where utility app subfolders are located.
            *   `User_Path_IPC_Dir`: (Default: `prompts/ipc/`) Base directory where IPC handler app subfolders might be located (and for general IPC file storage).
        *   **General Project & Task Spawning Paths:** (Used by `task_spawning_addon` app, etc.)
            *   `Main Iteration Folder`, `Prompts Folder`, `Submodule Plan Destination`, `Inter-AI Communication Folder` (note: often same as `User_Path_IPC_Dir`), `User-Specified Task Output Base Path`. (Descriptions as before).
        *   **Paths for Specific Processes/Add-ons:** (e.g., for `build_product_specs_process` app)
            *   `PRODUCT_NAME`, various `PRODUCT_SPECS_*` paths. (Descriptions as before).
            *   You **must** edit example values to match your target repository and project needs.

        **3.B.2. Configuring `[[USER_ADDON_SELECTION]]`**
        This block allows you to activate specific add-on apps.
            *   List the **folder name** of the add-on app you want to use (e.g., `[x] task_spawning_addon`). The framework will then look for `[User_Path_Addons_Dir]/task_spawning_addon/task_spawning_addon.txt`.
            *   **Order of Inheritable Add-ons:** If a primary add-on like `task_spawning_addon` is selected, other selected add-on apps whose purpose is to supplement task prompts (e.g., `task_resumption_addon`) will have their primary instruction file content appended to the generated task prompts in the order you list them here.
            *   **Primary Add-on Placement:** For clarity, it's often best to list the main orchestrating or process add-on (like `task_spawning_addon` or `build_product_specs_process`) last in your selection.
        *   **Example `[[USER_ADDON_SELECTION]]` for full planning:**
            ```
            [[USER_ADDON_SELECTION]]
            # Inheritable add-on apps - order matters for task prompts
            [x] task_resumption_addon
            # [ ] example_coding_standards_addon 
            
            # Primary Add-on app (typically select only one primary)
            # [ ] build_product_specs_process 
            [x] task_spawning_addon  
            [[END USER_ADDON_SELECTION]]
            ```

**C. Using Iterative Deepening for `build_product_specs_process`** (Content as before)

**D. Invoke the Planning AI:** (Content as before)

**E. Understanding the Planning AI's Process:**
The Planning AI will then:
1.  **Read your spawn prompt.**
2.  **Process Core Instructions:** It will first process the included `/prompts/iep/Core_Planning_Instructions.txt`. This involves:
    *   Parsing `[[USER PATH CONFIGURATION]]` to get all defined paths, including `User_Path_Addons_Dir` and `User_Path_Utils_Dir`.
    *   Discovering all available util apps from `User_Path_Utils_Dir`.
    *   Parsing `[[USER_ADDON_SELECTION]]` to identify selected add-on app names.
    *   Loading the primary instruction file (e.g., `app_name/app_name.txt`) for each selected add-on app.
    *   Preparing a filtered list of "inheritable" add-on app content.
3.  **Conditional Add-on Execution:** (Logic as before, but now referring to "add-on apps" and their primary instruction file content).
    *   If an `Active_Primary_Addon_AppName` (like `task_spawning_addon` or `build_product_specs_process`) is identified:
        *   The AI executes the instructions from that app's primary instruction file.
        *   If that primary add-on app generates sub-prompts (like `task_spawning_addon` does), it will use the `Inheritable_Addons_Content_Ordered_List` to append the primary instruction file content of other selected, non-primary add-on apps. The content of the active primary add-on app itself is not appended.
    *   If NO primary directive add-on app IS SELECTED: (Logic as before).
4.  **Read Supporting Files:** (Content as before).

---
## 4. Developing New MPS Components (Folder-per-App Architecture)

If you are developing new components (add-ons, utils, or potentially other types like IPC handlers) for the MPS framework, adhere to the "folder-per-app" architecture:

1.  **Create a Dedicated Subdirectory:**
    *   For an **add-on app**, create a new subdirectory under the directory specified by `User_Path_Addons_Dir` (typically `prompts/add_ons/`). The name of this subdirectory becomes the `addon_app_name` used in `[[USER_ADDON_SELECTION]]`.
        *   Example: `prompts/add_ons/my_new_feature_addon/`
    *   For a **utility app**, create a new subdirectory under `User_Path_Utils_Dir` (typically `prompts/util/`). The name of this subdirectory is the `util_app_name`.
        *   Example: `prompts/util/my_text_processing_util/`

2.  **Create the Primary Instruction File:**
    *   Inside your app's subdirectory, create a `.txt` file that has the **exact same name as the subdirectory**. This is the primary entry point or instruction set for your component.
        *   Example: `prompts/add_ons/my_new_feature_addon/my_new_feature_addon.txt`
        *   Example: `prompts/util/my_text_processing_util/my_text_processing_util.txt`

3.  **Include Supporting Files:**
    *   Place all other files required by your component (e.g., helper scripts, data files, detailed sub-prompts, templates, example outputs, sub-frameworks) *within its dedicated subdirectory*. This keeps the component self-contained.

4.  **Manifest (for Add-ons):**
    *   If you create a new add-on app, document its purpose, type, inputs, outputs, and compatibility notes in `prompts/add_ons/available_addons_manifest.md`. Use the `addon_app_name` (folder name) as its identifier.

This modular structure helps in organizing components, managing dependencies, and ensuring that the framework can discover and load them correctly. It also makes it easier for users to understand what files belong to which piece of functionality when they are setting up their target repositories.
---

This modular approach provides greater flexibility. While the full planning and task generation capabilities are available via the `task_spawning_addon` app, you can also use the MPS framework with other add-on apps for different purposes, or develop new add-on apps with unique functionalities.
