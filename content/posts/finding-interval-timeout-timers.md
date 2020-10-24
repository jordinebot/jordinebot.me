---
title: "Finding active interval timers"
date: 2018-05-14T12:49:33+02:00
tags: [javascript]
draft: false
---
I've been recently in the situation of having a legacy spaghetti website with a buggy behavior that clearly was executed
at constant intervals but I didn't have a clue of what code/function was being executed or even where was this code
defined.

Apparently no browser's developer tools provides an easy way to inspect and debug timers. How many active `setInterval`
do I have? Which functions are currently being called by `setTimeout` or `setInterval` timers? The only solution I
could find for this was [monkey patching](https://en.wikipedia.org/wiki/Monkey_patch) those functions to log information
that helps me identify the executed code.


```javascript
/* Backup the original function */
window.setIntervalBak = window.setInterval;

/* Monkey patch the function */
window.setInterval = function(func, delay) {
    /* Log useful information to identify the
       function like its name or code
    */
    console.log(func.name, func.toSource());

    /* Switch functions */
    window.setIntervalBak(func, delay);
    /* This way the interval timer will continue
       executing the hooked function, but logging
       whatever information we need to help finding
       what's going on
    */
}
```

Drop this code in your browser's console and it will log information about all interval called functions. I recommend
you to take a look at
[`Function.prototype`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/prototype)
for hints on useful information that could help you locate the code you're trying to find.
