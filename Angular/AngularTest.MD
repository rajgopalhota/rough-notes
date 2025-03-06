# **Angular Testing with Use Cases & Code Coverage**

## **1. Introduction to Angular Testing**
Angular applications require testing to ensure correctness, maintainability, and performance. The key aspects of Angular testing include:
- **Unit Testing**: Tests individual components, services, pipes, or directives.
- **Integration Testing**: Verifies the interaction between multiple components.
- **End-to-End (E2E) Testing**: Tests user interactions through tools like Cypress or Playwright.
- **Code Coverage**: Measures the extent of tested code to identify untested areas.

---

## **2. Code Coverage in Angular**
**Code coverage** refers to the percentage of application code covered by tests. It includes:
- **Statement Coverage**: Ensures every statement executes.
- **Branch Coverage**: Ensures all conditional branches are tested.
- **Function Coverage**: Ensures all functions execute.
- **Line Coverage**: Ensures every line of code executes.

### **Running Code Coverage**
```sh
ng test --code-coverage
```
This generates an HTML report in `coverage/` showing tested and untested parts.

---

## **3. Component Testing Use Cases**
### **Use Case 1: Basic Component Rendering**
#### **Scenario**: Verify that a component renders without errors.

#### **Test Code**
```ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { MyComponent } from './my.component';

describe('MyComponent', () => {
  let component: MyComponent;
  let fixture: ComponentFixture<MyComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [MyComponent],
    }).compileComponents();

    fixture = TestBed.createComponent(MyComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
  });

  it('should create the component', () => {
    expect(component).toBeTruthy();
  });
});
```
**Code Coverage Impact**: Ensures the component initializes properly.

---

### **Use Case 2: Testing Component Inputs**
#### **Scenario**: Ensure that component receives and displays input data.

#### **Test Code**
```ts
@Component({
  selector: 'app-child',
  template: `<p>{{ message }}</p>`,
})
export class ChildComponent {
  @Input() message!: string;
}
```
```ts
describe('ChildComponent', () => {
  let component: ChildComponent;
  let fixture: ComponentFixture<ChildComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [ChildComponent],
    }).compileComponents();

    fixture = TestBed.createComponent(ChildComponent);
    component = fixture.componentInstance;
  });

  it('should display input message', () => {
    component.message = 'Hello';
    fixture.detectChanges();
    const compiled = fixture.nativeElement as HTMLElement;
    expect(compiled.querySelector('p')?.textContent).toContain('Hello');
  });
});
```
**Code Coverage Impact**: Covers input property binding.

---

### **Use Case 3: Testing Component Outputs**
#### **Scenario**: Ensure the component emits an event when a button is clicked.

#### **Test Code**
```ts
@Component({
  selector: 'app-child',
  template: `<button (click)="sendMessage()">Send</button>`,
})
export class ChildComponent {
  @Output() messageSent = new EventEmitter<string>();
  
  sendMessage() {
    this.messageSent.emit('Test Message');
  }
}
```
```ts
describe('ChildComponent', () => {
  let component: ChildComponent;
  let fixture: ComponentFixture<ChildComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [ChildComponent],
    }).compileComponents();

    fixture = TestBed.createComponent(ChildComponent);
    component = fixture.componentInstance;
  });

  it('should emit an event on button click', () => {
    spyOn(component.messageSent, 'emit');
    const button = fixture.nativeElement.querySelector('button');
    button.click();
    expect(component.messageSent.emit).toHaveBeenCalledWith('Test Message');
  });
});
```
**Code Coverage Impact**: Covers event emission behavior.

---

## **4. Service Testing Use Cases**
### **Use Case 4: Testing a Simple Service**
#### **Scenario**: Verify a service returns expected data.

#### **Test Code**
```ts
@Injectable({ providedIn: 'root' })
export class MyService {
  getData() {
    return 'Test Data';
  }
}
```
```ts
describe('MyService', () => {
  let service: MyService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(MyService);
  });

  it('should return expected data', () => {
    expect(service.getData()).toBe('Test Data');
  });
});
```
**Code Coverage Impact**: Covers function execution.

---

### **Use Case 5: Testing HTTP Service**
#### **Scenario**: Mock an API call using `HttpClientTestingModule`.

#### **Test Code**
```ts
@Injectable({ providedIn: 'root' })
export class ApiService {
  constructor(private http: HttpClient) {}

  fetchData() {
    return this.http.get('https://api.example.com/data');
  }
}
```
```ts
describe('ApiService', () => {
  let service: ApiService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [ApiService],
    });

    service = TestBed.inject(ApiService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  it('should fetch data from API', () => {
    const mockData = [{ id: 1, name: 'Test' }];

    service.fetchData().subscribe((data) => {
      expect(data).toEqual(mockData);
    });

    const req = httpMock.expectOne('https://api.example.com/data');
    expect(req.request.method).toBe('GET');
    req.flush(mockData);
  });

  afterEach(() => {
    httpMock.verify();
  });
});
```
**Code Coverage Impact**: Covers API interaction and request handling.

---

## **5. Directive Testing Use Cases**
### **Use Case 6: Testing a Custom Directive**
#### **Scenario**: Verify a directive changes background color.

#### **Test Code**
```ts
@Directive({
  selector: '[appHighlight]',
})
export class HighlightDirective {
  constructor(private el: ElementRef) {
    el.nativeElement.style.backgroundColor = 'yellow';
  }
}
```
```ts
describe('HighlightDirective', () => {
  let fixture: ComponentFixture<TestComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [HighlightDirective, TestComponent],
    });

    fixture = TestBed.createComponent(TestComponent);
    fixture.detectChanges();
  });

  it('should apply background color', () => {
    const p: HTMLElement = fixture.nativeElement.querySelector('p');
    expect(p.style.backgroundColor).toBe('yellow');
  });
});
```
**Code Coverage Impact**: Covers directive logic.

---

## **6. Code Coverage Best Practices**
1. **Ensure Edge Case Testing**: Cover different inputs, null values, and exceptions.
2. **Test Error Handling**: Use `catchError` for observables.
3. **Mock Dependencies**: Use `Jasmine Spies` or `HttpTestingController`.
4. **Run Tests in CI/CD**: Use `ChromeHeadless` for automated builds.
5. **Improve Coverage for Business Logic**: Focus on services, directives, and complex computations.

---

## **7. Final Thoughts**
These notes provide **comprehensive** testing strategies covering **all Angular parts**. Let me know if you need **more detailed** examples on specific scenarios!