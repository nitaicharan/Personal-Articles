# 365Git | Writing Git commit messages

Created: October 2, 2022 12:16 AM
Finished: No
Source: https://365git.tumblr.com/post/3308646748/writing-git-commit-messages
Tags: #gitops

Unsurprisingly, there is a convention for writing Git commit messages. This comes from the [submitting patches guidelines](https://href.li/?http://git.kernel.org/?p=git/git.git;a=blob;f=Documentation/SubmittingPatches;h=ece3c77482b3ff006b973f1ed90b708e26556862;hb=HEAD) for Git itself. In summary:

- The first line of the commit message should be a short description (50 characters is the soft limit), and should skip the full stop
- The body should provide a meaningful commit message, which:
    - uses the imperative, present tense: “change” not “changed” or “changes”.
    - includes motivation for the change, and contrasts its implementation with previous behaviour.

Most tools only show the first line of the commit message, which is usually enough to know what it is about. It’s like the subject line in an email: you don’t need to see the whole message to know whether you are interested in it. The 50 character soft limit is to play nicely with command line displays. If you use vim as a commit message editor it knows about this soft limit, which is why the colour changes after the 50th character. Personally, I go past this if I have to (which is most of the time because of the long names that are used in [Cocoa](https://href.li/?http://en.wikipedia.org/wiki/Cocoa_(API))).

The use of the imperative, present tense is one that takes a little getting used to. When I started mentioning it, it was met with resistance. Usually along the lines of “The commit message records what I have done”. But, Git is a distributed version control system where there are potentially many places to get changes from. Rather than writing messages that say what you’ve done; consider these messages as the instructions for what applying the commit will do. Rather than having a commit with the title:

```
Renamed the iVars and removed the common prefix.

```

Have one like this:

```
Rename the iVars to remove the common prefix.

```

Which tells someone what applying the commit will do, rather than what you did. Also, if you look at your repository history you will see that the Git generated messages are written in this tense as well - “Merge” not “Merged”, “Rebase” not “Rebased” so writing in the same tense keeps things consistent. It feels strange at first but it does make sense (testimonials available upon application) and eventually becomes natural.

Having said all that - it’s your code, your repository: so set up your own guidelines and stick to them.

If, however, you do decide to go this way then `git rebase -i` with the `reword` option would be a good thing to look into.