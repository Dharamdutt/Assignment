# Loom Video Presentation Guide & Spoken Script
**Assessment:** Oytra Automation & QA Developer Take-Home Skills Assessment  
**Candidate:** Dharamdutt Kumar  
**Target Duration:** 2 minutes 30 seconds (Max 3 minutes)  

---

## 🎬 Recording Setup Checklist
1. **Screen Layout:** Have three browser tabs / windows open and ready:
   - **Tab 1:** `Task1_QA_Report_Dharamdutt_Kumar.pdf` (opened in Chrome or PDF reader)
   - **Tab 2:** n8n Canvas showing `Task 2: GitHub Intelligence Workflow`
   - **Tab 3:** n8n Canvas showing `Bonus: Uptime Monitor Workflow`
2. **Audio:** Clear microphone, quiet environment.
3. **Webcam:** Optional (small bubble in the bottom corner works great).

---

## 🎙️ Spoken Script (Word-for-Word Walkthrough)

### [0:00 - 0:30] Introduction & Task 1 Overview (Tab 1: PDF open on Screen)
> *"Hi Oytra team, my name is Dharamdutt Kumar, and this is my walkthrough for the Automation and QA Developer Take-Home Assessment.*
>
> *Starting with **Task 1**, I tested the Conduit RealWorld demo application across critical user journeys: authentication, article creation, feed pagination, and user settings.*
>
> *I identified **6 production deficiencies**, ranging from low to critical severity. The most urgent vulnerability is a **Critical Stored XSS** flaw in the article editor, where unescaped markdown allows raw HTML event handlers like `<img src=x onerror=alert()>` to execute in any reader's browser session.*
>
> *On page 2, I detailed a Root Cause Analysis explaining why this happens in rapid or AI-assisted builds—specifically piping raw markdown output straight into `dangerouslySetInnerHTML` without sanitization. I’ve outlined a three-tier remediation plan: integrating **DOMPurify** on the client, enforcing server-side sanitization, and deploying strict Content Security Policies."*

---

### [0:31 - 1:40] Task 2: n8n API Workflow Walkthrough (Switch to Tab 2: n8n Task 2 Canvas)
> *"Moving to **Task 2**, here is the automated API Integration Workflow I built in n8n.*
>
> *1. **Triggering:** The workflow triggers either on a daily schedule at 8:00 AM or on-demand via an incoming Webhook for testing.*
>
> *2. **Data Ingestion & Transformation:** It calls the **GitHub REST API** to query trending open-source TypeScript repositories. The first JavaScript Code node validates the payload, filters out archived repositories, sorts by stars, and extracts the top 5 candidates.*
>
> *3. **Enrichment:** For each repo, a secondary API call queries GitHub's **Releases endpoint** to retrieve the latest version tag and publish date. To ensure resilience, I enabled `continueOnFail: true` so that repositories without formal releases don't break execution; our merge node simply assigns a clean fallback.*
>
> *4. **Conditional Routing:** Next, an **IF node** routes repositories based on impact tier—separating tier-1 repos with over 25,000 stars from standard ones.*
>
> *5. **Dispatch:** Both branches format rich markdown digests and dispatch them to the notification webhook channel."*

---

### [1:41 - 2:15] Error Handling & Defense Strategy (Highlight Error Nodes & Bonus Task)
> *"For **Error Handling**, I took a multi-layered approach:*
>
> *First, network nodes use explicit timeouts and `continueOnFail: true` where optional data like releases might return a 404.*
>
> *Second, I implemented a global **Error Trigger subflow**. If any critical node fails, it catches the exception, captures the execution ID and failing node name, and dispatches an immediate incident payload to our alert channel without failing silently.*
>
> *(Switch briefly to Tab 3: Bonus Workflow)*
> *I also implemented the **Bonus Uptime Monitor**, which runs every 5 minutes against `demo.realworld.io`. It includes **3 automatic retries with a 2-second backoff**, tracks latency in milliseconds, and fires an alert if the status isn't 200.*"

---

### [2:16 - 2:30] Conclusion & Wrap-up
> *"All workflow JSON files, the QA PDF report, and complete documentation are organized in the submitted GitHub repository. Thank you for reviewing my assessment, and I look forward to discussing how I can contribute to the engineering and QA team at Oytra!"*

---

## 💡 Quick Tips for Dharamdutt
- Speak naturally and at a steady pace.
- Move your cursor smoothly to point at each node on the n8n canvas as you mention it.
- When done, copy the Loom video share link and make sure it is set to **"Anyone with the link can view"**.
