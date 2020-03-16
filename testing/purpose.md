## Testing

The purpose of testing is to ensure the product works according to its business purposes. It must work even after adding or removing functionality or refactoring the codebase.

Testing is crucial. It is also a convoluted topic and can quickly turn into a counterproductive time sink.

Keeping the goal of testing and the [core principles](../principles/principles.md) in mind, I suggest the following pragmatic approach.

Implement three types of automated tests, in order of importance:

* Complete end-to-end (browser/app) tests that verify the user-facing features
* Integration tests that verify the machine facing APIs
* A few developer tests (aka unit tests)

Sacrifice the lower priority tests if your resources are limited.

Treat tests as proper code, do not take shortcuts: [be declarative](../declarative.md), type-safe, lint - stick to the good practices.

Tests will usually require similar data across multiple test instances and types. Maintain a single source for the different types of test data, so that you can update it in one go. For anything non-trivial a type-safe factory is suggested (for example [factory-ts](https://www.npmjs.com/package/factory.ts)).
