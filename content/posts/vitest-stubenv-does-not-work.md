---
title: "Vitest's vi.stubEnv does't work? Let's fix it"
date: 2024-05-13T13:21:37+01:00
tags: [vitest, testing, react]
draft: true
---

I recently had an scenario where I wanted to test that a React component was only rendered in non-production environments. The setup looks like this:

```typescript
const { PROD: isProduction } = import.meta.env;

export const DebugComponent = () => {};
```
