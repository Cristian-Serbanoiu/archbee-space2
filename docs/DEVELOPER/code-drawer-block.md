---
title: Code Drawer
slug: code-drawer-blocks
icon: {"faIcon":"fa-solid fa-draw-polygon"}
docTags: Revenge
createdAt: 2026-03-30T10:00:00.000Z
updatedAt: 2026-03-30T10:00:00.000Z
---

::::CodeDrawer{title="Get User Profile (TypeScript)" codeEditorData="[object Object]" responsesEditorData="[object Object]" isResponseExpanded="true"}
:::CodeblockTabsExamples
```typescript
type UserProfile = {
  id: string;
  email: string;
  fullName: string;
  plan: "free" | "pro" | "enterprise";
};

export async function getUserProfile(userId: string): Promise<UserProfile> {
  const response = await fetch(`https://api.example.com/v1/users/${userId}`, {
    method: "GET",
    headers: {
      Accept: "application/json",
      Authorization: `Bearer ${process.env.API_TOKEN ?? ""}`
    }
  });

  if (!response.ok) {
    throw new Error(`Failed to fetch profile (${response.status})`);
  }

  return (await response.json()) as UserProfile;
}
```
:::

:::CodeblockTabsResponses
```typescript
{
  "id": "usr_8f3b91",
  "email": "ava@company.com",
  "fullName": "Ava Williams",
  "plan": "pro"
}
```
:::
::::
