Migrate to Angular's inject() function for dependency injection in components and services.

Replace constructor injection with property initializers using inject().
For class-based dependencies (e.g., services), use:
```typescript
private readonly myService = inject(MyService);
```
For injection tokens (e.g., TOASTR_PROVIDER), always provide the type:
```typescript
private readonly toastrService: ToastrProvider = inject(TOASTR_PROVIDER);
```
Do not repeat the type for class-based dependencies.
Always use readonly for injected properties.
Remove the dependency from the constructor after migrating.
Place all inject-based properties at the top of the class (immediately after the class declaration).
Ensure all usages in the class use this.<propertyName>.
Example migration:
From:
```typescript
constructor(
  private graphqlService: SlimGraphqlService,
  @Inject(TOASTR_PROVIDER) private toastrService: ToastrProvider
) {}
```
To:
```typescript
private readonly graphqlService = inject(SlimGraphqlService);
private readonly toastrService: ToastrProvider = inject(TOASTR_PROVIDER);
```
This ensures type safety, maintainability, and consistency with modern Angular best practices.
