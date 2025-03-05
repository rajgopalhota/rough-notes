# **AngularJS & Modern Angular (Angular 14+) - Expert-Level Notes**

---

## **1. Introduction to AngularJS**
AngularJS (released in 2010) is a JavaScript framework developed by Google for building dynamic, single-page applications (SPAs). It follows the **MVC (Model-View-Controller)** architecture and uses **two-way data binding** to synchronize data between the model and the UI automatically.

### **Key Features of AngularJS:**
- **Two-way data binding**: Changes in the model reflect in the view and vice versa.
- **Directives**: Custom HTML attributes (`ng-model`, `ng-repeat`, `ng-show`, etc.).
- **Dependency Injection (DI)**: Automatically injects services into components.
- **MVC Architecture**: Separates concerns into Model, View, and Controller.
- **Filters & Expressions**: Used for data transformation (`{{ data | uppercase }}`).
- **Routing**: Uses `ngRoute` module for single-page application navigation.

### **Basic AngularJS Example**
```html
<!DOCTYPE html>
<html ng-app="myApp">
<head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.8.2/angular.min.js"></script>
</head>
<body ng-controller="MainController">
    <h1>{{ message }}</h1>
    <input type="text" ng-model="message">
    <ul>
        <li ng-repeat="item in items">{{ item }}</li>
    </ul>
</body>

<script>
    var app = angular.module('myApp', []);
    app.controller('MainController', function($scope) {
        $scope.message = "Hello, AngularJS!";
        $scope.items = ["Item 1", "Item 2", "Item 3"];
    });
</script>
</html>
```

---

## **2. Angular (Modern Version)**
Angular (from Angular 2 onwards) is a complete **TypeScript-based** rewrite of AngularJS. It follows **Component-Based Architecture** and brings features like better performance, modularization, and reactive programming.

### **Key Features of Modern Angular (Angular 14+):**
- **Component-based architecture** (instead of Controllers & Directives in AngularJS)
- **Reactive Forms & Template-driven Forms** for handling user input
- **TypeScript support** for better tooling and maintainability
- **RxJS (Reactive Extensions for JavaScript)** for reactive programming
- **Ahead-of-Time (AOT) Compilation** for better performance
- **Ivy Rendering Engine** (introduced in Angular 9) for smaller bundle sizes and faster execution
- **Standalone Components** (introduced in Angular 14) to remove the need for `NgModule`
- **Improved Routing and State Management** using Angular Router and NgRx.

---

## **3. Key Differences: AngularJS vs. Modern Angular**
| Feature          | AngularJS (1.x) | Angular (2+) |
|-----------------|----------------|--------------|
| Language        | JavaScript      | TypeScript   |
| Architecture    | MVC             | Component-based |
| Data Binding    | Two-way binding | One-way & Two-way binding |
| Rendering       | Uses `$digest` cycles | Uses Virtual DOM |
| Forms           | `ng-model` for binding | Reactive & Template-driven Forms |
| Dependency Injection | Basic DI       | Advanced DI with Hierarchical Scopes |
| Performance     | Slower, more watchers | Faster due to AOT and Ivy |
| Mobile Support  | No built-in support | Optimized for mobile |
| Routing        | `ngRoute` or UI-Router | `@angular/router` |
| Build & Tooling | No CLI, manual setup | Uses Angular CLI |

---

## **4. Comparison of Angular 14 vs. New Angular Versions (Angular 16/17)**
| Feature          | Angular 14 | Angular 16/17 |
|-----------------|-----------|--------------|
| Standalone Components | Introduced | Improved and Default |
| Signals API (State Mgmt) | Not Available | Introduced in Angular 16 |
| SSR (Server-Side Rendering) | Partial | Fully Optimized |
| Hydration (for performance) | Not Available | Introduced in Angular 16 |
| Form Control API | Traditional Forms | Streamlined with Signals |
| Build Performance | Faster with Ivy | Faster with ESBuild & Vite |
| RxJS Version | RxJS 7 | RxJS 8 (improved API) |

---

## **5. Angular 14+ Code Snippets**
### **1. Creating a Standalone Component (Angular 14+)**
```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-hello',
  standalone: true,
  template: `<h1>Hello, Angular 14+</h1>`
})
export class HelloComponent {}
```
**(No need for `NgModule`)**

---

### **2. Angular 16+ - Signals API Example**
Angular 16 introduced **Signals**, a new reactivity model:
```typescript
import { signal } from '@angular/core';

export class CounterComponent {
  count = signal(0);

  increment() {
    this.count.set(this.count() + 1);
  }
}
```
**Benefits of Signals:** More optimized than RxJS Observables for state management.

---

### **3. Angular Forms in Modern Angular**
#### **Reactive Forms Example**
```typescript
import { Component } from '@angular/core';
import { FormControl, FormGroup } from '@angular/forms';

@Component({
  selector: 'app-form',
  template: `
    <form [formGroup]="userForm">
      <input formControlName="name" placeholder="Enter name">
      <button (click)="submit()">Submit</button>
    </form>
  `
})
export class FormComponent {
  userForm = new FormGroup({
    name: new FormControl('')
  });

  submit() {
    console.log(this.userForm.value);
  }
}
```

---

## **6. Modern Angular Tools & Optimization**
### **1. Angular CLI (Command Line Interface)**
- Install Angular CLI:
  ```bash
  npm install -g @angular/cli
  ```
- Create a new project:
  ```bash
  ng new my-app
  ```
- Serve the application:
  ```bash
  ng serve
  ```
- Build for production:
  ```bash
  ng build --prod
  ```

---

## **7. Routing in Angular (Modern Approach)**
Angular uses `@angular/router` for routing.
### **Basic Routing Example**
```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home.component';
import { AboutComponent } from './about.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {}
```

---

## **8. Lifecycle Hooks in Modern Angular**
| Hook             | Description |
|-----------------|-------------|
| `ngOnInit()`    | Runs after component initializes |
| `ngOnChanges()` | Runs when input properties change |
| `ngDoCheck()`   | Custom change detection |
| `ngAfterViewInit()` | Runs after view initialization |
| `ngOnDestroy()` | Cleanup logic when component is destroyed |

### **Lifecycle Hook Example**
```typescript
import { Component, OnInit, OnDestroy } from '@angular/core';

@Component({
  selector: 'app-demo',
  template: `<h1>Hello Lifecycle Hooks</h1>`
})
export class DemoComponent implements OnInit, OnDestroy {
  ngOnInit() {
    console.log("Component Initialized");
  }

  ngOnDestroy() {
    console.log("Component Destroyed");
  }
}
```

---

## **9. Best Practices in Angular**
- **Use Standalone Components** (Angular 14+) instead of traditional `NgModule`
- **Lazy Load Modules** to improve performance
- **Use Signals for State Management** (Angular 16+)
- **Optimize Change Detection** with `OnPush` strategy
- **Use Angular CLI** for consistent project structure
- **Avoid Direct DOM Manipulation** (Use Angular's Renderer2 API)

---

## **10. Conclusion**
- AngularJS is outdated and should be migrated to **modern Angular**.
- **Angular 14+** introduced **Standalone Components**, **better forms**, and **performance improvements**.
- **Angular 16/17** brings **Signals API**, **Hydration**, and **faster SSR**.
- Learning **modern Angular** will help in building scalable and performant applications.


