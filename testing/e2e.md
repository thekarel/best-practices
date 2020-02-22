## End-to-end tests

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