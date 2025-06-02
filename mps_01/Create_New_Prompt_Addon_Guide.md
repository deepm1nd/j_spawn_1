# Guide for Creating Prompt Add-ons (MPS-like Modules)

## 1. Introduction

This guide provides a methodology for developing "Prompt Add-ons" (also referred to as Master Prompt Segments or MPS-like modules). These add-ons are blocks of instructional text that can be appended to a user's primary prompt to guide an AI instance (e.g., Jules) in performing tasks with specific behaviors, generating particular artifacts, or adhering to defined protocols.

The goal is to create modular, reusable, and clear sets of instructions that enhance the capabilities and reliability of AI task execution. Designing with modularity also aids potential future integration into tools like a "Prompt Creation Webapp."

## 2. Core Principles for Add-on Design

*   **Clear Purpose:** Each add-on must have a single, well-defined purpose. What specific behavior or output should it induce in the AI?
*   **Explicit Instructions:** Use clear, direct, and unambiguous language. Avoid jargon where possible or define it clearly.
*   **Modularity:** Design the add-on to be self-contained as much as possible. If it relies on other processes or documents (like a Base IEP), these dependencies should be explicitly stated.
*   **User-Centricity:** Consider the end-user of the AI that will be guided by your add-on. How will this add-on make the AI's output more useful, reliable, or manageable for them?
*   **Atomicity (for AI Actions):** Instruct the AI to perform actions in logical, manageable steps.
*   **Idempotency (where applicable):** If an AI task might be retried, design instructions within the add-on so that re-executing them (or parts of them) on an already partially completed task doesn't cause errors.

## 3. Steps to Develop a New Prompt Add-on

### Step 3.1: Define the Add-on's Purpose and Scope

*   **Objective:** What is the primary goal of this add-on? (e.g., "Instruct an AI to generate a resumable prompt for its current task," or "Guide an AI to perform detailed code reviews according to a checklist.")
*   **Target AI:** Which type of AI is this add-on for? (e.g., a "Planning AI" that breaks down projects, or a "Task AI" that executes specific development tasks).
*   **Inputs:** What information does the add-on expect to be present in the user's primary prompt or in the environment?
*   **Outputs/Artifacts:** What specific files, text, or actions should the AI produce as a direct result of this add-on's instructions? (e.g., a new file, a section in an existing document, a specific commit message format).
*   **Success Criteria:** How will you know if the add-on is effective?

### Step 3.2: Draft the Add-on Text

This is the core instructional block. Structure it logically.

*   **Opening Directive:** Clearly state that the following text contains special instructions for the AI.
    *   Example: `[[START OF 'TASK RESUMPTION' ADD-ON: Instructions for AI Task Management]]`
*   **Core Instructions:** Detail the steps the AI must take.
    *   Use imperative verbs (e.g., "Create...", "Define...", "Append...", "Ensure...").
    *   Break down complex instructions into smaller, numbered, or bulleted steps.
    *   Specify any conditions or triggers for actions.
*   **Interaction with Other Protocols/Documents (e.g., Base IEP):**
    *   If the add-on requires the AI to use or extend a common protocol (like a Base Information Exchange Protocol), be explicit.
    *   **Extending a Base IEP:** A common pattern is to use a small `Base_IEP_Definition.md` that defines core commit message fields. Add-ons can then extend this by instructing the AI to add specific structured tags or data within a general-purpose field like `Notes-To-Next-Jules`.
        *   Example: "When you perform action X as per this add-on, you must include the following in your commit message under `Notes-To-Next-Jules`: `[ADDON_XYZ_STATUS]: Completed_Action_X_Details`."
*   **Error Handling/Edge Cases (Optional but Recommended):**
    *   Briefly consider common failure points and guide the AI on how to react or report them.
*   **Closing Directive:** Clearly mark the end of the add-on.
    *   Example: `[[END OF 'TASK RESUMPTION' ADD-ON]]`

### Step 3.3: Design Supporting Artifacts (if any)

*   If your add-on instructs the AI to create specific documents (e.g., a `Resumption_Prompt.txt`, a `Code_Review_Checklist_Report.md`), define the structure and content of these artifacts.
*   If your add-on relies on a template that the AI should populate, provide this template text within the add-on itself or specify its location.

### Step 3.4: Define Interaction with User-Specified Paths

*   If the add-on's behavior or output locations can be customized by user-provided paths, clearly state:
    *   What paths the user can specify.
    *   The default paths if the user doesn't provide them.
    *   How the AI should use these paths.
    *   Example: "The Resumption Prompt you generate should be saved to the user-specified 'Task Output Directory'. If not specified, save it as `[Default Path]`."

### Step 3.5: Create an Example Usage Guide for the Add-on

*   **Purpose:** Explain to an end-user (who wants to use your add-on) how to combine it with their primary prompt.
*   **Content:**
    *   Briefly explain what the add-on does.
    *   Show an example of how to append the add-on text to a typical user prompt.
    *   Describe the expected outcome or behavior from the AI when the add-on is used.
    *   Detail any user-configurable paths or parameters relevant to this add-on.
*   Save this as `<YourAddonName>_Usage_Guide.md`.

### Step 3.6: Test and Refine

*   Append your drafted add-on to various test prompts and submit them to an AI instance.
*   Evaluate the AI's response and output against your success criteria.
*   Refine the add-on text, supporting artifact definitions, and usage guide based on test results until it performs reliably and clearly.

## 4. Example: Add-on for Task Resumption (Conceptual Outline)

*   **Purpose:** Instruct a Task AI to create a "Resumption Prompt" for its own task.
*   **Add-on Text Would Include:**
    *   Instructions to summarize original goal, work done (referencing IEP commits), `_dev_plan.md` state, next steps.
    *   Instruction on where to save the Resumption Prompt (e.g., user-defined path or default).
    *   Instruction to log the creation/update of the Resumption Prompt in its commit message (e.g., `[RESUMPTION_PROMPT_STATUS]: Updated at path/to/file`).
*   **Supporting Artifact:** The Resumption Prompt file format.
*   **Usage Guide:** How to add this to any task prompt for a Task AI.

## 5. Conclusion

Developing effective Prompt Add-ons requires clear thinking, precise language, and iterative testing. By following this guide, you can create powerful, reusable modules to enhance AI capabilities across a variety of tasks. Remember that future integration into a UI or webapp is a possibility, so clarity, modularity, and well-defined interfaces (even if text-based) are key.
