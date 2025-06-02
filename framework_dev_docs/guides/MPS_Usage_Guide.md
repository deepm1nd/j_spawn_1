# MPS Usage Guide (for Canonical Framework using `prompts/Master_Prompt_Segment.txt`)

## 1. Introduction
This guide explains how to use the canonical `Master_Prompt_Segment.txt` (located at `/prompts/Master_Prompt_Segment.txt` in this framework's development repository) to instruct a Planning AI. The `Master_Prompt_Segment.txt` has been modularized. Its core instructional logic for parsing configurations and managing add-ons resides in `/prompts/iep/Core_Planning_Instructions.txt`. Notably, the primary project planning and task generation functionality is now encapsulated in an optional add-on, `prompts/add_ons/task_spawning_addon.txt`. This structure enhances maintainability and flexibility, allowing the framework to be adapted for various purposes beyond full-scale project planning if desired.

## 2. Core Components You Will Use/Prepare

*   **Content of `/prompts/Master_Prompt_Segment.txt` from this framework repo:** This text serves as the main wrapper for the Planning AI's instructions. You will copy its *content* into your main spawn prompt file. **Crucially, you must edit the `[[USER PATH CONFIGURATION]]` and `[[USER_ADDON_SELECTION]]` blocks at the top of the MPS content you copied to suit your target project.** This file now primarily contains user configuration sections and an include directive to load `/prompts/iep/Core_Planning_Instructions.txt`.

*   **Content of `/prompts/iep/Core_Planning_Instructions.txt` from this framework repo:** This file contains foundational instructions for the Planning AI. It dictates how the AI parses user configurations (paths and add-on selections from `Master_Prompt_Segment.txt`), loads add-on files, and implements the conditional logic for primary functionalities like task spawning. It also includes instructions for generating utility files like `Information_Exchange_Protocol.md`. You **must** copy its *content* to `/prompts/iep/Core_Planning_Instructions.txt` in your target repository, as it's essential for the framework's operation.

*   **Content of `/prompts/iep/Base_IEP.txt` from this framework repo:** This is the Base Information Exchange Protocol. You will copy its *content* to `/prompts/iep/Base_IEP.txt` in your target repository.

*   **Content of Add-on Files (from `/prompts/add_ons/` in this framework repo):**
    *   Add-ons provide specific functionalities. You select them in the `[[USER_ADDON_SELECTION]]` block. For any add-on you wish to use, copy its content to the corresponding path in `/prompts/add_ons/` in your target repository.
    *   **Key Add-on for Planning: `task_spawning_addon.txt`**: This add-on contains all instructions for the Planning AI to perform its traditional role: generating a main development plan, a Task Launch Plan (TLP), and individual task prompt files. **If you want the AI to generate a project plan, you MUST select this add-on and copy its file.**
    *   Other add-ons (e.g., `task_resumption_addon.txt`) provide supplementary features.
    *   Refer to `/prompts/add_ons/available_addons_manifest.md` for a list and descriptions of available add-ons.

*   **Your Project-Specific Request:** E.g., the "gritos Dev Plan (a003)" text, which is the input the Planning AI will work on if `task_spawning_addon.txt` is selected.

## 3. Setting up Your Target Repository & Crafting the Main Spawn Prompt

To have a Planning AI operate on your project (e.g., "gritos"):

**A. Prepare Your Target Repository (where Planning AI will run):**
1.  Ensure all your project-specific input documents (e.g., for gritos: `gritos_product_specification.md`, `concept/research/` files, etc.) are present at the paths your project request will refer to.
2.  Create a `/prompts/` directory at the root of your target repository.
3.  Inside `target_repo/prompts/`, create an `iep/` subdirectory.
    *   Copy the content from this framework's `/prompts/iep/Base_IEP.txt` into `target_repo/prompts/iep/Base_IEP.txt`.
    *   Copy the content from this framework's `/prompts/iep/Core_Planning_Instructions.txt` into `target_repo/prompts/iep/Core_Planning_Instructions.txt`. (This is mandatory for the framework to function).
4.  Inside `target_repo/prompts/`, create an `add_ons/` subdirectory.
    *   **If you want the Planning AI to generate a project plan, TLP, and task prompts**, you **must** copy the content of `task_spawning_addon.txt` from this framework's `/prompts/add_ons/task_spawning_addon.txt` to `target_repo/prompts/add_ons/task_spawning_addon.txt`.
    *   For any other add-ons you intend to use (e.g., `task_resumption_addon.txt`), copy their content from this framework's `/prompts/add_ons/` directory into the corresponding file in `target_repo/prompts/add_ons/`.
5.  The Planning AI will create its output "Prompts Folder" (e.g., `prompts/tasks/` or as specified by you in `[[USER PATH CONFIGURATION]]`) if directed by an active add-on like `task_spawning_addon.txt`.

**B. Create Your Main Spawn Prompt File in Target Repository:**
   (e.g., `target_repo/prompts/p_your_project_spawn.txt` - refer to `framework_dev_docs/guides/p_gritos_dev_plan_spawn_EXAMPLE.md` in this framework repo for a structural example)

   This file must contain, in order:
    1.  **Your Specific Project Request:** (e.g., the "gritos Dev Plan (a003)..." text). This is primarily used if `task_spawning_addon.txt` is active.
    2.  **The Full Content of `/prompts/Master_Prompt_Segment.txt` (from this framework repo), with its top `[[USER PATH CONFIGURATION]]` and `[[USER_ADDON_SELECTION]]` blocks EDITED BY YOU for your target project.**
        *   **Example `[[USER_ADDON_SELECTION]]` for full planning:**
            ```
            [[USER_ADDON_SELECTION]]
            [x] task_spawning_addon.txt  # Enables project planning and task generation
            [x] task_resumption_addon.txt  # Useful for generated tasks
            # [ ] example_coding_standards_addon.txt
            [[END USER_ADDON_SELECTION]]
            ```
        *   If you do *not* select `task_spawning_addon.txt`, the AI will not generate a project plan unless another selected add-on provides such specific instructions.

**C. Invoke the Planning AI:**
   Provide the simple prompt to your Planning AI, pointing to the spawn prompt file you just created in your target repository:
   `"/prompts/p_your_project_spawn.txt is the prompt, it is in the repo"` (adjust path if you named or placed it differently).

The Planning AI will then:
1.  **Read your spawn prompt.**
2.  **Process Core Instructions:** It will first process the included `/prompts/iep/Core_Planning_Instructions.txt`. This involves:
    *   Parsing the `[[USER PATH CONFIGURATION]]` to understand where to read inputs and place outputs.
    *   Parsing the `[[USER_ADDON_SELECTION]]` to identify which add-on files to load from `/prompts/add_ons/` in your target repository.
3.  **Conditional Add-on Execution:**
    *   **If `task_spawning_addon.txt` IS SELECTED** in `[[USER_ADDON_SELECTION]]`:
        *   The AI will load and execute the instructions from `task_spawning_addon.txt`. This will lead to the primary planning activities: interpretation of your project request, generation of a main development plan (e.g., `project_dev_plan.md`), a `00_task_launch_plan.md`, and individual task prompt files.
        *   Other selected add-ons (like `task_resumption_addon.txt`) will be treated as supplementary, typically by having their content appended to the generated task prompts as per instructions in `task_spawning_addon.txt`.
        *   Outputs will be placed according to your `[[USER PATH CONFIGURATION]]`.
    *   **If `task_spawning_addon.txt` IS NOT SELECTED**:
        *   The AI will acknowledge this and will **not** generate a main development plan, TLP, or task prompts by default.
        *   It will proceed to load and execute any *other* add-ons you have selected. These add-ons must provide their own complete directives if they are to perform significant actions.
        *   If no other primary directive add-ons are selected, the AI will perform minimal actions (e.g., generate `Information_Exchange_Protocol.md` as per `Core_Planning_Instructions.txt`), report that no main planning tasks were performed, and await further instructions or conclude its operations.
4.  **Read Supporting Files:** It will read `Base_IEP.txt` and the content of all successfully located selected add-ons from your target repository as needed.

This modular approach provides greater flexibility. While the full planning and task generation capabilities are available via `task_spawning_addon.txt`, you can also use the MPS framework with other add-ons for different purposes, or develop new add-ons with unique functionalities.
