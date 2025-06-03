# PromptApp Manifest Guide

This guide defines the structure and content of the `[prompt_app_name]_manifest.json` file, which is used to define a "promptApp" within the Promptu framework.

## Location

Each `promptApp` must reside in its own subdirectory within the `promptu/apps/` directory. The manifest file for a `promptApp` named `MyExampleApp` would be located at:
`promptu/apps/MyExampleApp/my_example_app_manifest.json`

## Format: JSON

The manifest file must be in JSON format.

## Top-Level Structure

The JSON object at the root of the manifest file has the following properties:

| Key                    | Type   | Required | Description                                                                 |
| ---------------------- | ------ | -------- | --------------------------------------------------------------------------- |
| `promptAppDisplayName` | String | Yes      | The user-friendly display name for the `promptApp`.                         |
| `description`          | String | Yes      | A brief description of what the `promptApp` does.                         |
| `phases`               | Array  | Yes      | An array of phase objects, defining the stages of the `promptApp` workflow. |

## Phase Object Structure

Each object within the `phases` array has the following properties:

| Key         | Type  | Required | Description                                                        |
| ----------- | ----- | -------- | ------------------------------------------------------------------ |
| `phaseName` | String| Yes      | The user-friendly display name for this phase of the `promptApp`.    |
| `tasks`     | Array | Yes      | An array of task objects, defining the tasks within this phase. |

## Task Object Structure

Each object within a `tasks` array (inside a phase object) has the following properties:

| Key                        | Type   | Required | Description                                                                                                                                                                                             |
| -------------------------- | ------ | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `taskName`                 | String | Yes      | The user-friendly display name for this task. This name will be used in the `[[USER_promptAPP_NAME_CONFIGURATION]]` block in the main user prompt file (e.g. `post_promptu.txt`).                        |
| `description`              | String | Yes      | A brief description of the task's purpose.                                                                                                                                                              |
| `componentName`            | String | Yes      | The logical name of the Promptu component (e.g., add-on, util) that this task executes. The framework will resolve the actual path to this component based on a defined search order (see `core_planning_instructions.txt`). |
| `isIterable`               | Boolean| Yes      | Set to `true` if this task supports iterative execution (i.e., can be run multiple times, potentially with increasing detail, and will prompt the user to "repeat or move on"). Set to `false` otherwise.      |
| `defaultConfigOverrides`   | Object | No       | (Optional) An object where keys are parameter names of the underlying `componentName` and values are the default values to be used for those parameters *specifically when this task is run as part of this `promptApp`*. These overrides take precedence over the component's own `user_..._config.txt` defaults but can be further overridden by the user in their main prompt file. |

## Example: `defaultConfigOverrides`

If a task uses the `create_research_report` component, you might want to set a default `REPORT_BASENAME` for it within this `promptApp`:

```json
"defaultConfigOverrides": {
  "REPORT_BASENAME": "ComplianceAuditReport_Q1",
  "ITERATIVE_OUTPUT_DEPTH": "1" // Note: values here should be strings if they are user-editable fields
}
```

## Input Document Formats

For any tasks that involve document processing (e.g., using the `create_research_report` component), the initial supported input document formats are plain text:
- `.txt` files
- `.md` (Markdown) files
- Textual content fetched from web URLs.

Support for other formats (PDF, DOCX, etc.) may be considered for future enhancements.

## Component Resolution

When a `componentName` is specified for a task, the Promptu framework will attempt to locate the component by searching in a specific order of directories. Please refer to the `core_planning_instructions.txt` and the main `mps_usage_guide.md` for details on this search order (typically global components like add-ons and utils first, then app-specific components).
```
