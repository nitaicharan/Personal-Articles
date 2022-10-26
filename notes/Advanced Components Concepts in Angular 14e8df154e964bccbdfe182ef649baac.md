# Advanced Components Concepts in Angular

Created: May 16, 2022 2:37 PM
Finished: No
Source: https://www.webagesolutions.com/blog/advanced-components-concepts-in-angular
Tags: #angular

**Author: Bibhas Bhattacharya**

This tutorial is adapted by Web Age course [Advanced Angular 12 Programming Training](https://www.webagesolutions.com/courses/WA3076-advanced-angular-12-programming).

![Advanced%20Components%20Concepts%20in%20Angular%2014e8df154e964bccbdfe182ef649baac/50-off-2.png](Advanced%20Components%20Concepts%20in%20Angular%2014e8df154e964bccbdfe182ef649baac/50-off-2.png)

Fill in the form and get this
 Advanced Angular 12 Programming Course at 50% off!

## 1.1 Detecting Change to @Input Binding

When a parent component updates a property of a child bound using **@Input** the child may like to get notified of the change. This is necessary when a change in a component’s state requires further processing. Example: When you change the sorting order property of a product list component it needs to sort the products differently.

There are two ways a child can intercept changes to an @Input property:

- Using a custom setter

## 1.2 Example Child and Parent Components

We will use these components to illustrate both approaches. The parent provides a GUI to increment a counter. The child simply displays the counter value.

```tsx
@Component({
  selector: 'app-child',
  template: `<p>{{counter}}</p>`
})
export class ChildComponent {
  @Input() counter = 0
}

@Component({
  selector: 'app-parent',
  template: `<app-child="p_counter"></app-child>
  <button (click)="update()">Update</button>`
})
export class ParentComponent {
  p_counter = 0

  update() { this.p_counter += 1 }
}
```

## 1.3 Using a Custom Setter

```tsx
@Component({
  selector: 'app-child',
  template: `<p>{{counter}}</p>`
})
export class ChildComponent {
  _counter = 0

  @Input()
  set counter(newValue:number) {
    console.log("Intercepted update:", newValue)
    this._counter = newValue
  }

  get counter() {
    return this._counter
  }
}
```

## Notes

In the example above “counter” is now a computed property with a custom getter and setter. It internally uses the _counter member variable to store state.

Code for the parent component doesn’t change. We still bind to the counter property.

## 1.4 Using ngOnChanges

```tsx
import { Component, Input, OnChanges, SimpleChange } from '@angular/core';

@Component({...})
export class ChildComponent implements OnChanges {
  @Input() counter = 0

  ngOnChanges(changes: {: SimpleChange}) {
    let c = changes
    console.log(c.previousValue, c.currentValue, c.firstChange)
  }
}
//Prints
undefined 0 true
0 1 false
1 2 false
```

## Notes

ngOnChanges receives a dictionary where the key is a string with the same name as the property that was modified. The value is a SimpleChange object. That object has these properties:

- 1. firstChange – If the property is being set for the first time.
- 2. previousValue – The value before the change. It is normally undefined when firstChange is true.
- 3. currentValue – The value after the update.

## 1.5 Advanced Inter Component Communication

Data and event binding using @Input and @Outrput work very well for immediate parent-child components. But this becomes cumbersome for deeply nested components or for communication between siblings.

Angular provides a couple of ways to deal with these situations:

- A component can directly access an instance of a child component.
- A component can directly access an instance of an ancestor.
- For truly complex situations use the Subject API discussed later in this chapter.

## Notes

For example, these situations become difficult to implement using @Input and @Output:

◦ A parent wants to set a property of a grand child or a child further down the descendants hierarchy. Every component in the chain has to set up @Input binding for the property.

◦ A component needs to raise an event for a grand parent or a component further higher in the hierarchy. Every component in the chain needs to setup an @Output binding.

◦ A component needs to communicate with a sibling.

◦ A component needs to invoke a method of another component.

## 1.6 Direct Access to Child

- How we can access a child or a collection of a children components?
    
    You can access an immediate child directive or component in a few ways:
    
    - Using a template local variable for the child component instance.
    - Using the **`@ViewChild`** decorator. This is strongly typed and recommended over template local variables.
    - Use the **`@ViewChildren`** decorator to get a collection of children and monitor changes in that collection.

Note: these give you access to immediate children and not any arbitrary descendants down the hierarchy.

## 1.7 Using a Template Local Variable

```tsx
@Component({
  selector: 'app-parent',
  template: `
  <app-child #ch></app-child>
  <button (click)="ch.counter = ch.counter + 1" >
    Update</button>
  `
})
export class ParentComponent {
}
```

## Notes

In the example above we declare a local variable called “ch” for an instance of `ChildComponent`. From the button click handler, we access the counter variable of the class. You could also call a method of `ChildComponent` from the template this way.

## 1.8 Using the `@ViewChild` Decorator

```tsx
import { Component, ViewChild } from '@angular/core';
import { ChildComponent } from '../child/child.component';

@Component({
  selector: 'app-parent',
  template: `<app-child></app-child>
  <button (click)="update()">Update</button>`
})
export class ParentComponent {
	@ViewChild(ChildComponent, {static: false})child:ChildComponent

  update() {this.child.counter += 1}
}
```

- Access to the ‘child’ property depends the type of the `ChildComponent` as well as the timing.
- With `@ViewChild` decorator, when (lifecycle hook) a child property is available or not?
    - In the case above the variable is available after the view initializes (from `ngAfterViewInit()`) but is not yet available when `ngOnInit()` is called.
- If the DOM changes and children are added or removed the variable is kept up to date.

## Notes

The `@ViewChild` decorator locates the child component by its class. This works well when there is only one instance of the child component inside the parent.

- If there are multiple instance of a child and which `@ViewChild` responds?
    
    If there are multiple instances then `@ViewChild` returns the first one.
    
- How to get a specific instance of a child in case of multiple instances?
    
    To get a specific instance you need to declare a template local variable for the child and refer to the variable name from `@ViewChild`.
    

In the example below we update the counter of the second child.

```tsx
@Component({
  selector: 'app-parent',
  template: `
  <app-child #ch1></app-child>
  <app-child #ch2></app-child>
  <button (click)="update()">Update</button>
  `
})
export class ParentComponent {
  @ViewChild("ch2", {static: true})
  child:ChildComponent

  update() {
    this.child.counter += 1
  }
}
```

## 1.9 Static and Dynamic Children with `@ViewChild`

- Which types are referenced by `@ViewChild`?
    
    Children referenced by `@ViewChild` have two types:
    
    - Dynamic `{static:false}` – If they are wrapped by an ngFor or ngInit and are thus subject to change.
    - Static {static:true} – If they are not dynamic
- How is declared a child’s `static` state from a child?
    
    The child’s static state needs to be declared in the second parameter (options) of the `@ViewChild` annotation.
    

```tsx
@ViewChild('ch1', {static:true}) prop1: StaticChildComponent;
@ViewChild('ch2', {static:false}) prop2: DynamicChildComponent;
```

- When properties based on Dynamic view children should be used?
    
    As a rule, properties based on Dynamic view children should be used only after the view initializes (for example from within `ngAfterViewInit()`) and not before.
    

## 1.10 More About `@ViewChild`

There may be several objects associated with the same element in the template. For example, an element can have many attribute directives.

- How specify which object type from a child component template variable?
    
    You can specify the object you need by supplying its type in the options object parameter of `@ViewChild`.
    

```tsx
@Component({
  selector: 'app-parent',
  template: `<div #d></div>`
})
export class ParentComponent implements OnInit {
  @ViewChild("d", {static: false, read: ViewContainerRef})
	childContainer
  @ViewChild("d", {static: false, read: ElementRef})
	ZchildElement

  ngOnInit() {
    console.log(this.childContainer)
    console.log(this.childElement)
  }
}
```

## Notes

You specify the type of object instance you are querying using the “read” property.

- In case of not supplying any type of a child variable `@ViewChild` variable what Angula does?
    
    If you do not supply the read property Angular behaves this way:
    
    - If the child is a component tag then the component instance is returned.
    - Otherwise, the ElementRef instance for the tag is returned.

## 1.11 The `@ViewChildren` Decorator

Similar to @ViewChild but gives you access to a collection of all matching children.

```tsx
import { Component, AfterViewInit, ViewChildren, QueryList } from '@angular/core';

@Component({
  selector: 'app-parent',
  template: `<app-child ="1"></app-child>
  <app-child ="2"></app-child>`
})
export class ParentComponent implements AfterViewInit {

@ViewChildren(ChildComponent) childList: QueryList<ChildComponent>

  ngAfterViewInit() {
    this.childList.forEach(child =>
        console.log(child.counter))
  }
}
```

Note: the `@ViewChildren` decorator does not require the static state `{static:boolean}` option parameter like we saw with `@ViewChild`

## 1.12 Live Monitoring of Children

You can monitor any dynamic addition or removal of children.

```tsx
@Component({
  selector: 'app-parent',
  template: `<app-child ="1"></app-child>
  <app-child *ngIf="showTwo" ="2"></app-child>`
})
export class ParentComponent implements AfterViewInit {
  showTwo = false
  @ViewChildren(ChildComponent) childList: QueryList<ChildComponent>

  ngAfterViewInit() {
    this.childList.changes.subscribe(queryList =>
      queryList.forEach(child => console.log(child.counter)))

    window.setTimeout(_ => this.showTwo = true, 1000)
  }
}
```

## Notes

In the example above we add the second child after one second. This change is picked up by our subscription callback. The callback receives a new QueryList which you can iterate over.

## 1.13 Direct Access to the Parent Component

- Which kinship are be able to inject in a child component?
    
    You can inject an instance of an ancestor component.
    

```tsx
export class ChildComponent implements OnInit {
  constructor(private parent:ParentComponent, private grandParent:AppComponent) { }
}
```

- Where is not possible to use `@ViewChild` and `@ViewChildren` to access children?
    
    Since `@ViewChild` and `@ViewChildren` do not give access to deeply nested children it is better for these children to look up an ancestor and initiate communication.
    
- How restrict a search to the immediate parent component?
    
    To restrict the search to the immediate parent use @Host.
    

```tsx
  constructor(@Host() private parent:ParentComponent) { }
```

- How to make a component more flexible to inject higher parent component?
    
    You can make a component more flexible by not requiring it to have a specific parent. Use @Optional for that.
    

```tsx
  constructor(@Optional() @Host() private parent:ParentComponent) { }
```

## Notes

- What degree of kinship using is able to be used with `@ViewChild` and `@ViewChildren`?
    
    `@ViewChild` and `@ViewChildren` limit access to the immediate children only.
    
- What strategy is more suitable when a parent needs to communicate with a deeply nested descendant?
    
    They are not very suitable when a parent needs to communicate with a deeply nested descendant. In a situation like that it will be much easier for the descendants to lookup an ancestor component and then make method calls to initiate communication.
    

## 1.14 Communication Using Subject API

When deeply nested components need to notify events to each other data and event binding may become complex.

In a situation like this RxJS Subject API can considerably simplify the implementation. The basic steps are as follows:

- Use a singleton service to create a subject (or topic of conversation).
- To notify an event a component will post a message to the subject.
- Any other component can subscribe to the subject and be notified.

## 1.15 Creating a `Subject`

```tsx
import { Subject } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class TopicManagerService {
	public cartTopic = new Subject<CartItem>()
}
```

A subject is strongly typed. You can specify the data type of messages posted in the subject when creating the subject.

## 1.16 Publishing a Message in a `Subject`

In this example, C1 component is publishing a message.

```tsx
@Component({
  selector: 'app-c1',
  template: `<button (click)="publish()">Go</button>`
})
export class C1Component {
  constructor(private tm:TopicManagerService) { }

  publish() {
		this.tm.cartTopic.next({itemId: 1001, qty: 2})
  }
}
```

## 1.17 Subscribing to the `Subject`

In this example C2 is subscribing. Code will be same for C3.

```tsx
export class C2Component implements OnInit {
  constructor(private tm:TopicManagerService) { }

  ngOnInit() {
		this.tm.cartTopic.subscribe(item => console.log(item))
  }
}
```

## 1.18 Problem With Ordering

- When is not advisable to use Subject?
    
    If you subscribe after a message is posted you will not receive the message. This can be a problem if components are posting and subscribing from ngOnInt since the order of initialization can be hard to control.
    
- In the case of retrieving the last messages, each RXJS component can be used?
    
    To receive the last posted message even if you have subscribed late, use the **`BehaviorSubject`** class instead of a plain Subject. The constructor needs a starter message which can be undefined.
    

```tsx
public cartTopic = new BehaviorSubject<CartItem>(undefined)
```

- And in the case of retrieving more than the one last message posted?
    
    To receive more than one last posted message use **`ReplaySubject`**. The constructor takes the number of past messages.
    

```tsx
public cartTopic = new ReplaySubject<CartItem>(5)
```

## 1.19 Content Projection

- What does a component projection give as an advantage?
    
    Content projection lets a parent component (i.e., the user of the component) provide parts of the template of a child component.
    

This has many useful benefits:

- The parent can customize how the component should render data.
- UI toolkit components can let users decide what content gets displayed. For example, a card component can simply focus on showing a shaded border and a title but the parent component can decide what gets displayed in the card.

## 1.20 Setup Projection Using `ng-content`

The `ng-content` directive lets you specify a placeholder.

- What advantage a placeholder of a `ng-content` component gives?
    
    A user of the component can supply the actual template for that area. Below is a card component that has two placeholders.
    

```tsx
@Component({
  selector: 'app-card',
  template: `<div class="card">
	<ng-content select=".title"></ng-content><hr/>
	<ng-content select=".body"></ng-content>
  </div>`
})
export class ChildComponent {}
```

`ng-content` takes a CSS selector which helps the parent supply different templates for different placeholders. We see that next.

## 1.21 Supplying Template for `ng-content`

Here a user of the card component supplies its templates using matching CSS selectors.

```tsx
@Component({
  selector: 'app-parent',
  template: `<app-card>
    <div class="title">First Card</div>
    <div class="body">First card body here.</div>
  </app-card>
  <app-card>
    <div class="title">{{title2}}</div>
    <div class="body"><button (click)="greet()">Hello</button></div>
  </app-card>`
})
export class ParentComponent {
  title2 = "Second Title"
  greet(){}
}
```

## Notes

The template supplied by the parent has full access to its own properties and methods. We see that above in the second card. This works just fine even though the template will actually be executed inside the card component. Without this feature, it will be very difficult to create a generic card component that can host any content. Content projection makes it all very easy.

## 1.22 The Host Element

Angular creates a DOM element for every component on the page. This is called the **host** element. The host contains as child the elements generated by the component’s template.

In the example of the previous slide the element created for the <app-card> tag will be the host element for the card component.

Sometimes you need to access the host element. For example

- A card component needs to show a border around it.
- A popup menu component needs to position the host where the mouse was clicked.

## 1.23 Static Styling of the Host Element

Use the **:host** pseudo selector from the component’s CSS.

```css
:host {
    display: block;
    border: 1px solid black;
    width: 200px;
    position: absolute;
}
```

## 1.24 Setting DOM Properties of the Host

Use the **`@HostBinding`** decorator to one-way bind a component property to a DOM property of the host element.

The following example shows the host element and sets its position.

```tsx
export class PopupMenuComponent {
  @HostBinding("style.top") y = "0px"
  @HostBinding("style.left") x = "0px"
  @HostBinding("style.visibility") visibility = "hidden"

  openAt(x:number, y:number) {
    this.x = `${x}px`
    this.y = `${y}px`

    this.visibility = "visible"
  }
}
```

## 1.25 Dynamically Loading a Component

Some applications may need to dynamically decide what component to show without directly referring to its tag in a template. For example, different products may need to be displayed using different components.

This can be done by a parent component following these steps:

- How create a dynamically loading component?
    - Create a **`ComponentFactory`** for the component class that you need to display.
- How create a **`ComponentFactory`** class?
    - This is done using the **`ComponentFactoryResolver`** service.
- In the parent’s template add a placeholder element where the child component will be dynamically inserted.
- Using the **`ViewContainerRef`** object for the placeholder element create an instance of the child component. This will add the child to the component tree and make it visible.
- Add all the child components that are dynamically displayed in the `**entryComponents**` list of the application module. Without this the tree shaker will exclude the child components from the build process (since they are never directly used in any template).

## 1.26 Dynamic Loading Example

In this example the parent component – `HostComponent` – dynamically loads either `BasicViewComponent` or `FullViewComponent` and adds it to the component tree.

Users can use a checkbox to toggle between the two components.

The template of the parent component:

```tsx
@Component({
  selector: 'app-host',
  template: `
  <div>
    <input type="checkbox" (change)="changeView()" ="isBasic"> Basic view
<div #compHost></div> <!-- Placeholder for child-->
  </div>
  `
})
export class HostComponent {...}
```

## 1.27 `HostComponent` Code

```tsx
export class HostComponent {
  isBasic = true
  @ViewChild("compHost", {static: true, read: ViewContainerRef})
  container:ViewContainerRef

  constructor(private resolver: ComponentFactoryResolver){}

  changeView() {
    let compClass = this.isBasic ? BasicViewComponent : FullViewComponent
    let factory = this.resolver.resolveComponentFactory(compClass)

    this.container.clear() //Remove old component
    let compRef = this.container.createComponent(factory)
  }
}
```

## Notes

Two key players here are `ComponentFactoryResolver` and `ViewContainerRef`. A `ViewContainerRef` is used to insert a component instance into the component tree. Every element in a template has an associated `ViewContainerRef`. Here we look it up for the `<div>` tag with the local variable `compHost`.

A `ComponentFactoryResolver` gives us a component factory appropriate for a given component class. The `createComponent()` method of `ViewContainerRef` takes the factory as input. The method uses the factory to create a new instance of the component and adds it to the component tree.

The `createComponent()` method returns a `ComponentRef` object. You can get the actual component instance using the “instance” property of that object. The instance here will be either a `BasicViewComponent` or `FullViewComponent`. Once you have the instance you can even set its properties to set its input. There is no other way to do this since you can’t really use data binding to set input for dynamically loaded components.

## 1.28 Setting `entryComponents`

Register the dynamically loaded components to the `entryComponents` list of the application module.

```tsx
@NgModule({
  declarations: ,
  imports: ,
	entryComponents: [BasicViewComponent,FullViewComponent],
	...
})
export class AppModule { }
```

## 1.29 Optimizing Change Detection

By default almost any user interaction will cause templates for all components on the page to be re-executed. For a complex application with hundreds of components on a page, this can slow down the screen update or cause flickers.

There are two ways you can take control of the situation and only re-execute a component’s template if it is truly necessary:

- What is the first action to optimize change detection and should be considered first?
    - Set the change detection strategy to `OnPush`. This is the simplest way and should be considered first.
- What is the second strategy to optimize change detection?
    - By programmatically detaching or attaching a component from the change detection process. This gives you more precise control over when a component’s template should be re-run. Not discussed here.

## 1.30 Example Excessive Template Execution

```tsx
@Component({
  selector: 'app-a',
  template: `<input type="text" ="todoItem"/>
<button (click)="addItem()">Add</button>

<app-b ="list"></app-b>`
})
export class AComponent {
  todoItem = ""
  list = []
  addItem() {
    this.list.push(this.todoItem)
  }
}

@Component({
  selector: 'app-b',
  template: `<p *ngFor="let item of todoList">{{item}}</p>`
})
export class BComponent { @Input() todoList = [] }
```

## Notes

As things stand right now every keystroke in the input field of AComponent will cause the template for both components to be re-executed. As you can imagine; if the list is long then rebuilding the DOM for BComponent can take some time.

## 1.31 Using `OnPush` Change Detection Strategy

Component B does not need to run its template for every key stroke in the parent component (A). It can use the `OnPush` strategy.

```tsx
@Component({
	...
	changeDetection: ChangeDetectionStrategy.OnPush
})
export class BComponent {@Input() todoList = []}

```

Now the template of B will be run only if any of its bound `@Input` variables have changed.

Unfortunately, this will not solve our problem. Component A adds new items to the existing array. Angular only does shallow checks for changes in the object reference. It does not check if the contents of a variable (such as an array or an object) have changed. We will see next how to work around this.

## 1.32 Properly Changing `@Input` Variables

A parent needs to set an entirely new object to abound property of a child for Angular to realize that the property has changed. In our case `AComponent` needs to create a new list. Now the template of B will be run only after user has clicked on the Add button.

```tsx
addItem() {
  this.list =
}
```

Note: Recreating a large array like this can be expensive. To solve that issue, store the array inside an object and just recreate the object. Pass the object as input to the child.

## 1.33 Additional Notes on OnPush

`OnPush` setting only takes effect if a parent changes variable bound via @Input. However, if the child itself directly changes that variable then the child’s template will be always executed. OnPush has no effect in that case. If change detection is skipped for a child due to the use of `OnPush` then the entire component tree from the child onwards will be skipped.

## 1.34 Summary

In this tutorial we covered:

- Sometimes you need to detect changes to the variables bound by @Input to perform certain work.
- Basic @Input and @Output based inter-component communication only works between direct parent and children. For communication between arbitrary components it is easier to use direct access or the Subject API.
- Content projection allows a parent component to supply parts of a child’s template. This is often used by GUI framework components like modal dialog.
- By default Angular re-executes templates of all components on a page if any change is detected in their state. This can slow down page refresh if you have many components. OnPush is an easy way to limit re-execution of a component’s template only if a @Input bound variable has changed