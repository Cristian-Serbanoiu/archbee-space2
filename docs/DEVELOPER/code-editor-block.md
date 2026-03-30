---
title: Code Editor
slug: code-editor-blocks
icon: {"faIcon":"fa-solid fa-code"}
docTags: Revenge
createdAt: 2026-03-30T10:00:00.000Z
updatedAt: 2026-03-30T10:00:00.000Z
---

```javascript
const fetchCake = async (id) => {
  const response = await fetch(`https://api.cakes.com/${id}`, {
    method: "GET",
    headers: {
      "Accept": "application/json"
    }
  });

  if (!response.ok) {
    throw new Error(`Request failed with status ${response.status}`);
  }

  return response.json();
};
```
