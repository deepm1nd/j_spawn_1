## Information Exchange Protocol (IEP) - Version 1.0

**1. Core Principles:**

*   **Clarity:** All information exchanged should be clear, concise, and unambiguous.
*   **Context Preservation:** Each exchange should build upon previous interactions, maintaining a shared understanding of the task.
*   **Structured Data:** When possible, data should be exchanged in structured formats (e.g., JSON, XML, CSV, Markdown tables) to facilitate processing.
*   **Version Control (Conceptual):** While not a formal Git system, treat significant changes in direction or information as new "versions" of the shared understanding. The User can explicitly state "Let's consider this a new approach, version X.Y."
*   **Error Handling:** If the AI encounters ambiguity or conflicting information, it should explicitly state the issue and request clarification from the User.

**2. Communication Structure:**

*   **User Request:**
    *   **Task ID:** (A unique identifier for the current sub-task, e.g., "TASK-001-DataAnalysis")
    *   **Objective:** (A clear statement of what the User wants to achieve with this request)
    *   **Context:** (Brief summary of relevant background information or a reference to previous messages, e.g., "Continuing from our discussion on customer segmentation...")
    *   **Inputs:** (Specific data, parameters, or files provided by the User)
        *   *Data Format:* (Specify if JSON, CSV, plain text, etc.)
        *   *Schema (if applicable):* (Describe the structure of the data)
    *   **Previous AI Output Reference (if any):** (Identifier of AI output being referenced or iterated upon)
    *   **Deliverables Expected:** (Description of the desired output from the AI, including format and key elements)
    *   **Constraints/Preferences:** (Any limitations, desired style, or specific methods to use/avoid)
*   **AI Response:**
    *   **Response ID:** (A unique identifier for this AI response)
    *   **Task ID Echo:** (Echoes the User's Task ID)
    *   **Summary of Understanding:** (AI briefly states its interpretation of the User's request)
    *   **Assumptions:** (Any assumptions made by the AI to fulfill the request)
    *   **Information Requests (if any):** (Specific questions if more information is needed)
    *   **Proposed Solution/Output:** (The AI's generated content, analysis, code, etc.)
        *   *Format:* (Specify the format of the output)
        *   *Confidence Score (Optional):* (AI's confidence in the solution, e.g., High/Medium/Low, or a numeric score with explanation)
    *   **Next Steps Suggested (Optional):** (AI may suggest potential follow-up actions or analyses)
    *   **Error Reporting (if any):** (Description of any issues encountered)

**3. Data Handling:**

*   **Sensitivity:** User should indicate if any data is sensitive. AI should treat sensitive data with appropriate care (details to be defined based on platform capabilities).
*   **Large Data:** For large datasets, User and AI should agree on methods for referencing or chunking data (e.g., "Process rows 1-1000 of dataset X," "Analyze the attached file 'customer_data.csv'").
*   **File Naming Conventions (Suggested):** `[TaskID]_[Descriptor]_[Version].[extension]` (e.g., `TASK-001-CustomerChurnAnalysis_Data_v1.csv`)

**4. Feedback Loop:**

*   User provides feedback on AI Response, referencing the Response ID.
*   Feedback should be specific (e.g., "In Response-AI-005, the customer segmentation for Group B is too broad. Please refine by incorporating purchase frequency.")
*   AI acknowledges feedback and incorporates it into subsequent responses.

**5. Evolution of the IEP:**

*   This protocol can be updated by the User. If the User proposes a change, they should state "Proposing update to IEP Section X.Y" and provide the new text. The AI should acknowledge and adopt the change for future interactions.

---
