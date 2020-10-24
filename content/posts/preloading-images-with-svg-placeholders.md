---
title: "Preloading images with SVG placeholders"
date: 2018-05-04T08:00:53+02:00
draft: true
---
Yesterday I read a couple articles ([How Medium does progressive image loading](https://medium.com/@jmperezperez/how-medium-does-progressive-image-loading-fd1e4dc1ee3d) and [How to use SVG as a Placeholder, and Other Image Loading Techniques](https://medium.freecodecamp.org/using-svg-as-placeholders-more-image-loading-techniques-bed1b810ab2c)),  by [José M. Pérez](https://medium.com/@jmperezperez) about some techniques for lazy-loading images in order to improve browser's performance and mainly to provide the users with a better experience. Specially, he strucked me with the use of SVG Placeholders.

Maybe the most remarkable thing about this it's not the lazy loading itself but the idea of using this low detailed SVG images based on real photographies. Pérez uses and recommends an application called [Primitive](https://primitive.lol/) that generates an image composed by overlaped simple geometric shapes. The iterative method used by this program allows the user to chose the number of rounds. The more rounds the bigger the resemblance with the original photography.

I've used this waterfall photo from Codepen's assets library (the original is 11MB – 7.952×4.811 px) and its 20KB SVG version produced by 200 rounds of triangle shapes for my tests. 

![Waterfall](http://jordinebot.cat/static/waterfall-lo.jpeg)

It's cool, isn't it? Obviously no website should be using 11MB images but I am in the pens below, just to be sure the image will take a while to load.

My first approach is to just swap the SVG with the original JPEG once it finishes loading:

<p data-height="313" data-theme-id="dark" data-slug-hash="MOOgob" data-default-tab="js,result" data-user="jordinebot" data-embed-version="2" data-pen-title="SVG Image Placeholder Swap" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jordinebot/pen/MOOgob/">SVG Image Placeholder</a> by Jordi Nebot (<a href="https://codepen.io/jordinebot">@jordinebot</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

I think this polygonal effect of the SVG Placeholder is pretty cool and it could be a great asset by itself in some designs. But if by any means it doesn't fit the design we could apply a blur filter on the SVG and it'll look totally different... almost like if we were using a blurred low resolution thumbnail.

<p data-height="313" data-theme-id="dark" data-slug-hash="XzzWey" data-default-tab="js,result" data-user="jordinebot" data-embed-version="2" data-pen-title="SVG Image Placeholder Blur-up" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jordinebot/pen/MOOgob/">SVG Image Placeholder</a> by Jordi Nebot (<a href="https://codepen.io/jordinebot">@jordinebot</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

I prefer the sharper SVG version but in either case I don't like the instant swap effect, so I tried to apply a transition. Since the image tag `src` attribute is not a valid [animatable CSS property](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties) we need some workaround like using two overlapped `<img>` tags and apply a crossfading transition on opacity or use a `background-image` in a `<div>`. In this case, to avoid the annoyance of content jumping around we need to know the image's aspect ratio in advance to apply a `padding-bottom` to the layer (this is a technique I learned from [Embed Responsively](http://embedresponsively.com/)).

We also need to find a magic number as `transition-delay` related to the original image filesize. I noticed that a low delay for a big enough image results in no transition at all, so I'm using 10″ in this example and honestly I think the result is pretty awesome! ==(Edit: It looks like it doesn't work in the new Firefox Quantum)==

<p data-height="313" data-theme-id="dark" data-slug-hash="vWWYrG" data-default-tab="js,result" data-user="jordinebot" data-embed-version="2" data-pen-title="SVG Image Placeholder with transition" data-preview="true" class="codepen">See the Pen <a href="https://codepen.io/jordinebot/pen/MOOgob/">SVG Image Placeholder</a> by Jordi Nebot (<a href="https://codepen.io/jordinebot">@jordinebot</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

I can't wait to apply this technique in a production site!

## Bonus

In Primitive's site they have this nice [animation](https://primitive.lol/#animation) so I decided to build my own:

<p data-height="565" data-theme-id="dark" data-slug-hash="RjjMqa" data-default-tab="result" data-user="jordinebot" data-embed-version="2" data-pen-title="SVG Waterfall Animation" class="codepen">See the Pen <a href="https://codepen.io/jordinebot/pen/RjjMqa/">SVG Waterfall Animation</a> by Jordi Nebot (<a href="https://codepen.io/jordinebot">@jordinebot</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>
