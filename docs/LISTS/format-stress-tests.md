---
title: Format Stress Tests
slug: format-stress-tests
icon: 🧭
docTags: ListLab
createdAt: 2026-03-25T00:00:00.000Z
updatedAt: 2026-03-25T00:00:00.000Z
---

# FORMAT STRESS TESTS

This page is intentionally "ugly" on purpose: it combines multiple list markers in the same block to test indentation behavior.

## Mixed nesting examples

1. Collect sources
   - [ ] Links
     - Website
     - Issue tracker
   - [ ] Notes
     - Decisions made
     - Open questions
2. Convert sources into doc lists
   - Bulleted section
   - Numbered section
   - Checklist section

## Indentation checkpoints (bullets + indented)

- Level A
  - Level B
    - Level C
      - Bullet content
      - Bullet content

## Checklist items with indented bullet siblings

- [ ] Validate layout
  - [ ] Confirm spacing is consistent
  - [ ] Confirm nested list markers start at the right column
- [ ] Validate content
  - Use precise verbs
  - Avoid vague items
  - Keep each list item self-contained

## Checklist items with indented numbered substeps

- [ ] Final rendering check
  1. Paste into your viewer
  2. Scan for mis-nesting
  3. Correct indentation before shipping

