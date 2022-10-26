# Complete Guide: Angular lifecycle hooks

Created: August 4, 2022 7:41 AM
Finished: No
Source: https://indepth.dev/posts/1494/complete-guide-angular-lifecycle-hooks
Tags: #angular

![Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/Complete-Guide_-Angular-lifecycle-hooks.jpg](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/Complete-Guide_-Angular-lifecycle-hooks.jpg)

A component instance in Angular has a lifecycle that starts when Angular instantiates the component class and renders the component view along with its child views. The lifecycle continues with change detection, as Angular checks to see when data-bound properties change, and updates both the view and the component instance as needed. The lifecycle ends when Angular destroys the component instance and removes its rendered template from the DOM.

Directives have a similar lifecycle, as Angular creates, updates, and destroys instances in the course of execution.

Angular applications can use [lifecycle hook methods](https://angular.io/guide/glossary#lifecycle-hook) to tap into key events in the lifecycle of a component or directive to initialize new instances, initiate change detection when needed, respond to updates during change detection, and clean up before the deletion of instances.

Angular calls these hook methods in the following order:

1. **ngOnChanges**: When an input/output binding value changes.
2. **ngOnInit**: After the first `ngOnChanges`.
3. **ngDoCheck**: Developer's custom change detection.
4. **ngAfterContentInit**: After component content initialized.
5. **ngAfterContentChecked**: After every check of component content.
6. **ngAfterViewInit**: After a component's views are initialized.
7. **ngAfterViewChecked**: After every check of a component's views.**ngOnDestroy**: Just before the component/directive is destroyed.

We will create a new project for practical understanding using the Angular CLI:

```bash
ng new life-cycle-hooks
```

Then run the following commands to create the needed components

```bash
ng g c components/parent
ng g c components/child
```

Then, add the following starter code.

```tsx
<!-- app.component.html -->

<app-parent></app-parent>
```

```tsx
<!-- components/parent/parent.component.html -->

<button (click)="updateUser()">Update</button>

<br/>
<br/>

<app-child [userName]="userName"></app-child>
```

```tsx
// components/parent/parent.component.ts

export class ParentComponent {
  userName = 'Maria';

  updateUser() {
     this.userName = 'Chris';
  }
}
```

```tsx
<!-- components/child/child.component.html -->

Here is the user name: {{ userName }}
```

With the above code, the UI looks like below:

![Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/1.png](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/1.png)

### ****ngOnChanges****

This method is called once on a component's creation and then every time changes are detected in one of the component’s input properties. It receives a [SimpleChanges](https://angular.io/api/core/SimpleChanges) object as a parameter, which contains information regarding which of the input properties has changed - in case we have more than one - and its current and previous values.

Note that if your component has no inputs or you use it without providing any inputs, the framework will not call `ngOnChanges()`.

This is one of the lifecycle hooks which can come in handy in multiple use cases. It is very useful if you need to handle any specific logic in the component based on the received input property.

To demonstrate, let’s add the following to our code:

```tsx
// components/child/child.component.ts

export class ChildComponent implements OnChanges {
  @Input() userName = '';

  ngOnChanges(changes:SimpleChanges) {
    console.log('ngOnChanges triggered', changes);
 }
}
```

Check the `console`, we should see that the *`console.log()`* is called after opening the application for the first time.

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/1pF8Yf2pCVm-8AOYtPkf2SH2J4DUgl_HWAEhFlAMraZ8s22yQ1HGh07h8j0G5r2zYFtXOQCxW2PgHVXWSgJmP7yEH8ISQSsaXYBqNRJC8rDkAF8GoHv_Hes8pNN4QZf-pABPGT1-](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/1pF8Yf2pCVm-8AOYtPkf2SH2J4DUgl_HWAEhFlAMraZ8s22yQ1HGh07h8j0G5r2zYFtXOQCxW2PgHVXWSgJmP7yEH8ISQSsaXYBqNRJC8rDkAF8GoHv_Hes8pNN4QZf-pABPGT1-)

Notice that, the received `changes` object has three keys `currentValue`*,* `previousValue`*,* and `firstChange`, they work as they sound.

We could say that we want to change the `userName` value if it’s not the first change, or if the current value is only `Chris`. We could do anything here, let’s implement the second case.

```tsx
// components/child/child.component.ts

export class ChildComponent implements OnChanges{

  @Input() userName = '';

  ngOnChanges(changes:SimpleChanges) {
     console.log('ngOnChanges triggered', changes);

     if (!changes['userName'].isFirstChange()){
        if (changes['userName'].currentValue === "Chris") {
           this.userName = 'Hello ' + this.userName
        } else {
           this.userName = changes['userName'].previousValue
        }
     }
  }
}
```

Here we update the `userName` value if it’s not the first value (because it won’t take the first input if we didn’t add this condition) and if the current value is `Chris` only.

Now, if we click on the `Update` button, we should see that the *`console.log()`* function is triggered one more time. This happens because the `@Input()` value changed (from `Maria` to `Chris`). If we keep clicking on the `Update` button we will not see anything new on the console, this happens because the *`@Input()`* is not changed.

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/9b2yTK_0CYxeUKqBQa4biTb-OkdRjytUdLQjFHQG73obriT1rDlS-lmv_-79O7loys1wV7hZjOU_4AC5oNH1Ap9RHePb2yCbfJeJLo8N6qiBqOwAc8kqyBCIrd0AXxxAHLAE0ejo](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/9b2yTK_0CYxeUKqBQa4biTb-OkdRjytUdLQjFHQG73obriT1rDlS-lmv_-79O7loys1wV7hZjOU_4AC5oNH1Ap9RHePb2yCbfJeJLo8N6qiBqOwAc8kqyBCIrd0AXxxAHLAE0ejo)

Also, if we checked the console’s output we will see that the received object is changed like this:

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/LqlNIuPy2O50pYP3AXNNw6noTAjCixU51L7yBj0KbhCiux39W8vRhRgBbCjVdDn39g_w2guD0-dOloorSuz7jrvqisSLvYxDx27JegKGOoPiDC1e_W2UCF0Jh0_mwrcAkyroeOG6](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/LqlNIuPy2O50pYP3AXNNw6noTAjCixU51L7yBj0KbhCiux39W8vRhRgBbCjVdDn39g_w2guD0-dOloorSuz7jrvqisSLvYxDx27JegKGOoPiDC1e_W2UCF0Jh0_mwrcAkyroeOG6)

### ****ngOnInit****

This method is called only once during the component lifecycle, after the first `ngOnChanges` call. `ngOnInit()` is still called even when `ngOnChanges()` is not, which is the case when there are no template-bound inputs.

This is one of the most used lifecycle hooks in Angular. Here is where you might set requests to the server to load content, maybe create a [FormGroup](https://angular.io/api/forms/FormGroup) for a form to be handled by that component, set [subscriptions](https://rxjs.dev/guide/subscription), and much more. It is where you can perform any initializations shortly after the component’s construction.

But what is the advantage of the `ngOnInit` hook if the same work (initializing a `FormGroup` or getting data from the server) could be done on the component’s `constructor()`? Well, let me explain.

Following are some of the main key points:

**The `constructor()`**

- The default method of the class is executed when the class is instantiated and ensures proper initialization of fields in the class and its subclasses.
- Dependency Injector (DI) in Angular, analyses the constructor parameters and when it creates a new instance by calling `new MyClass()` it tries to find providers that match the types of the constructor parameters, resolves them, and passes them to the constructor like `new MyClass(someArg)`
- Should only be used to initialize class members but shouldn't do actual work. This is because the `constructor` is called before `ngOnInit`, at this point the component hasn’t been created yet, only the component class has been instantiated thus the dependencies are brought in, but the initialization code will not run.

**The `ngOnInit()`**

- Is a life cycle hook called by Angular to indicate that Angular is done creating the component.
- Should be used for all the initialization/declaration. Because at this point the component will be initialized.

To demonstrate, let’s add the following to our code

```tsx
// components/parent/parent.component.ts

export class ParentComponent implements OnInit {
 ...

  ngOnInit() {
     console.log('ngOnInit from the parent component');
  }

  ...
}
```

```tsx
// components/child/child.component.ts

export class ChildComponent implements OnInit, OnChanges {
  ...

  ngOnInit() {
     console.log('ngOnInit from the child component');
  }

  ...
}
```

We should see the following result on the console

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/wCi8qe-BxIa7ecdisGYH7UUbifaFpBLs3GGZLpvFm36ksN9QhLpNLOBt_4syS9iCSvZCntCK45r2g2qdwJM6Ai8FhduNQX44UqCbVXudkUWFMCapT5P1j0jqK2x5vSN03CVd2KyH](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/wCi8qe-BxIa7ecdisGYH7UUbifaFpBLs3GGZLpvFm36ksN9QhLpNLOBt_4syS9iCSvZCntCK45r2g2qdwJM6Ai8FhduNQX44UqCbVXudkUWFMCapT5P1j0jqK2x5vSN03CVd2KyH)

It makes sense that the parent component is created first ( `ngOnInit` from the parent component is triggered first), then its child component. If we tried to click on the `Update` button, the `ngOnInit` will not trigger. Because, as I mentioned above, it only triggers once.

### ****ngDoCheck****

This hook can be interpreted as an “extension” of `ngOnChanges`. You can use this method to detect changes that Angular can’t or won’t detect. It is called in every change detection, immediately after the `ngOnChanges` and `ngOnInit` hooks.

This hook is costly since it is called with enormous frequency; after every change detection cycle no matter where the change occurred. Therefore, its usage should be careful to not affect the user experience.

To demonstrate, let’s add the following to our code:

```tsx
// components/child/child.component.ts

export class ChildComponent implements OnInit, OnChanges, DoCheck {
  @Input() userName = '';

  constructor() {
  }

  ...

  ngDoCheck() {
     console.log('ngDoCheck triggered');
  }

...
}
```

We should see the following result after running the application.

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/i06SnlfNdtUkM4KW--922ozQqPKwPVcPYuOr4sulRpF8mKn2rQJneBiCw4qadr8KwZ449090GGBnrWoGYN941x1OvtRwbmrx_NbB-KMy2nKWTJHClW7UVT1nzf-Mig-Oww5dnC83](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/i06SnlfNdtUkM4KW--922ozQqPKwPVcPYuOr4sulRpF8mKn2rQJneBiCw4qadr8KwZ449090GGBnrWoGYN941x1OvtRwbmrx_NbB-KMy2nKWTJHClW7UVT1nzf-Mig-Oww5dnC83)

If we click on the `Update` button we should see that the `ngOnChanges` and the `ngDoCheck` are triggered

And if we keep clicking on the `Update` we should see that only the `ngDoCheck` is triggered after each click, this happens because `ngDoCheck` captures a change.

**How does `ngDoCheck` capture a change if there is no change in the `@Input()`property?**

Well, since Angular tracks object reference and we mutate the object without changing the reference Angular won’t pick up the changes and it will not run change detection for the component. Thus the new name property value will not be re-rendered in DOM. Luckily, we can use the `ngDoCheck` lifecycle hook to check for object mutation and notify Angular.

### ****ngAfterContentInit****

This method is called only once during the component’s lifecycle, after the first `ngDoCheck`. Within this hook, we have access for the first time to the [ElementRef](https://angular.io/api/core/ElementRef) of the [ContentChild](https://angular.io/api/core/ContentChildren) after the component’s creation; after Angular has already projected the external content into the component’s view.

To demonstrate, let’s add the following to our code:

```tsx
<app-child [userName]="userName">
  <div #contentWrapper>foo</div> <!-- <== Add this -->
</app-child>
```

```tsx
// components/child/child.component.ts

export class ChildComponent implements OnInit, OnChanges, DoCheck, AfterContentInit {
  ...

  @ViewChild('wrapper') wrapper!: ElementRef;
  @ContentChild('contentWrapper') content!: ElementRef;

 ...

  ngAfterContentInit() {
     console.log('ngAfterContentInit - wrapper', this.wrapper);
     console.log('ngAfterContentInit - 'contentWrapper', this.content);
  }

...
}
```

```tsx
<div #wrapper>
  <ng-content></ng-content>
</div>
```

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/agkydaerloyRZeXfBwLZD8iatnIH1KkwtG00uNkEWcZZ_OXOwaA8cuSnTD5pKXfJ5HEKQHjqUgQ7zfaTxwRYDkrTZxwXir158-Lt6k7o7-8YR1UdlYMjLHxVgmXx4MdfwKb78wRe](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/agkydaerloyRZeXfBwLZD8iatnIH1KkwtG00uNkEWcZZ_OXOwaA8cuSnTD5pKXfJ5HEKQHjqUgQ7zfaTxwRYDkrTZxwXir158-Lt6k7o7-8YR1UdlYMjLHxVgmXx4MdfwKb78wRe)

In the above code snippet, we projected content from the `Parent` component to the `Child` component. To learn more about content projection, check this [doc](https://angular.io/guide/content-projection).

At this point, we only have access to the projected content (contentWrapper has the value of the projected content). Moreover, the component’s template is not initialized yet (wrapper is `undefined`). It will be initialized and ready to be accessed on the `ngAfterViewInit` hook

### ****ngAfterContentChecked****

This method is called once during the component’s lifecycle after `ngAfterContentInit` and then after every subsequent `ngDoCheck`. It is called after Angular has already checked the content projected into the component in the current digest loop.

To demonstrate, let’s add the following to our code:

```tsx
// components/child/child.component.ts

export class ChildComponent implements OnInit, OnChanges, DoCheck, AfterContentInit, AfterContentChecked {

  ngAfterContentChecked(): void {
     console.log('ngAfterContentChecked triggered');
  }
}
```

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/ReFVMGBcwNpKPYTi-iYhPZTsdJ_mD6-RNXzRCcUvPZBU3GupXCYYNW4y4Y-BbQaPb2siEOvmZVuyXneGaHj64PSL6RR0dPgH_jebcfJmfR0pUxK7iDG1U239QSlAD9KvOcLqGtAh](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/ReFVMGBcwNpKPYTi-iYhPZTsdJ_mD6-RNXzRCcUvPZBU3GupXCYYNW4y4Y-BbQaPb2siEOvmZVuyXneGaHj64PSL6RR0dPgH_jebcfJmfR0pUxK7iDG1U239QSlAD9KvOcLqGtAh)

If we click on the `Update` button, the `ngAfterContentChecked` will trigger each time, as well as `ngDoCheck`.

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/E7uYore1SGENQN5UuuZ5nNl9wduFCnblS85yZ_VpdAfmUUocLYSffc0Mlmys_dfDQze1gbwQG6F-iOFc1o9JWXyqF4wS7d-M-zg_vSDF39EU-lds8-1LL5staRquCUT7Bp3ugI9t](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/E7uYore1SGENQN5UuuZ5nNl9wduFCnblS85yZ_VpdAfmUUocLYSffc0Mlmys_dfDQze1gbwQG6F-iOFc1o9JWXyqF4wS7d-M-zg_vSDF39EU-lds8-1LL5staRquCUT7Bp3ugI9t)

### ****ngAfterViewInit****

This method is called only once during the component’s lifecycle, after `ngAfterContentChecked`. Within this hook, we have access for the first time to the [ElementRef](https://angular.io/api/core/ElementRef) of the [ViewChildren](https://angular.io/api/core/ViewChildren) after the component’s creation; after Angular has already composed the component’s views and its child views.

This hook is useful when you need to load content on your view that depends on its view’s components; for instance when you need to set a video player or create a chart from a canvas element

To demonstrate, let’s add the following to our code:

```tsx
// components/child/child.component.ts

export class ChildComponent implements OnInit, OnChanges, DoCheck, AfterContentInit, AfterContentChecked, AfterViewInit {

...

  ngAfterViewInit(): void {
     console.log('ngAfterViewInit - wrapper', this.wrapper);
  }

...
}
```

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/7PlkYm7l5SJ652udMozqxQ1n3mk3oTfLXMvTwbBQhtx-OEFvjvafFHxMx83Ry_I8G4FD2_TI7duvQrUzFyiJPCtyrG_GcVpsciGy6ihj_E-MnqWOucxgD1Lwkq-DCGhqbmx76AJh](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/7PlkYm7l5SJ652udMozqxQ1n3mk3oTfLXMvTwbBQhtx-OEFvjvafFHxMx83Ry_I8G4FD2_TI7duvQrUzFyiJPCtyrG_GcVpsciGy6ihj_E-MnqWOucxgD1Lwkq-DCGhqbmx76AJh)

At this point, the component’s template is created and we have access to it.

### ****ngAfterViewChecked****

This method is called once after `ngAfterViewInit` and then after every subsequent`ngAfterContentChecked`. It is called after Angular has already checked the component’s views and its child views in the current digest loop.

To demonstrate, let’s add the following to our code:

```tsx
// components/child/child.component.ts

export class ChildComponent implements OnInit, OnChanges, DoCheck, AfterContentInit, AfterContentChecked, AfterViewInit,
     AfterViewChecked {

  @Input() userName = '';
  @ViewChild('wrapper') wrapper!: ElementRef;
  @ContentChild('contentWrapper') content!: ElementRef;

  constructor() {
  }

...

  ngAfterViewChecked(): void {
     console.log('ngAfterViewChecked triggered');
  }

...
}
```

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/SatdUznW7biPwCoRwC5iAW2JsO7AWDNwnosFuH3dmRyg2dXaXuPh1Alv2yP7kI6sAwVk8UNzEHocrko6pKFsA5tN3VBxy8e8Qz166eaM09RUMIAhBE9iNaKK24Uuqp45NEZWtg7-](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/SatdUznW7biPwCoRwC5iAW2JsO7AWDNwnosFuH3dmRyg2dXaXuPh1Alv2yP7kI6sAwVk8UNzEHocrko6pKFsA5tN3VBxy8e8Qz166eaM09RUMIAhBE9iNaKK24Uuqp45NEZWtg7-)

If we continue clicking on the `Update` button many times, the `ngAfterViewChecked` will be triggered each time, as well as, `ngDoCheck` and `ngAfterContentChecked`.

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/3trO9tEs2lO0QKdALzOsXuSLdojgIiMlM-Y0tIVwFrg9KmYhrrr0dLweylnJT7HNGFXqKOoHEhGqqpwaVmtLHTQ7NYdDi9Xxd7716uUEvjyfnkUALMCn1w9QX-WMA_z-n2Bydn_F](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/3trO9tEs2lO0QKdALzOsXuSLdojgIiMlM-Y0tIVwFrg9KmYhrrr0dLweylnJT7HNGFXqKOoHEhGqqpwaVmtLHTQ7NYdDi9Xxd7716uUEvjyfnkUALMCn1w9QX-WMA_z-n2Bydn_F)

### ****ngOnDestroy****

Lastly, this method is called only once during the component’s lifecycle, right before Angular destroys it. Here is where you should inform the rest of your application that the component is being destroyed, in case there are any actions to be done regarding that information.

Also, it is where you should put all your cleanup logic for that component. For instance, it is where you can remove any [local storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) information and most importantly unsubscribe observables/detach event handlers/stop timers, etc. to avoid [memory leaks](https://www.geeksforgeeks.org/what-is-memory-leak-how-can-we-avoid/).

Note that the `ngOnDestroy` is not called when the user refreshes the page or closes the browser. So, in case you need to handle some cleanup logic on those occasions as well, you can use the [HostListener](https://angular.io/api/core/HostListener) decorator, as shown below:

```tsx
@HostListener(‘window:beforeunload’)
ngOnDestroy() {
  // Insert Logic Here!
}
```

To demonstrate, let’s add the following to our code:

```tsx
// components/parent/parent.component.ts

export class ParentComponent implements OnInit {
  ...

  isChildDestroyed = false;

 ...

  destroy() {
     this.isChildDestroyed = true;
  }

...
}
```

```html
<!-- components/parent/parent.component.html -->

<button (click)="destroy()">Destroy Child</button>

<app-child *ngIf="!isChildDestroyed"
          [userName]="userName">
  <div #contentWrapper>foo</div>
</app-child>

<div *ngIf="isChildDestroyed">Child component is destroyed! :(</div>

...
```

```tsx
// components/child/child.component.ts

export class ChildComponent implements OnInit, OnChanges, DoCheck, AfterContentInit, AfterContentChecked, AfterViewInit,
     AfterViewChecked, OnDestroy {

 ...

  ngOnDestroy(): void {
     console.log('Child component is destroyed! :(');
  }

...
}
```

This is our updated UI

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/5cKlJ8EbqFvtRgPNIiLcCORuFsyIqt37-Y_QltHTelahJscNq9keDyyFK4ynvjtblZxe8OwUB7TxrD5sndykp3O91l8WeqMDzucfEExPA2gd-6n-_uBiX1XdM3Ep8cg2BPksrxAF](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/5cKlJ8EbqFvtRgPNIiLcCORuFsyIqt37-Y_QltHTelahJscNq9keDyyFK4ynvjtblZxe8OwUB7TxrD5sndykp3O91l8WeqMDzucfEExPA2gd-6n-_uBiX1XdM3Ep8cg2BPksrxAF)

If we click on the `Destroy Child` button, notice that the `ngOnDestroy` function will be triggered and the component will be removed from the DOM.

The below picture represents the DOM before clicking on the `Destroy Child` button.

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/FntvEBUxGugLWae1aNELmEBhVLVdoILE82f0TUi7aFqExN8fT9Bej-LT0B-gRKrIPJY0a7HkdPu3Y0ds6QhyaT9F8Ccy3bVQYKP5lQq9A-QYqi7PWimqwWCJKgHU-hFppzQRE6cD](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/FntvEBUxGugLWae1aNELmEBhVLVdoILE82f0TUi7aFqExN8fT9Bej-LT0B-gRKrIPJY0a7HkdPu3Y0ds6QhyaT9F8Ccy3bVQYKP5lQq9A-QYqi7PWimqwWCJKgHU-hFppzQRE6cD)

And the following image represents the DOM after clicking on the `Destroy Child` button.

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/guhEzYFI3Krjw-w3HMO_lpUUITi4IdB9MuhlHouXaTqriKm5Cn6JE-qNS7hXAAt1-QT1YDvZa8D7Qta_c1Gb2HuZuJMIEQPcc1B2n90ZcYrCNnygR45lZoQjp4H3C2rvxuLGNlYV](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/guhEzYFI3Krjw-w3HMO_lpUUITi4IdB9MuhlHouXaTqriKm5Cn6JE-qNS7hXAAt1-QT1YDvZa8D7Qta_c1Gb2HuZuJMIEQPcc1B2n90ZcYrCNnygR45lZoQjp4H3C2rvxuLGNlYV)

Long story short, we can understand the lifecycle hooks by splitting the process into two steps,**” first-time hooks”**, and **“in every change detection cycle hooks”.**

Step 1: “first-time hooks”, the triggered hooks are:

- onChanges
- onInit
- doCheck
- afterContentInit
- afterContentChecked
- afterViewInit
- afterViewChecked

Step 2: “in every change detection cycle hooks”, the triggered hooks are:

- onChanges
- doCheck
- afterContentChecked
- afterViewChecked

The following diagram shows exactly how Angular component/directive lifecycle works

[Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/jqfQIpB5PJcoOn8n9fMW466u69Fs-kS4pKMzr3nKPmLRj_T730J9MB3kBRfaI9A_T3T5PFYOsjL0lSJkl_NifKbzhOJgkZKU5bQmiZhXwz8Tcu_uT6rsSlA8gFF5hl-YBRybh0RA](Complete%20Guide%20Angular%20lifecycle%20hooks%20e00dbf755ea1493e8fa936a88ae70b43/jqfQIpB5PJcoOn8n9fMW466u69Fs-kS4pKMzr3nKPmLRj_T730J9MB3kBRfaI9A_T3T5PFYOsjL0lSJkl_NifKbzhOJgkZKU5bQmiZhXwz8Tcu_uT6rsSlA8gFF5hl-YBRybh0RA)

### ****Conclusion****

We understood Angular Lifecycle Hooks, their objectives, and when they are called and they can be very useful when creating Angular applications. Therefore it is important to know how they work and what you can achieve with them to be able to apply it whenever you might need it.