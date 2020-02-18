## Modules

Use named module exports only.

```typescript
// Good
export const example = 1

// Bad
export default 1
```

It is the way modules are supposed to work: you have to decide what you call an exported entity. Calling something "default" is like saying "I can't decide what the name is, and don't care".

There are only benefits to named exports:

* the name of the module will be used consistently across the codebase.
* You can change all occurrences at once (refactoring in IDE).
* You get reliable auto-imports in IDEs, which is an enormous productivity boost.

On the other hand, there are no benefits to using `default`.

Naming exports is linked to file names too: once you decide the name of the module, the filename becomes obvious.

By default, a file is named as the export, and there is only one export in one file. It's fine to add a couple of small related exports to a module as long as they are not too long (we don't want big files). You can always refactor - with named exports moving code is easy.
