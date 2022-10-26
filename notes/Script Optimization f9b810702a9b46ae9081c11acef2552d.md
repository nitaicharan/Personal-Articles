# Script Optimization

Created: December 26, 2021 1:23 AM
Finished: No
Source: https://nextjs.org/docs/basic-features/script

![documentation.png](Script%20Optimization%20f9b810702a9b46ae9081c11acef2552d/documentation.png)

The Next.js Script component, `[next/script](https://nextjs.org/docs/api-reference/next/script)`, is an extension of the HTML `<script>` element. It enables developers to set the loading priority of third-party scripts anywhere in their application without needing to append directly to `next/head`, saving developer time while improving loading performance.

```tsx
import Script from 'next/script'

export default function Home() {
  return (
    <>
      <Script src="https://www.google-analytics.com/analytics.js" />
    </>
  )
}

```

Websites often use third-party scripts to include different types of functionality into their site, such as analytics, ads, customer support widgets, and consent management. However, this can introduce problems that impact both user and developer experience:

- Some third-party scripts are heavy on loading performance and can drag down the user experience, especially if they are render-blocking and delay any page content from loading
- Developers often struggle to decide where to place third-party scripts in an application to ensure optimal loading

The Script component makes it easier for developers to place a third-party script anywhere in their application while taking care of optimizing its loading strategy.

To add a third-party script to your application, import the `next/script` component:

```tsx
import Script from 'next/script'

```

With `next/script`, you decide when to load your third-party script by using the `strategy` property:

```tsx
<Script src="https://connect.facebook.net/en_US/sdk.js" strategy="lazyOnload" />

```

There are three different loading strategies that can be used:

- `beforeInteractive`: Load before the page is interactive
- `afterInteractive`: (**default**): Load immediately after the page becomes interactive
- `lazyOnload`: Load during idle time

Scripts that load with the `beforeInteractive` strategy are injected into the initial HTML from the server and run before self-bundled JavaScript is executed. This strategy should be used for any critical scripts that need to be fetched and executed before the page is interactive.

```tsx
<Script
  src="https://cdn.jsdelivr.net/npm/cookieconsent@3/build/cookieconsent.min.js"
  strategy="beforeInteractive"
/>

```

Examples of scripts that should be loaded as soon as possible with this strategy include:

- Bot detectors
- Cookie consent managers

Scripts that use the `afterInteractive` strategy are injected client-side and will run after Next.js hydrates the page. This strategy should be used for scripts that do not need to load as soon as possible and can be fetched and executed immediately after the page is interactive.

```tsx
<Script
  strategy="afterInteractive"
  dangerouslySetInnerHTML={{
    __html: `
    (function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
    new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
    j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
    'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
    })(window,document,'script','dataLayer', 'GTM-XXXXXX');
  `,
  }}
/>

```

Examples of scripts that are good candidates to load immediately after the page becomes interactive include:

- Tag managers
- Analytics

Scripts that use the `lazyOnload` strategy are loaded late after all resources have been fetched and during idle time. This strategy should be used for background or low priority scripts that do not need to load before or immediately after a page becomes interactive.

```tsx
<Script src="https://connect.facebook.net/en_US/sdk.js" strategy="lazyOnload" />

```

Examples of scripts that do not need to load immediately and can be lazy-loaded include:

- Chat support plugins
- Social media widgets

Inline scripts, or scripts not loaded from an external file, are also supported by the Script component. They can be written by placing the JavaScript within curly braces:

```tsx
<Script id="show-banner" strategy="lazyOnload">
  {`document.getElementById('banner').classList.remove('hidden')`}
</Script>

```

Or by using the `dangerouslySetInnerHTML` property:

```tsx
<Script
  id="show-banner"
  dangerouslySetInnerHTML={{
    __html: `document.getElementById('banner').classList.remove('hidden')`,
  }}
/>

```

There are two limitations to be aware of when using the Script component for inline scripts:

- Only the `afterInteractive` and `lazyOnload` strategies can be used. The `beforeInteractive` loading strategy injects the contents of an external script into the initial HTML response. Inline scripts already do this, which is why **the `beforeInteractive` strategy cannot be used with inline scripts.**
- An `id` attribute must be defined in order for Next.js to track and optimize the script

Some third-party scripts require users to run JavaScript code after the script has finished loading in order to instantiate content or call a function. If you are loading a script with either `beforeInteractive` or `afterInteractive` as a loading strategy, you can execute code after it has loaded using the `onLoad` property:

```tsx
import { useState } from 'react'
import Script from 'next/script'

export default function Home() {
  const [stripe, setStripe] = useState(null)

  return (
    <>
      <Script
        id="stripe-js"
        src="https://js.stripe.com/v3/"
        onLoad={() => {
          setStripe({ stripe: window.Stripe('pk_test_12345') })
        }}
      />
    </>
  )
}

```

Sometimes it is helpful to catch when a script fails to load. These errors can be handled with the `onError` property:

```tsx
import Script from 'next/script'

export default function Home() {
  return (
    <>
      <Script
        id="will-fail"
        src="https://example.com/non-existant-script.js"
        onError={(e) => {
          console.error('Script failed to load', e)
        }}
      />
    </>
  )
}

```

There are many DOM attributes that can be assigned to a `<script>` element that are not used by the Script component, like `[nonce](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce)` or [custom data attributes](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/data-*). Including any additional attributes will automatically forward it to the final, optimized `<script>` element that is outputted to the page.

```tsx
import Script from 'next/script'

export default function Home() {
  return (
    <>
      <Script
        src="https://www.google-analytics.com/analytics.js"
        id="analytics"
        nonce="XUENAJFW"
        data-test="analytics"
      />
    </>
  )
}

```