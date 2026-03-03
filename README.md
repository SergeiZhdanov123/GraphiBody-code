# Phase 1 Data Audit: Biddy Lead Gen System

**To:** Leadership
**From:** Engineering
**Date:** March 1, 2026
**Subject:** Audit Results of the Lead Finder Extraction Pipeline

---

## Executive Summary

I have completed Phase 1 of onboarding onto the Lead Finder project. This phase involved setting up the local environment, testing the Exa + Claude API pipeline, and running a scripted audit against our most recent 22-record CSV database export. 

**The bottom line: The current automated extraction pipeline is structurally broken.** 

While the system correctly pulls data from the few hardcoded architect websites (like FVHD and Streeter Associates), the core engine—which searches the web for *new* bid notices—fails to extract usable CRM data. **0% of the current automated records are clean and complete.** 

Our most critical data points—contact emails and distribution methods (which drive our lead scoring)—are completely missing from the automated results.

---

## Audit Methodology

1. **Local Environment & Endpoint Testing:** I successfully connected the local worker (`http://localhost:8787`) to all four required external APIs (Exa, Anthropic Claude, Google Sheets, Slack). 
2. **Database Audit Script:** I wrote a custom Node.js script to parse our 22-row CSV export and flag systemic failures mapped back to the `index.js` logic.

### Missing Secrets Vulnerability
I discovered that our `.dev.vars` file behaves dangerously if missing credentials:

| Missing Secret | Consequence |
| :--- | :--- |
| `GOOGLE_SERVICE_ACCOUNT` | **Critical:** The Google Sheets integration `/sync-sheet` crashes the entire worker instead of failing gracefully. |
| `EXA_API_KEY` | Search returns empty; the pipeline silently does nothing. |
| `ANTHROPIC_API_KEY` | Claude AI is skipped; the system falls back entirely to the broken regex logic. |

---

## Database Audit Results (22 Rows)

The custom audit script graded the database against four failure categories. 

### Core Failure Scorecard

| Problem | Count | Rate | Business Impact |
| :--- | :--- | :--- | :--- |
| **Missing Contact Emails** | 22 / 22 | 100% | **Critical:** Blocks outbound sales pipeline |
| **Null Process (No Distribution Data)** | 19 / 22 | 86% | **Critical:** Breaks lead temperature scoring |
| **Hallucinated Firm Names** | 4 / 22 | 18% | **High:** Pollutes CRM with garbage data |
| **Duplicate Entries** | 1 pair | 4% | **Medium:** CRM bloat |
| **Clean, Complete Rows** | **0 / 22** | **0%** | **Systemic pipeline failure** |


### Deep Dive: Why the Pipeline is Failing

I dug into the `index.js` code to find exactly *why* the automated Exa Search pipeline is returning blank data. 

#### 1. The 100% Email Failure
*   **What happens:** The database has zero contact emails from Exa Search results.
*   **Why it happens:** When Exa finds a bid notice, it only sends us a tiny 2,000-character preview. Contact info is almost never in the first 2,000 characters of a webpage. The code tries to download the *full* webpage to find the email, but those full-page downloads are currently failing/timing out, leaving our regex parser with no text to read.

#### 2. The 86% Null Process Failure
*   **What happens:** For 19 out of 22 firms, the system doesn't know if they use email, walk-in, or platforms to distribute bids.
*   **Why it happens:** 
    1. The code looks for exact, rigid legal jargon (e.g., *"plans available via email"*). Real-world notices vary wildly.
    2. **The worker explicitly skips downloading `.pdf` files.** Because the vast majority of government bid documents are PDFs, our system is intentionally throwing away the exact files that contain the distribution instructions.

#### 3. Hallucinated Firm Names
*   **What happens:** The database is logging sentence fragments as firm names (e.g., *"Harriman As Our Architect"* or *"Key Players to Design"*).
*   **Why it happens:** The regex parser simply looks for the word "awarded to" or "selected" and grabs whatever words come next. If the sentence continues, it grabs the extra words too.

---

## Phase 2 Remediation Plan

To get this pipeline generating actual, usable leads for the sales team, we need to pivot away from fragile text-matching and lean heavily into AI processing and expanded document support.

Here is what I will build in Phase 2:

| # | Proposed Fix | Technical Approach | Expected Outcome |
| :--- | :--- | :--- | :--- |
| **1** | **Implement PDF Parsing** *(High Priority)* | Remove the `.pdf` exclusion block and integrate a lightweight text-extraction utility. | Unlocks the vast majority of government bid documents currently being ignored. |
| **2** | **AI-Driven Firm Naming** *(High Priority)* | Expand the Claude AI system prompt so it extracts the actual Firm Name instead of relying on broken regex patterns. | Eliminates 100% of hallucinated sentence fragments in the CRM. |
| **3** | **Dedicated Contact SearchLoop** *(High Priority)* | Trigger a secondary, highly targeted search specifically for that architect's "Contact Us" or directory page. | Radically increases email extraction rates. |
| **4** | **Relax Content Thresholds** *(Quick Win)* | Lower the current 500-character threshold limit so that shorter bid summaries still receive Claude analysis. | More bid evaluations processed. |
| **5** | **Fuzzy Deduplication** *(Medium Priority)* | Update grouping logic to calculate string similarity (Levenshtein distance). | *Mckinley Architecture* and *Mckinley Architecture and Engineering* will update the same record. |
