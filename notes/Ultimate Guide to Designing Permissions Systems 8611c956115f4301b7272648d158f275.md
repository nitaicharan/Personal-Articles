# Ultimate Guide to Designing Permissions Systems

Created: May 4, 2022 11:37 PM
Finished: No
Source: https://www.missionkontrol.io/ultimate-guide-to-designing-user-permissions-in-saas-apps/
Subjects: design pattern
Tags: #design-pattern

The first application I was involved in building had one of the most complicated permissions systems ever conceived. Later in the product’s life it led to a significant and painful rebuild.

My first experience taught me that out of all the areas in a SaaS app, simplicity and ease of use in permissions is key.

![Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/autodraw-27_10_2019-1-1.png](Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/autodraw-27_10_2019-1-1.png)

This is often counter to what enterprises are after, which is extremely granular control. In reality, you need to find a middle ground.

## Technology

I don’t want to go into detail about all of the permissions systems currently available. There are simply too many languages and libraries to go into any detail here.

What I will say is that if you can use an off the shelf library that’s well supported then that will usually be preferable to starting from scratch.

We use a rails stack so the most popular libraries are [can can can](https://github.com/CanCanCommunity/cancancan) and [pundit](https://github.com/varvet/pundit).

If your team is suggesting that you need to start building from scratch it’s probably because you’ve gone wrong at the permissions design stage. See if you can simplify.

## Problem space

Why are we talking about this awful subject!?

In researching this post there weren’t many comprehensive articles to reference. That’s no surprise because this is one of the most mind-numbingly dull subjects out there.

Get it right however and it could be almost invisible to your users.

### **Legislation**

The EU has had the data protection act since 1998 and EU GDPR regulations have come into play since 25th May 2019.

In the US the FTC and individual states are looking more and more to companies to protect their data and are doling out bigger and bigger fines for data breaches. Most of which originate from a bad actor inside a company.

While the details vary, most legislation covers the following points:

- users should not be able to access more data than absolutely necessary;
- there should be adequate controls to protect sensitive information like social security numbers or dates of birth;
- data should be kept secure;

### **Training errors**

An unsurprising fact is that IT teams spend an inordinate amount of time restoring files unwitting users or more likely ‘super users’ accidentally deleted.

Anecdotally in the first SaaS app I created the super users would regularly delete groups of users or content. Sometime later they would contact support asking where they had gone…

We need to stop those same people causing accidental chaos with our apps.

### **Corporate secrets**

Generally speaking, companies like to protect their IP. This means that they don’t want:

- Salespeople stealing client and lead lists (Happens all the time);
- Personal details of customers leaking online or being sold;
- People internally to go where they have no business ;

### **Conclusion: We want to stop bad actors and accidents**

For the most part, we’re not trying to stop innocent employees going about their daily jobs. We’re trying to prevent them from breaking things by accident.

We’re also looking to stop bad actors doing or accessing bad things they shouldn’t.

## Role based access systems

The most common type of permissions system used in SaaS apps are Role-based permissions, otherwise known as RBAC.

### Before we start

For this post, we’re going to use a fictitious event planning app that has `users`, `events` and `venues`.

There will also be three users `John`, `Helen` and `Sarah`. The company running the app has three teams `Admins`, `Sales` and `Marketing`.

### User Roles & Permissions

There are three core elements to a classical Role Based Access Control (RBAC) permissions system:

There are three core elements to a classical Role Based Access Control (RBAC) permissions system:

- Users – There will usually be a table of users;
- Roles – Each user will be a member of one or more roles;
- Abilities – Each role will have a number of abilities;

![Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/autodraw-27_10_2019-2.png](Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/autodraw-27_10_2019-2.png)

This is the classical approach that most apps follow. I’ve outlined some alternative approaches at the end of this post that might also be worth considering.

## Abilities

From my personal experience there are two sub-types of ability:

- **Things I can do**: Manage users, permissions, export data etc;
- **Things I can access**: Access related to different types of content;

For consumer apps like social networks, you might also have sharing preferences but I’d categorise these as a subset of things I can see.

### Things I can do abilities

These generally cover settings and configuration but can include a whole host over other abilities as your app grows.

Most likely you may use them to limit functionality for the different levels of your plans.

They can be grouped roughly into one of the following three options:

![Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/Screen-Shot-2019-10-27-at-19.47.09.png](Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/Screen-Shot-2019-10-27-at-19.47.09.png)

In the long run you might need to add more granularity based on customer requests but from experience it’s best to start with the simplest option.

### **Things I can access abilities**

These generally relate to accessing content. For our example creating, reading, updating and deleting records from `users`, `events` and `venues`.

### Option 1 – Full granularity

This is an ideal level of control but if you have too many things to access then you can run into issues.

At first, you might have something like this:

However, eventually, you might want additional granularity and be able to control access within each of the tables.

For `users` this could look like this:

This more granular approach can cause issues as you can end up with black holes where some content isn’t accessible by anyone.

### Option 2 – On/Off

We really liked this option and it did resonate quite well with our target audience who are mainly smaller startups:

They don’t worry too much about their users having too much control as they tend to be small trusted well-trained teams.

### Option 3 – Hybrid

Ultimately we knew that the On/Off approach wouldn’t work exclusively. Based on inspiration from a great [post](https://community.canvaslms.com/thread/20515-granular-permissions-designs#comments) we found from Renne Carney the Community Manager at canvaslms, we included both options:

This hides the detail of each of the granular controls unless you really want/need to adjust them.

It also means that if you have a lot of roles and permissions you can easily see which role has access to the different permissions a glance.

## User Roles

So for our app we’re going to define some roles: `admin`, `sales`, `marketing`.

From earlier we have our three users `John`, `Helen` and `Sarah`.

There are two ways to go with roles, one role per user:

This is the simplest approach and generally the best if possible. Having a single role per user.

Multiple roles per user:

One of the challenges of multiple roles per user is that it can lead to complications such as overlapping permissions and which permissions should take priority.

Generally the ‘additive’ approach is taken. For example for `Helen`, this would mean that if Sales can only read `events` but marketing can read and update, Helen could do both.

## Role based access control example

Here is a case study example of the role based access control system that we have built into MissionKontrol.

### Users & Roles

We wanted to reduce the number of views so we combined users and roles (called Teams) into a single view.

This was inspired by Joey Cooper’s user table design on Dribble.

![Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/Settings-Databases-DB-Permissions-Copy-2-2.png](Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/Settings-Databases-DB-Permissions-Copy-2-2.png)

Users home

Within users we have the ability to set the role of the user as well as whether they’re active or not.

This was the simplest we could make the user settings.

![Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/Settings-Databases-DB-Permissions-Copy-3-2.png](Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/Settings-Databases-DB-Permissions-Copy-3-2.png)

Edit user

For the ‘what you can do’ permissions we grouped them into to two simple settings `admin` and `editor`. These can be set against each role.

![Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/Settings-Databases-DB-Permissions-Copy-30.png](Ultimate%20Guide%20to%20Designing%20Permissions%20Systems%208611c956115f4301b7272648d158f275/Settings-Databases-DB-Permissions-Copy-30.png)

### Permissions

For our what you can see permissions we’ve opted for a UI that’s very close to the designs from Canvaslms that I referenced earlier.

We really liked the ability to quickly enable/disable access to content but also wanted to be able to have granular controls.

### What’s next

It took a bit of time to work these out but we expect these permissions will evolve over time as the needs of our users move forward. However, for now, they should provide enough control for now.

## Alternative permissions concepts

One of the problems with roles is that not everyone fits into a neat role. While there are groups that can be generalised, usually sales teams.

You can end up with almost as many roles as users!

A workaround to this is to have a two-lane solution.

- The majority of users fit into a standardised role;
- More difficult users, usually heads of department and other middle management folks can live in a special subscription world;

### **Subscriptions** / One-to-One / ACL

Known as Access Control List (ACL), you get granular control but it can get messy at scale.

If you imagine the things to be accessed as subscriptions that users could request access to.

They could then be granted view/edit/manage permissions against that content on an ad-hoc basis.

This is the same approach that Google Drive and Microsoft Sharepoint takes where you can share docs with individual users.

### **Code word or sensitivity levels**

If the government can control systems based on codeword access (basically a subscription) or a sensitivity level i.e. `Confidential`, `Secret`, `Top secret` which is a type of Policy Based Control (PBAC), so could you.

The downside is that some users might feel too scared to access data they need to, especially if you start firing people for going too far. As with everything a balance is needed.

### **Dynamic / Intelligent permissions**

An option that we have discussed with some people is the idea of a more dynamic permissions system that is less rigid.

**How could it work:**

- Users can access the majority of content and functionality;
- When a user accesses a category/table/folder they’ve not accessed before they’re given a warning that they can access the data but that they are being logged;
- Limits exist for the number of records a user can view in a day and some request only controls exist for accessing large batches of data;
- Exception reporting highlights to managers and administrators users that are behaving in an unusual manner allowing them to check-in to make sure the user isn’t stuck or behaving badly;
- Using some basic machine learning you could identify unusual patterns to drive the rules.

This model is followed by some government agencies and does allow for responsible access to data.