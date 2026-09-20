# Naukri-ATS-Integration-Flaw-CaseStudy

# Case Study: Third-Party ATS Integration & State Validation Flaw

## Executive Summary
During a security and functionality audit of a major Indian job portal (Naukri Campus), I identified a platform logic flaw regarding external Applicant Tracking System (ATS) integrations. The platform allowed users to apply to job listings that pointed to deactivated third-party ATS accounts (`Trakstar Hire`), resulting in false "Applied" states on the candidate dashboard without successful application delivery.

I responsibly disclosed this issue to the platform's support and security team, which led to the validation and platform-wide cleanup of invalid job listings.

---

## Vulnerability Details
* **Target:** Major Job Portal Ecosystem
* **Category:** Business Logic Flaw / Broken Integration Validation
* **Impact:** High User Friction, Incorrect Application State Persistence, Dead External Callbacks

### Problem Flow:
1. **Broken Link Serving:** The portal listed active jobs (e.g., "Security Engineer") whose external application endpoint pointed to a defunct `Trakstar Hire` portal.
2. **Unvalidated Application State:** Clicking apply marked the status as "Applied" on the portal dashboard despite the external destination returning an "Inactive Account" error.
3. **Data Inconsistency:** Candidates were led to believe their applications were submitted when they never reached the employer.

---

## Proof of Concept & Evidence
1. **Initial Listing:** Job appeared active with an option to apply.
2. **External Redirection Failure:** The redirected domain returned an `Inactive account` response from Trakstar Hire.
3. **Responsible Reporting:** Reported the issue with full reproduction steps to Jobseeker Support.
4. **Resolution Confirmation:** Support team verified the flaw and permanently removed the invalid listings from the database.

*(Screenshots attached in `/assets` directory)*

---

## Developer/AppSec Root Cause & Remediation
* **Root Cause:** Lack of automated health checks/heartbeats between the job aggregator and external ATS APIs. The system assumed a listed URL was valid without handling downstream HTTP 4xx/5xx or account deactivation states.
* **Remediation Recommendation:**
  * Implement automated middleware to periodically ping external ATS endpoints.
  * Only mark an application as "Applied" after receiving a valid 200 OK webhook response from the external ATS API.
