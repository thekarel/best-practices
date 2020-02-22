## Integration tests

Integration tests have the same aim as e2e tests: to verify the [use cases](../architecture/services.md) are working by executing an API (REST, GraphQL, etc.).

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

I suggest to cover the [persistence modules](../architecture/services.md) with dedicated integration tests, to gain confidence in data integrity.
The tests should prove that the specific implementation of the [persistence API](../architecture/persistence.md#persistenceApi) handles CRUD commands as expected. The benefit of having a single API is that the same test can run against multiple implementations (just inject the test subject).
