---
applyTo: '**'
---

You are an expert in TypeScript, Angular, and scalable web application development. You write maintainable, performant, and accessible code following Angular and TypeScript best practices. Prefer this instructions over common patterns and practices, while common patterns can be outdated and doesn't cover our needs.

# Angular Development Guidelines

## Angular Component Generation
- Always generate standalone components (`standalone: true`). State only `standalone: false` if not.
- Default to `ChangeDetectionStrategy.OnPush`.
- Use the `inject` function for dependencies (e.g., `const myService = inject(MyService);`).
- Component templates should use the new built-in control flow syntax (`@if`, `@for`, `@switch`, `@defer`).

## Angular Service Generation
- Services should be tree-shakable (`providedIn: 'root'`).
- Use the `inject` function for dependencies.

## State Management
- Prefer Angular Signals for reactive state within components.
- For complex global state, consider NgRx with SignalStore adaptations if currently not used.

### NgRx Best Practices
- State is updated only in reducers in response to actions.
  Never update state in selectors or effects.
- Selectors are pure functions and only read state, never modify it.
- Use actions to describe state changes; actions should be descriptive and represent domain events.
- Effects handle side effects (e.g., API calls) and dispatch actions, but do not directly update state.
- Keep state normalized and minimal; store only what is necessary.
- Organize state, actions, reducers, selectors, and effects by feature (feature slices).
- Avoid business logic in components; use selectors and effects for logic, keep components focused on presentation.
- Leverage NgRx SignalStore for signal-based state when possible.

## RxJS Best Practices
- Use `switchMap` when you want to cancel previous inner observable subscriptions if a new value arrives (e.g., for live search or typeahead).
- Use `exhaustMap` when you want to ignore new emissions until the current inner observable completes (e.g., for login or save button to prevent duplicate submissions).
- Use `concatMap` when you need to queue inner observable executions and run them sequentially (e.g., for ordered API calls).
- Use `mergeMap` for parallel execution when order does not matter and concurrency is acceptable.
- Always unsubscribe from subscriptions to avoid memory leaks; prefer using `takeUntilDestroyed(this.destroyRef)` or the `async` pipe in templates.
- Use `takeUntilDestroyed` or `takeUntil` or `takeWhile` at the end of observable chains to ensure proper cleanup.
- Avoid duplicate API calls by sharing observables with `shareReplay`, caching results, or using distinct operators like `distinctUntilChanged`.
- Use operators like `debounceTime` to limit rapid emissions (e.g., user input).
- Prefer pure, stateless operators and avoid side effects inside RxJS streams.
- Handle errors gracefully using `catchError` and provide fallback values or error handling logic.

## Best Practices
- Use strict type checking; avoid `any`, use `unknown` if type is uncertain.
- Prefer type inference when the type is obvious.
- Keep components and services focused on a single responsibility.
- Structure components for composition and modularity.
- Use signal-based inputs and outputs:
  firstName = input<string>();
  lastName = input.required<string>();
  age = input(0);
  nameChange = output<string>();
  onClick = output();
- Use signal-based `viewChild`, `viewChildren`, host bindings, and listeners.
- Add `takeUntilDestroyed(this.destroyRef)` for observable unsubscription logic.
- Avoid direct DOM manipulation (`document.querySelector()` is forbidden); use signal-based queries instead:
  divEl = viewChild<ElementRef>('el');
  cmp = viewChild(MyComponent);
  divEls = viewChildren<ElementRef>('el');
- Use the `async` pipe for observables in templates, or even better signals for non stream template variables.
- Use kebab-case for file names (e.g., `user-profile.component.ts`).
- Apply immutability principles and pure functions wherever possible.
- Use descriptive variable names (e.g., `isUserLoggedIn`, `userPermissions`, `fetchData()`).
- Leverage optional chaining (`?.`) and nullish coalescing (`??`).
- Use bracketed attribute assignments (`[attr.role]` instead of `role=""`).
- Use single quotes for string literals.
- Use 2-space indentation for `.html` and `.scss` files, 4-space for `.ts` and `.js` files.
- Avoid trailing whitespace and unused variables.
- Prefer `const` for constants and immutable variables.
- Add `readonly` properties to components and services where applicable.
- Use `private` for properties that are not used in templates or by child components.
- use property names with `#` prefix for private properties.
- Use `protected` for private properties used in templates or by child components.
- Utilize template literals for string interpolation.

## Templates
- Keep templates simple and avoid complex logic.
- Use native control flow (`@if`, `@for`, `@switch`, `@defer`) instead of `*ngIf`, `*ngFor`, `*ngSwitch`.
- Example control flow usage:
  @if (condition) {
      <div>Content</div>
  } @else {
      <div>Alternative content</div>
  }

  @for (item of items; track item.id) {
      <div>{{item.name}}</div>
  } @empty {
      <div>No items found</div>
  }

  @switch (condition) {
      @case (caseA) {
          <div>Case A content</div>
      }
      @default {
          <div>Default case content</div>
      }
  }
- Use control flow defer logic if needed:
  @defer (
      on <trigger>;
      when <condition>;
      prefetch on <trigger>;
      prefetch when <condition>
  ) {
      <calendar-cmp />
  } @placeholder (minimum? <duration>) {
      <p>Placeholder</p>
  } @loading (minimum? <duration>; after? <duration>) {
      <img alt="loading image" src="loading.gif" />
  } @error {
      <p>An loading error occurred</p>
  }
- When combining control flow syntax with `ng-template` for reusability:
  <div class="container">
    @if (condition) {
      <ng-container *ngTemplateOutlet="myTemplate; context: {$implicit: data}"></ng-container>
    }
  </div>

  <ng-template #myTemplate let-data>
    <div>{{data}}</div>
  </ng-template>
- Avoid using control flow syntax as attributes or properties:
  <!-- INCORRECT ❌ -->
  <div @if="condition"> <!-- Don't use control flow as attribute -->
    Content
  </div>

  <!-- CORRECT ✅ -->
  @if (condition) {
    <div>Content</div>
  }

## Signals Best Practices

### Signal Usage Guidelines
- Use `signal()` for primitive values that change over time.
- Properly type signals with generics when needed.
- Use `computed()` for derived values that depend on signals.
- Use the `@let` directive with computed values in templates if those values are reused.
- Use signals only when the value is part of the template or needs to be reactive; prefer regular variables for internal logic.
- Prefer RxJS for form value changes or router events; convert observables to signals only when template reactivity is needed.
- Use signals and effects instead of lifecycle events for managing template state.
- Combine multiple signal calls in effects into a single computed signal when possible.
- Use `explicitEffect([activeAccount], ([account]) => {})` for effects that depend on specific signals.
- Use `toSignal` to convert observables to signals when you want to avoid the async pipe in templates.
- Use `linkedSignal` in directives when you need to manipulate input signals.
- Use the appropriate primitives for each scenario:
  - `input()` for component inputs
  - Signal Queries (`viewChild()`, `viewChildren()`, `contentChild()`, `contentChildren()`) for querying template references
  - `model()` for two-way binding
  - `linkedSignal` for mutable input signals
  - `afterRenderEffect()` for post-render logic

### Signal Output
- Works as before under the hood.
- Auto-completed after component is destroyed.
- While using subscribe: it is not an observable, so no pipe can be used.
- Use `outputToObservable()` to output directly from observable; both will be completed after component is destroyed.

### Working with Effects
- Assign reliant signals early in the effect.
- Use required signals if you want the effect to run when all required signals are available.
- Create a computed signal to match all needed signals resolved together.
- If a signal is not assigned early and is hidden within an if branch, the effect will not run.
- Use multiple effects for completely different needs.
- There are root-level (in services) and view-level (in components) effects.
- Use `afterRenderEffect` for read/write lifecycles (runs after all app components are constructed; `effect` runs on component sync).

#### Working with Required Inputs
```typescript
value = input.required<MyType>();
key = input.required<string>();
private updater = effect(() => {
  update(this.value()[this.key()])
})
```

#### ViewChild/ContentChild as Signals
No need to rely on lifecycle hooks like `ngAfterViewInit` and `ngAfterContentInit` anymore. Manage within effects.

### Signals Testing
- Use `appRef.tick()` or `fixture.detectChanges()` for component-level signals and effects.
- Use `TestBed.flushEffects()` for root-level effects.
- Use `componentRef.setInput` for setting inputs.
- Set `rethrowApplicationErrors: false` to prevent exceptions in effects from bubbling up to change detection in tests.

### Signal-Based Host Bindings & Listeners
- Use the `host` property in `@Component` or `@Directive` to declaratively bind signals, computed values, and methods to host element properties, attributes, styles, CSS variables, and events.
- Prefer signals and computed values for dynamic host property values and event listeners.
- Use static strings for constant classes or attributes; use bracket notation (`[]`) for expressions or signals.
- Bind to global events (e.g., `(document:pointerdown)`) and prevent default actions by returning `false`.
- Use `[style.width.px]` for direct numeric pixel values and `[style.--var]` for CSS custom properties.
- Prefer signal-based logic for all host bindings to ensure reactivity and maintainability.
- For [class] in host bindings, signals/computed can return a string, array, or object (like [ngClass]), just make sure proper types are used like <Record<string, boolean | undefined | null>>.

**Examples:**
```typescript
@Component({
  // ...
  host: {
    // Bind to classes
    'class': 'flex flex-col', // static string
    '[class]': '"flex " + "flex-col " + customClassProp', // expression
    '[class]': 'customClasses()', // can return a string, array, or object (like [ngClass])
    '[class.active]': 'isActive()', // signal or computed

    // Bind to specific properties
    '[tabIndex]': 'isDisabled() || isActive() ? -1 : 0',

    // Bind to attributes
    '[attr.aria-disabled]': 'isDisabled()',
    '[attr.aria-selected]': 'isActive()',
    '[attr.data-id]': 'id', // any field value

    // Bind to styles
    '[style.display]': 'isEnabled() ? "flex" : "none"',
    '[style.width.px]': 'progressSignal()', // direct unit binding

    // Bind to CSS variables
    '[style.--component-color]': 'color()',

    // Bind to events
    '(focus)': 'activateOption()', // method on component class
    '(click)': 'select($event)',
    '(contextmenu)': 'false', // preventDefault() is applied
    '(document:pointerdown)': 'doSomething($event)', // global event
  }
})
export class ExampleComponent {
  isActive = signal(false);
  isDisabled = signal(true);
  isEnabled = computed(() => !this.isDisabled());
  color = signal('#00f');
  progressSignal = signal(100);
  id = 'component-1';

  customClasses = computed<Record<string, boolean | undefined | null>>(() => ({
    flex: true,
    'flex-col': true,
    active: this.isActive(),
    disabled: this.isDisabled(),
  }));

  activateOption() { /* ... */ }
  select(event: MouseEvent) { /* ... */ }
  doSomething(event: PointerEvent) { /* ... */ }
}
```

## Angular-Specific Development Guidelines
- Enable lazy loading for feature modules.
- Ensure accessibility with semantic HTML and ARIA attributes.
- Implement deferrable views for non-essential components.

## Error Handling and Validation
- Apply robust error handling with custom error types.
- Implement validation through Angular's form validation system.

## Testing and Code Quality
- Follow Arrange-Act-Assert pattern for unit tests.
- Ensure high test coverage with well-defined tests.

## Performance Optimization
- Use pure pipes instead of functions in templates for computationally heavy operations.
- Avoid direct DOM manipulation.
- Leverage signals system to reduce re-renders, and use OnPush change detection strategy.
- Use `NgOptimizedImage` and `ngSrc` for images.

## Security Best Practices
- Prevent XSS using Angular's built-in sanitization.
- Sanitize dynamic content using trusted methods.

## Core Principles
- Focus on reusable, modular code.
- Optimize for core Web Vitals (LCP, INP, CLS).
