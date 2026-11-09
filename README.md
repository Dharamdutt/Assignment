# Automation & QA Developer Take-Home Skills Assessment
**Candidate:** Dharamdutt Kumar  
**Contact:** dharam53579@gmail.com | +91 9631950092 | Lucknow, India  
**Role Applied:** Associate Software Developer (QA & Automation) — Oytra  
**Evaluation Target:** Conduit (RealWorld Demo App — `https://demo.realworld.io`) & n8n Workflow Automation  

---

## 📁 Repository Deliverables Index

| Deliverable File | Description | Status |
| :--- | :--- | :--- |
| **[`Task1_QA_Report_Dharamdutt_Kumar.pdf`](./Task1_QA_Report_Dharamdutt_Kumar.pdf)** | 2-Page Web App QA & Debug Matrix (6 defects) + Root Cause Analysis on Stored XSS | ✅ Complete |
| **[`Task2_Workflow_Dharamdutt_Kumar.json`](./Task2_Workflow_Dharamdutt_Kumar.json)** | Automated Multi-API Intelligence Workflow (GitHub Search + Release Enrichment + Conditional Routing + Error Trap) | ✅ Complete |
| **[`Bonus_UptimeMonitor_Dharamdutt_Kumar.json`](./Bonus_UptimeMonitor_Dharamdutt_Kumar.json)** | 5-Minute Synthetic Uptime Monitor with 3x Retry Backoff, Latency Tracking & Incident Dispatch | ✅ Complete |
| **[`Loom_Video_Script.md`](./Loom_Video_Script.md)** | 2–3 Minute Word-for-Word Video Presentation & Walkthrough Guide | ✅ Complete |

---

## 🛠️ Task 1: Web App QA & Debug Report Summary

The Conduit application (`https://demo.realworld.io`) was systematically evaluated using Chrome DevTools (Network Throttling, DOM Inspection, LocalStorage Manipulation, and Input Boundary Testing).

### Defect Matrix Summary (6 Discovered Deficiencies)
1. **Critical:** Stored Cross-Site Scripting (XSS) via Unsanitized Markdown parser rendering into `dangerouslySetInnerHTML`.
2. **High:** Form Double-Submission causing Slug Collision (`422 Unprocessable Entity`) under high network latency.
3. **High:** Expired/Corrupted JWT Token triggers silent infinite spinner and dead UI state (missing 401 response interceptor).
4. **Medium:** Layout Shift and broken image placeholders when user sets an unreachable/404 avatar image URL.
5. **Medium:** Pagination Offset State Desynchronization across feed tabs (`/api/articles/feed?offset=20` rendered on new tab).
6. **Low:** Unvalidated Duplicate and Whitespace tag badges accumulating in Article Editor.

### Root Cause Analysis & Fix
- **Target:** Issue #1 (Stored XSS).
- **Core Cause:** The markdown library compiles markup verbatim without an HTML sanitization pipeline. AI-assisted "vibe-coding" often ignores defensive client-side sanitization.
- **Remediation:** Integrated `DOMPurify.sanitize(rawHtml, { ALLOWED_TAGS: [...], ALLOWED_ATTR: [...] })` with defense-in-depth backend API validation and strict Content Security Policy (CSP). Full code snippet provided on Page 2 of the PDF report.

---

## ⚡ Task 2: n8n API Integration Workflow Architecture

### 1. APIs Integrated
- **Primary Public REST API:** GitHub Repositories Search API (`GET https://api.github.com/search/repositories?q=stars:>1000+language:typescript+sort:stars&order=desc&per_page=15`).
- **Secondary Enrichment API:** GitHub Releases API (`GET https://api.github.com/repos/{owner}/{repo}/releases/latest`).
- **Notification Endpoint:** HTTP Webhook Dispatcher (configured for Discord/Slack/Custom Webhooks).

### 2. Transformation & Filtering Logic
- **Filtering:** Code node filters out archived/disabled repositories, cleans metadata, and selects the top 5 highest-ranked active repositories.
- **Enrichment:** For each repository, queries GitHub's Release API to extract the latest release version tag, release date, and release notes URL. Repositories without releases are gracefully merged with fallback metadata (`"No formal release published"`).
- **Conditional Routing:** An **IF Node** evaluates repository tier:
  - `stars >= 25,000` ➔ Routes to **High-Priority Digest** formatting node.
  - `stars < 25,000` ➔ Routes to **Standard Digest** formatting node.
- **Payload Synthesis:** Synthesizes structured markdown cards with repository metrics, direct links, and release summaries.

### 3. Error Handling & Defense
- `continueOnFail: true` enabled on all network calls so that 404 responses (e.g. repo with no releases) never crash the pipeline.
- **Global Error Trigger Subflow (`n8n-nodes-base.errorTrigger`):** Captures any unexpected unhandled pipeline exceptions, extracting workflow name, execution ID, failing node, and timestamp, immediately dispatching a structured JSON incident alert.
- Zero hardcoded secrets; parameters follow n8n credential and variable injection standards.

---

## ⏱️ Bonus Task: Uptime Monitor Workflow Architecture

- **Frequency:** Executes every 5 minutes (`*/5 * * * *` cron schedule).
- **Target Endpoint:** `https://demo.realworld.io/api/tags`
- **Retry Mechanism:** Configured with `maxTries: 3` and `waitBetweenTries: 2000ms` exponential backoff before marking a probe as failed.
- **Latency Tracking:** Measures round-trip duration in milliseconds (`endTime - startTime`) and categorizes status into `OPERATIONAL`, `DEGRADED (> 2500ms)`, or `OUTAGE`.
- **Incident Dispatch:** If status code is non-200 or connection times out after 3 retries, an incident alert is dispatched containing the incident ID, failing endpoint, status code, latency, and failure diagnostic.

---

## 🚀 How to Import and Run Workflows in n8n

1. Open your n8n workspace (Desktop, Docker, or Cloud instance).
2. Go to **Workflows** ➔ Click **Add Workflow** ➔ Click the **3 dots (top right)** ➔ Select **Import from File**.
3. Select `Task2_Workflow_Dharamdutt_Kumar.json` or `Bonus_UptimeMonitor_Dharamdutt_Kumar.json`.
4. Click **Test Step** or **Execute Workflow** to run immediately.
   - For on-demand webhook execution of Task 2:
     ```bash
     curl -X POST https://<your-n8n-instance>/webhook-test/trigger-github-digest
     ```
