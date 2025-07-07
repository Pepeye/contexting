# Create Bun PRP

Generate a comprehensive Bun/TypeScript PRP for feature implementation.

## Feature: $ARGUMENTS

Create a complete PRP optimized for Bun development with TypeScript and modern JavaScript.

## Process

1. **Research Bun patterns** in the codebase
2. **Use prp/templates/prp_base_bun.md** template
3. **Include package.json context** and dependencies
4. **Add Bun-specific validation** (bun test, typecheck, lint)

## Validation Gates

```bash
# Bun validation pipeline
bun run typecheck
bun test
bun run lint
bun run build
```

Save as: `prp/{feature-name}-bun.md`