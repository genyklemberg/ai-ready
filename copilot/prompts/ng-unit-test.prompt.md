---
mode: 'ask'
description: 'Generate thorough Angular unit tests following project standards and Arrange-Act-Assert pattern'
---

# Angular Unit Test Guidelines

When creating unit tests for Angular components, services, directives, and pipes, follow these standards:

## General Principles
- Use the **Arrange-Act-Assert** pattern for all tests.
- Ensure **high coverage** for all public methods, inputs, outputs, and effects.
- Prefer **strict type checking**; avoid `any` in tests.
- Use **descriptive test names** that clearly state the scenario and expected outcome.
- Structure tests for **modularity and readability**.

## Component Tests
- Test **standalone components** with `standalone: true` and `ChangeDetectionStrategy.OnPush`.
- Use Angular's **TestBed** for setup, including signals, inputs, outputs, and host bindings.
- Prefer **signal-based testing**:
  - Use `componentRef.setInput()` for inputs.
  - Use signals and effects directly in assertions.
  - Use `fixture.detectChanges()` or `appRef.tick()` to trigger updates.
- Test **template control flow** (`@if`, `@for`, `@switch`, `@defer`) by asserting rendered DOM.
- Mock **dependencies** using Angular's dependency injection and `inject()`.

## Service Tests
- Test services as **tree-shakable** (`providedIn: 'root'`).
- Mock dependencies using Angular's `inject()` function.
- Test all **public methods** and **effects**.

## State Management
- For **Signals**:
  - Test signal updates and computed values.
  - Use effects and verify their behavior.
- For **NgRx**:
  - Test reducers for state updates.
  - Test selectors for pure, correct state selection.
  - Test effects for side effects and action dispatching.

## RxJS
- Test observable streams using **marble testing** or by subscribing and asserting emissions.
- Ensure proper **cleanup** with `takeUntilDestroyed(this.destroyRef)` or similar.
- Test error handling with `catchError`.

## Best Practices
- Avoid direct DOM manipulation in tests.
- Use Angular's **testing utilities** for signals and effects (`TestBed.flushEffects()`, etc.).
- Mock global objects and services as needed.
- Use **single responsibility** for each test case.
- Assert **accessibility** (ARIA attributes, semantic HTML) in rendered output.

## Example Test Structure

```typescript
describe('UserProfileComponent', () => {
  let fixture: ComponentFixture<UserProfileComponent>;
  let component: UserProfileComponent;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [UserProfileComponent], // standalone
      providers: [MockUserService]
    });
    fixture = TestBed.createComponent(UserProfileComponent);
    component = fixture.componentInstance;
  });

  it('should render user name with @if control flow', () => {
    component.firstName.set('John');
    fixture.detectChanges();
    const nameEl = fixture.nativeElement.querySelector('.user-name');
    expect(nameEl.textContent).toContain('John');
  });

  it('should emit nameChange output signal', () => {
    let emittedValue: string | undefined;
    component.nameChange.subscribe(value => emittedValue = value);
    component.firstName.set('Jane');
    expect(emittedValue).toBe('Jane');
  });

  it('should clean up subscriptions with takeUntilDestroyed', () => {
    // Test that subscriptions are cleaned up after destroy
    // ...
  });
});
```

## References
- [copilot-instructions.md](../copilot-instructions.md)
- [Angular Testing Guide](https://angular.io/guide/testing)

---

**Always follow these guidelines for consistent, maintainable, and robust unit tests.**
