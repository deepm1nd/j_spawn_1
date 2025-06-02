# Potential Feature Enhancements for MPS Framework & Generated Plans (v2 List)

This document lists potential features brainstormed for further enhancing the Master Prompt Segment (MPS) framework, the capabilities of the Planning AI guided by it, or the Task AIs executing the generated plans.

1.  **Standardized Error Handling Add-on for Task AIs:**
    *   **Concept:** Develop a new "Prompt Add-on" that provides Task AIs with a standardized way to handle and report errors (e.g., error categories, logging formats, escalation procedures).
    *   **Benefit:** Improves consistency in error reporting, simplifies debugging, and enables more automated error analysis or recovery.

2.  **Inter-Task Data Exchange Protocol/Mechanism:**
    *   **Concept:** Define a formal way for tasks (especially parallel tasks within the same phase with data dependencies) to signal data readiness or share small, structured data payloads (e.g., via defined output/input variables and files in a shared IPC location).
    *   **Benefit:** Facilitates more complex parallel workflows based on specific data products from prerequisite tasks.

3.  **Enhanced Task Progress Metrics & Reporting Standards:**
    *   **Concept:** Augment IEP and `_dev_plan.md` directives to include more quantitative progress metrics (e.g., Task AI estimates % complete for sub-steps; optional `Progress-Percent` field in IEP for significant commits).
    *   **Benefit:** Provides users with a more granular and quantitative view of task and project completion.

4.  **Automated Test Stub Generation Guidance:**
    *   **Concept:** An add-on or MPS instruction where the Planning AI, for coding tasks, provides a basic outline or list of suggested unit test cases/stubs for the Task AI to implement.
    *   **Benefit:** Promotes Test-Driven Development (TDD)/Behavior-Driven Development (BDD), ensures baseline test coverage, and clarifies expected behavior/edge cases for Task AIs.

5.  **'Criticality' or 'Priority' Field for Tasks in TLP:**
    *   **Concept:** The Planning AI assigns a "Criticality: <High/Medium/Low>" or "Priority: <P0/P1/P2>" to each task in the `00_task_launch_plan.md`.
    *   **Benefit:** Helps users (or orchestrator AIs) prioritize task launch and monitoring, especially with limited concurrent resources.

6.  **Configurable Add-on Stack in User's Main Spawn Prompt:** (This is now part of MPS v0.3.2 in `prompts/Master_Prompt_Segment.txt`)
    *   **Concept:** Modify the MPS so the user's main spawn prompt (e.g., in a `[[USER_ADDON_SELECTION]]` block) can list which available add-ons (from a central `/prompts/add_ons/` directory) should be activated and appended by the Planning AI to generated task prompts.
    *   **Benefit:** Makes the MPS framework highly flexible and user-configurable, turning add-ons into plug-and-play capabilities. Simplifies core MPS text.

7.  **Resource Requirement Hints for Tasks in TLP:**
    *   **Concept:** The Planning AI adds a "Resource-Hints:" field to tasks in the `00_task_launch_plan.md`, suggesting potential needs (e.g., CPU-intensive, memory-intensive, network access, specific tools).
    *   **Benefit:** Could help users or an orchestrator make better decisions about where/how to run certain Task AIs. Highly heuristic.

8.  **Project Glossary / Key Terms Generation:**
    *   **Concept:** For large projects, the Planning AI could generate a `project_glossary.md`, initially populated with terms from input documents or its main plan. Task AIs could be prompted to suggest additions.
    *   **Benefit:** Helps maintain consistent terminology and understanding across a distributed team (AI or human).

*(This list is for brainstorming and future design. Features would likely be implemented iteratively in new MPS versions or as standalone add-ons.)*
