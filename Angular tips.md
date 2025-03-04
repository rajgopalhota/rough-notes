### **Expert-Level Notes on Angular 15**

Since you're familiar with React, I'll also provide comparisons where relevant.

---

## **1. Different Ways of Component Interaction in Angular 15**
In Angular, components interact in several ways depending on their relationship (parent-child, sibling, etc.). Here are the primary methods:

### **1.1 Parent to Child (Using @Input)**
Angular allows passing data from parent to child using `@Input`.

#### **Example:**
```typescript
// child.component.ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `<h2>{{ message }}</h2>`,
})
export class ChildComponent {
  @Input() message!: string;
}
```

```html
<!-- parent.component.html -->
<app-child [message]="'Hello from Parent'"></app-child>
```

**React Comparison:**
- Similar to passing props in React:  
  ```jsx
  function Child({ message }) {
    return <h2>{message}</h2>;
  }
  
  function Parent() {
    return <Child message="Hello from Parent" />;
  }
  ```

---

### **1.2 Child to Parent (Using @Output and EventEmitter)**
The child can send data to the parent using `@Output`.

#### **Example:**
```typescript
// child.component.ts
import { Component, Output, EventEmitter } from '@angular/core';

@Component({
  selector: 'app-child',
  template: `<button (click)="sendMessage()">Send</button>`,
})
export class ChildComponent {
  @Output() messageEvent = new EventEmitter<string>();

  sendMessage() {
    this.messageEvent.emit('Hello from Child');
  }
}
```

```html
<!-- parent.component.html -->
<app-child (messageEvent)="receiveMessage($event)"></app-child>
<p>{{ childMessage }}</p>
```

```typescript
// parent.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-parent',
  templateUrl: './parent.component.html',
})
export class ParentComponent {
  childMessage!: string;

  receiveMessage(message: string) {
    this.childMessage = message;
  }
}
```

**React Comparison:**
- Similar to passing a callback function in React:
  ```jsx
  function Child({ sendMessage }) {
    return <button onClick={() => sendMessage("Hello from Child")}>Send</button>;
  }

  function Parent() {
    const [message, setMessage] = useState("");

    return (
      <>
        <Child sendMessage={setMessage} />
        <p>{message}</p>
      </>
    );
  }
  ```

---

### **1.3 Sibling Communication (Using a Service)**
For communication between sibling components, Angular uses a shared service with `BehaviorSubject`.

#### **Example:**
```typescript
// message.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class MessageService {
  private messageSource = new BehaviorSubject<string>('Default Message');
  currentMessage = this.messageSource.asObservable();

  changeMessage(message: string) {
    this.messageSource.next(message);
  }
}
```

```typescript
// sibling1.component.ts
import { Component } from '@angular/core';
import { MessageService } from '../message.service';

@Component({
  selector: 'app-sibling1',
  template: `<button (click)="sendMessage()">Send Message</button>`,
})
export class Sibling1Component {
  constructor(private messageService: MessageService) {}

  sendMessage() {
    this.messageService.changeMessage('Hello from Sibling 1');
  }
}
```

```typescript
// sibling2.component.ts
import { Component, OnInit } from '@angular/core';
import { MessageService } from '../message.service';

@Component({
  selector: 'app-sibling2',
  template: `<h2>{{ message }}</h2>`,
})
export class Sibling2Component implements OnInit {
  message!: string;

  constructor(private messageService: MessageService) {}

  ngOnInit() {
    this.messageService.currentMessage.subscribe((msg) => (this.message = msg));
  }
}
```

**React Comparison:**
- Similar to using React Context API or Redux for global state management.

---

## **2. Observables and subscribe in Angular**
Observables are a powerful way to handle asynchronous data streams.

### **Example using HttpClient**
```typescript
// data.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root',
})
export class DataService {
  private apiUrl = 'https://jsonplaceholder.typicode.com/posts';

  constructor(private http: HttpClient) {}

  getPosts(): Observable<any> {
    return this.http.get<any>(this.apiUrl);
  }
}
```

```typescript
// component.ts
import { Component, OnInit } from '@angular/core';
import { DataService } from '../data.service';

@Component({
  selector: 'app-posts',
  template: `<ul><li *ngFor="let post of posts">{{ post.title }}</li></ul>`,
})
export class PostsComponent implements OnInit {
  posts: any[] = [];

  constructor(private dataService: DataService) {}

  ngOnInit() {
    this.dataService.getPosts().subscribe((data) => {
      this.posts = data;
    });
  }
}
```

**React Comparison:**
- Equivalent to `useEffect` with `fetch` or `axios`:
  ```jsx
  useEffect(() => {
    axios.get('https://jsonplaceholder.typicode.com/posts')
      .then(response => setPosts(response.data));
  }, []);
  ```

---

## **3. Reactive Forms in Angular**
Angular's Reactive Forms provide a structured way to manage form state and validation.

### **Example:**
```typescript
// component.ts
import { Component } from '@angular/core';
import { FormGroup, FormControl, Validators } from '@angular/forms';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
})
export class LoginComponent {
  loginForm = new FormGroup({
    email: new FormControl('', [Validators.required, Validators.email]),
    password: new FormControl('', Validators.required),
  });

  onSubmit() {
    console.log(this.loginForm.value);
  }
}
```

```html
<!-- component.html -->
<form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
  <input type="email" formControlName="email" placeholder="Email">
  <input type="password" formControlName="password" placeholder="Password">
  <button type="submit">Login</button>
</form>
```

**React Comparison:**
- Similar to React Hook Form:
  ```jsx
  const { register, handleSubmit } = useForm();

  <form onSubmit={handleSubmit(data => console.log(data))}>
    <input {...register("email", { required: true })} />
    <input {...register("password", { required: true })} />
    <button type="submit">Login</button>
  </form>
  ```

---

## **4. NgRx (State Management)**
NgRx is Angular's Redux-like state management library.

### **Setup**
1. Install NgRx:
   ```sh
   npm install @ngrx/store @ngrx/effects
   ```

2. Define a state:
   ```typescript
   export interface AppState {
     counter: number;
   }
   ```

3. Define an action:
   ```typescript
   export const increment = createAction('[Counter] Increment');
   ```

4. Define a reducer:
   ```typescript
   export const counterReducer = createReducer(
     initialState,
     on(increment, (state) => ({ ...state, counter: state.counter + 1 }))
   );
   ```

5. Select data in a component:
   ```typescript
   export class CounterComponent {
     counter$ = this.store.select('counter');

     constructor(private store: Store<AppState>) {}

     increment() {
       this.store.dispatch(increment());
     }
   }
   ```

**React Comparison:**
- Similar to Redux with Redux Toolkit.

---

# **For Corporate Use Cases**  

This guide provides deep insights into Angular 15 with **advanced corporate-level examples** and best practices.  

---

## **1. Advanced Component Interaction Patterns**  

Angular applications often require complex component interactions, including:  
- **Parent to Child (@Input)**
- **Child to Parent (@Output & EventEmitter)**
- **Shared Service (RxJS BehaviorSubject)**
- **Content Projection (ng-content)**
- **ViewChild & ViewChildren**
- **Dependency Injection for Cross-Module Communication**

---

### **1.1 Parent to Child (@Input) - Nested Components in Large Applications**  

Use case: Passing configuration settings from a dashboard to multiple widgets.

```typescript
// dashboard.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-dashboard',
  template: `
    <app-widget [config]="widgetConfig"></app-widget>
  `,
})
export class DashboardComponent {
  widgetConfig = { theme: 'dark', refreshRate: 30 };
}
```

```typescript
// widget.component.ts
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-widget',
  template: `<p>Theme: {{ config?.theme }}</p>`,
})
export class WidgetComponent {
  @Input() config!: { theme: string; refreshRate: number };
}
```

**Corporate Best Practice:** Use interfaces for type safety.  

```typescript
export interface WidgetConfig {
  theme: string;
  refreshRate: number;
}
```

---

### **1.2 Child to Parent (@Output & EventEmitter) - Modular Forms Submission**  

Use case: Submit a child component form and notify the parent.

```typescript
// form.component.ts
import { Component, Output, EventEmitter } from '@angular/core';
import { FormGroup, FormControl, Validators } from '@angular/forms';

@Component({
  selector: 'app-user-form',
  template: `
    <form [formGroup]="userForm" (ngSubmit)="submitForm()">
      <input formControlName="name" placeholder="Name">
      <button type="submit">Submit</button>
    </form>
  `,
})
export class UserFormComponent {
  @Output() formSubmitted = new EventEmitter<{ name: string }>();

  userForm = new FormGroup({
    name: new FormControl('', Validators.required),
  });

  submitForm() {
    if (this.userForm.valid) {
      this.formSubmitted.emit(this.userForm.value);
    }
  }
}
```

```typescript
// parent.component.html
<app-user-form (formSubmitted)="handleFormSubmission($event)"></app-user-form>
<p>User: {{ userData?.name }}</p>
```

```typescript
// parent.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-parent',
  templateUrl: './parent.component.html',
})
export class ParentComponent {
  userData!: { name: string };

  handleFormSubmission(data: { name: string }) {
    this.userData = data;
  }
}
```

---

### **1.3 Shared Service for Sibling Communication**  

Use case: Syncing filters between two sibling components inside a dashboard.

```typescript
// filter.service.ts
import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class FilterService {
  private filterSubject = new BehaviorSubject<string>('All');
  currentFilter$ = this.filterSubject.asObservable();

  updateFilter(filter: string) {
    this.filterSubject.next(filter);
  }
}
```

```typescript
// filter.component.ts
import { Component } from '@angular/core';
import { FilterService } from '../services/filter.service';

@Component({
  selector: 'app-filter',
  template: `<button (click)="setFilter('Active')">Active</button>`,
})
export class FilterComponent {
  constructor(private filterService: FilterService) {}

  setFilter(filter: string) {
    this.filterService.updateFilter(filter);
  }
}
```

```typescript
// item-list.component.ts
import { Component, OnInit } from '@angular/core';
import { FilterService } from '../services/filter.service';

@Component({
  selector: 'app-item-list',
  template: `<p>Selected Filter: {{ filter }}</p>`,
})
export class ItemListComponent implements OnInit {
  filter!: string;

  constructor(private filterService: FilterService) {}

  ngOnInit() {
    this.filterService.currentFilter$.subscribe((filter) => (this.filter = filter));
  }
}
```

---

## **2. Deep Dive into Observables & RxJS**  

**Best Practices:**  
✅ Use `async` pipe instead of `.subscribe()` where possible.  
✅ Use `takeUntil()` in `ngOnDestroy()` to prevent memory leaks.  
✅ Use `switchMap` for API chaining instead of nested subscriptions.  

---

### **2.1 Handling API Calls with Observables**
Use case: Fetch user data, then load their posts.

```typescript
// user.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class UserService {
  constructor(private http: HttpClient) {}

  getUser(): Observable<any> {
    return this.http.get('https://jsonplaceholder.typicode.com/users/1');
  }

  getUserPosts(userId: number): Observable<any> {
    return this.http.get(`https://jsonplaceholder.typicode.com/posts?userId=${userId}`);
  }
}
```

```typescript
// user.component.ts
import { Component, OnInit } from '@angular/core';
import { UserService } from '../services/user.service';
import { switchMap } from 'rxjs';

@Component({
  selector: 'app-user',
  template: `<ul><li *ngFor="let post of posts">{{ post.title }}</li></ul>`,
})
export class UserComponent implements OnInit {
  posts: any[] = [];

  constructor(private userService: UserService) {}

  ngOnInit() {
    this.userService.getUser()
      .pipe(switchMap(user => this.userService.getUserPosts(user.id)))
      .subscribe(posts => (this.posts = posts));
  }
}
```

---

### **3. Enterprise-Level Reactive Forms with Dynamic Form Controls**  

Use case: Dynamic form fields in a settings page.

```typescript
// settings.component.ts
import { Component } from '@angular/core';
import { FormGroup, FormArray, FormControl } from '@angular/forms';

@Component({
  selector: 'app-settings',
  templateUrl: './settings.component.html',
})
export class SettingsComponent {
  settingsForm = new FormGroup({
    preferences: new FormArray([
      new FormControl('Dark Mode'),
      new FormControl('Notifications'),
    ]),
  });

  get preferences() {
    return this.settingsForm.get('preferences') as FormArray;
  }

  addPreference() {
    this.preferences.push(new FormControl(''));
  }
}
```

```html
<!-- settings.component.html -->
<form [formGroup]="settingsForm">
  <div formArrayName="preferences">
    <div *ngFor="let pref of preferences.controls; let i = index">
      <input [formControlName]="i">
    </div>
  </div>
  <button type="button" (click)="addPreference()">Add Preference</button>
</form>
```

---

## **4. Complete NgRx Architecture for Corporate Projects**  

✅ **Structure:** `actions`, `reducers`, `selectors`, `effects`  
✅ **Performance Optimizations:** Lazy loading state, using `@ngrx/component-store`  

```typescript
// actions.ts
import { createAction, props } from '@ngrx/store';

export const loadUsers = createAction('[User] Load Users');
export const loadUsersSuccess = createAction('[User] Load Users Success', props<{ users: any[] }>());
```

```typescript
// reducer.ts
import { createReducer, on } from '@ngrx/store';
import { loadUsersSuccess } from './actions';

export const initialState = { users: [] };

export const userReducer = createReducer(
  initialState,
  on(loadUsersSuccess, (state, { users }) => ({ ...state, users }))
);
```
