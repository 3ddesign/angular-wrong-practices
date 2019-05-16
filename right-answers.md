#### #1
```html
<!-- Comment @3ddesign:
Some declared variables are not initialized and/or not typed.
-->
```
```ts
// some-page.component.ts
@Component({
  selector: 'appprefix-some-page',
})
export class SomePageComponent implements OnInit {

  today: number = Date.now();
  wrongDataText;
  isWrongData = false;
  isErrorShow;
  isErrorShowUserName;
  isErrorShowPassword;
  arrControls;
  isLoading = false;
  dataFormGroup: FormGroup;
  validators: ValidatorFn[];
  constructor(private some: SomeService,
              private someOther: SomeOtherService,
              private translate: TranslateService,
              private router: Router
  ) {
  
```
---------------
Possible solution:
At least designate types of declared variables. 
Also maybe there is a way to combine error variables (isErrorShow, isErrorShowUserName, isErrorShowPassword)?
```

#### #2
```html
<!-- Comment @3ddesign:
I do not like nesting in accessing the service and order in conditions for directives
As well as the difficulty of determining the first / last elements 
-->
```
```ts
// something.component.html
  <li class="something-list-item"
      *ngFor="let something of somethingService.stepSomething;
              let i = index">
    <a
      *ngIf="i !== somethingbService.stepSomething.length - 1"
      class="something-link"
      (click)="somethingService.clickSmth(i)"
    >{{ something.title }}</a>
    <span
      *ngIf="i === somethingService.stepSomethings.length - 1"
      class="something-link"
    >{{ something.title }}</span>
  </li>
```
---------------
```html
Possible solution:
Use a getter in the service, to avoid nested calls like this: somethingService.stepSomething
Use local variables with NgFor directive (first, last, even, odd), istead this constructions 'somethingService.stepSomethings.length - 1'  
So its may looks like this: *ngIf="last"
```
#### #3
```html
<!-- Comment @3ddesign:
Work with DOM using 'document' is not Angular way
Also in this case, we definitely can avoid using typing with 'any'
And not clear why we use here setTimeout
-->
```
// something.service.ts
@Injectable({
  providedIn: 'root'
})
export class SomethingService {
...
  nextSomething() {
    const somethingListHtmlElement: any = document.getElementsByClassName('something-list');
    const somethingRowHtmlElement: any = document.getElementsByClassName('something-row');

    if (somethingRowHtmlElement[0] &&
      somethingRowHtmlElement[0] &&
      somethingRowHtmlElement[0].offsetWidth >=
      somethingRowHtmlElement[0].offsetWidth) {
      this.stepSomething.splice(1, 1);
      setTimeout(() => {
        this.nextSomething();
      }, 300);
    }
...
}
```
---------------
Possible solution:
Using the tools provided by Angular as @ViewChild
Exact typing of declared variables
It seems in this case it is possible to use life cycle hooks instead setTimeout, but otherwise it is necessary to leave a  comment with description of solved problem 

#### #6
```html
<!-- Comment @dmkorol:
I noticed that "something-row" is applied for all `some-app-something` components.
Maybe, it would be better to move thouse css rules into component?
-->
<some-app-something class="something-row"></some-app-something>
```
---------------
Possible solution:
In most cases you can apply thouse rules to `:host` directly in compoinent.scss
```scss
:host { ... }
```
BUT if you have to add PREDEFINED class(es) for all components in the whole 
application use this approach 'cause it is a secure way and there are 
no side effects (nobody can accidentally delete your styles)
```html
<some-app-something></some-app-something>
```ts
@Component({
  selector:"class="something-row"
})
constructor(public breadcrumbService: BreadcrumbsService,
              private elementRef: ElementRef) {
	this.elementRef.nativeElement.classList.add('breadcrumbs-row');
}
```
***BEAWERE***: 
There is some tricky parts with usage `:host` and `@HostBinding`	
they can override class='' [ngClass]="..."
See details: https://github.com/angular/angular/issues/7289

