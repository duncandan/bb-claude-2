# PRD: Self-Service Remove/Delete Functionality

# What is this?

An admin interface that enables internal users (CSMs, Operations) to remove employees, plan cards, contracts, coverage periods, and duplicate records from the BenefitBay platform without requiring engineering intervention.

# Problem Alignment

---

**Remove/Delete operations represent 13.7% of all ProductionSupport tickets** (41 out of 300 analyzed). These are manual deletions that internal users cannot perform themselves through the current UI, requiring them to file Jira tickets and wait for engineering to execute database operations.

This creates several problems:
- **Delays for customers:** Simple removals that should take seconds require ticket filing, triage, and engineering time
- **Engineering burden:** ~14% of production support work is spent on operations that don't require engineering judgment
- **Scalability risk:** As BenefitBay grows, this manual process won't scale
- **User frustration:** CSMs feel blocked when they can't resolve straightforward requests independently

### Evidence from ProductionSupport Analysis

| Remove/Delete Type | Example Tickets | Frequency |
|-------------------|-----------------|-----------|
| Remove employee from platform | PRODSUP-2167, PRODSUP-2125, PRODSUP-2120 | High |
| Delete plan cards | PRODSUP-2142, PRODSUP-2052, PRODSUP-2046 | High |
| Remove contracts | PRODSUP-2108, PRODSUP-2101, PRODSUP-1846 | Medium |
| Remove duplicate records | PRODSUP-2129, PRODSUP-2024, PRODSUP-1950 | Medium |
| Remove coverage periods | PRODSUP-2060, PRODSUP-1939, PRODSUP-1930 | Low |

**Common patterns observed:**
- EE removals often follow terminations or data entry errors
- Plan card deletions frequently occur after carrier cancellations or employee decisions to waive
- Contract removals happen when groups don't move forward after initial setup
- Duplicate records created during census uploads or manual entry errors

### Goals + Outcomes

| Metric | What It Tells Us | Goal |
|--------|------------------|------|
| **Remove/Delete PRODSUP tickets** | Are internal users self-serving? | Reduce by 80% (from ~41 to <10 per 300 tickets) |
| **Time to resolution** | How fast can we fix data issues? | < 5 minutes (vs. current 24-48 hour SLA) |
| **CSM satisfaction** | Do CSMs feel empowered? | Qualitative feedback in post-launch survey |
| **Audit trail completeness** | Can we trace who deleted what? | 100% of deletions logged with user, timestamp, reason |

# Solution Alignment

---

### In Scope (Phase 1: Employee Removal)

1. **New "Super Admin" role** — Create a new permission role above Admin for internal Product and Engineering users that enables destructive operations. See [JIRA Draft: Super Admin Role](./JIRA-Draft-Super-Admin-Role.md).
2. **Employee removal** — Remove an employee and their dependents from an employer group
3. **Cascading plan card cleanup** — When removing an EE, associated plan cards are also removed
4. **Audit logging** — Complete trail of who deleted what, when, and why
5. **Confirmation UX** — Clear impact summary before destructive action

### Future Phases

1. **Plan card deletion** — Standalone deletion of plan cards without removing the employee
2. **Contract removal** — Remove contracts for groups that didn't proceed
3. **Coverage period removal** — Delete enrollment periods created in error
4. **Duplicate record cleanup** — Merge or remove duplicate employees, employer groups

### Out of Scope

1. **Bulk operations** — Mass deletion tools (future consideration based on usage patterns)
2. **Undo/restore functionality** — Recovering deleted records (v2 consideration)
3. **External user access** — Employers or brokers performing deletions (requires separate analysis)
4. **Automated cleanup rules** — System-initiated deletions based on business rules

### Guiding Principles

1. **Restricted access** — Deletion capabilities limited to Super Admin role (internal Product + Engineering only)
2. **Audit everything** — Every deletion must be logged with who, when, what, and why
3. **Soft delete first** — Records should be marked as deleted, not permanently removed, to allow for recovery and compliance
4. **Confirmation required** — Destructive actions require explicit confirmation with clear impact summary
5. **No cascading surprises** — Users must see what related records will be affected before confirming
6. **HIPAA compliance** — Deletion logs must be retained per data retention policy

# Key Flows

---

### Flow 1: Remove Employee from Platform (Primary Flow)

**Trigger:** Internal user needs to remove an employee who was added in error, is a duplicate, or needs to be completely removed from the system.

**Entry points:**
- Employee Profile page → "Remove Employee" action
- Employer Group → Employee List → Row action menu

*Note: "Remove Employee" action only visible to users with Super Admin role.*

**Flow:**

1. User (with Super Admin role) clicks "Remove Employee" action
2. System displays confirmation modal showing:
   - Employee name, SSN (masked), and employer group
   - Dependents that will also be removed (listed by name)
   - Active plan cards that will be deleted (with carrier/plan details)
   - Warning banner if employee has:
     - Active coverage submitted to carrier
     - Pending payments or transactions
     - Historical enrollment data
3. User selects removal reason (required):
   - Added in error
   - Duplicate record
   - Employee never worked here
   - Data cleanup
   - Other (free text required)
4. User clicks "Remove Employee" button (red, destructive styling)
5. System soft-deletes:
   - Employee record
   - All dependent records
   - All associated plan cards
   - Enrollment period participation
6. Audit log captures:
   - User who performed deletion
   - Timestamp
   - Employee ID and name
   - Employer group
   - Reason selected
   - List of all affected records (dependents, plan cards)
7. Success toast displayed: "Employee removed successfully"
8. User redirected to Employer Group employee list

**Edge cases:**

| Scenario | Behavior |
|----------|----------|
| Employee has active carrier coverage | Show warning: "This employee has active coverage with [Carrier]. Ensure carrier has been notified before removing." Allow removal with acknowledgment checkbox. |
| Employee exists in multiple employer groups | Show which group(s) and clarify removal is only for the current group context |
| Employee has pending payments | Show warning with payment details. Allow removal (payments will need manual reconciliation). |
| Employee is the only signer on a contract | Block removal. Display message: "This employee is a contract signer. Reassign signer role before removing." |

---

### Future Flows (Out of Scope for Phase 1)

**Plan Card Deletion** — Standalone deletion without removing the employee. Useful when employee decides to waive or carrier cancels.

**Contract Removal** — For groups that don't move forward. Needs compliance review.

**Coverage Period Removal** — Delete enrollment periods created in error.

**Duplicate Merge** — Side-by-side comparison and merge of duplicate records.

# Open Questions

---

| Question | Context | Initial Thinking |
|----------|---------|------------------|
| **Who should have removal permissions?** | Different entity types may need different permission levels | **Resolved:** New "Super Admin" role for internal Product + Engineering users. See [JIRA Draft](./JIRA-Draft-Super-Admin-Role.md). |
| **What should the role be called?** | Need clear naming that conveys elevated access | Options: "Super Admin", "Platform Admin", "Internal Admin" — leaning toward Super Admin or Platform Admin |
| **Soft delete vs. hard delete?** | Data retention and compliance requirements | Prefer soft delete with scheduled hard delete after retention period — need to confirm retention requirements |
| **What data retention period?** | HIPAA and business requirements | Need input from compliance team |
| **What happens to active carrier enrollments?** | EE may have coverage submitted to carrier | Should we warn? Block? Require confirmation that carrier was notified? |
| **Should we require a reason for all deletions?** | Audit trail vs. friction | Yes — reason provides context for audits and helps identify process issues |
| **How do we handle EEs in multiple employer groups?** | Some EEs exist across sister companies | Clarify whether removal is per-group or global |

# Design

---

*Link to design specs when ready.*

Suggested design patterns:
- Destructive actions use red button styling
- Confirmation modals clearly list all affected records
- Audit history visible on each entity's detail page
- "Deleted" records shown in a separate tab or filter (for admins)

# Resources

---

- [ProductionSupport Ticket Analysis](../ProdSupp-Analysis.md) — Source data for this PRD
- [JIRA Draft: Super Admin Role](./JIRA-Draft-Super-Admin-Role.md) — Prerequisite ticket for new permission role
- **Key PRODSUP tickets for reference:**
  - PRODSUP-2167: "Remove EE from BB"
  - PRODSUP-2142: "Remove plan card"
  - PRODSUP-2108: "ATEA | Remove contract - Client did not move forward"
  - PRODSUP-2129: "Please remove Employer Group duplicate"
  - PRODSUP-2060: "Remove unintended coverage period"
