## UI

From an architectural point of view, the user interface is a client to the public HTTP layer - and mostly talks the language of the HTTP layer.

### UI and HTTP

It is OK to import domain modules into the UI layer (but not others, obviously).

Once GraphQL is introduced into the stack, the graph types might be slightly different from the domain types, causing annoying issues. For this reason, it's best to stick to the graph's language here.

Use a [type generator](https://github.com/dotansimha/graphql-code-generator) to minimise effort. These can turn graph types into static type definitions for the UI, resulting in end-to-end type safety.

### Declarative UI

There are many great UI libraries and frameworks today. React is suggested here because it's a *[declarative](../declarative.md)* and *functional* solution.

See [thekarel/best-practices-example](https://github.com/thekarel/best-practices-example) for examples.
