# ViewChildren

Created: June 4, 2022 6:09 PM
Finished: No
Source: https://angular.io/api/core/ViewChildren
Tags: #angular

Property decorator that configures a view query.

[See more...](https://angular.io/api/core/ViewChildren#description)

## Description

Use to get the `[QueryList](https://angular.io/api/core/QueryList)` of elements or directives from the view DOM. Any time a child element is added, removed, or moved, the query list will be updated, and the changes observable of the query list will emit a new value.

View queries are set before the `ngAfterViewInit` callback is called.

**Metadata Properties**:

- **selector** - The directive type or the name used for querying.
- **read** - Used to read a different token from the queried elements.
- **emitDistinctChangesOnly** - The  `[QueryList](https://angular.io/api/core/QueryList)#changes` observable will emit new values only if the QueryList result has changed. When `false` the `changes` observable might emit even if the QueryList has not changed.  **Note: *** This config option is **deprecated**, it will be permanently set to `true` and removed in future versions of Angular.

The following selectors are supported.

- A template reference variable as a string (e.g. query `<my-component #cmp></my-component>` with `@[ViewChildren](https://angular.io/api/core/ViewChildren)('cmp')`)
- Any provider defined in the child component tree of the current component (e.g. `@[ViewChildren](https://angular.io/api/core/ViewChildren)(SomeService) someService!: SomeService`)
- Any provider defined through a string token (e.g. `@[ViewChildren](https://angular.io/api/core/ViewChildren)('someToken') someTokenVal!: any`)

In addition, multiple string selectors can be separated with a comma (e.g. `@[ViewChildren](https://angular.io/api/core/ViewChildren)('cmp1,cmp2')`)

The following values are supported by `read`:

- Any provider defined on the injector of the component that is matched by the `selector` of this query
- Any provider defined through a string token (e.g. `{provide: 'token', useValue: 'val'}`)

Further information is available in the [Usage Notes...](https://angular.io/api/core/ViewChildren#usage-notes)

## Usage notes

```
import {AfterViewInit,Component,Directive,QueryList,ViewChildren} from '@angular/core';@Directive({selector: 'child-directive'})class ChildDirective {}@Component({selector: 'someCmp', templateUrl: 'someCmp.html'})class SomeCmp implementsAfterViewInit {
  @ViewChildren(ChildDirective) viewChildren!:QueryList<ChildDirective>;

  ngAfterViewInit() {
    // viewChildren is set
  }}
```

```
import {AfterViewInit,Component,Directive,Input,QueryList,ViewChildren} from '@angular/core';@Directive({selector: 'pane'})export class Pane {
  @Input() id!: string;}@Component({
  selector: 'example-app',
  template: `
    <pane id="1"></pane>
    <pane id="2"></pane>
    <pane id="3" *ngIf="shouldShow"></pane>

    <button (click)="show()">Show 3</button>

    <div>panes: {{serializedPanes}}</div>
  `,})export class ViewChildrenComp implementsAfterViewInit {
  @ViewChildren(Pane) panes!:QueryList<Pane>;
  serializedPanes: string = '';

  shouldShow = false;

  show() {
    this.shouldShow = true;
  }

  ngAfterViewInit() {
    this.calculateSerializedPanes();
    this.panes.changes.subscribe((r) => {
      this.calculateSerializedPanes();
    });
  }

  calculateSerializedPanes() {
    setTimeout(() => {
      this.serializedPanes = this.panes.map(p => p.id).join(', ');
    }, 0);
  }}
```