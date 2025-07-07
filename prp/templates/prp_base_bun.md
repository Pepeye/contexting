name: "Bun PRP Template - Modern TypeScript Development"
description: |
  Template optimized for Bun development with TypeScript,
  leveraging Bun's native APIs and modern JavaScript features.

---

## Goal

[What needs to be built - be specific about Bun/TypeScript implementation]

## Why

- [Business value and user impact]
- [Performance benefits of Bun runtime]
- [Integration with existing TypeScript codebase]

## What

[User-visible behavior and technical requirements for Bun implementation]

### Success Criteria

- [ ] [Specific measurable outcomes]
- [ ] All tests pass with `bun test`
- [ ] TypeScript compilation successful
- [ ] No linting errors

## All Needed Context

### Documentation & References

```yaml
- url: [Bun documentation URL]
  why: [Specific Bun APIs needed]

- file: [path/to/typescript_example.ts]
  why: [Pattern to follow, TypeScript idioms]

- package: [package_name]
  version: [version]
  why: [Specific functionality needed]
```

### package.json Dependencies

```json
{
  "dependencies": {
    // Runtime dependencies
  },
  "devDependencies": {
    "@types/node": "latest",
    "typescript": "latest"
  }
}
```

### TypeScript Configuration

```typescript
// Type definitions
interface FeatureConfig {
  readonly apiUrl: string;
  readonly timeout: number;
}

// Error types
class FeatureError extends Error {
  constructor(
    message: string,
    public readonly code: string
  ) {
    super(message);
    this.name = 'FeatureError';
  }
}
```

## Implementation Blueprint

### Data Models

```typescript
// Define interfaces and types
export interface FeatureData {
  readonly id: string;
  readonly value: string;
  readonly createdAt: Date;
}

export type FeatureResult = {
  success: true;
  data: FeatureData;
} | {
  success: false;
  error: string;
};
```

### Tasks

```yaml
Task 1: CREATE src/feature/index.ts
  - DEFINE core interfaces
  - IMPLEMENT main functionality
  - ADD type guards and validators

Task 2: CREATE src/feature/feature.test.ts
  - ADD unit tests with Bun test
  - INCLUDE edge cases
```

### Implementation Pattern

```typescript
import { Database } from 'bun:sqlite';

export class FeatureService {
  constructor(private readonly config: FeatureConfig) {}

  async processFeature(input: string): Promise<FeatureResult> {
    try {
      // Validate input
      if (!input.trim()) {
        return {
          success: false,
          error: 'Input cannot be empty'
        };
      }

      // Use Bun APIs for performance
      const response = await fetch(this.config.apiUrl, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ input }),
      });

      if (!response.ok) {
        throw new FeatureError(
          `API request failed: ${response.statusText}`,
          'API_ERROR'
        );
      }

      const data = await response.json() as FeatureData;
      
      return {
        success: true,
        data
      };
    } catch (error) {
      return {
        success: false,
        error: error instanceof Error ? error.message : 'Unknown error'
      };
    }
  }
}
```

## Validation Loop

### Level 1: TypeScript & Linting

```bash
bun run typecheck             # TypeScript compilation
bun run lint                  # ESLint checking
bun run format:check          # Prettier formatting
```

### Level 2: Testing

```bash
bun test                      # All tests
bun test --watch              # Watch mode
bun test --coverage           # Coverage report
```

### Level 3: Build & Performance

```bash
bun run build                 # Production build
bun run bundle                # Bundle analysis
bun run perf                  # Performance tests
```

## Final Checklist

- [ ] All tests pass: `bun test`
- [ ] TypeScript compiles: `bun run typecheck`
- [ ] No linting errors: `bun run lint`
- [ ] Formatted correctly: `bun run format`
- [ ] Type safety maintained
- [ ] Error handling comprehensive

## Anti-Patterns to Avoid

- ❌ Don't use `any` types without justification
- ❌ Don't ignore TypeScript errors
- ❌ Don't mix promise patterns with async/await
- ❌ Don't forget to handle rejected promises
- ❌ Don't use var or ignore const/let rules