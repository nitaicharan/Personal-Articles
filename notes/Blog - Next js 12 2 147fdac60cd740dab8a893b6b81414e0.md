# Blog - Next.js 12.2

Created: July 5, 2022 10:20 AM
Finished: No
Source: https://nextjs.org/blog/next-12-2

![twitter-card.png](Blog%20-%20Next%20js%2012%202%20147fdac60cd740dab8a893b6b81414e0/twitter-card.png)

We're laying the foundation for the future of Next.js with 12.2:

Update today by running `npm i next@latest`.

We're excited to announce Middleware is now stable in 12.2 and has an improved API based on feedback from users.

Middleware allows you to run code before a request is completed. Based on the incoming request, you can modify the response by rewriting, redirecting, adding headers, or setting cookies.

```
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

// If the incoming request has the "beta" cookie
// then we'll rewrite the request to /beta
export function middleware(req: NextRequest) {
  const isInBeta = JSON.parse(req.cookies.get('beta') || 'false');
  req.nextUrl.pathname = isInBeta ? '/beta' : '/';
  return NextResponse.rewrite(req.nextUrl);
}

// Supports both a single value or an array of matches
export const config = {
  matcher: '/',
};

```

To update to the newest API changes for Middleware, please see the [migration guide](https://nextjs.org/docs/messages/middleware-upgrade-guide).

Try Middleware for [free on Vercel](https://vercel.com/edge) or when [self-hosting](https://nextjs.org/docs/deployment#self-hosting) using `next start`.

On-Demand Incremental Static Regeneration (ISR) allows you to update the content on your site without needing to redeploy. This makes it easy to update your site immediately when data changes in your headless CMS or commerce platform. This was one of the most requested features from the community and we're excited it is now stable.

```
// pages/api/revalidate.js

export default async function handler(req, res) {
  // Check for secret to confirm this is a valid request
  if (req.query.secret !== process.env.MY_SECRET_TOKEN) {
    return res.status(401).json({ message: 'Invalid token' });
  }

  try {
    await res.revalidate('/path-to-revalidate');
    return res.json({ revalidated: true });
  } catch (err) {
    // If there was an error, Next.js will continue
    // to show the last successfully generated page
    return res.status(500).send('Error revalidating');
  }
}

```

Incremental Static Regeneration works for any providers supporting the [Next.js Build API](https://nextjs.org/docs/deployment#nextjs-build-api) (`next build`). When deployed to [Vercel](https://vercel.com/), on-demand revalidation propagates globally in ~300ms when pushing pages to the edge.

For more information, [check out the documentation](https://nextjs.org/docs/basic-features/data-fetching/incremental-static-regeneration) or [view our demo](https://on-demand-isr.vercel.app/) to see on-demand revalidation in action.

Next.js now also supports using the [Edge Runtime](https://nextjs.org/docs/api-reference/edge-runtime) for API Routes. The Edge Runtime is a lighter-weight runtime than Node.js providing fast startups for low latency. In addition, Edge API Routes support streaming responses from the server.

You can set the runtime for an API Route in the `config`, specifying either `nodejs` (default) or `experimental-edge`:

```
import type { NextRequest } from 'next/server';

export default (req: NextRequest) => {
  return new Response(`Hello, from ${req.url} I'm now an Edge API Route!`);
};

export const config = {
  runtime: 'experimental-edge',
};

```

Because the Edge Runtime is lightweight, it has limitations to accommodate fast startup — for example, it does not support Node.js-specific APIs like `fs`. Therefore, the default runtime for API Routes remains `nodejs`.

For more information, [check out the documentation](https://nextjs.org/docs/api-routes/edge-api-routes).

Next.js now supports using the [Edge Runtime](https://nextjs.org/docs/api-reference/edge-runtime) for server rendering.

As mentioned above, the Edge Runtime is a lighter-weight runtime than Node.js providing fast startups for low latency. When used with React 18, it enables streaming server-rendering for Pages.

Next.js uses Node.js as the default runtime for server-side rendering pages. Starting with 12.2, if you are using React 18 you can opt into using the Edge Runtime.

You can set the runtime globally in `next.config.js`, specifying either `nodejs` or `experimental-edge`:

```
// next.config.js

module.exports = {
  experimental: {
    runtime: 'experimental-edge',
  },
};

```

Changing the default page runtime affects all pages, including [SSR streaming](https://nextjs.org/docs/advanced-features/react-18/streaming) and [Server Components](https://nextjs.org/docs/advanced-features/react-18/server-components) features. You can also override this default on a per-page basis, by exporting a `runtime` config:

```
export const config = {
  runtime: 'nodejs',
};

```

You can detect which runtime you're using by looking at the `process.env.NEXT_RUNTIME` Environment Variable during runtime, and examining the `options.nextRuntime` variable during webpack compilation.

For more information, [check out the documentation](https://nextjs.org/docs/advanced-features/react-18/switchable-runtime).

We've heard your feedback on the current Image component and are excited to share an early preview of the new `next/image`. This new and improved image component requires less client-side JavaScript and simplifies how you style images:

- Renders a single `<img>` without `<div>` or `<span>` wrappers
- Added support for canonical `style` prop
- Removed `layout`, `objectFit`, and `objectPosition` props in favor of `style` or `className`
- Removed `IntersectionObserver` implementation in favor of [native lazy loading](https://caniuse.com/loading-lazy-attr)
- Removed `loader` config in favor of `loader` prop
- Note: no `fill` mode (yet) so `width` & `height` props are required

This improves performance because native `loading="lazy"` doesn't need to wait for React hydration and client-side JavaScript.

For more information, [check out the documentation](https://nextjs.org/docs/api-reference/next/future/image).

`next/image` now supports an experimental configuration option `remotePatterns` that allows you to specify wildcards for remote images when using the built-in Image Optimization API. This allows for more powerful matching beyond the existing `[images.domains](http://images.domains/)` configuration, which only performs exact matches on the domain name.

```
// next.config.js

module.exports = {
  experimental: {
    images: {
      remotePatterns: [
        {
          // The `src` property hostname must end with `.example.com`,
          // otherwise this will respond with 400 Bad Request.
          protocol: 'https',
          hostname: '**.example.com',
        },
      ],
    },
  },
};

```

For more information, [check out the documentation](https://nextjs.org/docs/api-reference/next/image#remote-patterns).

The zero-config Image Optimization API prevents the use of `next export` since it requires a server to optimize images on-demand as they're requested. Until today, users targeting `next export` needed to configure the `loader` to use a 3rd party Image Optimization provider, but there was no clear solution if there was no provider available. Starting today, `next export` users can disable Image Optimization for all instances of `next/image` with a new configuration property:

```
module.exports = {
  experimental: {
    images: {
      unoptimized: true,
    },
  },
};

```

The [Next.js Compiler](https://nextjs.org/docs/advanced-features/compiler) uses [SWC](https://swc.rs/) to transform and minify your JavaScript code for production. SWC was introduced in Next.js 12.0 to improve both local development and build performance.

You can now add plugins (written in WebAssembly) to customize the SWC transformation behavior during compilation:

```
// next.config.js

module.exports = {
  experimental: {
    swcPlugins: [
      ['plugin', {
        [require.resolve("css-variable/swc"), { displayName: true, basePath: __dirname }]
      }]
    ]
  }
}

```

For more information, [check out the documentation](https://github.com/vercel/next.js/blob/canary/docs/advanced-features/compiler.md#experimental-swc-plugin-support).

- Improved support for CSS-in-JS libraries like `styled-components` and `emotion` with a smoother upgrade experience and no breaking changes.
- AMP and HTML post-optimization (CSS, fonts optimizations) are properly supported now.
- `next/head` now supports React 18.
- Next.js' route announcer, which is used to properly announce page transitions to screen readers and other assistive technology, now supports React 18.
- Support for Emotion transform in the Next.js Compiler. Most features of `@emotion/babel-plugin` are now supported, and unless you are using `importMap`, it can be removed. For more information, [check out the documentation](https://nextjs.org/docs/advanced-features/compiler#emotion).
- Better support for the `styled-components` transform in the Next.js Compiler by allowing customization of the default options, including the `cssProp` option. For more information, [check out the documentation](https://nextjs.org/docs/advanced-features/compiler#styled-components).
- Better support for JavaScript ES Modules, so components like `next/image` and `next/link` can properly be `import`ed.
- `next/link` no longer requires manually adding `<a>` as a child. You can now [opt into this behavior](https://github.com/vercel/next.js/pull/36436) in a backward-compatible way.
- We've added experimental support for shipping only modern JavaScript by modifying `browsersList`. You can opt-into this behavior by setting `browsersListForSwc: true` and `legacyBrowsers: false` in the `experimental` option of `next.config.js`.
- New `@swc/helpers` optimizations prevent duplication across bundles, reducing bundle size by around `2KB` in a minimal configuration, and more on larger apps
- We've significantly reduced the Next.js install size. We did this by moving our monorepo to `pnpm`, which allows us to remove duplicate packages while creating the pre-compiled versions that we use. This leads to a reduction in install size of 14MB.
- In our continued effort to improve self-hosting Next.js, we are stabilizing our experimental `outputStandalone: true` config to `output: 'standalone'`. This config reduces deployment sizes drastically by only including necessary files/assets, including removing the need for installing all of `node_modules` in the built deployment package. This config can be seen in action in our `[with-docker` example](https://github.com/vercel/next.js/blob/canary/examples/with-docker/README.md).

In case you missed it, last month we announced the [Layouts RFC](https://nextjs.org/blog/layouts-rfc) – the biggest update to Next.js since it was introduced in 2016 including:

- **Nested Layouts:** Build complex applications with nested routes.
- **Designed for Server Components:** Optimized for subtree navigation.
- **Improved Data Fetching:** Fetch in layouts while avoiding waterfalls.
- **Using React 18 Features:** Streaming, Transitions, and Suspense.
- **Client and Server Routing:** Server-centric routing with *SPA-like* behavior.
- **100% incrementally adoptable**: No breaking changes so you can adopt gradually.
- **Advanced Routing Conventions**: Offscreen stashing, instant transitions, and more.

For more information, [check out the RFC](https://nextjs.org/blog/layouts-rfc) or [provide feedback](https://github.com/vercel/next.js/discussions/37136).

Next.js is the result of the combined work of **over 2,000 individual developers**, industry partners like Google Chrome and Meta, and our core team at Vercel.

This release was brought to you by the contributions of: @huozhi, @ijjk, @kwonoj, @ViolanteCodes, @akrabdev, @timneutkens, @jpveilleux, @stigkj, @jgoping, @oof2win2, @Brooooooklyn, @CGamesPlay, @lfades, @molebox, @steven-tey, @SukkaW, @Kikobeats, @balazsorban44, @erikbrinkman, @therealmarzouq, @remcohaszing, @perkinsjr, @shuding, @hanneslund, @housseindjirdeh, @RobertKeyser, @styfle, @htunnicliff, @lukeshumard, @sagnik3, @pixelass, @JoshuaKGoldberg, @rishabhpoddar, @nguyenyou, @kdy1, @sidwebworks, @gnoff, @gaspar09, @feugy, @mfix-stripe, @javivelasco, @Chastrlove, @goncharov-vlad, @NaveenDA, @Firfi, @idkwhojamesis, @FLCN-16, @icyJoseph, @ElijahPepe, @elskwid, @irvile, @Munawwar, @ykolbin, @hulufei, @baruchadi, @imadatyatalah, @await-ovo, @menosprezzi, @gazs, @Exortions, @rubens-lopes, @woochul2, @stefee, @stmtk1, @jlarmstrongiv, @MaedahBatool, @jameshfisher, @fabienheureux, @TxHawks, @mattbrandlysonos, @iggyzap, @src200, @AkifumiSato, @hermanskurichin, @kamilogorek, @ben-xD, @dawsonbooth, @Josehower, @crutchcorn, @ericmatthys, @CharlesStover, @charlypoly, @apmatthews, @naingaungphyo, @alexandrutasica, @stefanprobst, @dc7290, @DilwoarH, @tommarshall, @stanhong, @leerob, @appsbytom, @sshyam-gupta, @saulloalmeida, @indicozy, @ArianHamdi, @Clariity, @sebastianbenz, @7iomka, @gr-qft, @Schniz, @dgagn, @sokra, @okbel, @tbvjaos510, @dmvjs, @PepijnSenders, @JohnPhamous, @kyliau, @eric-burel, @alabhyajindal, @jsjoeio, @vorcigernix, @clearlyTHUYDOAN, @splatterxl, @manovotny, @maxproske, @nvh95, @frankievalentine, @nuta, @bagpyp, @dfelsie, @qqpann, @atcastle, @jsimonrichard, @mass2527, @ekamkohli, @Yuddomack, @tonyspiro, @saurabhmehta1601, @banner4422, @falsepopsky, @jantimon, @henriqueholtz, @ilfa, @matteobruni, @ryscheng, @hoonoh, @ForsakenHarmony, @william-keller, @AleksaC, @Miikis, @zakiego, @radunemerenco, @AliYusuf95, and @dominiksipowicz.