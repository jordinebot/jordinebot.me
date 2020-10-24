---
title: "Javascript object with undefined key"
date: 2020-05-14T19:01:06+02:00
tags: [javascript]
---

I've just discovered that you can do:

```javascript
const obj = {[undefined]: 42};
obj.undefined; // 42
obj[undefined]; // 42
```

but unfortunately you cannot do:

```javascript
obj[someUndefinedVar]; // ReferenceError: someUndefinedVar is not defined
```

It'd have been nice to have map-like objects providing a default value...
