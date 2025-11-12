# 🛠️ Design Document: Automated Credential Rotation and Registration

## 1. Introduction: Credential Rotation in the DevSecOps Dashboard

The objective of this document is to automate and centralize the **Credential Rotation** process, eliminating the dependence on manually maintained Excel spreadsheets. The process aims to ensure that credentials (**Secrets**) are rotated regularly and that their status is centrally tracked in the **DevSecOps Dashboard**.

The **Credential Rotation** process will cover:
* **Registration** of secrets (e.g., API keys, database passwords) along with their metadata (responsible team/person, rotation frequency, expiration date, storage location).
* **Monitoring** of secrets based on their rotation schedule.
* **Notification** of the assigned owners when rotation is due.
* **Updating** the status in the central dashboard after successful rotation.
* **Deletion/Archiving** of secrets that are no longer needed.

---

## 2. Options for Registration and Workflow

Two main options are proposed for managing credential entries and the rotation workflow:

### 2.1. Option 1: Centralized Management via UI (Dashboard-Focused)

In this approach, all management of credential entries—including creation, updating, and deletion—is handled directly via a **User Interface (UI)** within the central DevSecOps Dashboard.

#### Workflow Description:

1.  **Creation/Update/Deletion:** A team member uses the UI in the DevSecOps Dashboard to add new credential entries, update metadata (like the responsible person, due date), or delete an existing entry.
2.  **Rotation Notification:** The Dashboard backend triggers notification emails to the assigned person when a secret is due for rotation.
3.  **Status Update:** After the actual secret rotation (performed outside the Dashboard, e.g., in a Key Vault or the source system itself), the responsible person returns to the UI to manually mark the entry as **"Updated"** and set a new future due date.

#### Pros and Cons:

| Pros | Cons |
| :--- | :--- |
| **Ease of Use:** Low barrier to entry due to a graphical interface (GUI). | **Lack of Version Control:** Changes are difficult to track (no Git-based audit trail). |
| **Direct Interaction:** Quick manual corrections and ad-hoc updates are possible. | **UI Development Overhead:** The Dashboard team must build and maintain a complex CRUD (Create, Read, Update, Delete) interface. |
| **Central Data Storage:** All data resides directly within the Dashboard backend from the start. | **Manual Status Update:** Updating the status *after* rotation remains a manual step in the UI, increasing friction. |

---

### 2.2. Option 2: Infrastructure as Code (IaC) via GitHub Repo with Jira Integration (Recommended Approach)

This approach leverages **each team's GitHub repositories** to declaratively manage secret metadata using YAML files. The workflow is driven by automation (GitHub Actions, the Jira API, and Jira Webhooks).

#### Workflow Description:

1.  **Creation (IaC):** Teams define their secret metadata in a **YAML file** (e.g., `corporate-account-credential-rotation.yaml`) within their respective repository structure.
2.  **Registration/Synchronization:** Any change to the credential YAML file committed to the `main` branch triggers a dedicated **GitHub Action**.
    * This Action reads the YAML files and **synchronizes/persists** the data into the DevSecOps Dashboard backend.
3.  **Rotation Ticket (Jira-Driven):** The Dashboard backend identifies secrets that are due. It automatically creates a **Jira Ticket** (e.g., "Rotate Secret X") assigned to the responsible person/team via the **Jira API**.
4.  **Status Update via Jira Webhook:** The person completes the physical rotation of the secret. When the Jira ticket is moved to the **"Done"** status:
    * A **Webhook** from Jira notifies the Dashboard backend.
    * The Dashboard backend automatically registers the credential as **"Updated"** and calculates the next due date.
5.  **Reporting:** The dashboard sends weekly emails reporting on the credential Key Performance Indicators (KPIs).

#### Pros and Cons:

| Pros | Cons |
| :--- | :--- |
| **Version Control:** Complete Git history (Who, When, What) and audit trail for all changes. | **Learning Curve:** Teams must adopt the practices of using YAML and the Pull Request (PR) process (IaC approach). |
| **Process Enforcement (PRs):** Mandates a code review process (`Pull Request`), which inherently enhances data quality and security. | **Dependency on Third Parties:** Requires stable API integration and communication with GitHub (Actions) and Jira. |
| **Zero UI Development:** Eliminates the need for the Dashboard team to build the credential input UI. | **Complex Initial Setup:** Higher effort required for the initial setup of GitHub Actions, webhooks, and Jira API integration. |
| **Automated Status Update:** Jira status transition automatically updates the rotation status, ensuring data accuracy. | **Synchronization:** Robust mechanisms must be implemented to ensure data from the repository is always successfully persisted to the Dashboard. |
| **Decentralized Ownership:** Metadata is stored with the relevant team's code, promoting ownership. | |

---

## 3. Recommendation

**Option 2 (IaC via GitHub/Jira)** is the **recommended approach**.

This option provides the necessary mechanisms for a **secure, automated, and auditable** process that aligns seamlessly with modern **DevSecOps** workflows (IaC, Pull Requests, Automation). The challenges of a more complex initial setup are outweighed by the long-term benefits of **version control** and **automated status updates** (via Jira).