# Cumulative Layout Shift (CLS)

Created: June 6, 2022 6:17 PM
Finished: No
Source: https://web.dev/cls/

Have you ever been reading an article online when something suddenly changes on the page? Without warning, the text moves, and you've lost your place. Or even worse: you're about to tap a link or a button, but in the instant before your finger lands—BOOM—the link moves, and you end up clicking something else!

Most of the time these kinds of experiences are just annoying, but in some cases, they can cause real damage.

[layout-instability2.webm](https://storage.googleapis.com/web-dev-assets/layout-instability-api/layout-instability2.webm)

A screencast illustrating how layout instability can negatively affect users.

Unexpected movement of page content usually happens because resources are loaded asynchronously or DOM elements get dynamically added to the page above existing content. The culprit might be an image or video with unknown dimensions, a font that renders larger or smaller than its fallback, or a third-party ad or widget that dynamically resizes itself.

What makes this issue even more problematic is that how a site functions in development is often quite different from how users experience it. Personalized or third-party content often doesn't behave the same in development as it does in production, test images are often already in the developer's browser cache, and API calls that run locally are often so fast that the delay isn't noticeable.

The Cumulative Layout Shift (CLS) metric helps you address this problem by measuring how often it's occurring for real users.

## What is CLS? [#](https://web.dev/cls/#what-is-cls)

CLS is a measure of the largest burst of *layout shift scores* for every [unexpected](https://web.dev/cls/#expected-vs.-unexpected-layout-shifts) layout shift that occurs during the entire lifespan of a page.

A *layout shift* occurs any time a visible element changes its position from one rendered frame to the next. (See below for details on how individual [layout shift scores](https://web.dev/cls/#layout-shift-score) are calculated.)

A burst of layout shifts, known as a *[session window](https://web.dev/evolving-cls/#why-a-session-window)*, is when one or more individual layout shifts occur in rapid succession with less than 1-second in between each shift and a maximum of 5 seconds for the total window duration.

The largest burst is the session window with the maximum cumulative score of all layout shifts within that window.

[session-window.webm](https://storage.googleapis.com/web-dev-assets/better-layout-shift-metric/session-window.webm)

Example of session windows. Blue bars represent the scores of each individual layout shift.

### What is a good CLS score? [#](https://web.dev/cls/#what-is-a-good-cls-score)

To provide a good user experience, sites should strive to have a CLS score of **0.1** or less. To ensure you're hitting this target for most of your users, a good threshold to measure is the **75th percentile** of page loads, segmented across mobile and desktop devices.

![Cumulative%20Layout%20Shift%20(CLS)%2007d5a3dff4064f728d79541216087125/uqclEgIlTHhwIgNTXN3Y.svg](Cumulative%20Layout%20Shift%20(CLS)%2007d5a3dff4064f728d79541216087125/uqclEgIlTHhwIgNTXN3Y.svg)

## Layout shifts in detail [#](https://web.dev/cls/#layout-shifts-in-detail)

Layout shifts are defined by the [Layout Instability API](https://github.com/WICG/layout-instability), which reports `layout-shift` entries any time an element that is visible within the viewport changes its start position (for example, its top and left position in the default [writing mode](https://developer.mozilla.org/docs/Web/CSS/writing-mode)) between two frames. Such elements are considered *unstable elements*.

Note that layout shifts only occur when existing elements change their start position. If a new element is added to the DOM or an existing element changes size, it doesn't count as a layout shift—as long as the change doesn't cause other visible elements to change their start position.

### Layout shift score [#](https://web.dev/cls/#layout-shift-score)

To calculate the *layout shift score*, the browser looks at the viewport size and the movement of *unstable elements* in the viewport between two rendered frames. The layout shift score is a product of two measures of that movement: the *impact fraction* and the *distance fraction* (both defined below).

### Impact fraction [#](https://web.dev/cls/#impact-fraction)

The [impact fraction](https://github.com/WICG/layout-instability#Impact-Fraction) measures how *unstable elements* impact the viewport area between two frames.

The union of the visible areas of all *unstable elements* for the previous frame *and* the current frame—as a fraction of the total area of the viewport—is the *impact fraction* for the current frame.

![Cumulative%20Layout%20Shift%20(CLS)%2007d5a3dff4064f728d79541216087125/BbpE9rFQbF8aU6iXN1U6.png](Cumulative%20Layout%20Shift%20(CLS)%2007d5a3dff4064f728d79541216087125/BbpE9rFQbF8aU6iXN1U6.png)

In the image above there's an element that takes up half of the viewport in one frame. Then, in the next frame, the element shifts down by 25% of the viewport height. The red, dotted rectangle indicates the union of the element's visible area in both frames, which, in this case, is 75% of the total viewport, so its *impact fraction* is `0.75`.

### Distance fraction [#](https://web.dev/cls/#distance-fraction)

The other part of the layout shift score equation measures the distance that unstable elements have moved, relative to the viewport. The *distance fraction* is the greatest distance any *unstable element* has moved in the frame (either horizontally or vertically) divided by the viewport's largest dimension (width or height, whichever is greater).

![Cumulative%20Layout%20Shift%20(CLS)%2007d5a3dff4064f728d79541216087125/ASnfpVs2n9winu6mmzdk.png](Cumulative%20Layout%20Shift%20(CLS)%2007d5a3dff4064f728d79541216087125/ASnfpVs2n9winu6mmzdk.png)

In the example above, the largest viewport dimension is the height, and the unstable element has moved by 25% of the viewport height, which makes the *distance fraction* 0.25.

So, in this example the *impact fraction* is `0.75` and the *distance fraction* is `0.25`, so the *layout shift score* is `0.75 * 0.25 = 0.1875`.

The next example illustrates how adding content to an existing element affects the layout shift score:

![Cumulative%20Layout%20Shift%20(CLS)%2007d5a3dff4064f728d79541216087125/xhN81DazXCs8ZawoCj0T.png](Cumulative%20Layout%20Shift%20(CLS)%2007d5a3dff4064f728d79541216087125/xhN81DazXCs8ZawoCj0T.png)

The "Click Me!" button is appended to the bottom of the gray box with black text, which pushes the green box with white text down (and partially out of the viewport).

In this example, the gray box changes size, but its start position does not change so it's not an *unstable element*.

The "Click Me!" button was not previously in the DOM, so its start position doesn't change either.

The start position of the green box, however, does change, but since it's been moved partially out of the viewport, the invisible area is not considered when calculating the *impact fraction*. The union of the visible areas for the green box in both frames (illustrated by the red, dotted rectangle) is the same as the area of the green box in the first frame—50% of the viewport. The *impact fraction* is `0.5`.

The *distance fraction* is illustrated with the purple arrow. The green box has moved down by about 14% of the viewport so the *distance fraction* is `0.14`.

The layout shift score is `0.5 x 0.14 = 0.07`.

This last example illustrates multiple *unstable elements*:

![Cumulative%20Layout%20Shift%20(CLS)%2007d5a3dff4064f728d79541216087125/FdCETo2dLwGmzw0V5lNT.png](Cumulative%20Layout%20Shift%20(CLS)%2007d5a3dff4064f728d79541216087125/FdCETo2dLwGmzw0V5lNT.png)

In the first frame above there are four results of an API request for animals, sorted in alphabetical order. In the second frame, more results are added to the sorted list.

The first item in the list ("Cat") does not change its start position between frames, so it's stable. Similarly, the new items added to the list were not previously in the DOM, so their start positions don't change either. But the items labelled "Dog", "Horse", and "Zebra" all shift their start positions, making them *unstable elements*.

Again, the red, dotted rectangles represent the union of these three *unstable elements*' before and after areas, which in this case is around 38% of the viewport's area (*impact fraction* of `0.38`).

The arrows represent the distances that *unstable elements* have moved from their starting positions. The "Zebra" element, represented by the blue arrow, has moved the most, by about 30% of the viewport height. That makes the *distance fraction* in this example `0.3`.

The layout shift score is `0.38 x 0.3 = 0.1172`.

### Expected vs. unexpected layout shifts [#](https://web.dev/cls/#expected-vs.-unexpected-layout-shifts)

Not all layout shifts are bad. In fact, many dynamic web applications frequently change the start position of elements on the page.

### User-initiated layout shifts [#](https://web.dev/cls/#user-initiated-layout-shifts)

A layout shift is only bad if the user isn't expecting it. On the other hand, layout shifts that occur in response to user interactions (clicking a link, pressing a button, typing in a search box and similar) are generally fine, as long as the shift occurs close enough to the interaction that the relationship is clear to the user.

For example, if a user interaction triggers a network request that may take a while to complete, it's best to create some space right away and show a loading indicator to avoid an unpleasant layout shift when the request completes. If the user doesn't realize something is loading, or doesn't have a sense of when the resource will be ready, they may try to click something else while waiting—something that could move out from under them.

Layout shifts that occur within 500 milliseconds of user input will have the `[hadRecentInput](https://wicg.github.io/layout-instability/#dom-layoutshift-hadrecentinput)` flag set, so they can be excluded from calculations.

### Animations and transitions [#](https://web.dev/cls/#animations-and-transitions)

Animations and transitions, when done well, are a great way to update content on the page without surprising the user. Content that shifts abruptly and unexpectedly on the page almost always creates a bad user experience. But content that moves gradually and naturally from one position to the next can often help the user better understand what's going on, and guide them between state changes.

Be sure to respect `[prefers-reduced-motion](https://web.dev/prefers-reduced-motion/)` browser settings, as some site visitors can experience ill effects or attention issues from animation.

CSS `[transform](https://developer.mozilla.org/docs/Web/CSS/transform)` property allows you to animate elements without triggering layout shifts:

- Instead of changing the `height` and `width` properties, use `transform: scale()`.
- To move elements around, avoid changing the `top`, `right`, `bottom`, or `left` properties and use `transform: translate()` instead.

## How to measure CLS [#](https://web.dev/cls/#how-to-measure-cls)

CLS can be measured [in the lab](https://web.dev/user-centric-performance-metrics/#in-the-lab) or [in the field](https://web.dev/user-centric-performance-metrics/#in-the-field), and it's available in the following tools:

### Field tools [#](https://web.dev/cls/#field-tools)

### Lab tools [#](https://web.dev/cls/#lab-tools)

- [Chrome DevTools](https://developer.chrome.com/docs/devtools/)
- [Lighthouse](https://developer.chrome.com/docs/lighthouse/overview/)
- [WebPageTest](https://webpagetest.org/)

### Measure CLS in JavaScript [#](https://web.dev/cls/#measure-cls-in-javascript)

To measure CLS in JavaScript, you can use the [Layout Instability API](https://github.com/WICG/layout-instability). The following example shows how to create a `[PerformanceObserver](https://developer.mozilla.org/docs/Web/API/PerformanceObserver)` that listens for unexpected `layout-shift` entries, groups them into sessions, and logs the maximum session value any time it changes.

In most cases, the current CLS value at the time the page is being unloaded is the final CLS value for that page, but there are a few important exceptions:

The following section lists the differences between what the API reports and how the metric is calculated.

### Differences between the metric and the API [#](https://web.dev/cls/#differences-between-the-metric-and-the-api)

- If a page is loaded in the background, or if it's backgrounded prior to the browser painting any content, then it should not report any CLS value.
- If a page is restored from the [back/forward cache](https://web.dev/bfcache/#impact-on-core-web-vitals), its CLS value should be reset to zero since users experience this as a distinct page visit.
- The API does not report `layout-shift` entries for shifts that occur within iframes, but to properly measure CLS you should consider them. Sub-frames can use the API to report their `layout-shift` entries to the parent frame for [aggregation](https://github.com/WICG/layout-instability#cumulative-scores).

In addition to these exceptions, CLS has some added complexity due to the fact that it measures the entire lifespan of a page:

- Users might keep a tab open for a *very* long time—days, weeks, months. In fact, a user might never close a tab.
- On mobile operating systems, browsers typically do not run page unload callbacks for background tabs, making it difficult to report the "final" value.

To handle such cases, CLS should be reported any time a page is background—in addition to any time it's unloaded (the `[visibilitychange` event](https://developer.chrome.com/blog/page-lifecycle-api/#event-visibilitychange) covers both of these scenarios). And analytics systems receiving this data will then need to calculate the final CLS value on the backend.

Rather than memorizing and grappling with all of these cases yourself, developers can use the `[web-vitals` JavaScript library](https://github.com/GoogleChrome/web-vitals) to measure CLS, which accounts for everything mentioned above:

You can refer to [the source code for `getCLS)`](https://github.com/GoogleChrome/web-vitals/blob/master/src/getCLS.ts) for a complete example of how to measure CLS in JavaScript.

## How to improve CLS [#](https://web.dev/cls/#how-to-improve-cls)

For most websites, you can avoid all unexpected layout shifts by sticking to a few guiding principles:

- **Always include size attributes on your images and video elements, or otherwise reserve the required space with something like [CSS aspect ratio boxes](https://css-tricks.com/aspect-ratio-boxes/).** This approach ensures that the browser can allocate the correct amount of space in the document while the image is loading. Note that you can also use the [unsized-media feature policy](https://github.com/w3c/webappsec-feature-policy/blob/master/policies/unsized-media.md) to force this behavior in browsers that support feature policies.
- **Never insert content above existing content, except in response to a user interaction.** This ensures any layout shifts that occur are expected.
- **Prefer transform animations to animations of properties that trigger layout changes.** Animate transitions in a way that provides context and continuity from state to state.

For a deep dive on how to improve CLS, see [Optimize CLS](https://web.dev/optimize-cls/) and [Debug layout shifts](https://web.dev/debug-layout-shifts) .

## Additional resources [#](https://web.dev/cls/#additional-resources)

- Google Publisher Tag's guidance on [minimizing layout shift](https://developers.google.com/doubleclick-gpt/guides/minimize-layout-shift)
- [Understanding Cumulative Layout Shift](https://youtu.be/zIJuY-JCjqw) by [Annie Sullivan](https://anniesullie.com/) and [Steve Kobes](https://kobes.ca/) at [#PerfMatters](https://perfmattersconf.com/) (2020)

## CHANGELOG [#](https://web.dev/cls/#changelog)

Occasionally, bugs are discovered in the APIs used to measure metrics, and sometimes in the definitions of the metrics themselves. As a result, changes must sometimes be made, and these changes can show up as improvements or regressions in your internal reports and dashboards.

To help you manage this, all changes to either the implementation or definition of these metrics will be surfaced in this [CHANGELOG](http://bit.ly/chrome-speed-metrics-changelog).