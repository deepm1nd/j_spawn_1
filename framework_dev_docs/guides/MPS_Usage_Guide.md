# MPS Usage Guide (for Canonical Framework using `prompts/Master_Prompt_Segment.txt`)

## 1. Introduction
This guide explains how to use the canonical `Master_Prompt_Segment.txt` (located at `/prompts/Master_Prompt_Segment.txt` in this framework's development repository) to instruct a Planning AI. The `Master_Prompt_Segment.txt` has been modularized to separate its core instructional logic into a dedicated file, making it more maintainable.

## 2. Core Components You Will Use/Prepare

*   **Content of `/prompts/Master_Prompt_Segment.txt` from this framework repo:** This text serves as the main wrapper for the Planning AI's instructions. You will copy its *content* into your main spawn prompt file. **Crucially, you must edit the `[[USER PATH CONFIGURATION]]` and `[[USER_ADDON_SELECTION]]` blocks at the top of the MPS content you copied to suit your target project.** This file now primarily contains user configuration sections and an include directive to load the core planning instructions.
*   **Content of `/prompts/iep/Core_Planning_Instructions.txt` from this framework repo:** This file contains the detailed, core instructions for the Planning AI, dictating how it should analyze requests, structure plans, generate task prompts, and manage outputs. You will copy its *content* to `/prompts/iep/Core_Planning_Instructions.txt` in your target repository.
*   **Content of `/prompts/iep/Base_IEP.txt` from this framework repo:** This is the Base Information Exchange Protocol. You will copy its *content* to `/prompts/iep/Base_IEP.txt` in your target repository.
*   **Content of add-on files (e.g., `/prompts/add_ons/task_resumption_addon.txt`) from this framework repo:** For any add-ons you wish to use (as selected in the `[[USER_ADDON_SELECTION]]` block). You will copy their *content* to corresponding paths in `/prompts/add_ons/` in your target repository. Refer to `/prompts/add_ons/available_addons_manifest.md` for a list.
*   **Your Project-Specific Request:** E.g., the "gritos Dev Plan (a003)" text.

## 3. Setting up Your Target Repository & Crafting the Main Spawn Prompt

To have a Planning AI generate a plan for your project (e.g., "gritos"):

**A. Prepare Your Target Repository (where Planning AI will run):**
1.  Ensure all your project-specific input documents (e.g., for gritos: `gritos_product_specification.md`, `concept/research/` files, etc.) are present at the paths your project request will refer to.
2.  Create a `/prompts/` directory at the root of your target repository.
3.  Inside `target_repo/prompts/`, create an `iep/` subdirectory.
    *   Copy the content from this framework's `/prompts/iep/Base_IEP.txt` into `target_repo/prompts/iep/Base_IEP.txt`.
    *   Copy the content from this framework's `/prompts/iep/Core_Planning_Instructions.txt` into `target_repo/prompts/iep/Core_Planning_Instructions.txt`.
4.  Inside `target_repo/prompts/`, create an `add_ons/` subdirectory. For each add-on you intend to use, copy its content from this framework's `/prompts/add_ons/` directory into the corresponding file in `target_repo/prompts/add_ons/` (e.g., `target_repo/prompts/add_ons/task_resumption_addon.txt`).
5.  The Planning AI will create its output "Prompts Folder" (e.g., `prompts/tasks/` or as specified by you in `[[USER PATH CONFIGURATION]]`) for the target project's TLP, task prompts, etc.

**B. Create Your Main Spawn Prompt File in Target Repository:**
   (e.g., `target_repo/prompts/p_your_project_spawn.txt` - refer to `framework_dev_docs/guides/p_gritos_dev_plan_spawn_EXAMPLE.md` in this framework repo for a structural example)

   This file must contain, in order:
    1.  **Your Specific Project Request:** (e.g., the "gritos Dev Plan (a003)..." text).
    2.  **The Full Content of `/prompts/Master_Prompt_Segment.txt` (from this framework repo), with its top `[[USER PATH CONFIGURATION]]` and `[[USER_ADDON_SELECTION]]` blocks EDITED BY YOU for your target project.**

**C. Invoke the Planning AI:**
   Provide the simple prompt to your Planning AI, pointing to the spawn prompt file you just created in your target repository:
   `"/prompts/p_your_project_spawn.txt is the prompt, it is in the repo"` (adjust path if you named or placed it differently).

The Planning AI will then:
- Read your spawn prompt.
- Parse the `[[USER PATH CONFIGURATION]]` and `[[USER_ADDON_SELECTION]]` blocks from the top of the MPS segment you embedded.
- Follow all instructions in the MPS segment. This includes processing the `[[INCLUDE Core_Planning_Instructions.txt FROM /prompts/iep/Core_Planning_Instructions.txt]]` directive, which causes it to load and execute the detailed planning instructions from `/prompts/iep/Core_Planning_Instructions.txt` (which you set up in Step A.3).
- It will also read the Base IEP from *its local* `/prompts/iep/Base_IEP.txt` and selected add-ons from `/prompts/add_ons/[addon_name].txt` (which you set up in Step A).
- Generate its outputs (target project's TLP, task prompts, etc.) into the paths you defined in `[[USER PATH CONFIGURATION]]` (e.g., its generated prompts going into the 'Prompts Folder' like `prompts/tasks/`).
