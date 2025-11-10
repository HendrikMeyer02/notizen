# Secret Rotation Management - Implementation Plan

## Phase 1: Preparation & Repository Structure (IaC)

### Task 1.1: Repository Setup
Create a dedicated GitHub repository (or folder within a Monorepo) for secret metadata.

### Task 1.2: Folder Structure
Define the folder structure, e.g., `credential_rotation/credentials.yaml`.

### Task 1.3: Template Creation
Create a template YAML file (`template.yaml`) with all required fields to lower the team's learning curve.

### Task 1.4: Documentation
Document the IaC process (YAML structure, Pull Request (PR) workflow) for the development teams.

---

## Phase 2: Jira Configuration (Ticketing System)

### Task 2.1: Ticket Type Definition
Define the Ticket Type (e.g., "Secret Rotation Task") with relevant fields:
- Credential Name
- Next Due Date
- Contact Person

### Task 2.2: Workflow Setup
Set up the Jira workflow: **TO DO → IN PROGRESS → DONE**

### Task 2.3: API Token Generation
Generate a Jira API Token for the Dashboard Backend service to allow automatic ticket creation.

### Task 2.4: Webhook Configuration
Configure the Jira Webhook: The webhook must notify the Dashboard Backend endpoint when a ticket transitions to "DONE" status.

---

## Phase 3: Dashboard Backend & API Integration

### Task 3.1: Database Development
Develop the Backend database to store secret metadata (including fields like `last_synced`, `next_due_date`).

### Task 3.2: Sync API Endpoint
Develop the Sync API Endpoint that will be called by GitHub Actions (receives and persists the YAML data).

### Task 3.3: Jira API Integration
Develop the Jira API Integration (functionality for automatically creating rotation tickets).

### Task 3.4: Webhook Endpoint
Develop the Webhook Endpoint that receives Jira notifications and updates the `last_changed` and `next_due_date` in the backend.

### Task 3.5: Date Calculation Logic
Implement the logic to calculate the `next_due_date` (based on `last_changed + period_days`).

### Task 3.6: Reporting Mechanism
Implement the Reporting Mechanism (Weekly KPI status emails).

---

## Phase 4: Automation (GitHub Actions)

### Task 4.1: Workflow File Creation
Create the GitHub Action Workflow file (`sync_metadata.yml`).

### Task 4.2: Trigger Configuration
Configure the trigger:
```yaml
on:
  push:
    branches: [main]
    paths: ['credential_rotation/**.yaml']
```

### Task 4.3: Validation Script
Implement the script within the Action to read and validate the changed YAML files (ensuring data quality).

### Task 4.4: API Integration
The script serializes the YAML data and calls the Dashboard's Sync API Endpoint.

### Task 4.5: Secrets Management
Set up necessary GitHub Secrets (e.g., API Key for the Dashboard Backend).