---
title: Roadmap Constraints and Risks
slug: roadmap-constraints-and-risks
icon: {"lucideIcon":"BookOpen"}
docTags: ListLab
createdAt: 2026-03-25T00:00:00.000Z
updatedAt: 2026-03-25T00:00:00.000Z
---

# ROADMAP CONSTRAINTS AND RISKS

This page shows a practical way to combine bullets, numbered mitigation steps, and checklists as gates.

## Risks to plan for (bulleted)

- Scope creep
  - New requirements appear mid-draft
  - Formatting changes request deeper nesting
- Rendering ambiguity
  - Some indentation levels may render differently
  - Mixed list types can be misread
- Maintenance cost
  - Too many list variants becomes hard to reuse

## Mitigations (numbered)

1. Lock a simple list grammar
   1. Bullets for groups
   2. Numbers for steps
   3. Checkboxes for gates
2. Include a visual indentation example
   - Show where level 2 starts
   - Show where level 3 starts
3. Run a copy/paste review pass
   - Check each list is still nested correctly
   - Confirm list markers did not shift

## Release readiness checklist

- [ ] Each doc demonstrates: bullets + numbers + checkboxes
- [ ] At least one doc includes 3+ nesting levels
- [ ] Every checklist item has a specific expected result
- [ ] No empty list markers remain

