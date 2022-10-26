# Writing User Stories With Gherkin

Created: May 13, 2022 11:06 PM
Finished: No
Source: https://medium.com/@nic/writing-user-stories-with-gherkin-dda63461b1d2

![1*XlbGNIwjk1qqNJjR9AMRYw.png](Writing%20User%20Stories%20With%20Gherkin%207890989f9c21453d8c6ba91847a5cde2/1XlbGNIwjk1qqNJjR9AMRYw.png)

Ok, you’ve mastered the concept of *“As a <user>, I want <x> because <y>”.* But when it comes to writing the details inside the story, you’re inconsistent — either wildly detailed and rambling, or a spare one-liner that even confuses you. Your planning meetings include a lot of *“Can you flesh this out a little more?”, “Wait, but what happens here?”, “How is QA supposed to accept this?”*

Today, we’re going to wipe the slate clean and use Gherkin, a straightforward framework for describing what should be built.

# The Origin of Gherkin

Originally intended for developers, Gherkin is a structured approach to writing *behavioral* tests, also called [Behavior Driven Development (BDD)](https://en.wikipedia.org/wiki/Behavior-driven_development). Instead of testing little bits of code, behavioral tests seek to follow true user workflow, such as signing in, or applying for a refund. This means a focus on how users interact with your system.

(Brought to you by the good people at cucumber)

# But I’m a product manager. Why are we talking about Gherkin?

Turns out, what comes out of writing behavioral tests is identical to writing user stories: An overview of the goal the user wants to accomplish, the steps they’ll take, and how the product should respond. Sound familiar?

> Gherkin is the perfect framework for writing user stories because it gives a consistent approach for reviewing all scenarios, defines the definition of Done, and provides crisp acceptance criteria.
> 

As a PM, the benefits of using Gherkin are:

- You’ll catch missing workflows before any work is started.
- It’s a direct correlation to the user workflows that you and Design have developed.
- Developers know when the story is Done because they have clear acceptance criteria. QA and PM too!
- A consistent language across stories helps the team focus on delivering user value, unhindered by your writing style that day.

# What does it look like?

> Scenario: Jeff returns a faulty microwave Given Jeff has bought a microwave for $100 And he has a receipt When he returns the microwave Then Jeff should be refunded $100
> 

A familiar example for product managers:

> Feature: As a user I want to sign in so I can see my marketing campaigns
> 
> 
> **Scenario: User supplies correct user name and password** Given that I am on the sign-in page When I enter my user name and password correctly And click ‘Sign In’ Then I am taken to the dashboard **Scenario: User does NOT supply correct user name and password** Given that I am on the sign-in page When I enter my user name and password incorrectly and click ‘Sign In’ Then I see an error message ‘Sorry, incorrect user name or password.”
> 

# What happened there?

Gherkin follows a very specific syntax:

## ***Scenario** -> **Given** -> **When** -> **Then***

1. *Scenarios*: All the actions a user could take (including bad input)
2. *GIVEN*: Sets the context. What page are we on and what state are we in? Is the user an admin? Signed-in? Has created a campaign?
3. *WHEN*: What actions the user is performing. What event occurred?
4. *THEN*: What should the system do in response? What is the expected outcome?

For the sign-in example above:

A user wants to access their dashboard, but they are signed out.

*As a MailChimp user, I want to access my account so I can start working*

1. *Scenarios*: User supplies proper credentials or incorrect credentials
 2. *GIVEN*: They’re on the sign-in page
 3. *WHEN*: They entered their credentials and then clicked ‘Sign In’
 4. *THEN*: Take them to the dashboard, or stay there with an error

## What did we NOT do?

We didn’t mention any technical details, such as database columns or CSS classes. The focus should be on the user, and their behaviors. Push yourself to keep the technical details out of here, unless you’re writing an API. Even then, there shouldn’t be a reference to HOW the code should be written.

# Jumping in yourself

Writing in Gherkin is really that easy! Just follow these steps:

0) Pull out a blank notepad or full-screen writer. Don’t use your tracking system, you want plenty of space.
 1) Choose a user story.
 2) Write out all the possibilities for the user. *What if the user didn’t have a receipt? What if they are returning the microwave past the return policy?*
 3) Write out the Given/When/Then for each scenario.
 4) Review all your When/Then conditions. If you feel like you have a lot of conditions “THEN the user sees a spinner, AND then sees the dashboard AND sees new notifications”, it’s a sign that those should be separate stories. Remember, a user story is the smallest piece that delivers value.
 5) Focus: Looking at all your scenarios, narrow them down to what is truly necessary Put that last group into separate user stories and store them in the Icebox for another day.
 6) Now, create user stories in your tracking system.

Now, you’re likely going to realize you’re missing some details and you’ll need to talk with your developers and designers before you can finish the stories. That Is A Good Thing! Identifying the missing parts and reducing scope before even building is exactly what Gherkin uncovers for you.

Stick with it! When you’ve mastered the Given/When/Then syntax, team confusion about user stories melts away — you and the team will now enjoy the excitement of what you’re building!

*Still not sure if this will work for you? Paste what you have below, and I’ll help you through it.*