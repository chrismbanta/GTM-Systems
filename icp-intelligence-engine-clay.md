# ICP Intelligence Engine — Built with Clay

## What It Does

Takes every deal (won and lost) from HubSpot, resolves company identity, enriches each company with firmographic and tech stack data, normalizes key signals, and outputs clean segmentation tiers — to answer: who are we winning with, who are we losing to, and why.

The output feeds into a Google Spreadsheet → n8n → Gamma, which generates the final ICP analysis report.

**Stack:** Clay · HubSpot · Claygent · n8n · Google Sheets · Gamma

---

## Pipeline — Step by Step

### 1. Raw Deal Import from HubSpot
Pulls every deal record into Clay. Each row = one deal. Fields imported:
- Deal Name, Deal Stage, Close Date, Amount, Deal Owner, Service Line
- Monthly Recurring Revenue, Closed Won Reason, Closed Lost Reason
- Associated Company, Contacts, and all relational IDs

### 2. Deal Outcome Classification
Formula categorizes every deal into one of three buckets:
- **CLOSED_WON**
- **CLOSED_LOST**
- **NURTURE_EXCLUDE** — "Later Opportunity" deals closing after March 23, 2025, filtered out of analysis

### 3. Company Identity Resolution
Many deals in HubSpot have missing or inconsistent company data. Three HubSpot lookup actions run to fill the gaps:
- Looks up the deal object to find associated company info
- Fetches associated company records and properties (name, domain)
- Retrieves company domain specifically for lost deals

Waterfall formulas then consolidate all sources into a single best-available company name and domain for every row.

### 4. Lost Reason Normalization
Free-text "Closed Lost Reason" fields from reps are messy and inconsistent. A normalization formula standardizes them into clean categories:
**Ghosted · Budget · Competitor · Deprioritized · Timing · Not Provided**

### 5. Company Enrichment
Using the resolved domain, Clay enriches each company with firmographic data:
- Company size, employee count, country, company type (public/private)
- Annual revenue, total funding range
- Industry, address, business type, primary offerings, revenue streams

### 6. AI Revenue Research (Claygent)
When Clay's database doesn't have revenue data, Claygent browses job posts, LinkedIn, and public sources to find real annual revenue or ARR — normalizes it to USD and classifies it into a revenue range (e.g. $10M–$25M).

### 7. CRM & GTM Stack Research (Claygent)
Claygent researches each company's tech stack across job postings, LinkedIn, and review sites to identify:
- CRM platform
- Marketing Automation tool
- Sales Engagement platform
- Data Enrichment tool
- ABM Platform
- Conversation Intelligence tool
- Other GTM tools in use
- Whether the company has a dedicated RevOps function

This tells you exactly what tools your won and lost customers are already using — critical for positioning and targeting.

### 8. Segmentation Tiers
Formula columns bucket every company into standardized segments for analysis:

| Tier | Buckets |
|------|---------|
| Revenue Tier | <$5M · $5M–$25M · $25M–$100M · $100M–$500M · $500M+ |
| Company Size Tier | 1–50 · 51–200 · 201–500 · 501–1000 · 1001–5000 · 5000+ |
| Industry Tier | Industry label with Unknown fallback |
| Company Type Tier | Public / Private / etc. with Unknown fallback |
| Sales Cycle Days | Days between deal creation and close date |
| Country / State | Geography with fallback and US state code extraction |

### 9. Output & Report Generation
Enriched, classified deal data is exported to Google Sheets → picked up by n8n → processed and sent to Gamma, which generates the final formatted ICP Analysis Report.

The ISW Certent Equity Management ICP report in this repo (`report-outputs/isw-certent-winloss-analysis-sample.pdf`) is a real output of this pipeline — analyzing 221 opportunities, identifying a 33% conversion rate, and surfacing that public companies convert at 43.1% with the top segments being Banking, Medical Equipment Manufacturing, Biotechnology, and Oil & Gas.

---

## Why This Matters

Most companies do ICP analysis manually in spreadsheets — pulling data from CRM, enriching it by hand, and trying to spot patterns. This pipeline does it automatically across every deal, with live firmographic and tech stack data, and delivers a formatted report. The output directly informs which accounts to prioritize in outbound and how to position against competitors already in the account's stack.
