# Self-Hosted n8n AI Automation Pipelines

A production-ready collection of asynchronous, event-driven automation workflows running on a self-hosted n8n instance via Docker Compose. These workflows leverage Large Language Models (LLMs) via native JavaScript processing to automate data extraction, triage backend databases, and handle real-time communications.

##  System Architecture & Tech Stack

- **Orchestration Engine:** n8n (Self-hosted via Docker container)
- **AI Core:** Google Gemini 
- **Database & Storage:** MySQL (Relational Lead Storage), Google Sheets API (Analytical Dashboarding)
- **Communication Layers:** Webhook endpoints (Ingestion), Gmail API (Alerting & Triage)
- **Scripting Layer:** Custom JavaScript (Node.js engine in n8n for defensive parsing and data sanitization)

---

##  Workflows Showcase

### 1. AI Customer Support Automation System
An event-driven pipeline that intercepts incoming customer messages, dynamically classifies user intent, assigns operational priority, and routes critical requests immediately.

- **File:** `workflows/AI_Customer_Support.json`
- **Core Pipeline Pipeline Flow:**
  1. **Ingestion:** A `POST` Webhook listener intercepts incoming payloads containing customer messages.
  2. **Defensive Validation:** An `If` conditional node flags and throws an API error response if critical values (`email` or `message`) are missing.
  3. **Intelligent Intent Parsing:** The agent runs zero-shot classification to group the inquiry (*Refund Request, Order Issue, Pricing, General Inquiry, or Spam*) and assigns a corresponding priority status.
  4. **Data Normalization:** A JavaScript script sanitizes and extracts the structured JSON response using regex logic to ensure schema consistency.
  5. **Persistence:** The processed ticket logs dynamically to a Google Sheets master dashboard.
  6. **Real-time Alerting:** A secondary conditional routing block isolates `High` priority states and instantly alerts internal teams via the Gmail API, while concurrently returning automated receipt responses back to the webhook client.

---

### 2. Operations Intelligence & SQL Lead Processor
An automated extraction pipeline designed to intercept structured structural inputs, compute a high-precision urgency score based on data-risk profiles, and dynamically commit data records to a relational database.

- **File:** `workflows/sql_lead_data_automation.json`
- **Core Pipeline Flow:**
  1. **Ingestion:** Listens for operational metadata updates via a customized secure webhook path (`/lead-db`).
  2. **Entity Extraction & Risk Scoring:** The text payload routes to an operational AI agent that strips conversational padding and runs Named Entity Recognition (NER) to locate parameters like Product name etc. it maps financial and operational constraints into a linear numeric `urgency_score`.
  3. **Robust JSON Parsing:** A native JavaScript node processes the model's output block, running a try/catch exception-handling routine over the data parser to guarantee string safety before writing to the database.
  4. **Relational Database Sync:** The structured entity properties (`extracted_entities` and `urgency_score`) are mapped onto defined tables and securely appended directly to a live **MySQL database instance**.
  5. **Client Response:** Issues an explicit JSON synchronization receipt back to the connection origin verifying data integrity.

---

##  Local Deployment & Import

### Prerequisites
- Docker & Docker Compose installed on your host machine.
- Active API keys configured for Google Gemini (PaLM/Gemini Cloud), Google Workspace (OAuth2 for Sheets/Gmail), and an accessible MySQL Instance.

### Import Workflow Files
1. Spin up your n8n container workspace.
2. In the n8n UI, create a new blank workflow workspace.
3. Click on the top-right menu icon (**⋮**) and select **Import from File**.
4. Choose either `AI_Customer_Support.json` or `sql_lead_data_automation.json` from the `workflows/` directory.
5. Re-link your local node environment variables to your custom credentials profiles (Gemini, SQL database targets, etc.) and switch the workflow status to **Active**.
