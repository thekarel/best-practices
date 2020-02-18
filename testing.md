## Testing

The purpose of testing is to ensure the product works according to its business purposes. It must work even after adding or removing functionality or refactoring the codebase.

Testing is crucial. It is also a convoluted topic and can quickly turn into a counterproductive time sink.

Keeping the goal of testing and the [core principles](principles.md) in mind, I suggest the following pragmatic approach.

Implement three types of automated tests, in order of importance:

* Complete end-to-end (browser/app) tests that verify the user-facing features
* Integration tests that verify the machine facing APIs
* A few developer tests (aka unit tests)

Sacrifice the lower priority tests if your resources are limited.

Treat tests as proper code, do not take shortcuts: be declarative, type-safe, lint - stick to the good practices.

Tests will usually require similar data across multiple test instances and types. Maintain a single source for the different types of test data, so that you can update it in one go. For anything non-trivial a type-safe factory is suggested (for example [factory-ts](https://www.npmjs.com/package/factory.ts)).

### End-to-end tests

E2E validates the user interface.

Put an automated, end-to-end test in place right at the beginning; make sure it validates PRs. The initial setup is the most difficult; adding or changing things will be more comfortable moving forward.

The e2e test should also be able to validate that a *deployed* version of the app works as expected: this verifies the environment, configuration and integration. You can use this before promoting code to a new environment.

Most importantly: e2e proves that the app does what it supposed to do - no other form of testing will give you that kind of confidence.

It is OK to run automated tests on production, where confidence is the most important - using dedicated test users is one way.

E2E tests should cover only high-level features derived from business requirements. These are the user journeys that interest the stakeholders and include such things as being able to register, log in, look at things in different ways and buy them.

These tests run in a browser or mobile app, without mocks.

Suggestion: put e2e tests in the UI project, next to the `src` folder:

```sh
ui/
  src/
  e2e/ # <-- here
```

### Integration tests <a name="integrationTests"></a>

Integration tests have the same aim as e2e tests: to verify the [use cases](architecture.md) are working by executing an API (REST, GraphQL, etc.).

Aim to test on real infrastructure: tools like Docker (Docker Compose) can spin up anything quickly. Mocking 3rd party services might be OK if there is no way around it, but try to avoid mocking your services.

Integration tests spin up the public interface (HTTP/GraphQL) and other resources (databases, etc.), set the configuration just like in regular deployment.

The assertions are all related to the high-level use cases: users can log in, see products, give us their money etc. Thanks to the machine-friendly API, these tests are more robust than e2e tests, so a lot more edge cases can be verified (higher coverage).

Do not auto-generate tests based on a GraphQL schema: the schema might be unfit for business purposes but still be technically valid.

Since we inject all modules that deal with the external world to the services, it's straightforward to use in-memory implementations during testing. The drawback is that someone must write these implementations and keep them up-to-date with the "real" ones - this is potentially a lot of extra effort. All in all, the gain doesn't justify the effort, in my opinion.

Suggestion: put integration tests in the public interface project, next to the `src` folder:

```sh
graph/
  src/
  features/ # <-- here
    chopChips.feature.ts
```

#### Persistence tests

I suggest to cover the [persistence modules](architecture.md) with dedicated integration tests, to gain confidence in data integrity.
The tests should prove that the specific implementation of the [persistence API](architecture.md) handles CRUD commands as expected. The benefit of having a single API is that the same test can run against multiple implementations (just inject the test subject).

### Developer tests (unit tests) <a name="unitTests"></a>

Domain logic and rules deserve high test coverage.

As for other unit tests, my advice is to use them only as aides when working on a tricky problem (hence the name "developer tests"). Test complicated algorithms, data transformations, complex logic that would be very time consuming to test using the UI only.

Do not write tests for trivial things or ones that are covered by the static checkers. Do not test 3rd party libraries.

Units of the UI are always implementation details, and we don't want to tests those (see the goal of testing). Covering UI components in unit tests (including snapshots) is very time consuming but provides little proof that the app is working, let alone it looks as it should. Fry the bigger fish first (e2e, integration).

A highly recommended tool for UI development is [Storybook](https://storybook.js.org/). It lets you work on components in isolation and verify functionality plus look and feel in various states (not an automated test in itself though).

If your tests fail, but the app is working: delete the tests. If your tests pass, but the app is not working: you're testing the wrong thing.

Code coverage is a trap. What you need is *feature coverage* but that is not coming from unit tests.

Suggestion: put unit tests (and Storybook stories) right next to the source files, so they are easy to find and add:

```sh
addRumToTea.ts
addRumToTea.test.ts
DrinkTea.tsx
DrinkTea.story.tsx
```
