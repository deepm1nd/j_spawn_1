# Promptu Framework User Guide

## Introduction

Welcome to the Promptu Framework! This framework is designed to help you orchestrate complex AI-driven tasks by chaining together various components like promptApps, add-ons, and utilities. It uses a directive-based system where a planning AI processes instructions from configuration files you prepare.

## Core Concepts

*   **`pre_promptu.txt`**: This file is processed *before* your main project request. It's used to configure and run utilities that might prepare the environment or inputs for your main task (e.g., code analysis, prompt optimization).
*   **`post_promptu.txt`**: This is the main file where you define your project goal, select a primary workflow (either a multi-stage "promptApp" or a set of "add-ons"), and configure specific components.
*   **`core_planning_instructions.txt`**: This is an internal framework file located in `promptu/core/`. It contains the core logic that the planning AI uses to understand your selections in `pre_promptu.txt` and `post_promptu.txt`, discover components, and execute them. You generally don't need to edit this file.
*   **Components**:
    *   **promptApps**: Pre-defined multi-stage workflows. Each promptApp has a manifest defining its phases and tasks, and which underlying components (add-ons or utils) perform those tasks. Found in `promptu/apps/`.
    *   **Add-ons**: Modular components that perform specific complex tasks (e.g., generating a research report, planning a project). Found in `promptu/add_ons/`.
    *   **Utils (Utilities)**: Smaller components that perform specific, often preparatory or analytical, tasks. Found in `promptu/util/`.
*   **Configuration Blocks**: Special blocks within your `pre_promptu.txt` and `post_promptu.txt` files (e.g., `[[USER_APP_SELECTION]]`, `[[USER_CONFIG_FOR_componentName]]`) where you make selections and provide parameters. Parameter names within component-specific configurations (e.g., in `user_component_name_config.txt` or `[[USER_CONFIG_FOR_componentName]]` blocks) should be in `lowercase_snake_case`.
*   **Invocation Directive**: You initiate processing by providing the AI system with a simple directive, like `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/your_config_file.txt]]`.

## Setting Up Your Project

1.  **Copy Framework Files:**
    *   It's recommended to have a `promptu/` directory in your project repository.
    *   Copy `pre_promptu.txt` and `post_promptu.txt` from the Promptu framework source into your project's `promptu/` directory. You will customize these copies.
    *   Ensure the core framework files (`promptu/core/core_planning_instructions.txt`, `promptu/core/base_iep.txt`) and the component directories (`promptu/apps/`, `promptu/add_ons/`, `promptu/util/`) are present in your project, typically by copying them from the framework source.

## Using `pre_promptu.txt`

This file is for configuring utilities that run *before* your main `post_promptu.txt` processing.

**Initial Setup Instructions (from `pre_promptu.txt`):**
```
# === PRE-PROMPTU TEMPLATE INSTRUCTIONS FOR USER ===
#
# This file (`pre_promptu.txt`) is a template for configuring and running utilities
# *before* your main project request is processed by `post_promptu.txt`.
#
# 1. **COPY TO YOUR PROJECT (if not already there):**
#    Ensure this file (or a copy you configure) is in your project's
#    `promptu/` directory (e.g., `my_project_repo/promptu/pre_promptu.txt`).
#
# 2. **CONFIGURE YOUR `pre_promptu.txt` FILE:**
#    Edit the user configuration blocks within this file as needed:
#    *   `[[USER_UTIL_SELECTION]]`: Select utilities to run.
#    *   `[[USER_CONFIG_FOR_utilityName]]`: Provide specific configurations for
#       any selected utilities that require them. (Parameter names inside these
#       blocks should be lowercase_snake_case, e.g., `example_parameter_name`).
#
# 3. **INVOKE THE PRE-PROMPTU FRAMEWORK (YOUR ACTUAL PROMPT TO THE AI SYSTEM):**
#    Your actual prompt to the AI planning system for these pre-processing utilities
#    will be a single directive pointing to THIS prepared file. For example:
#
#    `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/pre_promptu.txt]]`
#
#    The AI system will load this file and process your utility selections and configurations.
#
# === END OF PRE-PROMPTU TEMPLATE INSTRUCTIONS ===
```

**Configuring Utility Selection (`[[USER_UTIL_SELECTION]]`):**
```
# Instructions for user:
# - List desired utility "app names" (folder names or direct .txt filenames) below, one per line.
# - Mark selection with '[x]' (case-insensitive for 'x').
# - Utilities must exist in `promptu/util/` in the target repository, either as
#   `promptu/util/utility_name.txt` or `promptu/util/utility_name/utility_name.txt`.
# - If a utility requires specific configuration, ensure you provide a corresponding
#   `[[USER_CONFIG_FOR_utilityName]]` block later in this file.

# Example:
# [x] promptimizer  # Assuming promptimizer is a utility available in promptu/util/
# [ ] another_utility
```

**Configuring Specific Utilities (`[[USER_CONFIG_FOR_utilityName]]`):**
Many utilities will have their own set of parameters. You provide these in a dedicated block. Remember, parameter names inside these blocks should be in `lowercase_snake_case`.
```
# Example of a utility-specific configuration block:
# [[USER_CONFIG_FOR_promptimizer]]
# # Add any parameters promptimizer might need here
# # example_parameter_name: Example_Value
# [[END_USER_CONFIG_FOR_promptimizer]]
```
Consult the documentation for each utility (often in a `user_utilityName_config.txt` file within its folder, or in the `promptu_usage_guide.md`) to know what parameters it accepts.

## Using `post_promptu.txt`

This file is your main configuration for defining your project goal and the primary AI workflow.

**Initial Setup Instructions (from `post_promptu.txt`):**
```
# === POST-PROMPTU TEMPLATE INSTRUCTIONS FOR USER ===
#
# This file (`post_promptu.txt`) is a template. To use this framework:
#
# 1. **COPY TO YOUR PROJECT:**
#    Copy the entire content of this file into a new file within your own project's
#    `promptu` directory (e.g., `my_project_repo/promptu/my_project_main_post_promptu.txt`).
#
# 2. **ADD YOUR PROJECT REQUEST (IN YOUR COPY):**
#    At the VERY BEGINNING of your new file (e.g., `my_project_main_post_promptu.txt`),
#    add your high-level project request. It is **highly recommended** to wrap
#    your request in `[[USER_PROJECT_REQUEST]]` and `[[END_USER_PROJECT_REQUEST]]` tags.
#    For example:
#
#    [[USER_PROJECT_REQUEST]]
#    My project is to design and plan for a new automated hydroponics system
#    for urban farming, focusing on modularity and water efficiency.
#    [[END_USER_PROJECT_REQUEST]]
#
#    (The rest of this template's content, starting with `[[USER_APP_SELECTION]]`
#    or other initial blocks, should follow immediately after your project request.)
#
# 3. **CONFIGURE YOUR `post_promptu.txt` FILE (IN YOUR COPY):**
#    Edit the user configuration blocks within your new file as needed:
#    *   `[[USER_APP_SELECTION]]`: Select a promptApp if you are using one.
#    *   `[[USER_ADDON_SELECTION]]`: Select standalone add-ons if not using a promptApp.
#    *   `[[USER_FRAMEWORK_SETTINGS]]`: Configure framework-level settings (see new block below).
#    *   `[[USER_promptAPP_NAME_CONFIGURATION]]`: If you selected a promptApp, define
#       which of its tasks to run. (See examples in this template).
#    *   `[[USER_CONFIG_FOR_componentName]]`: Provide specific configurations for
#       any components that require them. (Parameter names inside these
#       blocks should be lowercase_snake_case, e.g., `primary_input_doc_path`).
#
# 4. **INVOKE THE MPS FRAMEWORK (YOUR ACTUAL PROMPT TO THE AI SYSTEM):**
#    Your actual prompt to the AI planning system will then be a single directive
#    pointing to YOUR prepared file. For example:
#
#    `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/my_project_main_post_promptu.txt]]`
#
#    The AI system will load your file, find your project request within the
#    `[[USER_PROJECT_REQUEST]]` tags, and then process your configurations.
#
# === END OF POST-PROMPTU TEMPLATE INSTRUCTIONS ===
```

**Defining Your Project Request (`[[USER_PROJECT_REQUEST]]`):**
As noted above, place your high-level project goal at the very top of your `post_promptu.txt` copy, ideally wrapped in `[[USER_PROJECT_REQUEST]]` and `[[END_USER_PROJECT_REQUEST]]` tags. This is critical for the AI to understand the overall objective.

**Selecting a PromptApp (`[[USER_APP_SELECTION]]`):**
Use this block to select a multi-stage workflow.
```
# Instructions for user:
# - List desired "promptApp" names below, one per line.
# - **Select at most ONE promptApp.** Mark selection with '[x]' (case-insensitive for 'x').
# - promptApp "apps" must exist as subdirectories in `promptu/apps/` in the target repository.
# - Each promptApp folder must contain a manifest file: `[appName]/[app_name]_manifest.json`.
# - If a promptApp is selected, it will be the primary workflow. You will also need to provide a
#   corresponding `[[USER_promptAPP_NAME_CONFIGURATION]]` block (see example section below)
#   to specify which tasks within the promptApp to execute.
# - If no promptApp is selected, you can select one or more add-ons from `[[USER_ADDON_SELECTION]]`
#   to define the AI's task.

# [ ] CompliancePLM_App # Example: Select this to run the Compliance PLM workflow
```

**Configuring PromptApp Tasks (`[[USER_promptAPP_NAME_CONFIGURATION_EXAMPLES_SECTION]]`):**
If you select a promptApp, you need to specify which of its tasks to run. The following is an example configuration block for a hypothetical `CompliancePLM_App`. You would create a similar block, named appropriately for your selected promptApp (e.g., `[[USER_YourApp_CONFIGURATION]]`). Remember that any component-specific configurations referenced (like for `create_research_report` or `task_spawning_addon`) will use `lowercase_snake_case` for their parameters.
```
[[USER_promptAPP_NAME_CONFIGURATION_EXAMPLES_SECTION]]
# Instructions for user:
# - If you select a promptApp in `[[USER_APP_SELECTION]]` (e.g., `CompliancePLM_App`), you MUST provide a
#   corresponding configuration block in your main prompt file, formatted as:
#   `[[USER_CompliancePLM_App_CONFIGURATION]] ... [[END_USER_CompliancePLM_App_CONFIGURATION]]`.
# - This block allows you to specify which phases and tasks defined in the promptApp's manifest
#   you want to execute.
# - The Planning AI will use this block to determine the workflow.
# - For each task you select to run, you may also need to provide standard app-specific configuration
#   blocks (e.g., `[[USER_CONFIG_FOR_componentName]]`) if the underlying component used by that
#   task requires parameters beyond its defaults or any overrides set in the promptApp manifest.
#   (Parameter names in these blocks should be lowercase_snake_case).

# Example for `[[USER_CompliancePLM_App_CONFIGURATION]]` block:
# [[USER_CompliancePLM_App_CONFIGURATION]]
# # Instructions:
# # 1. Review the phases and tasks below, which correspond to the CompliancePLM app's manifest.
# # 2. Mark the tasks you want to execute for the current session with '[x]'.
# # 3. For tasks that use configurable components (like 'create_research_report' or 'task_spawning_addon'),
# #    ensure you also provide the necessary `[[USER_CONFIG_FOR_componentName]]` blocks
# #    in your main prompt if the defaults (from the component's user_config.txt or
# #    the promptApp manifest's defaultConfigOverrides) are not sufficient for your needs.
# #    Consult the component's `user_..._config.txt` file or the promptu_usage_guide.md for parameter details
# #    (remembering parameters are now lowercase_snake_case, e.g., primary_input_doc_path).
#
# # --- Concept Phase ---
# [ ] Research
# [ ] Architectural Specification
# [ ] Product Specification
# [ ] Product Requirements
#
# # --- Plan Phase ---
# [ ] Development Planning
# [ ] Change Management Planning
# [ ] Document Management Planning
# [ ] Requirements Management Planning
# [ ] Safety Planning
# [ ] CyberSecurity Planning
#
# # --- Development Phase ---
# [ ] Design
# [ ] Develop
# [ ] Test
# [ ] Review
#
# # --- Release Phase ---
# # (No specific tasks defined in manifest yet - selecting this phase currently has no direct action
# # unless future framework versions allow phase-level directives without specific tasks)
# # [ ] *Placeholder for future Release Task 1*
#
# # --- Retire Phase ---
# # (No specific tasks defined in manifest yet - selecting this phase currently has no direct action)
# # [ ] *Placeholder for future Retire Task 1*
# [[END_USER_CompliancePLM_App_CONFIGURATION]]

[[END_USER_promptAPP_NAME_CONFIGURATION_EXAMPLES_SECTION]]
```

**Selecting Add-ons (`[[USER_ADDON_SELECTION]]`):**
If you are *not* using a promptApp, you can select one or more add-ons here.
```
# Instructions for user:
# - If no promptApp is selected in `[[USER_APP_SELECTION]]`, you can select one or more add-on "apps" here
#   to define the AI's operation.
# - If a promptApp *is* selected, the tasks within that promptApp will define which components (including add-ons)
#   are run. Add-ons selected here might be ignored if a promptApp is active, or they might provide
#   supplementary, inheritable instructions if the promptApp's design allows for it (consult promptApp documentation).
#   Generally, a selected promptApp takes precedence.
#
# - List desired add-on "app names" (folder names) below, one per line.
# - Mark selection with '[x]' (case-insensitive for 'x').
# - Add-on apps must exist as subdirectories in `promptu/add_ons/` in the target repository.
# - Each add-on app folder must contain a primary instruction file: `[app_name]/[app_name].txt`.
#
# - If a primary add-on like 'task_spawning_addon' is selected (and no promptApp is active), other selected add-on apps
#   listed *before* it will typically have their primary instruction file content appended to the
#   generated task prompts, preserving their listed order.
#
# - App-specific configurations: Some add-on apps (and components used by promptApps) may use parameters
#   defined in a `[[USER_CONFIG_FOR_appName]]...[[END_USER_CONFIG_FOR_appName]]` block, which you would
#   place in your main prompt file. Refer to the component's documentation or its
#   `user_app_name_config.txt` template file for details (parameters are lowercase_snake_case).

[x] task_resumption_addon               # Example: Provides task state resumption capabilities for Task AIs.
# [ ] create_research_report          # Example: Select to generate a research report.
# [ ] task_spawning_addon             # CRITICAL (if no promptApp selected): Enables project planning & task generation.
                               # If selected as primary, list last.
```

**Configuring Framework Settings (`[[USER_FRAMEWORK_SETTINGS]]`):**
This block allows you to control framework-level behaviors.
```
# --- Handoff Notes Granularity ---
# Controls the level of detail Jules will record in the handoff_notes.md file at the end of its session.
# If checked, Jules will record detailed session notes.
# If unchecked (default), Jules will only record a concise summary.
[ ] log_full_handoff_notes

# --- Performance Feedback Logging ---
# Controls whether detailed performance feedback is logged to /framework_dev_docs/meta/performance_feedback_log.md.
# If checked, Jules will record detailed feedback at the end of its session.
# If unchecked (default), no performance feedback entry will be made.
[ ] log_performance_feedback
```

**General Component Configuration (`[[USER_CONFIG_FOR_componentName]]`):**
Whether part of a promptApp task or a standalone add-on, components often require specific parameters. These are provided in `[[USER_CONFIG_FOR_componentName]]` blocks, similar to the utility configuration example shown for `pre_promptu.txt`. Parameter names within these blocks and their corresponding `user_componentName_config.txt` files are expected to be in `lowercase_snake_case` (e.g., `primary_input_doc_path` instead of `PRIMARY_INPUT_DOC_PATH`). Always refer to the component's own documentation for details on its parameters.

## Invocation

Once your `pre_promptu.txt` (if used) and `post_promptu.txt` (your customized copy, e.g., `my_project_main_post_promptu.txt`) are configured, you instruct the AI system to process them using a directive:

*   For pre-promptu utilities:
    `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/pre_promptu.txt]]`
*   For your main project request and workflow:
    `[[PROCESS_FRAMEWORK_INSTRUCTIONS FROM /promptu/my_project_main_post_promptu.txt]]` (replace with the actual path to your file)

The AI planning system will then load the specified file and follow the instructions within, guided by `promptu/core/core_planning_instructions.txt`.
