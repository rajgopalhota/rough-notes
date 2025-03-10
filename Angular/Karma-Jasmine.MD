# **Angular Testing with Karma & Jasmine**

## **1. Introduction**
### **What is Karma?**
Karma is a test runner for JavaScript that runs tests in real browsers. It helps execute test cases in different environments.

### **What is Jasmine?**
Jasmine is a behavior-driven testing framework for JavaScript. It provides syntax for writing test cases.

### **Setting Up Karma and Jasmine in Angular**
Angular applications come with Karma and Jasmine by default. If not installed, run:
```bash
ng add @angular/cli
ng add @angular-devkit/build-angular
```
To run tests:
```bash
ng test
```

---

# **2. Testing Components**
### **Basic Component Test**
A component test verifies rendering and interaction logic.

#### **Example: `counter.component.ts`**
```typescript
@Component({
  selector: 'app-counter',
  template: `<button (click)="increment()">+</button> {{ count }} <button (click)="decrement()">-</button>`
})
export class CounterComponent {
  count = 0;
  increment() { this.count++; }
  decrement() { this.count--; }
}
```

#### **Test: `counter.component.spec.ts`**
```typescript
describe('CounterComponent', () => {
  let component: CounterComponent;
  let fixture: ComponentFixture<CounterComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [CounterComponent]
    }).compileComponents();

    fixture = TestBed.createComponent(CounterComponent);
    component = fixture.componentInstance;
  });

  it('should increment count', () => {
    component.increment();
    expect(component.count).toBe(1);
  });

  it('should decrement count', () => {
    component.count = 5;
    component.decrement();
    expect(component.count).toBe(4);
  });
});
```
---

# **3. Testing Services**
### **Example: `data.service.ts`**
```typescript
@Injectable({ providedIn: 'root' })
export class DataService {
  getData(): Observable<string> {
    return of('Hello World');
  }
}
```

#### **Test: `data.service.spec.ts`**
```typescript
describe('DataService', () => {
  let service: DataService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(DataService);
  });

  it('should return expected data', (done: DoneFn) => {
    service.getData().subscribe(data => {
      expect(data).toBe('Hello World');
      done();
    });
  });
});
```
---

# **4. Testing HTTP Calls**
### **Example: `api.service.ts`**
```typescript
@Injectable({ providedIn: 'root' })
export class ApiService {
  constructor(private http: HttpClient) {}

  fetchData(): Observable<any> {
    return this.http.get('/api/data');
  }
}
```

#### **Test: `api.service.spec.ts`**
```typescript
describe('ApiService', () => {
  let service: ApiService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [ApiService]
    });

    service = TestBed.inject(ApiService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  it('should fetch data from API', () => {
    const mockData = { id: 1, name: 'Test' };

    service.fetchData().subscribe(data => {
      expect(data).toEqual(mockData);
    });

    const req = httpMock.expectOne('/api/data');
    expect(req.request.method).toBe('GET');
    req.flush(mockData);
  });

  afterEach(() => {
    httpMock.verify();
  });
});
```
---

# **5. Testing Directives**
### **Example: `highlight.directive.ts`**
```typescript
@Directive({ selector: '[appHighlight]' })
export class HighlightDirective {
  constructor(private el: ElementRef) {
    this.el.nativeElement.style.backgroundColor = 'yellow';
  }
}
```

#### **Test: `highlight.directive.spec.ts`**
```typescript
describe('HighlightDirective', () => {
  let fixture: ComponentFixture<TestComponent>;

  beforeEach(() => {
    fixture = TestBed.configureTestingModule({
      declarations: [HighlightDirective, TestComponent]
    }).createComponent(TestComponent);
    fixture.detectChanges();
  });

  it('should apply yellow background', () => {
    const div = fixture.nativeElement.querySelector('div');
    expect(div.style.backgroundColor).toBe('yellow');
  });
});

@Component({ template: `<div appHighlight></div>` })
class TestComponent {}
```
---

# **6. Testing Pipes**
### **Example: `capitalize.pipe.ts`**
```typescript
@Pipe({ name: 'capitalize' })
export class CapitalizePipe implements PipeTransform {
  transform(value: string): string {
    return value.charAt(0).toUpperCase() + value.slice(1);
  }
}
```

#### **Test: `capitalize.pipe.spec.ts`**
```typescript
describe('CapitalizePipe', () => {
  let pipe: CapitalizePipe;

  beforeEach(() => {
    pipe = new CapitalizePipe();
  });

  it('should capitalize the first letter', () => {
    expect(pipe.transform('hello')).toBe('Hello');
  });
});
```
---

# **7. Mocking & Spies**
- Use **spyOn()** to mock method calls.

```typescript
spyOn(service, 'getData').and.returnValue(of('Mocked Data'));
```

- Use **fakeAsync()** and **tick()** for asynchronous testing.

```typescript
fakeAsync(() => {
  let data = '';
  service.getData().subscribe(res => data = res);
  tick();
  expect(data).toBe('Mocked Data');
});
```
---

# **8. Component Interaction Tests**
### **Parent-Child Communication**
#### **Parent Component**
```typescript
@Component({
  selector: 'app-parent',
  template: `<app-child (event)="onEvent($event)"></app-child>`
})
export class ParentComponent {
  data = '';
  onEvent(value: string) { this.data = value; }
}
```

#### **Child Component**
```typescript
@Component({
  selector: 'app-child',
  template: `<button (click)="sendData()">Send</button>`
})
export class ChildComponent {
  @Output() event = new EventEmitter<string>();
  sendData() { this.event.emit('Hello Parent'); }
}
```

#### **Test: `parent.component.spec.ts`**
```typescript
describe('ParentComponent', () => {
  let fixture: ComponentFixture<ParentComponent>;

  beforeEach(() => {
    fixture = TestBed.configureTestingModule({
      declarations: [ParentComponent, ChildComponent]
    }).createComponent(ParentComponent);
    fixture.detectChanges();
  });

  it('should receive data from child', () => {
    const child = fixture.debugElement.query(By.directive(ChildComponent)).componentInstance;
    child.event.emit('Test');
    expect(fixture.componentInstance.data).toBe('Test');
  });
});
```
---

# **9. End-to-End (E2E) Testing with Protractor**
```typescript
describe('App', () => {
  it('should display title', () => {
    browser.get('/');
    expect(element(by.css('h1')).getText()).toEqual('Welcome');
  });
});
```

---

# **10. Code Coverage**
To generate a test coverage report:
```bash
ng test --code-coverage
```
View coverage in `coverage/index.html`.

---

# **Conclusion**
- **Unit tests** for components, services, pipes, and directives.
- **Mocking HTTP calls** using `HttpTestingController`.
- **SpyOn for mocking methods**.
- **Component communication testing**.
- **E2E Testing** with Protractor.
- **Code Coverage Report** generation.

This guide provides **expert-level** Angular testing using **Karma & Jasmine**.