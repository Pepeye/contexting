# CLAUDE-BUN.md

Comprehensive development guidelines for Bun/TypeScript projects using Claude Code.

## Core Bun Development Principles

### Modern JavaScript/TypeScript
- **Type Safety First**: Leverage TypeScript for compile-time safety
- **Performance Oriented**: Use Bun's native APIs for optimal performance
- **Modern Syntax**: Embrace latest ECMAScript features
- **Zero-config Setup**: Minimize configuration overhead

### Bun-Specific Advantages
- **Fast Runtime**: Native speed with JavaScript compatibility
- **Built-in Testing**: Use Bun's integrated test runner
- **Package Management**: Fast install and resolution
- **TypeScript Support**: Native TypeScript execution

## Essential Development Commands

```bash
# Package management
bun install
bun add <package>
bun remove <package>
bun update

# Development
bun run dev
bun run build
bun run start

# Testing
bun test
bun test --watch
bun test --coverage

# Type checking
bun run typecheck
bun tsc --noEmit
```

## Project Structure

```
bun-project/
├── package.json             # Dependencies and scripts
├── bun.lockb               # Bun lock file
├── tsconfig.json           # TypeScript configuration
├── src/
│   ├── index.ts            # Entry point
│   ├── types/              # Type definitions
│   ├── utils/              # Utility functions
│   └── features/           # Feature modules
├── tests/                  # Test files
├── dist/                   # Build output
└── docs/                   # Documentation
```

## Code Quality Standards

### TypeScript Configuration

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "noEmit": true,
    "strict": true,
    "skipLibCheck": true,
    "types": ["bun-types"]
  }
}
```

### Error Handling Patterns

```typescript
import { z } from 'zod';

// Enhanced error classes with structured data
export class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly context?: Record<string, unknown>
  ) {
    super(message);
    this.name = 'AppError';
  }
}

export class ValidationError extends AppError {
  constructor(
    message: string,
    public readonly field: string,
    public readonly value?: unknown
  ) {
    super(message, 'VALIDATION_ERROR', { field, value });
    this.name = 'ValidationError';
  }
}

// Input validation with Zod
const CreateUserRequest = z.object({
  email: z.string().email(),
  name: z.string().min(2).max(50),
  password: z.string().min(8)
});

export function validateUserInput(input: unknown): CreateUserRequest {
  try {
    return CreateUserRequest.parse(input);
  } catch (error) {
    if (error instanceof z.ZodError) {
      const firstError = error.errors[0];
      throw new ValidationError(
        `Invalid ${firstError.path.join('.')}: ${firstError.message}`,
        firstError.path.join('.'),
        firstError.received
      );
    }
    throw new AppError('Unknown validation error', 'VALIDATION_ERROR');
  }
}

// Result pattern with enhanced error handling
export type Result<T, E = AppError> = 
  | { success: true; data: T }
  | { success: false; error: E };

export async function safeOperation<T>(
  operation: () => Promise<T>
): Promise<Result<T>> {
  try {
    const data = await operation();
    return { success: true, data };
  } catch (error) {
    return { 
      success: false, 
      error: error instanceof AppError ? error : new AppError(
        error instanceof Error ? error.message : String(error),
        'UNKNOWN_ERROR'
      )
    };
  }
}
```

### Type Safety Patterns

```typescript
// Strict type definitions
export interface User {
  readonly id: string;
  readonly name: string;
  readonly email: string;
  readonly createdAt: Date;
}

// Branded types for type safety
type UserId = string & { readonly __brand: 'UserId' };
type Email = string & { readonly __brand: 'Email' };

// Type guards
export function isValidEmail(value: string): value is Email {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(value);
}

// Utility types
export type CreateUserRequest = Omit<User, 'id' | 'createdAt'>;
export type UpdateUserRequest = Partial<Pick<User, 'name' | 'email'>>;
```

## Bun-Specific Patterns

### Async Processing with Rate Limiting

```typescript
// Rate-limited async processing
class RateLimiter {
  private queue: Array<() => void> = [];
  private running = 0;

  constructor(private maxConcurrent: number = 10) {}

  async acquire(): Promise<void> {
    return new Promise((resolve) => {
      if (this.running < this.maxConcurrent) {
        this.running++;
        resolve();
      } else {
        this.queue.push(() => {
          this.running++;
          resolve();
        });
      }
    });
  }

  release(): void {
    this.running--;
    if (this.queue.length > 0) {
      const next = this.queue.shift()!;
      next();
    }
  }
}

const rateLimiter = new RateLimiter(5);

export async function processItems(items: Item[]): Promise<ProcessedItem[]> {
  return Promise.all(
    items.map(async (item) => {
      await rateLimiter.acquire();
      try {
        return await processItem(item);
      } finally {
        rateLimiter.release();
      }
    })
  );
}
```

### File System Operations

```typescript
// Using Bun's file API with error handling
export async function readJsonFile<T>(path: string): Promise<Result<T>> {
  return safeOperation(async () => {
    const file = Bun.file(path);
    
    if (!(await file.exists())) {
      throw new AppError(`File not found: ${path}`, 'FILE_NOT_FOUND');
    }
    
    const content = await file.json();
    return content as T;
  });
}

export async function writeJsonFile(path: string, data: unknown): Promise<Result<void>> {
  return safeOperation(async () => {
    const jsonString = JSON.stringify(data, null, 2);
    await Bun.write(path, jsonString);
  });
}

// Efficient file processing with streaming
export async function processLargeFile(path: string): Promise<Result<ProcessResult>> {
  return safeOperation(async () => {
    const file = Bun.file(path);
    const stream = file.stream();
    const reader = stream.getReader();
    
    let processed = 0;
    const decoder = new TextDecoder();
    
    try {
      while (true) {
        const { done, value } = await reader.read();
        if (done) break;
        
        const chunk = decoder.decode(value, { stream: true });
        processed += await processChunk(chunk);
      }
      
      return { totalProcessed: processed };
    } finally {
      reader.releaseLock();
    }
  });
}
```

### HTTP Server with Bun

```typescript
import { serve } from 'bun';

const server = serve({
  port: 3000,
  async fetch(request) {
    const url = new URL(request.url);
    
    if (url.pathname === '/api/health') {
      return new Response(JSON.stringify({ status: 'ok' }), {
        headers: { 'Content-Type': 'application/json' },
      });
    }
    
    if (url.pathname.startsWith('/api/')) {
      return await handleApiRequest(request);
    }
    
    return new Response('Not Found', { status: 404 });
  },
});

console.log(`Server running on http://localhost:${server.port}`);
```

### Database with Bun

```typescript
import { Database } from 'bun:sqlite';

export class UserService {
  private db: Database;

  constructor(dbPath: string) {
    this.db = new Database(dbPath);
    this.initDatabase();
  }

  private initDatabase(): void {
    this.db.run(`
      CREATE TABLE IF NOT EXISTS users (
        id TEXT PRIMARY KEY,
        name TEXT NOT NULL,
        email TEXT UNIQUE NOT NULL,
        created_at INTEGER NOT NULL
      )
    `);
  }

  async createUser(user: CreateUserRequest): Promise<User> {
    const id = crypto.randomUUID();
    const createdAt = Date.now();

    const query = this.db.prepare(`
      INSERT INTO users (id, name, email, created_at)
      VALUES (?, ?, ?, ?)
    `);

    query.run(id, user.name, user.email, createdAt);

    return {
      id,
      name: user.name,
      email: user.email,
      createdAt: new Date(createdAt),
    };
  }

  async getUserById(id: string): Promise<User | null> {
    const query = this.db.prepare('SELECT * FROM users WHERE id = ?');
    const row = query.get(id) as any;

    if (!row) return null;

    return {
      id: row.id,
      name: row.name,
      email: row.email,
      createdAt: new Date(row.created_at),
    };
  }
}
```

## Testing Patterns

### Unit Tests with Bun

```typescript
import { test, expect, describe, beforeEach } from 'bun:test';
import { UserService } from './user-service';

describe('UserService', () => {
  let userService: UserService;

  beforeEach(() => {
    userService = new UserService(':memory:');
  });

  test('should create user successfully', async () => {
    const userData = {
      name: 'John Doe',
      email: 'john@example.com',
    };

    const user = await userService.createUser(userData);

    expect(user.id).toBeDefined();
    expect(user.name).toBe(userData.name);
    expect(user.email).toBe(userData.email);
    expect(user.createdAt).toBeInstanceOf(Date);
  });

  test('should return null for non-existent user', async () => {
    const user = await userService.getUserById('non-existent');
    expect(user).toBeNull();
  });
});
```

### Integration Tests

```typescript
import { test, expect } from 'bun:test';

test('API endpoint integration', async () => {
  const response = await fetch('http://localhost:3000/api/users', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      name: 'Test User',
      email: 'test@example.com',
    }),
  });

  expect(response.status).toBe(201);

  const user = await response.json();
  expect(user.id).toBeDefined();
  expect(user.name).toBe('Test User');
});
```

## Validation Gates

### Level 1: TypeScript and Linting
```bash
bun run typecheck              # TypeScript compilation
bun run lint                   # ESLint checking
bun run format:check           # Prettier formatting
```

### Level 2: Testing
```bash
bun test                       # All tests
bun test --watch               # Watch mode
bun test --coverage            # Coverage report
```

### Level 3: Build and Performance
```bash
bun run build                  # Production build
bun run bundle:analyze         # Bundle analysis
bun run perf                   # Performance tests
```

## Package.json Scripts

```json
{
  "scripts": {
    "dev": "bun run --watch src/index.ts",
    "build": "bun build src/index.ts --outdir dist --target bun",
    "start": "bun dist/index.js",
    "test": "bun test",
    "test:watch": "bun test --watch",
    "test:coverage": "bun test --coverage",
    "typecheck": "bun tsc --noEmit",
    "lint": "eslint src/**/*.ts",
    "lint:fix": "eslint src/**/*.ts --fix",
    "format": "prettier --write src/**/*.ts",
    "format:check": "prettier --check src/**/*.ts"
  }
}
```

## Anti-Patterns to Avoid

### TypeScript Anti-Patterns
- ❌ Using `any` type without justification
- ❌ Ignoring TypeScript errors with `@ts-ignore`
- ❌ Not using strict mode in TypeScript
- ❌ Mixing CommonJS and ES modules
- ❌ Not handling promise rejections

### Performance Anti-Patterns
```typescript
// Bad: Synchronous file operations
const data = Bun.file('large-file.json').text(); // Blocks

// Good: Asynchronous operations
const data = await Bun.file('large-file.json').text();

// Bad: Not using Bun's native APIs
import fs from 'fs/promises';
const content = await fs.readFile('file.txt', 'utf-8');

// Good: Using Bun's optimized file API
const content = await Bun.file('file.txt').text();
```

## Environment Configuration

```typescript
// env.ts - Type-safe environment variables
import { z } from 'zod';

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']).default('development'),
  PORT: z.coerce.number().default(3000),
  DATABASE_URL: z.string().url(),
  API_KEY: z.string().min(1),
});

export const env = envSchema.parse(process.env);
```

## PRP Integration

When creating Bun PRPs:
1. Always include `package.json` dependencies
2. Specify TypeScript configuration requirements
3. Include Bun-specific API usage examples
4. Document any Node.js compatibility requirements
5. Provide comprehensive type definitions

### Validation Template

```bash
# Include in every Bun PRP
bun install
bun run typecheck
bun run lint
bun test
bun run build
```

This ensures consistent, type-safe, and performant Bun/TypeScript code across all projects.