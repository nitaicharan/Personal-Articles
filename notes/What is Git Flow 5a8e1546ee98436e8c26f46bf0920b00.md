# What is Git Flow

Created: July 12, 2022 8:50 AM
Finished: No
Source: https://www.gitkraken.com/learn/git/git-flow
Tags: #git

![og-learn-git-1024x538.png](What%20is%20Git%20Flow%205a8e1546ee98436e8c26f46bf0920b00/og-learn-git-1024x538.png)

[Git flow](https://nvie.com/posts/a-successful-git-branching-model/) is a popular [Git branching strategy](https://www.gitkraken.com/learn/git/best-practices/git-branch-strategy) aimed at simplifying release management, and was introduced by software developer Vincent Driessen in 2010. Fundamentally, Git flow involves isolating your work into different types of [Git branches](https://www.gitkraken.com/learn/git/branch). In this article, we’ll cover the different branches in the Git flow workflow, [how to use Git flow in GitKraken Client](https://www.gitkraken.com/learn/git/git-flow#how-to-use-git-flow), and briefly discuss two other Git branching strategies, GitHub flow and GitLab flow.

## **The Git Flow Workflow**

In the Git flow workflow, there are five different branch types:

## **Other Git Branching Strategies**

[GitHub Flow](https://www.gitkraken.com/learn/git/git-flow#github-flow)

## **The Git Flow Diagram**

*Custom image inspired by Vincent Driessen in [“A Successful Git Branching Model”](https://nvie.com/posts/a-successful-git-branching-model/).*

## **Git Flow: Main Branch**

*Please note: the main branch is commonly referred to as “master”; we have made an intentional decision to avoid that outdated term and have chosen to use “main” instead.*

The purpose of the main branch in the Git flow workflow is to contain production-ready code that can be released.

In Git flow, the main branch is created at the start of a project and is maintained throughout the development process. The branch can be tagged at various commits in order to signify different versions or releases of the code, and other branches will be merged into the main branch after they have been sufficiently vetted and tested.

## **Git Flow: Develop Branch**

The develop branch is created at the start of a project and is maintained throughout the development process, and contains pre-production code with newly developed features that are in the process of being tested.

Newly-created features should be based off the develop branch, and then merged back in when ready for testing.

## **Git Flow: Supporting Branches**

When developing with Git flow, there are three types of supporting branches with different intended purposes: feature, release, and hotfix.

### **Git Flow: Feature Branch**

The feature branch is the most common type of branch in the Git flow workflow. It is used when adding new features to your code.

When working on a new feature, you will start a feature branch off the develop branch, and then merge your changes back into the develop branch when the feature is completed and properly reviewed.

### **Git Flow: Release Branch**

The release branch should be used when preparing new production releases. Typically, the work being performed on release branches concerns finishing touches and minor bugs specific to releasing new code, with code that should be addressed separately from the main develop branch.

### **Git Flow: Hotfix Branch**

In Git flow, the hotfix branch is used to quickly address necessary changes in your main branch.

The base of the hotfix branch should be your main branch and should be merged back into both the main and develop branches. Merging the changes from your hotfix branch back into the develop branch is critical to ensure the fix persists the next time the main branch is released.

## **How to use Git Flow in GitKraken Client**

Now that we’ve covered what Git flow is, let’s take a look at how to use Git flow in GitKraken Client.

To start using a Git flow workflow in GitKraken Client, perform the following steps:

1. Navigate to Preferences in the top toolbars
2. In the left panel select Gitflow and set your preferred branch naming conventions
3. and Select then hit the green Initialize Gitflow button

Now when you exit Preferences, you see a Gitflow section at the top of the left panel. From here,

GitKraken Client will help you start and finish feature, release, and hotfix branches.

For example, most devs will probably hit “Feature” and then name and create their feature branch:

Then when your feature branch has been approved, you may close the feature branch using the Gitflow panel. This will automatically merge the feature branch into develop, or alternatively, you can choose to rebase the feature branch on develop instead.

## **Git Flow FAQs**

### **Q: What is a Git flow?**

A: Git flow is a Git branching strategy created to make releasing new versions of software easier.

### **Q: How to install Git flow?**

A: Git flow is a Git branching strategy, and therefore isn’t a tool or software that can be downloaded. Developers can *use* Git flow to organize their Git branches and development process to ship releases more efficiently.

## **Other Git Workflows**

While the popularity of Git flow has soared since its inception in 2010, even Driessen himself admits that it might not be the optimal Git workflow for every development team or environment, as is shown in his [“Note of reflection”](https://nvie.com/posts/a-successful-git-branching-model/) from March 2020.

*“Web apps are typically continuously delivered, not rolled back, and you don’t have to support multiple versions of the software running in the wild. This is not the case of software that I had in mind…10 years ago.”*

There are two Git workflows that are more simple than Git flow and can allow for continuous delivery.

***GitTip: Learn more about [Git branch strategies](https://www.gitkraken.com/learn/git/best-practices/git-branch-strategy), including in-depth details and workflow diagrams for GitHub flow and GitLab flow.***

## **GitHub Flow**

More simple than Git flow, [GitHub flow](https://www.gitkraken.com/learn/git/best-practices/git-branch-strategy#github-flow-branch-strategy) is great for smaller teams and web applications or products that don’t require supporting multiple versions. Because of its simplicity, the GitHub flow workflow allows for continuous delivery and continuous integration.

Of course, there are also related risks to consider. The lack of dedicated development branches, for example, makes this workflow more susceptible to bugs in production.

## **GitLab Flow**

Also more simple than Git flow, [GitLab flow](https://www.gitkraken.com/learn/git/best-practices/git-branch-strategy#gitlab-flow-branch-strategy) is more organized and structured when compared to the GitHub flow workflow.

GitLab flow introduces environment branches, such as production and pre-production, or release branches, depending on your situation.

With slight modifications, GitLab flow can allow for versioned releases and continuous delivery.

## **Get Started with** **Git Flow in GitKraken Client**

At its core, Git flow helps better organize your work. Combine that with the visual power of a Git client to take your workflow to the next level.

GitKraken Client supports Git flow and allows you to customize branch names and other details to your liking during the configuration process. This can be particularly helpful for standardizing processes across development teams for more effective collaboration. Discover more about using [Git flow with GitKraken Client.](https://support.gitkraken.com/git-workflows-and-extensions/git-flow/)