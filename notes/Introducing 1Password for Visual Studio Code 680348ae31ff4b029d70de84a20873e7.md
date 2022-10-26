# Introducing 1Password for Visual Studio Code

Created: June 23, 2022 9:41 AM
Finished: No
Source: https://blog.1password.com/1password-visual-studio-code/

![Introducing%201Password%20for%20Visual%20Studio%20Code%20680348ae31ff4b029d70de84a20873e7/header.png](Introducing%201Password%20for%20Visual%20Studio%20Code%20680348ae31ff4b029d70de84a20873e7/header.png)

In writing software, we’re used to embedding secrets and other configurable values right in the codebase. They might be Stripe keys to power your online shop, webhooks for a custom Slack bot, a Docker username and password for a CI config, AWS credentials, or an API token and host to set up 1Password [Connect](https://developer.1password.com/docs/connect/).

Secrets are used *everywhere* in our code. Sometimes, though, we forget when we’ve been using real secrets in our work. Maybe there’s a leftover token you dropped in to build that one feature, or maybe you didn’t delete the `.env` file you set up to test drive the app. Now you’ve got to rotate your secrets because you accidentally committed and pushed sensitive values for the whole world to see. Yikes.

We’ve all been there. That’s why I’m delighted that I get to announce the launch of the all-new [1Password for VS Code extension](https://developer.1password.com/docs/vscode).

## Go ahead, commit your secrets references

With [1Password Secrets Automation](https://1password.com/products/secrets/), the 1Password Developer Products team introduced the concept of [secret references](https://developer.1password.com/docs/cli/secrets-reference-syntax). It starts by storing a sensitive value, such as an API credential or client ID, in 1Password. That item and the field you’d like to get the value from can then be retrieved through a special `op://` URL scheme that 1Password’s tooling knows how to parse. It’s made up of three parts: vault, item, and field. This is known as a “secret reference”.

![Introducing%201Password%20for%20Visual%20Studio%20Code%20680348ae31ff4b029d70de84a20873e7/sr_light.png](Introducing%201Password%20for%20Visual%20Studio%20Code%20680348ae31ff4b029d70de84a20873e7/sr_light.png)

1Password Secret Reference example consisting of vault, item, and field

Now, instead of using a real value in your configs, environment variable files, or anywhere else in the codebase, just drop in the secret reference in VS Code. When you do, you can rest easy knowing that the real value will never accidentally make its way into your codebase.

The best part? Through our [suite of tools and integrations](https://developer.1password.com/), you can work with references in both local and deployed environments.

To help make sure you’re not accidently leaving secrets in your code, you can move them over to 1Password with just a couple clicks. The extension uses a series of [secret detection](https://developer.1password.com/docs/vscode/#secret-detection) techniques to look for values that might be sensitive. With these matches, it makes inline suggestions to store them in 1Password, automatically replacing them with secret references.

[Secret_Detection.mp4](https://blog.1password.com/posts/2022/1password-visual-studio-code/Secret_Detection.mp4)

Secret reference integration doesn’t stop there. You can hover a reference to inspect the item and field details, click it to open the item in the desktop app, and even [preview the real values](https://developer.1password.com/docs/vscode/#inspect-and-preview-secret-references) of an entire file full of references.

Beyond secret detection suggestions, 1Password for VS Code makes it easy to [retrieve items](https://developer.1password.com/docs/vscode/#get-values-from-1password) for use in your code, as well as [store any bits of code](https://developer.1password.com/docs/vscode/#save-in-1password) you’d like in 1Password. If you’ve got multiple values you want stored in the same item – perhaps a username, password, and email – it supports that as well. Just select each value and run the “Save in 1Password” command.

[Save_in_1Password.mp4](https://blog.1password.com/posts/2022/1password-visual-studio-code/Save_in_1Password.mp4)

## Built using tools available to everyone

I’ll let you in on a little secret: we didn’t plan to build this extension. It wasn’t requested by our developer community, and wasn’t part of any roadmap. Instead this extension began as a side project for myself. I wanted to scratch my own itch and integrate 1Password more closely into my development workflow, and to generally [learn more about developing with 1Password](https://developer.1password.com/).

So you can imagine my excitement when, after a quick demo at an internal call, I was invited to polish it up and get it slated for release.

To my delight, after demoing the extension and then going on vacation, [Dave posted a video](https://www.youtube.com/watch?v=hghKTE_pUaQ) of the presentation from his [CLI launch blog post](https://blog.1password.com/1password-cli-2_0/) and it was met with some pretty wild enthusiasm from the developer community. There was even some love for it at our [1Password 8 for Mac Reddit AMA](https://www.reddit.com/r/1Password/comments/ui9exd/were_the_team_behind_1password_8_for_mac_ask_us/i7fyfp9/):

![Introducing%201Password%20for%20Visual%20Studio%20Code%20680348ae31ff4b029d70de84a20873e7/reddit_ama.png](Introducing%201Password%20for%20Visual%20Studio%20Code%20680348ae31ff4b029d70de84a20873e7/reddit_ama.png)

Reddit comment from user arnebr asking if the VS Code plugin will be live soon

Although not a goal from the outset, an interesting aspect of this project is that it’s built using only tools available to the public – there’s nothing internal or proprietary powering the features of the extension. We’ve even [open-sourced the whole project on our GitHub](https://github.com/1Password/op-vscode), so if you want to help iterate on it or report an issue, that’s a great place to start.

VS Code extensions run in a Node environment, and we wanted to interact with the new CLI. So we built and open-sourced an entirely new package for doing exactly this: [op-js](https://github.com/1Password/op-js). It wraps the CLI with a simple-to-use JavaScript interface and ships with TypeScript declarations, making 60+ commands, including those that support biometrics unlock, available to your Node-based application.

Ultimately my hope is that this extension demonstrates some of the neat ways you can extend the power of 1Password by building your own integrations, whether it be for yourself or others. And you should [have fun doing it](https://blog.1password.com/developers-deserve-great-ux/). We’re in early days here, with plenty more developer offerings coming down the line.

I’d love to hear what you think, and we’ll be iterating on the extension as feedback rolls in. Learn more about [1Password for VS Code](https://developer.1password.com/docs/vscode) and our other developer tools by checking out our [developer portal](https://developer.1password.com/). While you’re there, consider joining our [Developer Slack workspace](https://join.slack.com/t/1password-devs/shared_invite/zt-15k6lhima-GRb5Ga~fo7mjS9xPzDaF2A), where you’ll find myself and others on the Developer Products team who are eager to hear how you’re incorporating 1Password into your development workflow. And if you’re building something cool, be sure to tag it [#BuildWith1Password](https://twitter.com/hashtag/buildwith1password?f=live)!

Finally, we owe a tremendous debt of gratitude to [Mike Selander](https://www.linkedin.com/in/mikeselander/), [Chris Dunn-Birch](https://www.linkedin.com/in/chrisdunnbirch/), [Floris van der Grinten](https://www.linkedin.com/in/florisvdg/), the incredibly helpful folks over in the [VS Code Extension community](https://github.com/1Password/op-vscode/blob/main/CONTRIBUTING.md#acknowledgments), and so many more for providing endless help and guidance while working on this project. Thank you!