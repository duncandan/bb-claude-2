# JIRA Ticket Draft: Super Admin Role

**Project:** [TBD - likely Platform or Core]
**Issue Type:** Story
**Priority:** High

---

## Summary

Create a new "Super Admin" role with elevated permissions for internal Product and Engineering users to perform destructive operations (deletions) in the BenefitBay platform.

---

## Description

### Background

Currently, there is no user role that allows internal team members to delete records (employees, plan cards, contracts, etc.) from the platform. All deletion requests must be routed through ProductionSupport tickets and executed directly in the database by engineering.

Analysis of 300 recent PRODSUP tickets shows that **13.7% (41 tickets) are Remove/Delete operations** that could be self-served if an appropriate admin role existed.

### Proposed Solution

Create a new role above the existing Admin role that grants permissions to perform destructive operations. This role should be limited to a small number of trusted internal users on Product and Engineering teams.

### Role Name Options

| Option | Pros | Cons |
|--------|------|------|
| **Super Admin** | Clear hierarchy, commonly understood | May sound informal |
| **Platform Admin** | Professional, implies system-level access | Could be confused with hosting/infra |
| **Internal Admin** | Clearly differentiates from customer admins | Less descriptive of capabilities |
| **Operations Admin** | Describes the use case | May conflict with Ops team naming |

**Recommendation:** "Super Admin" or "Platform Admin"

---

## Acceptance Criteria

- [ ] New role created in the permission system (name TBD)
- [ ] Role is not visible or selectable for external users (Employers, Brokers, Agents)
- [ ] Role can only be assigned by existing Super Admins or via database (not self-assignable)
- [ ] Role inherits all existing Admin permissions
- [ ] Role adds the following new permissions:
  - [ ] `employee:delete` — Remove employees from employer groups
  - [ ] `plan_card:delete` — Delete plan cards (future phase)
  - [ ] `contract:delete` — Remove contracts (future phase)
  - [ ] `coverage_period:delete` — Delete enrollment periods (future phase)
- [ ] Audit log captures when Super Admin permissions are used
- [ ] Role assignment changes are logged (who granted/revoked, when)

---

## Technical Notes

- Consider whether this should be a new role or a permission flag on existing Admin accounts
- Deletion permissions should be granular (not all-or-nothing) to allow phased rollout
- May need to update role hierarchy in the auth system
- UI components for delete actions should check for this permission before rendering

---

## Out of Scope

- UI for delete operations (separate ticket/PRD)
- Bulk deletion capabilities
- External user access to deletions

---

## Dependencies

- PRD: [Self-Service Remove/Delete Functionality](./Remove-Delete-Self-Service-PRD.md)

---

## Estimated Scope

**T-shirt size:** Small-Medium

This is primarily a permissions/auth change with minimal UI impact. The actual delete functionality will be built in subsequent tickets.

---

## Labels

`permissions`, `admin`, `internal-tools`, `prodsup-reduction`

---

## Related PRODSUP Tickets

- PRODSUP-2167: "Remove EE from BB"
- PRODSUP-2142: "Remove plan card"
- PRODSUP-2125: "Remove EE from platform"
- PRODSUP-2108: "ATEA | Remove contract - Client did not move forward"
