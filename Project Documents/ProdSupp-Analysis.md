# ProductionSupport Ticket Analysis

**Analysis Date:** February 4, 2026
**Tickets Analyzed:** 300 (PRODSUP-1822 through PRODSUP-2174)
**Project:** ProductionSupport (PRODSUP)
**Purpose:** Identify common themes and patterns in production support requests

---

## Executive Summary

The ProductionSupport Jira project serves as the channel for BenefitBay team members to file bug reports and urgent engineering requests. Analysis of the 300 most recent tickets reveals that **89% are operational tasks** (manual data fixes) rather than software bugs. The top drivers are payment/funds management, employee/plan removals, and contract modifications—many of which represent self-service gaps in the platform.

---

## Summary Statistics

| Metric | Value |
|--------|-------|
| Total tickets analyzed | 300 |
| Issue type: Task | 266 (88.7%) |
| Issue type: Bug | 34 (11.3%) |
| Status: Done | 296 (98.7%) |
| Status: In Progress | 3 (1.0%) |
| Status: To Do | 1 (0.3%) |

---

## Ticket Categories by Theme

### 1. Payment/Funds Splitting & Mapping (50 tickets, 16.7%)

The highest volume operational burden. Team members need engineering to manually split or reallocate payments across months, plans, or people.

**Common patterns:**
- Split payments across multiple months (especially Dec → Jan year-end transitions)
- Map Medicare funds between 2025 and 2026 plans
- Reallocate transactions to different plans or family members

**Example tickets:**
- PRODSUP-2170: "Payment Needs to Split for 2 Different Plans"
- PRODSUP-2166: "SALLY MCCARTHY needs Medicare funds mapped between 2025 and 2026"
- PRODSUP-2144: "Transaction Needs to Split Between Dec 2025, and Jan 2026"
- PRODSUP-2169: "ORAWAN MAHITTIPHINYO needs Medicare Part B funds mapped to 3 different plans 2025-2026"

---

### 2. Remove/Delete Operations (41 tickets, 13.7%)

Manual deletions that users cannot perform themselves through the UI.

**Common patterns:**
- Remove employees from the platform
- Delete plan cards (often after terminations or user errors)
- Remove contracts, duplicate records, or coverage periods

**Example tickets:**
- PRODSUP-2167: "Remove EE from BB"
- PRODSUP-2142: "Remove plan card"
- PRODSUP-2129: "Please remove Employer Group duplicate"
- PRODSUP-2108: "ATEA | Remove contract - Client did not move forward"

---

### 3. Contract Modifications (28 tickets, 9.3%)

Post-signature contract changes that require engineering intervention.

**Common patterns:**
- Edit completed contracts (dates, PEPM, agent fees, addresses)
- Revert contracts back to editing stage
- Delete contracts for groups that didn't move forward

**Example tickets:**
- PRODSUP-2153: "Loureiro Contract needs amending - Agent Fees"
- PRODSUP-2140: "Update the PEPM in the platform to match the contracts"
- PRODSUP-2135: "Edit Contract for Epling's Pest Control"
- PRODSUP-2126: "Need Completed Contract Amended"

---

### 4. Move/Transfer Operations (26 tickets, 8.7%)

Relocating entities between organizational structures.

**Common patterns:**
- Transfer employer groups to new agencies
- Move employees between sister companies
- Move ARC accounts between entities

**Example tickets:**
- PRODSUP-2174: "Transfer ER Group-Christian Home Health Care to new agency"
- PRODSUP-2152: "KPM Employer Group move to new Agency"
- PRODSUP-2096: "Move Groups/Agents from Agency profile to another"
- PRODSUP-2081: "Move employees in sister companies"

---

### 5. Model Lock/Unlock (23 tickets, 7.7%)

High-frequency administrative task for contribution model management.

**Common patterns:**
- Unlock locked models for contribution changes
- Relock models after updates
- Often needed mid-enrollment when changes are discovered

**Example tickets:**
- PRODSUP-2133: "Need Model Unlocked"
- PRODSUP-2047: "Avania - Unlock/Lock Model"
- PRODSUP-1996: "DigeTekS - Model needs to be unlocked and the Hawaii contribution changed"
- PRODSUP-1936: "Affari Project - unlock old model + relock new model"

---

### 6. Employee Terminations (23 tickets, 7.7%)

Users frequently blocked from completing employee terminations.

**Common patterns:**
- Employer can't process termination through UI
- Plan cards need removal after termination
- Dependencies on future-dated plans blocking termination

**Example tickets:**
- PRODSUP-2121: "Possibilities ABA - Trevor Stookey - Terminated on 12/26. ER can't put in the termination."
- PRODSUP-2107: "Plexsum - Terminations needed for Nikki Renninger, Sarah Renninger, Devin Lafferty, and Slade Storm"
- PRODSUP-2062: "Can't terminate EE - had a 2026 API plan"
- PRODSUP-1973: "ER (and me) can't enter terminations"

---

### 7. User Blocked/Unable to Complete Action (23 tickets, 7.7%)

Various scenarios where users hit dead ends in the platform.

**Common patterns:**
- Coverage/plans won't populate
- Can't submit applications
- UI elements not responding or saving

**Example tickets:**
- PRODSUP-2164: "Decent Homecare - EE showing as in process and unable to close enrollment period"
- PRODSUP-2161: "Jefferson Green - Transport Refrigeration - Coverage won't populate"
- PRODSUP-1998: "EE can't submit application"
- PRODSUP-1883: "EE Cannot Confirm and Submit Plan"

---

### 8. Enrollment Issues (23 tickets, 7.7%)

Problems with enrollment periods, shopping experience, and plan selection.

**Common patterns:**
- Enrollment periods need to be created or modified
- Employees unable to shop
- Plan cards showing incorrect status

**Example tickets:**
- PRODSUP-2160: "Friday Pickle need a ancillary enrollment period created?"
- PRODSUP-1923: "Southern Plus - December 1 Open Enrollment shows as pending - Employees unable to shop"
- PRODSUP-2056: "EE Waived is showing up as 'In Process'"
- PRODSUP-1987: "EE waived coverage but is showing as 'In Process'"

---

## Bug Analysis (34 bugs, 11.3%)

### Bug Categories

| Category | Count | Examples |
|----------|-------|----------|
| Can't complete enrollment/application | 6 | "Sorry Dog when trying to enroll", "EE can't submit application" |
| Termination blocked | 4 | "Can't terminate EE", "ER can't enter terminations" |
| Data not displaying | 4 | "Coverage won't populate", "No plans showing in shopping" |
| UI changes not persisting | 2 | "Manual change to Legal Entity not persisting" |
| HubSpot integration | 3 | "App ticket associated to wrong person in HubSpot" |
| Calculation errors | 2 | "Affordability calculation didn't update" |
| Data sync issues | 3 | "Plan showing as not submitted but it was" |

### Full Bug List

| Ticket | Date | Summary |
|--------|------|---------|
| PRODSUP-2161 | 2026-01-26 | Jefferson Green - Transport Refrigeration - Coverage won't populate |
| PRODSUP-2147 | 2026-01-16 | Manual change to Legal Entity on UI not persisting |
| PRODSUP-2146 | 2026-01-16 | EE Received 'Closed & Waived' email but it isn't clear why |
| PRODSUP-2130 | 2026-01-13 | Transaction Mapped into February without Flagging of Possible Dual Enrollment |
| PRODSUP-2128 | 2026-01-13 | Transaction Mapped to the Non-Existing Plan Card |
| PRODSUP-2102 | 2026-01-05 | Enrollment mismatch error |
| PRODSUP-2086 | 2026-01-02 | Sorry Dog when trying to enroll |
| PRODSUP-2082 | 2026-01-02 | EEs all showing as waived (Priority: Highest) |
| PRODSUP-2078 | 2026-01-01 | ER can't add locations for employees (Priority: High) |
| PRODSUP-2062 | 2025-12-23 | Can't terminate EE - had a 2026 API plan |
| PRODSUP-2058 | 2025-12-23 | Can't Terminate Employee |
| PRODSUP-2055 | 2025-12-22 | Can't terminate employee |
| PRODSUP-2048 | 2025-12-22 | App ticket associated to wrong person in HubSpot |
| PRODSUP-2039 | 2025-12-18 | No application ticket in HubSpot |
| PRODSUP-2014 | 2025-12-15 | EE Salary Changed and affordability calculation didn't update |
| PRODSUP-2003 | 2025-12-12 | Change employer assigned agent |
| PRODSUP-1990 | 2025-12-10 | Enrollment Dashboard - Medical Enrollment Per Pay Period Incorrect |
| PRODSUP-1973 | 2025-12-09 | ER (and me) can't enter terminations |
| PRODSUP-1970 | 2025-12-09 | Address will not validate for HSA plan selection |
| PRODSUP-1966 | 2025-12-08 | Unaffordable model for Penn Foam |
| PRODSUP-1964 | 2025-12-08 | EE gets blank screen when trying to submit SSN |
| PRODSUP-1960 | 2025-12-05 | Plan selection not showing on Enrollment Page |
| PRODSUP-1920 | — | ER plan doc not clearing checklist |
| PRODSUP-1888 | — | EEs plan is showing under digital enrollment but bb platform not reflecting |
| PRODSUP-1879 | — | EE can't submit application - ENROLLMENT ENDING TODAY |
| PRODSUP-1865 | — | HIPAA not populating |
| PRODSUP-1859 | — | BCBS of TX Application Issues |
| PRODSUP-1844 | — | EE can't submit application |
| PRODSUP-1843 | — | Plan showing as not submitted but it was |
| PRODSUP-1837 | — | EEs not able to update their information in their bb dashboards |
| PRODSUP-1836 | — | No plans are showing in shopping for EE |
| PRODSUP-1834 | — | EE can't add SSN or phone number |
| PRODSUP-1827 | — | ER going through enrollment can't add provider |

---

## Keyword Frequency Analysis

| Keyword/Pattern | Count | % of Tickets |
|-----------------|-------|--------------|
| Move/Transfer operations | 75 | 25.0% |
| Remove/Delete operations | 51 | 17.0% |
| Contract-related | 37 | 12.3% |
| Lock model | 27 | 9.0% |
| User blocked (can't/unable) | 23 | 7.7% |
| Unlock model/contract | 21 | 7.0% |
| Termination-related | 19 | 6.3% |
| Medicare-related | 18 | 6.0% |
| Plan card issues | 16 | 5.3% |
| Waiver issues | 8 | 2.7% |
| Address issues | 5 | 1.7% |
| Payment/Transaction split | 5 | 1.7% |
| Funds mapping | 4 | 1.3% |
| Enrollment period | 3 | 1.0% |
| Census issues | 3 | 1.0% |
| HubSpot issues | 3 | 1.0% |

---

## Key Insights

### 1. Operational Work Dominates
89% of tickets are Tasks (manual data fixes), not actual software defects. Engineering time is heavily spent on operational support rather than feature development.

### 2. Year-End/Month-End Transitions Are Painful
Significant spike in "split funds between December and January" requests, especially for Medicare. The platform doesn't handle year-boundary transitions gracefully.

### 3. Self-Service Gaps Are Significant
Many requests represent actions users *should* be able to do themselves:
- Remove employees/plans from the platform
- Process employee terminations
- Edit contracts after signature
- Unlock/relock contribution models

### 4. Medicare Complexity
A substantial portion of tickets specifically involve Medicare funds mapping across plan years, suggesting this use case needs dedicated tooling.

### 5. Termination Workflow Is Broken
Multiple bugs and tasks around inability to terminate employees, often due to downstream plan dependencies that block the action.

### 6. HubSpot Integration Issues
Several tickets indicate sync problems between BenefitBay and HubSpot for application tracking.

---

## Recommendations

### High-Impact Product Improvements

| Priority | Area | Recommendation | Potential Impact |
|----------|------|----------------|------------------|
| 1 | Payment Management | Build self-service funds splitting/mapping tool | ~17% ticket reduction |
| 2 | Terminations | Fix termination blockers, handle plan dependencies gracefully | ~8% ticket reduction |
| 3 | Model Management | Admin unlock/relock capability for CSMs | ~8% ticket reduction |
| 4 | Remove/Delete | Self-service removal for EEs, plans, contracts | ~14% ticket reduction |
| 5 | Contract Editing | Post-signature amendment workflow | ~9% ticket reduction |
| 6 | Year-End Automation | Automated funds transition Dec → Jan | Reduces seasonal spike |

### Quick Wins
- Add admin UI for model lock/unlock
- Enable EE removal by CSMs
- Allow plan card deletion for termed employees

### Longer-Term Investments
- Medicare funds management module
- Self-service contract amendment workflow
- Automated year-end funds transition logic

---

## Appendix: Data Sources

- **Jira Project:** ProductionSupport (PRODSUP)
- **Cloud ID:** d6fcecd6-23dd-47bb-961f-d519ee3f6b7c
- **Query:** `project = PRODSUP ORDER BY created DESC`
- **Fields Analyzed:** summary, description, status, issuetype, priority, created, labels
- **Analysis Period:** Recent 300 tickets (approximately December 2025 - February 2026)
