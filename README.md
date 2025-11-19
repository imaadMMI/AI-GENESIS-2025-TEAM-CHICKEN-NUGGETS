# ProcurePilot – Multi-Industry Vendor Selection & Evaluation

## Overview
ProcurePilot is an Opus workflow that automates vendor shortlisting and selection across industries. It turns noisy requirements + CSV vendor data into a ranked, audited recommendation using the **Intake → Understand → Decide → Review → Deliver** pattern.

## Access the workflow at:
https://app.opus.com/app/workflow/share/c33c50ac-6d26-46ef-9360-8a3916a22017

## Make sure to configure these before you start your workflow:
1. Insert one industry CSV and one industry requirements doc in workflow input
2. Generate an API key from LangSearch | Free Web Search API, Free Rerank API. The World Engine For AGI. And insert it a Bearer Auth in the External Service.

---

## Inputs
**Workflow Input**
- **Vendor CSV File** – list of vendors and products.
- **Business Requirements Form** – product/service needs, constraints, budget.
- **Soft Constraint Thresholds** – JSON object defining allowed relaxations (e.g. `0.05` for 5%).

---

## Workflow Logic

### 1. Intake & Understand
- **Extract Structured Requirements** (Agent)  
  Parses the requirements form into structured JSON (product, specs, quantity, budget, constraints).
- **Filter Vendors By Product**  
  Keeps only vendors whose products match the requested category.

### 2. Decide (Fit + Reputation)
- **Select Closest-Fit Vendors**  
  Applies hard constraints and soft thresholds to narrow down candidate vendors.
- **List Vendors for Reputation Check** → **Loop Vendor Reputation Search (External Service)**  
  For each candidate, calls:
  - `POST https://api.langsearch.com/v1/web-search`  
  to summarize web results and gauge brand awareness and quality.
- **Vendor Reliability Validation** → **Filter Reliable Vendors**  
  Interprets API responses, flags risks, and removes unreliable vendors.

### 3. Review (Human-in-the-loop)
- **Human Review for Vendor Reputation…**  
  Used when data is incomplete or risk signals are high.
- **Human review to verify unreliable vendors…**  
  Reviewer can confirm removal or override and keep a vendor.

### 4. Score, Rank & Justify
- **Score Vendors Multi-Criteria**  
  Scores each vendor on fit, reliability, constraints, and price.
- **Rank Vendors by Score**  
  Produces an ordered list.
- **Generate Vendor Justifications**  
  Creates short, human-readable rationales for each selected vendor.
- **Consolidate Procurement Outputs**  
  Bundles requirements, ranking, scores, justifications, and review results into one object.

### 5. Deliver & Export
- **Workflow Output**  
  Exposes the final consolidated decision as structured JSON (suitable for API or downstream tools).
- **Export Data (Email)**  
  Sends an email to the procurement contact with:
  - Subject: *“Best vendor identified for procurement requirements”*  
  - Body: brief summary of the chosen vendor(s) and rationale.

---

## Reviews, Errors & Audit

- Automatic checks validate required fields, product–vendor match, and API responses.
- API timeouts or malformed data trigger retries, fall back to “unknown reliability”, and route to human review.
- Human review nodes capture accept/reject/override decisions.
- The final JSON output acts as an **audit artifact**, containing:
  - Extracted requirements  
  - Filter/validation outcomes  
  - Reputation data and reliability flags  
  - Human review actions  
  - Scores, ranks, and justifications.
