# How to Dynamically Add and Remove Form Fields in Angular?

Created: June 3, 2022 8:36 AM
Finished: No
Source: https://www.itsolutionstuff.com/post/how-to-dynamically-add-and-remove-form-fields-in-angularexample.html
Tags: #angular

This tutorial is focused on how to add remove input fields dynamically angular. this example will help you adding form fields dynamically in angular. if you want to see example of dynamically add and remove form input fields with angular then you are a right place. This article goes in detailed on how to dynamically add and remove form fields in angular.

I will show you very simple and step by step example of add more input fields dynamic in angular 6, anuglar 7, angular 8 and angular 9 application.

In this example we will create form with product name and user can add multiple quantity with price. we will use formgroup and formarray to create dynamic form in angular application.

You can see bellow layout for demo. let's follow bellow step.

![How%20to%20Dynamically%20Add%20and%20Remove%20Form%20Fields%20in%20A%202c0e1a1e9f3d47d2a2e585865ce9c7df/angular-add-more-input.png](How%20to%20Dynamically%20Add%20and%20Remove%20Form%20Fields%20in%20A%202c0e1a1e9f3d47d2a2e585865ce9c7df/angular-add-more-input.png)

**Import FormsModule:**

src/app/app.module.ts

```
import { NgModule } from '@angular/core';import { BrowserModule } from '@angular/platform-browser';import { FormsModule, ReactiveFormsModule } from '@angular/forms';import { AppComponent } from './app.component';@NgModule({  imports:      [ BrowserModule, FormsModule, ReactiveFormsModule ],  declarations: [ AppComponent ],  bootstrap:    [ AppComponent ]})export class AppModule { }
```

**updated Ts File**

In this file we will first import FormGroup, FormControl,FormArray and FormBuilder from angular forms library.

src/app/app.component.ts

```
import { Component } from '@angular/core';import { FormGroup, FormControl, FormArray, FormBuilder } from '@angular/forms'@Component({  selector: 'my-app',  templateUrl: './app.component.html',  styleUrls: [ './app.component.css' ]})export class AppComponent  {  name = 'Angular';  productForm: FormGroup;  constructor(private fb:FormBuilder) {    this.productForm = this.fb.group({      name: '',      quantities: this.fb.array([]) ,    });  quantities() : FormArray {    return this.productForm.get("quantities") as FormArray  newQuantity(): FormGroup {    return this.fb.group({      qty: '',      price: '',    })  addQuantity() {    this.quantities().push(this.newQuantity());  removeQuantity(i:number) {    this.quantities().removeAt(i);  onSubmit() {    console.log(this.productForm.value);
```

**Template Code:**

In this step, we will write code of html form with ngModel. so add following code to app.component.html file.

I used bootstrap class on this form. if you want to add than then follow this link too: [Install Boorstrap 4 to Angular 9](https://www.itsolutionstuff.com/post/install-bootstrap-4-in-angular-9-how-to-add-bootstrap-in-angular-9example.html).

src/app/app.component.html

```
<div class="container">  <h1>Angular FormArray Example - ItSolutionStuff.com</h1>  <form [formGroup]="productForm" (ngSubmit)="onSubmit()">    <p>      <label for="name">Product Name:</label>      <input type="text" id="name" name="name" formControlName="name" class="form-control">    </p>    <table class="table table-bordered" formArrayName="quantities">      <tr>        <th colspan="2">Add Multiple Quantity:</th>        <th width="150px"><button type="button" (click)="addQuantity()" class="btn btn-primary">Add More</button></th>      </tr>      <tr *ngFor="let quantity of quantities().controls; let i=index" [formGroupName]="i">        <td>            Quantity :            <input type="text" formControlName="qty" class="form-control">        </td>        <td>            Price:            <input type="text" formControlName="price" class="form-control">        </td>        <td>            <button (click)="removeQuantity(i)" class="btn btn-danger">Remove</button>        </td>      </tr>    </table>    <button type="submit" class="btn btn-success">Submit</button>  </form>  <br/>  {{this.productForm.value | json}}</div>
```

Now you can run your application using following command:

Read Also: [How to Disable Enter Key to Submit Form in Angular?](https://www.itsolutionstuff.com/post/how-to-disable-enter-key-to-submit-form-in-angularexample.html)

```
ng serve
```

I hope it can help you...

![How%20to%20Dynamically%20Add%20and%20Remove%20Form%20Fields%20in%20A%202c0e1a1e9f3d47d2a2e585865ce9c7df/hardik-savani.png](How%20to%20Dynamically%20Add%20and%20Remove%20Form%20Fields%20in%20A%202c0e1a1e9f3d47d2a2e585865ce9c7df/hardik-savani.png)

### We are Recommending you