# angularLearning

Personal learning notes on Angular and some simple projects following the guidance on [Angular tutorial](https://angular.io/start).

My learning notes on AngularJS is [here](https://github.com/agathayin/angularLearning/tree/main/angularjs-learning)

### codes on div

```javascript
//loop
*ngFor=""
//if
*ngIf=""
//variable
{{ product.name }}  /* use variable directly */
[title]="product.name + 'details'" /* add variable for attributes */
//onclick
(click)="fucntion()"
```

### pass data to child component

*child components.ts* 
```javascript
//add type for prop
//product is the name of the variable used in html
//Product is certain Type
//! forces to use the mentioned type
@Input() product!: Product;
```
*parent components.html* 
```javascript
//app-product-alerts here is the child component selector, 
//[product] is the variable passed to child
<app-product-alerts
  [product]="product">
</app-product-alerts>
```

### Pass data to a parent component

*child components.ts* 
```javascript
//@Output() allows the ProductAlertsComponent to emit an event when the value of the notify property changes.
@Output() notify = new EventEmitter();
```

*child components.html* 
```javascript
//function should be the same as output
<button (click)="notify.emit()">Notify Me</button>
```

*parent components.ts and components.html* 
```javascript
//add function in class
onNotify() {
    window.alert('You will be notified when the product goes on sale');
}
//add function in html as prop
<app-product-alerts
  [product]="product"
  (notify)="onNotify()">
</app-product-alerts>
```

### router
add path in `src/app/app.module.ts`

    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      { path: 'products/:productId', component: ProductDetailsComponent },
    ])
add routerLink in html

    [routerLink]="['/products',product.id]"
    
get router id
```javascript
export class ProductDetailsComponent implements OnInit {
    //this types
    product: Product|undefined;
    //parameters in constructor can be called as this.method in functions
    constructor(
        private route: ActivatedRoute,
    ) { }
    //get product id and set member variable
    ngOnInit() {
    const routeParams = this.route.snapshot.paramMap;
    const productIdFromRoute = Number(routeParams.get('productId'));
    this.product = products.find(product => product.id === productIdFromRoute);
    }
}
```

### pipes

example: 

`<h4>{{ product.price | currency }}</h4>` show number to a currency string eg. "$10"  
`*ngFor="let shipping of shippingCosts | async"` returns the latest value from a stream of data and continues to do so for the life of a given component

### Service

can be used for the whole project.

E.g.

```
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class CartService {
	items: Product[] = [];

    addToCart(product: Product) {
      this.items.push(product);
    }
}
```

### HTTPClient

1. add HttpClientModule in `imports` in app.modules.ts 
```
import { HttpClientModule } from '@angular/common/http';
```
2. add http in service constructor
```
import { HttpClient } from '@angular/common/http';
constructor(
    private http: HttpClient
  ) {}
```
3. add function such as return getting data from json
```
this.http.get<{type: string, price: number}[]>('/assets/shipping.json');
```

### form

uses FormBuilder to create form

in ts

```
import { Component } from '@angular/core';
import { FormBuilder } from '@angular/forms';

import { CartService } from '../cart.service';

@Component({
  selector: 'app-cart',
  templateUrl: './cart.component.html',
  styleUrls: ['./cart.component.css']
})
export class CartComponent {
  items = this.cartService.getItems();
  checkoutForm = this.formBuilder.group({
    name: '',
    address: ''
  });
  constructor(
    private cartService: CartService,
    private formBuilder: FormBuilder,
    ) {}

  onSubmit(): void {
    // Process checkout data here
    this.items = this.cartService.clearCart();
    console.warn('Your order has been submitted', this.checkoutForm.value);
    this.checkoutForm.reset();
  }
}
```
in html
```
<form [formGroup]="checkoutForm" (ngSubmit)="onSubmit()">

  <div>
    <label for="name">
      Name
    </label>
    <input id="name" type="text" formControlName="name">
  </div>

  <div>
    <label for="address">
      Address
    </label>
    <input id="address" type="text" formControlName="address">
  </div>

  <button class="button" type="submit">Purchase</button>

</form>
```

### cannot find module issue

This is usually a problem after adding routes in *src/app/app.app.module.ts*. The problem is nothing about codes but because of stackblitz.com. To fix the issue, just simply **save the codes and reload the page**.

