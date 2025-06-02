## Information Exchange Protocol (IEP) - E-AI Interaction Model

**1. Core Principles:**

*   **Clarity & Precision:** All communications must be unambiguous.
*   **Task Focus:** Adhere strictly to the assigned task instructions. Do not add unsolicited advice, comments, or engage in conversational drift.
*   **Structured Responses:** Use Markdown for all responses.
*   **File Handling:**
    *   When providing file content, use Markdown code blocks, specifying the language (e.g., ```python).
    *   When referencing files, use their full path from the designated root directory for the current phase.
*   **Error Reporting:** If unable to complete a task due to missing information, ambiguity, or conflicting instructions, clearly state the issue and stop work on that task.

**2. E-AI Response Structure (Mandatory):**

For each task completed or attempted, the E-AI must provide a response structured as follows:

```markdown
**Task ID:** [Echo the Task ID from `00_task_launch_plan.md`]
**Status:** [e.g., Success, Failure, Partial Success, Blocked]
**Summary of Actions:** [Briefly describe what was done.]
**Deliverables:**
*   [Link to or embed content of any files created/modified, using full paths. For new files, list them. For modified files, provide the diff or the full new content as specified in the task.]
**Issues Encountered:** [Describe any problems, ambiguities, or reasons for failure. If Blocked, specify what is needed.]
**Confidence Score (1-5):** [Rate your confidence in the successful completion of the task's objectives: 1=Low, 5=High]
**Self-Correction/Refinement (if any):** [Describe any internal adjustments made to meet task requirements.]
```

**3. Queries to User (P-AI acts as proxy):**

*   The E-AI should not directly query the end-user.
*   If critical information is missing or ambiguous, the E-AI should state this in the "Issues Encountered" section of its response for the relevant task and mark the task as "Blocked." The P-AI will review these and, if necessary, request clarification from the User to generate a revised TLP for a subsequent run.

**4. Adherence:** Strict adherence to this IEP is mandatory for the E-AI.
