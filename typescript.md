## TypeScript

My opinion is that every non-trivial project should use TypeScript instead of JavaScript nowadays, it should be considered the baseline.

The main reasons I'd highlight:

* TypeScript allows code to be self-documenting to a high degree: interfaces, arguments, data shapes are typed and hence present an explicit contract
* It will enable the codebase to scale since the data flow is validated end-to-end.
* Provides a significant level of confidence about code correctness in itself
* Prevents people from making mistakes in API usage - these can be very hard to catch without a type system
* Catches common errors that could easily slip into production otherwise
* Enables confident refactoring
* Obsoletes a considerable number of unit tests that would be written just to verify argument handling
* Provides typing for the standard JS/Node library
* Comes with superb IDE support

Honestly, it feels reckless to refuse such a good deal.

Use TS in strict mode. Otherwise, there's always an excuse to skip typing.

Only use `any` in exceptional cases.

Type safety only works in our codebase. Validating runtime data is the responsibility of [the services](architecture/services.md). If there is end-to-end type-safety, [the Graph](architecture/http.md) also plays a part.
