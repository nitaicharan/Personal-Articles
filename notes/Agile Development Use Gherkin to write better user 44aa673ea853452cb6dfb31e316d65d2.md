# Agile Development: Use Gherkin to write better user stories

Created: May 13, 2022 11:05 PM
Finished: No
Source: https://lazaroibanez.com/agile-development-use-gherkin-to-write-better-user-stories-62bbff72145a

![Agile%20Development%20Use%20Gherkin%20to%20write%20better%20user%2044aa673ea853452cb6dfb31e316d65d2/1a6OSBggNV0KAsNt5WHdBgg.jpeg](Agile%20Development%20Use%20Gherkin%20to%20write%20better%20user%2044aa673ea853452cb6dfb31e316d65d2/1a6OSBggNV0KAsNt5WHdBgg.jpeg)

Photo by [Christian Lue](https://unsplash.com/@christianlue?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/user-stories?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

A user story is an informal, general explanation of a software feature written from the perspective of the end-user. The definition is given by [Martin Fowler](http://martinfowler.com/), pioneer of agile methodologies is:

> User stories are a chunk of functionality that is of value to the customer.
> 

If you want to dig deeper about the user stories concept, the following article might be of interest:

When it is needed to improve the communication between Business and Tech, the use of Gherkin can help you write better user stories, but…

## What is Gherkin?

> Gherkin is a specific English-like language, that you can use for writing requirements and acceptance criteria.
> 

Gherkin has five main statements:

1. `Scenario`— Overall behavior.
2. `Given` — Context of the scenario.
3. `When` — The action the user takes.
4. `Then` — Testable expected result caused by the action in `When`
5. `And` — Use to concatenate other contexts, expected outcomes, and actions as needed.

Together these statements describe all of the actions that a user must take to perform a task and the result of those actions. Gherkin can be used and understood by non-technical roles but provides the structure to be used by developers as a basis for automated testing and living documentation.

After using this technique, user stories are now getting more focused, short and understandable, on the product and technical sides. This has another benefit, the conversations about stories during refinement meetings are shorter and better framed.

## How does it work?

Start describing the feature in the usual format of a user story:

> AS A <persona>, I WANT <feature> , FOR <Business Value>
> 

Then you can write the business scenarios (Overall behavior) you want to obtain with the user story.

A Gherkin example might look like this:

```
FEATURE:Payments with Mastercard

AS AMarstercard Cardholder,
I WANTto use my Martercard Card,
FORpaying my purchases.Scenario:The Mastercard Cardholder use the Mastercard for paying
GivenI am a Mastercard Cardholder
WhenI pay with my Mastercard
ThenMy Mastercard card is accepted
```

You can even have two scenarios for the same feature:

```
FEATURE:Bank account calculationAs Abank customer,
I WANTthe balance of my account correctly calculated To be certain of my financial sistuationScenario 1:Bank account in-flow calculation
Giventhe starting balance has 4000 Euro
When3000 euros are added to the account
Thenthe final balance is 7000 euroScenario 2:Bank account out-flow calculation
Giventhe starting balance has 4000 Euro
When3000 euros are transferred to another account
Thenthe final balance is 1000 euroThe scenario outline includes the two previous scenarios into a single one.Scenario Outline:Bank account cash flow calculations
Giventhe starting balance has <start> euros
When<amount> is trasferred
ThenI should have <left> euros
| start | trasferred | left |
| 4000  | +3000      | 7000 |
| 4000  | -3000      | 1000 |
```

Generalizing Gherkin, it looks like this:

```
Scenario: Some determinable business situationGiven some context (precondition)
  And some other precondition
When some action by the actor
  And some other action
  And yet another action
Then some testable result is achieved
  And something else we can check happens too
```

The Gerkin approach helps you to better communicate with the IT and business side about the functionalities you are developing. The tech professionals will acquire better domain-related competencies and the business will have a better understanding of what you want to achieve.

Via: