## Developer tests (unit tests) <a name="unitTests"></a>

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
