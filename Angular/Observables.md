**Angular Observables, Services, Component Mounting, and Advanced Angular Concepts** specifically for **Angular 14**:

---

# **1. Observables in Angular**
## **1.1 What Are Observables?**
Observables are a part of **RxJS (Reactive Extensions for JavaScript)** and provide a powerful way to handle **asynchronous programming** in Angular. They are used extensively for handling **HTTP calls, user interactions, and reactive data streams**.

## **1.2 Key Concepts**
- **Observer**: A consumer that subscribes to an Observable.
- **Observable**: Represents a data stream that can be observed.
- **Subscription**: Represents the execution of an Observable.
- **Operators**: Functions that allow transformation and manipulation of data streams.
- **Subjects**: A special type of Observable that allows multiple subscribers.

## **1.3 Creating Observables**
```typescript
import { Observable } from 'rxjs';

const myObservable = new Observable(observer => {
  observer.next('First Value');
  observer.next('Second Value');
  observer.complete(); // No more data will be emitted
});

myObservable.subscribe(value => console.log(value));
```

## **1.4 Common RxJS Operators**
| Operator  | Description |
|-----------|------------|
| `map` | Transforms emitted values |
| `filter` | Filters values based on a condition |
| `switchMap` | Cancels the previous request and subscribes to a new one |
| `mergeMap` | Runs multiple requests concurrently |
| `debounceTime` | Ignores values for a specified time period before emitting |

### **Example of Using Operators**
```typescript
import { of } from 'rxjs';
import { map, filter } from 'rxjs/operators';

const numbers$ = of(1, 2, 3, 4, 5);
numbers$.pipe(
  filter(num => num % 2 === 0),
  map(num => num * 10)
).subscribe(console.log);
```

---

# **2. Services in Angular**
## **2.1 What Are Angular Services?**
Angular services are singleton objects used to **share data and logic** between components.

## **2.2 Creating an Angular Service**
```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root' // Automatically available across the app
})
export class DataService {
  private data = 'Hello from Service';

  getData() {
    return this.data;
  }
}
```

## **2.3 Injecting a Service in a Component**
```typescript
import { Component } from '@angular/core';
import { DataService } from './data.service';

@Component({
  selector: 'app-example',
  template: `<p>{{ message }}</p>`
})
export class ExampleComponent {
  message: string;

  constructor(private dataService: DataService) {
    this.message = this.dataService.getData();
  }
}
```

## **2.4 Using HTTPClient with Services**
```typescript
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class ApiService {
  constructor(private http: HttpClient) {}

  getUsers(): Observable<any> {
    return this.http.get('https://jsonplaceholder.typicode.com/users');
  }
}
```

---

# **3. Component Mounting & Lifecycle Hooks**
## **3.1 Angular Lifecycle Hooks**
| Lifecycle Hook  | Description |
|----------------|-------------|
| `ngOnInit` | Called after component initialization |
| `ngOnChanges` | Called when input properties change |
| `ngDoCheck` | Detects changes manually |
| `ngAfterViewInit` | Called after the view is initialized |
| `ngOnDestroy` | Called before the component is destroyed |

### **Lifecycle Hooks Example**
```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';

@Component({
  selector: 'app-lifecycle-demo',
  template: `<p>Lifecycle demo</p>`
})
export class LifecycleDemoComponent implements OnInit, OnDestroy {
  constructor() { console.log('Constructor called'); }

  ngOnInit() {
    console.log('ngOnInit called');
  }

  ngOnDestroy() {
    console.log('ngOnDestroy called');
  }
}
```

---

# **4. Advanced Angular Concepts**
## **4.1 Angular Dependency Injection (DI)**
Dependency Injection (DI) is a design pattern that allows dependencies to be injected rather than being created inside a class.

### **Using DI with Providers**
```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  isAuthenticated() {
    return true;
  }
}
```

Injecting the service:
```typescript
constructor(private authService: AuthService) { }
```

---

## **4.2 Angular Modules & Lazy Loading**
Lazy loading improves performance by loading modules only when they are needed.

### **Example: Lazy Loading a Feature Module**
```typescript
const routes: Routes = [
  { path: 'dashboard', loadChildren: () => import('./dashboard/dashboard.module').then(m => m.DashboardModule) }
];
```

---

## **4.3 Component Interaction**
### **Parent to Child via `@Input()`**
```typescript
// Child Component
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `<p>{{ data }}</p>`
})
export class ChildComponent {
  @Input() data!: string;
}
```
```html
<!-- Parent Component -->
<app-child [data]="'Hello from Parent'"></app-child>
```

### **Child to Parent via `@Output()`**
```typescript
// Child Component
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `<button (click)="sendMessage()">Send</button>`
})
export class ChildComponent {
  @Output() messageEvent = new EventEmitter<string>();

  sendMessage() {
    this.messageEvent.emit('Hello from Child');
  }
}
```
```html
<!-- Parent Component -->
<app-child (messageEvent)="receiveMessage($event)"></app-child>
```
```typescript
receiveMessage(message: string) {
  console.log(message);
}
```

---

## **4.4 NgRx for State Management**
NgRx is a Redux-like state management solution for Angular.

### **NgRx Example**
```typescript
import { createAction, createReducer, on } from '@ngrx/store';

// Define Actions
export const increment = createAction('[Counter] Increment');
export const decrement = createAction('[Counter] Decrement');

// Define Reducer
export const counterReducer = createReducer(
  0,
  on(increment, state => state + 1),
  on(decrement, state => state - 1)
);
```

---

# **Conclusion**
These concepts provide a strong foundation for working with Angular **Observables, Services, Lifecycle Hooks, and Advanced Features** in **Angular 14**.