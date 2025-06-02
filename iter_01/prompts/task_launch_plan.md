## Task Launch Plan (TLP): [Specific Task Name]

**1. Overall Goal:**
    *   [User to define the ultimate objective of this multi-step task. e.g., "Develop a Python script to analyze customer feedback and categorize it by sentiment and product mentioned."]

**2. Current Sub-Task:**
    *   **ID:** [e.g., TLP-001-DataIngestion]
    *   **Objective:** [e.g., "Ingest customer feedback from 'feedback.csv' and 'survey_results.json'."]
    *   **Expected Deliverables:** [e.g., "A consolidated Pandas DataFrame containing data from both sources. A summary report of data types and missing values."]

**3. Context & Background:**
    *   [User to provide any relevant information, links to previous discussions, or data sources. e.g., "Feedback data is semi-structured. Survey results are in JSON format. We previously discussed the need to normalize date formats."]

**4. Information Requirements (for AI to complete the current sub-task):**
    *   [ ] File: `feedback.csv` (Provided by User)
    *   [ ] File: `survey_results.json` (Provided by User)
    *   [ ] Clarification on how to handle conflicting entries for the same customer ID across files. (User to provide or AI to ask)

**5. Steps & Execution Strategy (User's initial plan, AI can suggest modifications):**
    *   1. Load `feedback.csv` into a Pandas DataFrame.
    *   2. Load `survey_results.json` into a Pandas DataFrame.
    *   3. [User or AI to detail merge/join strategy]
    *   4. Perform initial data cleaning (e.g., handle missing values, normalize text).
    *   5. Generate a data quality report.

**6. AI Role & Constraints:**
    *   **Role:** [e.g., "Act as a data processing assistant. Write Python code using Pandas. Provide explanations for each step."]
    *   **Constraints:** [e.g., "Do not use external libraries beyond Pandas and NumPy. Ensure code is well-commented."]
    *   **Tone/Style:** [e.g., "Formal and explanatory."]

**7. Evaluation Criteria (for the current sub-task's deliverables):**
    *   [e.g., "Successful creation of the consolidated DataFrame. Accuracy of the data quality report. Code readability."]

**8. Next Anticipated Sub-Task (Provisional):**
    *   [e.g., "Sentiment analysis of the 'comments' field in the consolidated data."]

**9. Iteration & Feedback Points:**
    *   [User to define when they want to review progress, e.g., "Review after data loading and cleaning (Step 4)."]
    *   AI should explicitly ask for confirmation to proceed past these points.

**10. Log of Significant Decisions / Changes to TLP:**
    *   [User and AI can record major deviations or clarifications here. e.g., "User clarified that conflicting entries should be resolved by prioritizing 'survey_results.json' data."]

---
