---
mode: 'ask'
description: 'Review Angular/TypeScript PR for code quality, maintainability, and adherence to project standards'
---

# Pull Request Review

Review the changes in this PR for the following:

- **Angular & TypeScript Best Practices:**
  Ensure code follows [copilot-instructions.md](../angular.instructions.md) for maintainability, performance, and accessibility.

- **Component Guidelines:**
  - Standalone components (`standalone: true`)
  - `ChangeDetectionStrategy.OnPush`
  - Use `inject()` for dependencies
  - Use new control flow syntax (`@if`, `@for`, `@switch`, `@defer`)
  - Signal-based inputs/outputs and queries
  - Host bindings/listeners via signals
  - No direct DOM manipulation

- **Service Guidelines:**
  - Tree-shakable (`providedIn: 'root'`)
  - Use `inject()` for dependencies

- **State Management:**
  - Prefer Angular Signals for local state
  - Use NgRx SignalStore for global/complex state
  - Pure selectors, descriptive actions, effects for side effects only

- **RxJS Usage:**
  - Correct operator usage (`switchMap`, `exhaustMap`, etc.)
  - Proper cleanup (`takeUntilDestroyed(this.destroyRef)`, `async` pipe)
  - Avoid duplicate API calls
  - Graceful error handling

- **Templates:**
  - Simple, minimal logic
  - Accessibility and ARIA attributes
  - Pure pipes for heavy computations

- **Signals:**
  - Typed signals for reactive state
  - Computed signals for derived values
  - Effects instead of lifecycle hooks
  - Signal-based host bindings/listeners

- **Testing & Quality:**
  - Arrange-Act-Assert pattern
  - Sufficient test coverage
  - Signals/effects tested with Angular utilities

- **Performance & Security:**
  - OnPush, signals, pure pipes
  - No direct DOM manipulation
  - Content sanitized to prevent XSS

Reference: [copilot-instructions.md](../angular.instructions.md)

Leave actionable feedback for any deviations.
