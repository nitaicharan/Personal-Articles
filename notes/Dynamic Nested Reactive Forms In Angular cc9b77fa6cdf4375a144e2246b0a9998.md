# Dynamic Nested Reactive Forms In Angular

Created: July 29, 2022 2:58 PM
Finished: No
Source: https://joshblf.medium.com/dynamic-nested-reactive-forms-in-angular-654c1d4a769a
Tags: #angular, #react

![Dynamic%20Nested%20Reactive%20Forms%20In%20Angular%20cc9b77fa6cdf4375a144e2246b0a9998/1_hdKWLF7iq8s2qDhRB8vSQ.jpeg](Dynamic%20Nested%20Reactive%20Forms%20In%20Angular%20cc9b77fa6cdf4375a144e2246b0a9998/1_hdKWLF7iq8s2qDhRB8vSQ.jpeg)

When you think about it, most apps are just forms. Lots and lots of forms. Sometimes those forms need to contain a lot of data. Some even need to be… *gulp*… dynamic. Meaning that the structure of the form can change depending on the user. The server will never know exactly what it’s getting. When this is the case you might need to tap into the power of nested forms. That’s exactly what I’m going to cover here.

Let’s go over our goals.

By the end of this article we want to have a “parent” form that will contain an indeterminate amount of “child” forms. We will build an NBA team management app to demonstrate how this works. In order to do that we will cover the following concepts:

- Share a single parent form for submitting
- Handling CRUD operations from a single point
- Dynamically insert and remove child forms in/from a parent form
- Compose validation statuses

Okay, let’s do this!

First things first. You need to know why this pattern is useful so that you know when it’s appropriate to use. In order to really understand the inner-workings of this pattern you need to fully understand how Angular’s Reactive Forms work. The best place to find out is [here](https://angular.io/guide/reactive-forms) but, we will briefly cover some basics.

Angular forms are made up of a few different classes that you can create with the [Form-builder](https://angular.io/api/forms/FormBuilder) class. This is the tool you can use to create the different pieces of the form. These pieces are as follows:

## **Form**

- Has built-in CRUD methods

## Form Group

- The main wrapper of the form
- Can contain any type of form element

## Form Array

- Can contain any type of form elements
- Has array-like methods to update children
- This is where the magic happens!

## Form Control

- The main building block of forms. Allows for user interaction

Now that we know what’s available, let’s see what our form anatomy will look like.

![Dynamic%20Nested%20Reactive%20Forms%20In%20Angular%20cc9b77fa6cdf4375a144e2246b0a9998/1aLq_-gh4EImND4rV5CM8tA.png](Dynamic%20Nested%20Reactive%20Forms%20In%20Angular%20cc9b77fa6cdf4375a144e2246b0a9998/1aLq_-gh4EImND4rV5CM8tA.png)

Form Layout Illustration

Here we can see there will be a parent form (FormGroup) that will hold everything. An “Add” button which will trigger the CRUD server to add another child form (FormGroup) into the FormArray that holds the individual child FormGroups.

Following me so far?

Okay, now let’s look at the code. Here’s our file structure that we’ll end up with:

![Dynamic%20Nested%20Reactive%20Forms%20In%20Angular%20cc9b77fa6cdf4375a144e2246b0a9998/1i6uOdT_YHwcp9dyPBqTSaQ.png](Dynamic%20Nested%20Reactive%20Forms%20In%20Angular%20cc9b77fa6cdf4375a144e2246b0a9998/1i6uOdT_YHwcp9dyPBqTSaQ.png)

Our app component will only serve to hold the `<router-outlet>` where our Team Component will be rendered. The Team Component will hold the parent form.

```
teamForm: FormGroup;
teamFormSub: Subscription;
players: FormArray;ngOnInit() {
 this.teamFormSub = this.teamFormService.teamForm$
    .subscribe(team => {
      this.teamForm = team;
      this.players = this.teamForm.get('players') as FormArray;
})
}
```

The `teamForm` property will allow us to reach goal number one, share a single parent form for submitting. The actual form itself is of type FormGroup, which is no surprise. The form itself isn’t actually created in the component, but in a service. Then an *observable* version of the form is used via the `teamFormSub`. This allows you to keep a more immutable version of the form and it’s changes.

## Handling CRUD operations from a single point

You can also see that we’re injecting the `teamFormService` which will help us achieve goal number 2, handling all CRUD from a single point. This service is where all the magic happens, so let’s take a closer look.

```
import { Injectable } from '@angular/core'
import { Observable, BehaviorSubject } from 'rxjs'
import { FormGroup, FormBuilder, FormArray, Validators } from '@angular/forms'
import { TeamForm, Team } from './_models'import { PlayerForm, Player } from './player'@Injectable()
export class TeamFormService {
  private teamForm: BehaviorSubject<FormGroup | undefined> =
    new BehaviorSubject(this.fb.group(
      new TeamForm(new Team('Cavaliers'))
    ))  addPlayer() {
    const currentTeam = this.teamForm.getValue()
    const currentPlayers = currentTeam.get('players') as FormArray    currentPlayers.push(
      this.fb.group(
        new PlayerForm(new Player('', '', 0, ''))
      )
    )    this.teamForm.next(currentTeam)
  }  deletePlayer(i: number) {
    const currentTeam = this.teamForm.getValue()
    const currentPlayers = currentTeam.get('players') as FormArray    currentPlayers.removeAt(i)
    this.teamForm.next(currentTeam)
  }
```

Let’s clarify what the purpose of this service is.

- Create a shareable instance of the Team form as a BehaviorSubject which is then exposed as an observable. This is considered a best practice because it ensures that no other sources can call `.next()` on the Team form and therefore it is impossible to update outside of this service.
- Add and remove players (child forms) from the Team form.
- Expose a user friendly API that can be used in components.

If we look at when the `teamForm` value is set, we’ll see something interesting. I’m using the Form Builder class to create a new “group” with `this.fb.group()` which expects and argument to set the controls of this new group. This is usually done “inline” in the component. However, when you have a large form, or want to abstract it, you can also use a service as I have here. I’ve abstracted this out into the team-form model. Let’s see exactly what happens in there.

```
export class TeamForm {
  name = new FormControl()
  players = new FormArray([])

  constructor(team: Team) {
    if (team.name) {
      this.name.setValue(team.name)
    }    if (team.players) {
      this.players.setValue([team.players])
    }
  }
}
```

When you call `new TeamForm()` it will create a new form for you. I like this because it keeps things more modular and flexible. You can also set defaults as we have here with the players form array. Or pass in your own values like `new TeamForm({name: 'Lakers'})` .

## Dynamically insert and remove child forms in/from a parent form

Once we have our team form created we need the CRUD methods, add and delete. Which are there to well… add and delete. These functions are similar but there are a few interesting things they’re doing that are specific to Angular. Let’s take a look.

```
addPlayer() {
  const currentTeam = this.teamForm.getValue()
  const currentPlayers = currentTeam.get('players') as FormArraycurrentPlayers.push(
    this.fb.group(
      new PlayerForm(new Player('', '', 0, ''))
    )
  )this.teamForm.next(currentTeam)
}
```

This method starts by getting the last value of the `teamForm` observable. This makes sure it’s up to date. Then it is digging into the form to `get` the control named “players”. Now as of right now we don’t know what type of control this is. It could be a form control, form array, or a form group. So, we’re going to tell TypeScript that we expect it to be a FormArray. That’s because form arrays have array-like methods such as “push”. Which is exactly what we’re going to use! So, we’re going to push a new form group onto the array of players. That new form group will be created with the `new` keyword as an instance of `PlayerForm` which will expect an argument of `Player` . Then once this has been updated, we update the BehaviorSubject which will cause a new Observable to be emitted. Any subscribers will then be updated.

These service methods can now be used in the components like so:

```
addPlayer() {
  this.teamFormService.addPlayer()
}deletePlayer(index: number){
  this.teamFormService.deletePlayer(index)
}
```

Ahhh, so simple. The component doesn’t *care* how the player is added or deleted. It just taps into the service API and goes on about it’s business. Keeping the components as thin as possible is ideal. This also promotes re-usability.

Let’s take a look at what the `PlayerForm` class is doing.

```
import { FormControl, Validators } from '@angular/forms'
import { Player } from './player.model'export class PlayerForm {
  firstName = new FormControl()
  lastName = new FormControl()
  position = new FormControl()
  number = new FormControl()constructor(
  player: Player
) {
    this.firstName.setValue(player.firstName)
    this.firstName.setValidators([Validators.required])    this.lastName.setValue(player.lastName)
    this.position.setValue(player.position)    this.number.setValue(player.number)
    this.number.setValidators([Validators.required])
  }
}
```

We’re setting all the form controls at the top with empty defaults. Then in the constructor we’re expecting a `Player` object to be passed in which we’ll use to set all the values. Inside the constructor we see more of the Angular form methods at work. The `setValue` method assigns the value of the control, and the `setValidators` method sets a validator. We haven’t discussed Validators yet but, they’re a built-in tool that Angular provides to do some basic form validation. They’re super useful and not very complicated once you get used to them. Find more about it [here](https://angular.io/guide/form-validation). All they’re doing in this case is saying that the player’s first name and number are required in order for this form to be valid. Which leads us to our next goal.

## Composing Validation Statuses

Now we have the ability to add players and delete players. The user can update the player information and save the form right? Maybe. It depends on one thing. Validation! As I mentioned above Angular comes standard with some pretty cool validation. Lets go over how this works when you’re using dynamically nested forms.

```
Parent Form : status
  > child form : status
  > child form : status
```

This is the basic structure of how our forms will look from a validation point of view. All controls have their own validation statuses, and they are used to compose the parent forms validation status. Which could then be used to compose the parent’s parent form validation and so on. Let’s take a look at a few validation scenarios.

```
Parent Form : status (Valid)
  > child form : status (Valid)
  > child form : status (Valid)
```

This example shows the “happy path”. Where all the child forms have met their individual validation requirements and are deemed “valid” by Angular. This allows the parent form to be valid as well, assuming it has met all it’s own validation requirements outside of the child forms.

```
Parent Form : status (Invalid)
  > child form : status (Valid)
  > child form : status (Invalid)
```

Uh oh. It looks like something didn’t go well in this example. The parent form’s status is “invalid” and a child form’s status is also “invalid”. This illustrates that if a child form is invalid the parent form will also be invalid. Even if all the other requirements for the parent form have been met, it will still be also be based on the validation status of *all of it’s children*. Long story short, if a child form is invalid the parent form will also be invalid. What you choose to do with that information from a UI/UX point of view is up to you. Angular also comes with some cool status classes that you can tap into with your CSS. You can also choose to let the user submit the invalid form or block it. It depends entirely on the situation.

## Share a single parent form for submitting

Last but not least, what happens when you finally submit this giant (or small) form? Let’s take a look.

Final submitted form result

Sweet! Here we see what is sent to the imaginary server when this form is submitted. We can see the Team object with the `name` property set to the “Cavaliers” and we can see the `players` property set with the array of players. In this case we have only one player listed, Kevin Love. However, because we set this up to change according to the user’s wishes this value could change every time. Ultimately, this proves that the child forms you dynamically nest inside the parent team form **will** be submitted along with the parent. Nice!

Let’s Recap.

In Angular you get a lot of form functionality out of the box. This includes the ability to compose multiple types of form controls, and several layers of form children/parents. Each with their own unique validation status and requirements. Pretty cool stuff.

I’m hoping by now you can see how using this pattern could be very useful when you have a large form with nested data sets that need to be updated by the user before saving. Forms are not going away anytime soon. As I said earlier most apps are just a series of forms to collect and manipulate data. We will always need them. Angular helps make that process a little easier when building a SPA.

If you have questions or comments about this let me know in the comments below. You can view the full code here: