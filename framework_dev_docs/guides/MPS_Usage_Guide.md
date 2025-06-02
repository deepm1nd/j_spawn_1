# MPS Usage Guide (for Canonical Framework using `prompts/Master_Prompt_Segment.md`)

## 1. Introduction
This guide explains how to use the canonical `Master_Prompt_Segment.md` (located at `/prompts/Master_Prompt_Segment.md` in this framework's development repository) to instruct a Planning AI.

## 2. Core Components You Will Use/Prepare
*   **Content of `/prompts/Master_Prompt_Segment.md` from this framework repo:** This is the core instructional text. You will copy its *content* into your main spawn prompt file.
*   **Content of `/prompts/iep/Base_IEP.txt` from this framework repo:** This is the Base IEP. You will copy its *content* to a specific path in your target repository.
*   **Content of add-on files (e.g., `/prompts/add_ons/task_resumption_addon.txt`) from this framework repo:** For any add-ons you wish to use. You will copy their *content* to specific paths in your target repository.
*   **Your Project-Specific Request:** E.g., the "gritos Dev Plan (a003)" text.
*   **Your `[[USER_ADDON_SELECTION]]` block:** If you are using add-ons.

## 3. Setting up Your Target Repository & Crafting the Main Spawn Prompt

To have a Planning AI generate a plan for your project (e.g., "gritos"):

**A. Prepare Your Target Repository (where Planning AI will run):**
1.  Ensure all your project-specific input documents (e.g., for gritos: `gritos_product_specification.md`, `concept/research/` files, etc.) are present at the paths your project request will refer to.
2.  Create a `/prompts/` directory at the root of your target repository.
3.  Inside `target_repo/prompts/`, create an `iep/` subdirectory. Copy the content from this framework's `/prompts/iep/Base_IEP.txt` into `target_repo/prompts/iep/Base_IEP.txt`.
4.  Inside `target_repo/prompts/`, create an `add_ons/` subdirectory. For each add-on you intend to use (e.g., `task_resumption_addon.txt`), copy its content from this framework's `/prompts/add_ons/` directory into the corresponding file in `target_repo/prompts/add_ons/` (e.g., `target_repo/prompts/add_ons/task_resumption_addon.txt`). Refer to this framework's `/prompts/add_ons/available_addons_manifest.md` for available add-ons.

**B. Create Your Main Spawn Prompt File in Target Repository:**
   (e.g., `target_repo/prompts/p_your_project_spawn.md`)

   This file must contain, in order:
    1.  **Your Specific Project Request:** (e.g., the "gritos Dev Plan (a003)..." text).
    2.  **(Optional but Recommended if using add-ons) Your `[[USER_ADDON_SELECTION]]` Block:** Example:
        ```text
        [[USER_ADDON_SELECTION]]
        [x] task_resumption_addon.txt
        [[END USER_ADDON_SELECTION]]
        ```
    3.  **The Full Content of `/prompts/Master_Prompt_Segment.md` (from this framework repo):** Copy and paste the entire content of the canonical MPS here.
        *   **Crucially, before using it, EDIT the `[[USER PATH CONFIGURATION]]` block at the VERY TOP of the `Master_Prompt_Segment.md` text you just pasted.** Set the output paths for the Planning AI relevant to *your target project*. For example:
            ```text
            [[USER PATH CONFIGURATION]]
            Main Iteration Folder: . # Target repo root for this planning run's outputs
            Prompts Folder: generated_gritos_prompts # Planning AI will create this for TLP, task prompts
            Submodule Plan Destination: project_plans # Planning AI will create this for main plan & _dev_plans
            Inter-AI Communication Folder: generated_gritos_prompts/ipc # Planning AI will create this
            User-Specified Task Output Base Path: dev/src/kernel # For gritos code
            [[END USER PATH CONFIGURATION]]
            ```

**C. Invoke the Planning AI:**
   Provide the simple prompt to your Planning AI, pointing to the spawn prompt file you just created in your target repository:
   `"/prompts/p_your_project_spawn.md is the prompt, it is in the repo"` (adjust path if you named it or placed it differently).

The Planning AI will then:
- Read your spawn prompt.
- Parse the `[[USER PATH CONFIGURATION]]` from the top of the MPS segment you embedded.
- Parse your project request.
- Parse the `[[USER_ADDON_SELECTION]]` (if present) from your project request.
- Follow all instructions in the MPS segment, including reading the Base IEP and selected add-ons from *its local* `/prompts/iep/Base_IEP.txt` and `/prompts/add_ons/[addon_name].txt` (which you set up in Step A).
- Generate its outputs (target project's TLP, task prompts, etc.) into the paths you defined in `[[USER PATH CONFIGURATION]]`.
