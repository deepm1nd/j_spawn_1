# Potential Feature Enhancements for MPS Framework & Generated Plans

This document lists potential features that could add further insight and utility to the Master Prompt Segment (MPS) framework or the project plans it helps generate.

## For the MPS Framework & Generated Artifacts:

1.  **Dynamic Risk Assessment Field in IEP:**
    *   **Feature:** Add a `Risk-Level: <Low/Medium/High>` field to the Information Exchange Protocol (IEP). When a Task AI makes a commit, it assesses the risk/complexity of its changes.
    *   **Insight:** Helps human reviewers prioritize, identify problematic commits sooner, and could inform automated testing strategies.

2.  **Estimated Time / Complexity in Task Prompts:**
    *   **Feature:** The Planning AI, when generating task prompts, includes an *estimated effort* (e.g., T-shirt size: S, M, L, XL, or story points) for each task.
    *   **Insight:** Aids in overall project timeline estimation, resource allocation, and identifying overly large tasks. Accuracy would be heuristic.

3.  **Automated Dependency Linkage in `00_task_launch_plan.md`:**
    *   **Feature:** The Planning AI, when generating `00_task_launch_plan.md`, explicitly includes a "Depends on: \[Task ID]" note within task descriptions if dependencies (especially serialized ones) are identified.
    *   **Insight:** Makes task dependencies clearer to the user directly from the launch plan.

4.  **"Knowledge Capture" Section in `_next_steps.md`:**
    *   **Feature:** Task AIs are instructed to include a "Key Learnings & Discoveries" section in their `_next_steps.md` file.
    *   **Insight:** Captures non-obvious insights, tricky aspects of the codebase, or clever workarounds, forming an organic knowledge base.

5.  **Visual Plan Overview (Advanced):**
    *   **Feature:** The Planning AI attempts to generate a simple visual representation of the plan (e.g., DOT language graph description or a Markdown mermaid chart) showing phases, tasks, and dependencies.
    *   **Insight:** Provides an easier-to-grasp overview of complex plans.

6.  **Automated Sanity Checks for Task Prompts by Planning AI:**
    *   **Feature:** The Planning AI includes a meta-routine to perform sanity checks on the prompts it generates (e.g., presence of `_dev_plan.md` creation instruction, IEP reference).
    *   **Insight:** Improves consistency and quality of generated task prompts.

7.  **Feedback Loop for MPS Refinement via `HANDOFF_NOTES.md`:**
    *   **Feature:** The `HANDOFF_NOTES.md` (used for the MPS development task itself) could have a dedicated section for "MPS Performance Feedback." Users or Task AIs could note when prompts lead to confusion or suboptimal results.
    *   **Insight:** Creates a direct mechanism for improving the MPS based on the performance of AIs using its output.

8.  **Confidence Score from Planning AI:**
    *   **Feature:** The Planning AI provides a "Confidence Score" (e.g., 0-1.0) on how well it believes its generated plan meets all MPS constraints and user request specifics, especially for complex task breakdowns or merge conflict avoidance.
    *   **Insight:** Signals to the user areas of the plan that might require more human scrutiny.

## For the Process of Using the MPS:

9.  **Pre-flight Check Prompt for User's High-Level Plan:**
    *   **Feature:** A small, separate utility prompt for users to get AI feedback on the clarity and completeness of their high-level project plan *before* combining it with the MPS.
    *   **Insight:** Improves the quality of input to the main MPS-driven planning process, leading to better outputs from the Planning AI.
