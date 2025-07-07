# Create Bun Base PRP

Generate a comprehensive Bun/TypeScript PRP for feature implementation with thorough research and validation.

## Feature: $ARGUMENTS

Create a complete PRP optimized for Bun development with TypeScript, modern JavaScript features, and performance focus.

## Research Process

During the research process, create clear tasks and spawn subagents as needed using batch tools for comprehensive investigation.

1. **Codebase Analysis in depth**
   - Search the codebase for similar Bun/TypeScript features/patterns
   - Identify necessary package.json dependencies and versions
   - Note existing TypeScript conventions and type patterns
   - Check existing test patterns with Bun test runner

2. **External Research at scale**
   - Research Bun documentation and native APIs
   - Library documentation (include specific URLs)
   - Implementation examples (GitHub/npmjs/blogs)
   - TypeScript patterns and modern JavaScript features
   - Performance optimization techniques

3. **User Clarification**
   - Ask for clarification if needed about Bun-specific requirements

## PRP Generation

Using `prp/templates/prp_base_bun.md` as template:

### Critical Context to Include

- **Bun Documentation**: URLs with specific sections
- **TypeScript Examples**: Real snippets showing type patterns
- **Performance Gotchas**: Native APIs vs Node.js compatibility
- **Type Safety**: Strict typing and validation patterns
- **Modern Features**: Latest ECMAScript and Bun capabilities

### Implementation Blueprint

- Start with TypeScript-specific pseudocode
- Reference real package.json dependencies
- Include type safety and performance considerations
- List tasks in order with Bun/TypeScript keywords

### Validation Gates (Must be Executable)

```bash
# Level 1: Type Safety and Linting
bun run typecheck
bun run lint
bun run format:check

# Level 2: Testing
bun test
bun test --watch
bun test --coverage

# Level 3: Build and Performance
bun run build
bun run bundle:analyze
```

## Output

Save as: `prp/{feature-name}-bun.md`

## Quality Checklist

- [ ] All necessary Bun/TypeScript context included
- [ ] Validation gates are executable
- [ ] References existing patterns
- [ ] Clear type safety considerations
- [ ] Performance implications noted
- [ ] Modern JavaScript features utilized

Score the PRP on a scale of 1-10 for confidence in one-pass Bun implementation success.