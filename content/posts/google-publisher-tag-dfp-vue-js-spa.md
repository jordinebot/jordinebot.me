---
title: "Google Publisher Tag for DFP in a Vue.js Single-Page Application"
date: 2018-08-24T00:26:37+02:00
tags: [vue, ads]
---

{{% announcement title="Update on 28 Oct, 2018" %}}
I've written [A better approach to include GPT Ads in a Vue.js Single-Page Application](/posts/better-approach-gpt-dfp-for-vue-js-spa/).
{{% /announcement %}}

I've recently contributed to re-coding a legacy Symfony 1.x/jQuery website as a Vue.js SPA consuming a Python
Lambda-based API. Improvement in terms of page load speed, total page size and number of requests has been great. With
the same features and identical UI, we achieved an average decrease of 55% in page load time and requests and almost a
70% in downloaded data (images were poorly optimized, no gzip compression enabled, lots of
[dead](https://en.wikipedia.org/wiki/Dead_code) and [unreachable](https://en.wikipedia.org/wiki/Unreachable_code)
code...)

Anyway, I'd like to write about the solution I've used to have the Ads properly running with [Google Publisher
Tag](https://developers.google.com/doubleclick-gpt/) within a Single-Page Application. The site is some kind of search
engine so, to simplify, let's consider it has only a couple of templates:

- Home page, with two Ad Units.
- Results page, with four Ad Units.

Only one of these units is common in both templates: a 728×90
[Leaderboard](https://theonlineadvertisingguide.com/ad-size-guide/728x90/) above the Header. The main layout would be
something as simple as:

```html
<div id="app">
    <Advertising :id="'MySite_Home_Leaderboard'" class="leaderboard"/>
    <Header/>
    <router-view/>
    <Footer/>
</div>
```

For demo purposes, let's consider the `<Advertising/>` component just renders an empty `<div>` with the given ID. The
`<router-view/>` element loads the main content either if it's the Home or the results.

## How do I display Ads in my Single-Page Application?

1. Include GPT scripts.
2. Define the required Ad Units for the current URL.
3. Insert the empty `div` elements for the Ads in the DOM.
4. Call the Display function for every slot in the page.

Obviously, we want our Ads to be displayed as soon as possible, so the goal here is to make the required calls the
sooner the better.

### 1. Including Google Publisher Tag Scripts

GPT must be included in all URLs so, the best place for this script is the entry point to the SPA, the `index.html`
template file. Ideally at the top of the `<head>` tag. That'd be:

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <!-- Google Publisher Tags -->
        <script type="text/javascript">
            var googletag = googletag || {};
            googletag.cmd = googletag.cmd || [];
            // ...
```

Just keep in mind:

> The `<meta>` element declaring the encoding must be inside the `<head>` element and within the first 1024
> bytes of the HTML as some browsers only look at those bytes before choosing an encoding.
> [[MDN]](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta#Attributes)

### 2. Defining the required Ad Units
If all our pages were to have the same Ad Units, `index.html` would be the best place to define them. But that's not the
case and, of course, Vue.js doesn't allow using `script` tags inside templates either:

>  Templates should only be responsible for mapping the state to the UI. Avoid placing tags with side-effects in your
>  templates, such as `script`, as they will not be parsed.

The obvious alternative was using Vue's Lifecycle hooks to define the Ads. But, which ones? in which component?

Besides the leaderboard we've seen on the App Component, all the other Ads are on the component associated to the route
that'll be used in the `<router-view/>` (or its children), so considering how the [lifecycle for parent/child
components](https://medium.com/@brockreece/vue-parent-and-child-lifecycle-hooks-5d6236bd561f) takes place, the safest
place to call `googletag.defineSlot()` seems to be the `mounted` hook in the current route component, since the call
requires all `Advertising` tags to be rendered in the DOM.

To define the slots I've created a `src/common/ads.js` module with:

```javascript
function defineSlots(slots) {
    slots.forEach(slot => {
        window.ads[slot.opt_div] = googletag.defineSlot(
            slot.adUnitPath,
            slot.size,
            slot.opt_div
        );
    });
}
```

I use a global `window.ads` to store references to the returned `googletag.Slot` objects.  This module also exports some
functions that define the required slots for different templates:

```javascript
export function slotsResultsPage() {
    const slots = [
        {
            adUnitPath: "/1234567/sports",
            size: [728, 90],
            opt_div: "div-1"
        },
        // ...
    ];
    defineSlots(slots);
}
```

I use this `slotsResultsPage` function as a `created` hook in `ResultsPageComponent`. Obviously, the idea is to reuse
this function (probably with a more appropiate name) for every template with the same Ad slots.

```javascript
import { slotsResultsPage as created } from "@/common/ads";

export default {
    name: "ResultsPageComponent",
    components: {
        // ...
    },
    created,
    // ...
}
```

### 3. Inserting the empty `div` elements to contain the Ads

Before calling the `googletag.display` method we have to be sure that the DOM nodes for them are already there. 

> The display call must not happen until the element is present in the DOM.
> [[GPT Reference]](https://developers.google.com/doubleclick-gpt/reference#googletag.display)

A simplified version of my `Advertising` component would be something like:

```javascript
Vue.component("Advertising", {
    props: {
        id: String,
    },
    template: '<div :id="id" class="mysite-ad"></div>'
});
```

This is actually so simple that wouldn't require a component at all, but this way you can include other features (a
close button, collect stats...) or e.g. to add some new class to all Ads across the site, you just have to update your
Ad component's template.

Insert ads wherever you want as we've seen in the intro:

```html
<Advertising :id="MySite_Home_Leaderboard" />
```

### 4. Displaying Ads

Probably, the safest place to do it all at once is on the page's `mounted` hook. To achieve that, I add a `displayAds`
method to my common ads module:

```javascript
export function displayAds() {
    document.querySelector(".mysite-ad").forEach(node => {
        googletag.cmd.push(() => googletag.display(node.id));
    });
}
```

And I use it on the page component (let's complete the former code):

```javascript
import { slotsResultsPage as created } from "@/common/ads";
import { displayAds as mounted } from "@/common/ads";

export default {
    name: "ResultsPageComponent",
    components: {
        // ...
    },
    created,
    mounted,
    // ...
}
```

---

That should be enough to show Ads in our site. The thing is, since this is a Single-Page Application, once we navigate
we will see some errors in [Google Publisher Tag's
Console](https://developers.google.com/doubleclick-gpt/reference#googletag.openConsole). I'm still messing around to
find the _best_ way to handle this situation, but one thing that works perfectly fine is to destroy all the slots when
_loading_ a new URL.

The easiest way to do it would be to add a `destroySlots` function in our commons ads module:

```javascript
export function destroySlots() {
    if (Object.keys(window.ads).length) {
        googletag.destroySlots(Object.values(window.ads));
    }
    // Or just googletag.destroySlots();
}
```

Of course, we need to destroy the slots before the execution of the `created` hook (that will create the new ones again)
for the Page Component we're navigating to. We can achieve that in many Router [Navigation
Guards](https://router.vuejs.org/guide/advanced/navigation-guards.html#navigation-guards) or even in Page
Component's `beforeCreate` lifecycle hook:

```javascript
import { destroySlots as beforeCreate } from "@/common/ads";
import { slotsResultsPage as created } from "@/common/ads";
import { displayAds as mounted } from "@/common/ads";

export default {
    name: "ResultsPageComponent",
    components: {
        // ...
    },
    beforeCreate,
    created,
    mounted,
    // ...
}

```

_**Disclaimer:** I'm not a GPT/DFP expert at all but I've spent a decent amount of time trying to figure out how to deal
with Ads in a Vue.js SPA. The setup I tried to explain in this article worked well for me but it might probably be
improved (e.g. I suspect there's no actual reason to destroy all slots when changing routes, some of them maybe you
could just reuse). Anyway, I'll be glad if this can help anyone. Comments, questions, doubts, other approaches/theories,
experiences are more than welcome._
