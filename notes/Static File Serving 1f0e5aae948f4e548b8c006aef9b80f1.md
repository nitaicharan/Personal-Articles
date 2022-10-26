# Static File Serving

Created: June 6, 2022 6:04 PM
Finished: No
Source: https://nextjs.org/docs/basic-features/static-file-serving

![documentation.png](Static%20File%20Serving%201f0e5aae948f4e548b8c006aef9b80f1/documentation.png)

Next.js can serve static files, like images, under a folder called `public` in the root directory. Files inside `public` can then be referenced by your code starting from the base URL (`/`).

For example, if you add an image to `public/me.png`, the following code will access the image:

```
import Image from 'next/image'

function Avatar() {
  return <Image src="/me.png" alt="me" width="64" height="64" />
}

export default Avatar

```

> 
> 
> 
> Note: `next/image` requires Next.js 10 or later.
> 

This folder is also useful for `robots.txt`, `favicon.ico`, Google Site Verification, and any other static files (including `.html`)!

> 
> 
> 
> **Note**: Don't name the `public` directory anything else. The name cannot be changed and is the only directory used to serve static assets.
> 

> 
> 
> 
> **Note**: Be sure to not have a static file with the same name as a file in the `pages/` directory, as this will result in an error.
> 
> Read more: [https://nextjs.org/docs/messages/conflicting-public-file-page](https://nextjs.org/docs/messages/conflicting-public-file-page)
> 

> 
> 
> 
> **Note**: Only assets that are in the `public` directory at [build time](https://nextjs.org/docs/api-reference/cli#build) will be served by Next.js. Files added at runtime won't be available. We recommend using a third party service like [AWS S3](https://aws.amazon.com/s3/) for persistent file storage.
>