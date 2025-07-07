# Bun Review Staged and Unstaged Changes

Review staged and unstaged Bun/TypeScript code changes with comprehensive analysis.

## Process

1. **Analyze Changes**
   - Review git diff for staged and unstaged changes
   - Identify modified TypeScript/JavaScript files
   - Check for breaking changes or API modifications

2. **Bun-Specific Review**
   - TypeScript type safety
   - Modern JavaScript patterns
   - Bun API usage
   - Performance implications

3. **Validation Commands**

```bash
# Check staged changes
git diff --cached

# Check unstaged changes  
git diff

# Bun validation pipeline
bun run typecheck
bun test
bun run lint
bun run build
```

## Review Focus Areas

### TypeScript and Type Safety
- [ ] Proper type definitions
- [ ] No any types without justification
- [ ] Effective use of interfaces and types
- [ ] Generic types used appropriately

### Modern JavaScript/Bun Patterns
- [ ] Using latest ECMAScript features
- [ ] Leveraging Bun's native APIs
- [ ] Proper async/await usage
- [ ] Efficient error handling

### Code Quality
- [ ] Consistent code style and formatting
- [ ] Comprehensive tests
- [ ] Clear documentation
- [ ] Optimized imports and dependencies

## Validation Checklist

- [ ] TypeScript compiles: `bun run typecheck`
- [ ] Tests pass: `bun test`
- [ ] Linting passes: `bun run lint`
- [ ] Build succeeds: `bun run build`
- [ ] Code is formatted: `bun run format:check`
- [ ] No type errors or warnings