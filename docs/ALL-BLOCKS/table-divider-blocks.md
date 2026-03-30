---
title: Tables & Dividers
slug: table-divider-blocks
icon: {"lucideIcon":"Table"}
docTags: Revenge
createdAt: 2026-03-30T10:00:00.000Z
updatedAt: 2026-03-30T10:00:00.000Z
---

## Markdown Table

| Name      | Status  | Score |
| --------- | ------- | ----- |
| Dummy A1  | Active  | 91    |
| Dummy A2  | Pending | 76    |

***

## HTML Table - Colored Cells

<table isTableHeaderOn="true" columnWidths="220,220,220">
  <tr>
    <td darkBackgroundColor="#1E40AF" lightBackgroundColor="#3B82F6" align="center">
      <p><strong>Product</strong></p>
    </td>
    <td darkBackgroundColor="#1E40AF" lightBackgroundColor="#3B82F6" align="center">
      <p><strong>Price</strong></p>
    </td>
    <td darkBackgroundColor="#1E40AF" lightBackgroundColor="#3B82F6" align="center">
      <p><strong>Status</strong></p>
    </td>
  </tr>
  <tr>
    <td darkBackgroundColor="#1F2937" lightBackgroundColor="#E5E7EB" align="left">
      <p>Dummy Keyboard</p>
    </td>
    <td darkBackgroundColor="#14532D" lightBackgroundColor="#D1FAE5" align="right">
      <p>$99</p>
    </td>
    <td darkBackgroundColor="#166534" lightBackgroundColor="#86EFAC" align="center">
      <p>In Stock</p>
    </td>
  </tr>
  <tr>
    <td darkBackgroundColor="#1F2937" lightBackgroundColor="#E5E7EB" align="left">
      <p>Dummy Mouse</p>
    </td>
    <td darkBackgroundColor="#7F1D1D" lightBackgroundColor="#FEE2E2" align="right">
      <p>$59</p>
    </td>
    <td darkBackgroundColor="#991B1B" lightBackgroundColor="#FCA5A5" align="center">
      <p>Low Stock</p>
    </td>
  </tr>
</table>

***

## HTML Table - Colored & Merged Cells

<table isTableHeaderOn="true" columnWidths="180,180,180,180">
  <tr>
    <td darkBackgroundColor="#0C4A6E" lightBackgroundColor="#0EA5E9" align="center" colSpan="4">
      <p><strong>Q1 TEAM PLAN</strong></p>
    </td>
  </tr>
  <tr>
    <td darkBackgroundColor="#1E3A8A" lightBackgroundColor="#60A5FA" align="center">
      <p><strong>Team</strong></p>
    </td>
    <td darkBackgroundColor="#1E3A8A" lightBackgroundColor="#60A5FA" align="center">
      <p><strong>Task</strong></p>
    </td>
    <td darkBackgroundColor="#1E3A8A" lightBackgroundColor="#60A5FA" align="center">
      <p><strong>Owner</strong></p>
    </td>
    <td darkBackgroundColor="#1E3A8A" lightBackgroundColor="#60A5FA" align="center">
      <p><strong>Status</strong></p>
    </td>
  </tr>
  <tr>
    <td darkBackgroundColor="#312E81" lightBackgroundColor="#C7D2FE" align="left" rowSpan="2">
      <p><strong>Platform</strong></p>
    </td>
    <td darkBackgroundColor="#374151" lightBackgroundColor="#F3F4F6" align="left">
      <p>API Cleanup</p>
    </td>
    <td darkBackgroundColor="#374151" lightBackgroundColor="#F3F4F6" align="left">
      <p>Alex</p>
    </td>
    <td darkBackgroundColor="#14532D" lightBackgroundColor="#BBF7D0" align="center">
      <p>Done</p>
    </td>
  </tr>
  <tr>
    <td darkBackgroundColor="#374151" lightBackgroundColor="#F3F4F6" align="left" colSpan="2">
      <p>Migration Batch 2</p>
    </td>
    <td darkBackgroundColor="#9A3412" lightBackgroundColor="#FED7AA" align="center">
      <p>In Progress</p>
    </td>
  </tr>
  <tr>
    <td darkBackgroundColor="#4C1D95" lightBackgroundColor="#DDD6FE" align="left">
      <p><strong>Design</strong></p>
    </td>
    <td darkBackgroundColor="#581C87" lightBackgroundColor="#F3E8FF" align="left" colSpan="3">
      <p>Dashboard Refresh Approved</p>
    </td>
  </tr>
</table>

***
