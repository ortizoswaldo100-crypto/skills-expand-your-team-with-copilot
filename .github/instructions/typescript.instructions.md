# TypeScript Code Quality Instructions

**Applies to:** `**/*.ts`, `**/*.tsx`

This document provides detailed code quality guidelines for TypeScript files in this repository.

## Type Safety & Declarations

- Use explicit type annotations for function parameters and return types
- Avoid `any` type - use specific types or generics instead
- Enable `strict` mode in `tsconfig.json`
- Use `unknown` instead of `any` when type is truly unknown
- Leverage TypeScript's type inference where it's clear

Example:
```typescript
// ❌ AVOID: Using any
function processData(data: any): any {
  return data.value;
}

// ✅ PREFER: Explicit types
interface DataItem {
  value: string;
  timestamp: Date;
}

function processData(data: DataItem): string {
  return data.value;
}
```

## Function Documentation

- Ensure all functions and methods have JSDoc comments explaining:
  - A brief description of what the function does
  - Parameter types and descriptions
  - Return type and description
  - Any exceptions that may be thrown
  - Example usage for complex functions

Example:
```typescript
/**
 * Calculates the total order value including tax and shipping.
 *
 * @param items - Array of items in the order
 * @param taxRate - Tax rate as a decimal (e.g., 0.08 for 8%)
 * @param shippingCost - Shipping cost in dollars
 * @returns The total order value
 * @throws {Error} If tax rate is negative or items array is empty
 *
 * @example
 * const order = new Order();
 * const total = order.calculateTotal(items, 0.08, 10.0);
 * // Returns: 108.50
 */
function calculateTotal(
  items: OrderItem[],
  taxRate: number,
  shippingCost: number
): number {
  // implementation
}
```

## Component Documentation (React/TSX)

- Document React components with JSDoc explaining props and behavior
- Use TypeScript interfaces for component props
- Provide examples of component usage

Example:
```typescript
/**
 * Displays a loading spinner with optional message.
 *
 * @param message - Optional message to display below the spinner
 * @param size - Size of the spinner ('sm' | 'md' | 'lg')
 * @param fullScreen - Whether to display spinner full screen
 *
 * @example
 * <LoadingSpinner message="Loading data..." size="md" />
 */
interface LoadingSpinnerProps {
  message?: string;
  size?: 'sm' | 'md' | 'lg';
  fullScreen?: boolean;
}

const LoadingSpinner: React.FC<LoadingSpinnerProps> = ({
  message,
  size = 'md',
  fullScreen = false,
}) => {
  // implementation
};
```

## TypeScript Best Practices

- Use interfaces for object shapes, types for unions and primitives
- Use `readonly` for immutable properties
- Use `Pick`, `Partial`, `Omit`, `Record` utility types to avoid duplication
- Use discriminated unions for type-safe pattern matching
- Avoid optional chaining (`?.`) for error handling - handle errors explicitly

Example with Discriminated Union:
```typescript
type Result<T> = 
  | { status: 'success'; data: T }
  | { status: 'error'; error: string }
  | { status: 'loading' };

function handleResult<T>(result: Result<T>): void {
  switch (result.status) {
    case 'success':
      console.log(result.data);
      break;
    case 'error':
      console.error(result.error);
      break;
    case 'loading':
      console.log('Loading...');
      break;
  }
}
```

## Naming Conventions

- Use `PascalCase` for classes and interfaces: `UserProfile`, `OrderItem`
- Use `camelCase` for variables and functions: `userName`, `calculateTotal`
- Use `UPPER_SNAKE_CASE` for constants: `DEFAULT_TAX_RATE`, `MAX_RETRIES`
- Use descriptive names that indicate purpose: `activeUsers` instead of `userList`
- Prefix boolean variables/functions with `is`, `has`, `should`: `isActive`, `hasPermission`

## React Component Guidelines

- Keep components focused on a single responsibility
- Use functional components with hooks (not class components)
- Extract complex logic into custom hooks
- Use `React.memo` for components that receive the same props frequently
- Avoid inline arrow functions in render/JSX (pass function references instead)
- Use TypeScript `as const` for action type definitions

Example:
```typescript
// Custom hook for data fetching
interface UseFetchResult<T> {
  data: T | null;
  loading: boolean;
  error: Error | null;
}

function useFetch<T>(url: string): UseFetchResult<T> {
  const [data, setData] = React.useState<T | null>(null);
  const [loading, setLoading] = React.useState(true);
  const [error, setError] = React.useState<Error | null>(null);

  React.useEffect(() => {
    // implementation
  }, [url]);

  return { data, loading, error };
}
```

## Error Handling

- Always handle errors explicitly
- Create custom error types for specific error conditions
- Use try-catch blocks for async operations
- Provide meaningful error messages to users
- Log errors for debugging but never expose sensitive information

Example:
```typescript
class ValidationError extends Error {
  constructor(message: string) {
    super(message);
    this.name = 'ValidationError';
  }
}

async function validateAndProcess(data: unknown): Promise<void> {
  try {
    if (!isValidData(data)) {
      throw new ValidationError('Invalid data format');
    }
    await processData(data);
  } catch (error) {
    if (error instanceof ValidationError) {
      console.error('Validation failed:', error.message);
    } else {
      console.error('Processing failed:', error);
    }
    throw error;
  }
}
```

## Testing Requirements

- Write unit tests for all functions and components
- Test success cases, error cases, and edge cases
- Use descriptive test names that explain what is being tested
- Aim for high code coverage (>80%)
- Use testing libraries like Jest, React Testing Library, or Vitest

Example:
```typescript
describe('calculateTotal', () => {
  it('should calculate total with tax and shipping', () => {
    const items = [{ price: 50 }, { price: 30 }];
    const result = calculateTotal(items, 0.08, 10);
    expect(result).toBe(96.4);
  });

  it('should throw error for negative tax rate', () => {
    expect(() => calculateTotal([], -0.08, 0)).toThrow(Error);
  });
});
```

## Code Organization

- Group related types and interfaces at the top of files
- Export public interfaces, hide internal types
- Use barrel exports (`index.ts`) for modules
- Keep files focused and under 300 lines
- Extract complex business logic into separate utility files

Example structure:
```typescript
// types.ts
export interface User {
  id: string;
  name: string;
  email: string;
}

// utils.ts
export function formatUserName(user: User): string {
  return `${user.name} (${user.email})`;
}

// User.tsx
import { User } from './types';
import { formatUserName } from './utils';

export const UserComponent: React.FC<{ user: User }> = ({ user }) => (
  <div>{formatUserName(user)}</div>
);

// index.ts (barrel export)
export { UserComponent } from './User';
export type { User } from './types';
```

## Performance Considerations

- Use `React.memo` to prevent unnecessary re-renders
- Use `useCallback` to memoize callbacks passed to memoized components
- Use `useMemo` to memoize expensive computations
- Avoid creating new object/array literals in render
- Use code splitting with `React.lazy` for large components

Example:
```typescript
const UserList = React.memo(({ users }: { users: User[] }) => {
  const handleClick = React.useCallback((id: string) => {
    // handle click
  }, []);

  return (
    <div>
      {users.map(user => (
        <UserItem 
          key={user.id}
          user={user}
          onSelect={handleClick}
        />
      ))}
    </div>
  );
});
```

## Async/Promise Handling

- Use `async`/`await` instead of `.then()` chains when possible
- Always handle Promise rejections
- Use `Promise.all()` for parallel operations, `Promise.race()` sparingly
- Implement timeout logic for long-running operations
- Clean up subscriptions and timers in cleanup functions

Example:
```typescript
async function fetchUserData(userId: string): Promise<User> {
  try {
    const response = await fetch(`/api/users/${userId}`);
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return await response.json();
  } catch (error) {
    console.error('Failed to fetch user:', error);
    throw error;
  }
}
```
