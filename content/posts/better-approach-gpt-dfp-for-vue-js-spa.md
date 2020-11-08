---
title: "A better approach to include GPT Ads in a Vue.js Single-Page Application"
date: 2018-10-28T19:50:34+01:00
tags: [vue, ads]
---

It's been a while since I wrote [Google Publisher Tag for DFP in a Vue.js Single-Page
Application](/posts/google-publisher-tag-dfp-vue-js-spa/). Since the writing of the article I've refactored (and
simplified my solution). The main trigger was a wrong assumption I made, as stated in the final disclaimer of the
article:

> The setup I tried to explain in this article worked well for me but it might probably be improved (e.g. I suspect
> there’s no actual reason to destroy all slots when changing routes, some of them maybe you could just reuse).

Well, it turned out there was a reason. Even though it was not intentional, I actually reused some Ad slots. I was
destroying slots on `beforeCreate` hook for my page component and creating them again on `created`. Finally, calling
display on `mounted`.  Remember:

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

The thing is `ResultsPageComponent` also allows the user to perform a new search, and of course, the result for this new
search will reuse the `ResultsPageComponent` to present the new results. So, neither `destroySlots()`,
`slotsResultsPage()`, nor `displayAds()` will be called. Apparently this is not a problem, Ads will be there, but the fact
is reusing slots is a bad idea because you'll lose impressions per PV.

So how can we be sure Ad slots will be destroyed and created again every time we navigate to a new URL even if the new
URL is reusing components? Easy, move this Ad destroy/create logic to Vue's router.

```javascript
router.beforeEach((to, from, next) => {
    destroySlots();
    next();
});

router.afterEach((to, from) => {
    createPageSlots(to.name);
});
```

`destroySlots()` is exactly the same function I was hooking to page components `beforeCreate`. `createPageSlots()` just
takes the name of the route we're navigating to and calls `createSlots()` passing the Ads definitions required for this
particular URL.

This way we've fixed this issue with page components reuse and we've lighten up all of our page components. But there's
a problem now. On the first load everything is fine, but if we do a second search, `displayAds()` won't be called. It's
still hooked to `mounted` for the page component.  So, of course, it won't be called when Vue reuses the page component.

How do we fix this? We cannot call `displayAds()` right after `createPageSlots()` in router's `afterEach` navigation
guard, because at this point the Ad element might not be present in the DOM yet. The solution is in the App Component:

```html
<div id="app">
    <Advertising :id="'MySite_Home_Leaderboard'" class="leaderboard"/>
    <Header/>
    <router-view/>
    <Footer/>
</div>
```

Whether next route reuses the page component or not, some component inside the `router-view` is going to be updated.
Therefore, the main `App` component will also be updated, so let's call `displayAds()` at this point. We just have to
keep in mind that at the first load `updated` won't be called, but `mounted` will. So, we'll do:

```javascript
import { displayAds } from "@/common/ads";

export default {
    name: "App",
    components: {
        Advertising,
        Footer,
        Header,
    },
    mounted() {
        displayAds();
    },
    updated() {
        displayAds();
    }
};
```

Or just:

```javascript
import { displayAds as mounted, displayAds as updated } from "@/common/ads";

export default {
    name: "App",
    components: {
        Advertising,
        Footer,
        Header,
    },
    mounted,
    updated
};
```

You can check a working example on the repo: [ jordinebot/vuejs-gpt-demo ](https://github.com/jordinebot/vuejs-gpt-demo)

As always, comments, questions, doubts, other approaches/theories/experiences as well as spell, grammar or any kind of
language corrections are more than welcome! Thanks for reading!


