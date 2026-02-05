# FULCRUM 2026 Enhancements

# What is this?

A series of enhancements to FULCRUM, BenefitBay's internal ICHRA modeling tool, that reduce manual effort for the sales enablement team and lay the foundation for auto modeling capabilities. These enhancements focus on automating repetitive tasks (MRA classing, benchmark plan mapping), improving data capture (group plan details), and enabling faster scenario creation.

# Problem Alignment

---

The FULCRUM modeling process today requires excessive manual effort that doesn't scale. For large employer groups (500+ employees across multiple states and MRAs), Kevin and Andrew spend hours clicking through repetitive tasks—manually breaking out MRA classes one by one, re-applying benchmark plans after copying a model, and reconciling plan mappings in spreadsheets. A single employer model with 122 benchmark plans across 60+ MRAs can require thousands of clicks and late-night work sessions.

- **Why does this matter to our customers and business?**
  - Sales cycle speed: Faster modeling means faster proposals and shorter time-to-close
  - Team capacity: Reducing manual work lets the team handle more employer prospects
  - Quality: Manual processes increase error risk; automation improves accuracy
  - Competitive positioning: Auto modeling is a differentiator that agencies will value

- **What evidence or insights do you have to support this?**
  - Kevin and Andrew described spending entire nights on single employer models
  - One employer (Physician Solutions) required 122 benchmark plans to be manually added/removed
  - MRA breakouts currently require clicking through each MRA individually—no bulk actions
  - When copying models or uploading new census data, benchmark mappings break and must be manually re-applied
  - Current workaround: reconciling plan mappings in Excel spreadsheets outside the system

### Goals + Outcomes

| Metric | What It Tells Us | Goal |
| --- | --- | --- |
| **Time to complete a model** | How long it takes to go from census upload to deliverable model | Reduce by 50% for enterprise groups (500+ employees) |
| **Clicks per model creation** | Quantifies manual effort in the current process | Reduce by 75% for MRA-heavy employers |
| **Models created per week** | Team throughput and capacity | Increase by 25% without adding headcount |
| **Error rate in plan mappings** | Quality of model outputs | Reduce manual reconciliation in spreadsheets to zero |
| **User satisfaction (Kevin, Andrew)** | Qualitative feedback on workflow improvements | "This doesn't suck anymore" |

# Solution Alignment

---

### In Scope

**Priority 0 - In Flight (Engineering has designs)**
1. **Dependent Contributions** - Already designed and in development
2. **Show ICHRA Only** - Already designed, includes page redesign

**Priority 1 - Reapply Benchmarks**
3. **Reapply Benchmark Plans Button** - Add a button on the benchmark plans page to re-apply all benchmark plan mappings after a model copy or census re-upload. Currently users must delete all 122+ plans and re-add them one by one to re-trigger the sequencing logic.

**Priority 2 - MRA Auto-Classing**
4. **MRA Step in Auto-Class Wizard** - Add a new step between "States" and "Pay Type" in the class builder wizard that:
   - Shows all MRAs with employee counts
   - Hides MRAs with zero employees (or filters them out)
   - Allows "Select All" to create classes for all MRAs with employees
   - Auto-applies age bands to newly created MRA classes

**Priority 3 - Group Plan Import**
5. **Import Group Insurance Product Details** - Capture prior group plan information (carrier, plan name, HDHP/HSA status) at the employee level during census import

**Priority 4 - Multi-Plan Mapping**
6. **Visual Plan Mapping Editor** - Enable mapping of group plans to benchmark plans with:
   - Visual representation of which employees are mapped to which plans
   - Show unmapped employees count
   - Ability to drag/assign employees to different benchmark plans
   - Support for mapping prior group plans to ICHRA benchmark plans

**Priority 5 - Performance**
7. **Performance Improvements** - Address performance bottlenecks as they arise during implementation of above features (particularly for large census processing)

**Priority 6 - Notes & Versions**
8. **Model Notes** - Add free-form notes capability to models (via kebab menu) to document modeling decisions and differences between versions

### Out of Scope

1. **Auto Modeling / FULCRUM AI** - The "push play and let it run" experience is targeted for 2027. This PRD focuses on the foundational features that enable auto modeling.
2. **AI-Powered Recommendations** - Intelligent suggestions based on employer size, industry, or aggregate data (future patent opportunity)
3. **Projection/Renewal Models** - Renewal conversation workflows mentioned but not prioritized for this scope
4. **Agency Self-Service** - Enabling external agencies to upload census and auto-model without BenefitBay involvement

### Guiding Principles

1. **Reduce clicks, not capabilities** - Every feature should maintain full control for power users while removing tedious repetition
2. **Build toward auto modeling** - Each enhancement should be a building block for the future "FULCRUM AI" vision
3. **Design for the 99% case** - Optimize for common workflows; edge cases can be handled manually
4. **Keep data integrity** - Census uploads and model copies should preserve benchmark mappings and contribution structures
5. **Performance matters** - Large employers (3,000+ employees) should not experience degraded performance

# Key Flows

---

### Flow 1: Reapply Benchmark Plans

*Current state:* User copies a model or uploads a new census. Benchmark plans appear in the list but are not actually applied to employees. User must delete all plans and re-add them one by one (122+ times for large groups).

*Future state:*
1. User copies a model or uploads new census
2. User navigates to Benchmark Plans page
3. User sees existing benchmark plan list
4. User clicks **"Reapply Benchmarks"** button
5. System re-sequences all benchmark plans and applies them to employees by state
6. User sees confirmation of successful application

*Design notes:* Button should be prominent on the benchmark plans page. Consider adding to the copy model wizard as an optional step.

---

### Flow 2: MRA Auto-Classing

*Current state:* User creates state classes, then must manually break out each MRA one by one—clicking into the state, selecting "Break out by MRA," picking the MRA, naming it, saving, and repeating for every MRA with employees. For 60+ MRAs, this takes hours.

*Future state:*
1. User starts Auto-Class Builder
2. User selects states (existing flow)
3. **NEW STEP:** User sees "MRA Classes" screen showing:
   - List of MRAs with employee counts
   - Only MRAs with employees displayed (or filter toggle)
   - Checkbox for each MRA
   - "Select All" button
4. User clicks "Select All" (or selects specific MRAs)
5. User proceeds to Pay Type step (existing flow)
6. User completes wizard
7. System creates all state + MRA class combinations with proper naming (e.g., "Florida 01", "Florida 02")
8. Age bands are automatically applied to all created classes

*Design notes:* The MRA step should mirror the States step visually. Employee counts help users verify data accuracy.

---

### Flow 3: Group Plan Import & Mapping

*Current state:* Census import captures employee demographics but not their current group plan details. Kevin/Andrew track this in spreadsheets and manually map to benchmark plans.

*Future state:*
1. User uploads census with additional columns: Current Carrier, Current Plan Name, HDHP/HSA indicator
2. System ingests group plan data at employee level
3. User navigates to new **Plan Mapping** screen
4. User sees:
   - List of unique group plans from census (e.g., "United Healthcare HDHP")
   - Employee count per plan
   - Mapping dropdown to select benchmark plan(s)
5. User maps each group plan to corresponding benchmark plan(s)
6. System applies mappings to all employees on that group plan
7. User can view/edit individual employee mappings if needed

*Design notes:* This inverts the current flow—instead of going employee by employee, users work at the plan level. Visual representation should show coverage and highlight unmapped employees.

---

### Flow 4: Model Notes

*Current state:* Users differentiate models by typing descriptions in the model name field. No way to document reasoning or track what changed between versions.

*Future state:*
1. User opens model list page
2. User clicks kebab menu on a model row
3. User selects **"Add Note"**
4. Modal opens with free-form text field
5. User enters notes (e.g., "Locked model for client review - 95% EE only contribution")
6. Note is saved and visible on hover or in expanded view

*Design notes:* Consider adding notes as part of the model copy workflow. Notes should be searchable/filterable in the future.

# Open Questions

---

1. **Performance thresholds:** At what census size do we need to optimize? 500 employees? 1,000? 3,000?

2. **MRA display:** Should we show MRAs with zero employees but allow manual addition, or hide them entirely? (Kevin suggested hiding them in auto-class, with manual add-back later being the edge case)

3. **Benchmark reapply scope:** Should "Reapply Benchmarks" be available only on copied models, or also on the original model after census re-upload?

4. **Group plan data format:** What columns/fields are needed on census import for group plan details? Need to define schema with Kevin/Andrew.

5. **Plan mapping persistence:** When a new census is uploaded, should group plan → benchmark mappings persist, or should they be re-validated?

6. **Notes vs. versions:** Is free-form notes sufficient, or do we need structured version history with diff tracking?

7. **Patent timing:** When should we engage the patent attorney for FULCRUM AI scoring/recommendation algorithms?

# Design

---

*Figma designs in progress. Jason Fallas to complete designs for:*
- [ ] Reapply Benchmarks button (on existing benchmark plans page)
- [ ] MRA Auto-Classing wizard step
- [ ] Group Plan Mapping visual editor (future, with Priority 4)

*Link to Figma when ready.*

# Resources

---

- **Meeting Recording:** Ryan, Kevin and Jason (Bi-weekly Sales Enablement Product Needs) - February 3, 2026
- **Existing Figma:** Dependent Contributions and Show ICHRA Only designs (in progress)
- **IDEON Plans Tool:** Pavel's tool showing available plans per census (reference for plan mapping UI)
- **Competitive Reference:** AI auto-modeling visualization video (shared in leadership meeting)

---

*Last Updated: February 4, 2026*
