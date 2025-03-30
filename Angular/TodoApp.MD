# To-Do App with AngularJS, Tailwind CSS, and LocalStorage

## Table of Contents
1. Introduction
2. Project Setup
3. Folder Structure
4. Components Overview
5. Services and State Management (RxJS & NgRx)
6. Lifecycle Hooks in Action
7. Handling UI with Tailwind CSS
8. Toastr for Notifications
9. Drag-and-Drop Functionality
10. Unit Testing with Karma & Jasmine
11. Conclusion

---

## 1. Introduction
This document provides a complete guide to building a To-Do app using AngularJS, Tailwind CSS, and LocalStorage, covering key Angular concepts like RxJS, NgRx, Services, Component Interaction, Lifecycle Hooks, and Unit Testing.

---

## 2. Project Setup

### Install Angular CLI
```sh
npm install -g @angular/cli
```

### Create an Angular Project
```sh
ng new angular-todo-app
cd angular-todo-app
```

### Install Dependencies
```sh
npm install tailwindcss postcss autoprefixer @ngrx/store @ngrx/effects rxjs ngx-toastr
```

### Configure Tailwind CSS
Create a `tailwind.config.js` file:
```js
module.exports = {
  content: ["./src/**/*.{html,ts}"],
  theme: { extend: {} },
  plugins: [],
};
```
Import Tailwind in `src/styles.css`:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

## 3. Folder Structure
```
angular-todo-app/
├── src/
│   ├── app/
│   │   ├── components/
│   │   │   ├── todo-list/
│   │   │   ├── todo-item/
│   │   ├── services/
│   │   ├── store/
│   ├── assets/
│   ├── styles.css
```

---

## 4. Components Overview

### `TodoListComponent`
```ts
@Component({ selector: 'app-todo-list', templateUrl: './todo-list.component.html' })
export class TodoListComponent {
  @Input() tasks: Task[] = [];
  @Output() taskUpdated = new EventEmitter<Task>();
}
```

### `TodoItemComponent`
```ts
@Component({ selector: 'app-todo-item', templateUrl: './todo-item.component.html' })
export class TodoItemComponent {
  @Input() task!: Task;
  @Output() deleteTask = new EventEmitter<number>();
  @Output() toggleComplete = new EventEmitter<number>();
}
```

---

## 5. Services and State Management (RxJS & NgRx)

### `TodoService`
```ts
@Injectable({ providedIn: 'root' })
export class TodoService {
  private tasksSubject = new BehaviorSubject<Task[]>([]);
  tasks$ = this.tasksSubject.asObservable();

  addTask(task: Task) { /* Logic to update tasks */ }
  deleteTask(id: number) { /* Logic to delete a task */ }
}
```

### NgRx Store Setup
```ts
export interface AppState { tasks: Task[]; }
export const initialState: AppState = { tasks: [] };
```

---

## 6. Lifecycle Hooks in Action

### `ngOnInit`
```ts
ngOnInit() {
  this.todoService.tasks$.subscribe(tasks => this.tasks = tasks);
}
```

### `ngOnDestroy`
```ts
ngOnDestroy() {
  this.subscription.unsubscribe();
}
```

---

## 7. Handling UI with Tailwind CSS
```html
<button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
  Add Task
</button>
```

---

## 8. Toastr for Notifications

### Install ngx-toastr
```sh
npm install ngx-toastr
```

### Usage in a Component
```ts
constructor(private toastr: ToastrService) {}
showSuccess() { this.toastr.success('Task Added!'); }
```

---

## 9. Drag-and-Drop Functionality

### Install Angular CDK
```sh
npm install @angular/cdk
```

### Implement Drag & Drop
```html
<div cdkDropList (cdkDropListDropped)="drop($event)">
  <div *ngFor="let task of tasks" cdkDrag>{{ task.title }}</div>
</div>
```

```ts
import { CdkDragDrop, moveItemInArray } from '@angular/cdk/drag-drop';

drop(event: CdkDragDrop<string[]>) {
  moveItemInArray(this.tasks, event.previousIndex, event.currentIndex);
}
```

---

## 10. Unit Testing with Karma & Jasmine

### Test a Component
```ts
describe('TodoComponent', () => {
  let component: TodoComponent;
  let fixture: ComponentFixture<TodoComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({ declarations: [TodoComponent] }).compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(TodoComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });
});
```

---

## 11. Conclusion
This project implements all core Angular concepts with best practices, covering Services, RxJS, NgRx, Component Communication, Lifecycle Hooks, Tailwind UI, Toastr notifications, Drag-and-Drop, and Unit Testing.