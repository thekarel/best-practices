## Software Architecture

Explicitly assign the responsibilities of the system to appropriate modules.

In a web context, an application is made up of layers, each being closer to the end-user:

* Domain (farthest from the user)
* Service
* Persistence
* HTTP
* UI (closest to the user)

The crucial rule in this context is the **arrow of dependency**. It states that a layer can never import or mention anything from another closer to the user.

This approach is a powerful concept and the key to a maintainable and scalable codebase.

My advice: never compromise on architecture. It's fine to be pragmatic and take shortcuts in any other areas. But you need a solid foundation for your system to grow and scale. It's virtually impossible to retro-fit this later.

Implementing a clean architecture is the same effort as implementing a convoluted one. It's a habit that is easy to pick up. [See an example of these concepts here](https://github.com/thekarel/best-practices-example).

The layers are [represented as modules in the workspace](monorepo.md).

Architecture is not the same as [folder structure](folderStructure.md).

### Domain <a name="domainLayer"></a>

Responsibility: encapsulate business entities and core rules

The domain is the gravitational core of the ecosystem you're building, pulling in every bit of information that is not specific to a representational interface. In truth, very little - if any - knowledge about the business is coded elsewhere.

The domain contains two kinds of definitions.

First, it represents the business entity shapes as classes or types. To avoid sounding too abstract, let's imagine we're building a restaurant booking app. In this case, our business entities would be the restaurants, bookings, payments, invoices, ratings. The shape of these things will be defined only once throughout the application - here in the domain module.

Entity names start in uppercase in code: `type Restaurant`, `interface Booking`, `class Payment`. Use `interface` if you're not sure.

Second, the domain contains rules that apply to these things, no matter where they are or where actions take place (e.g. online and telephone booking). Examples for rules would be: bookings are valid only for future dates, at most three months in advance; the maximum number of guests can not exceed a limit.

At the most basic level, you can implement rules as pure functions: they take the entities and the arguments for the action. In the above example, we could send a `Booking` and the current date to a rule function, and it would return `false` if the time is too far in the future.

When looking at the domain module one needs to be able to understand what kind of "things" are there in the system, how they look like, what kind of properties they can have.

The domain module is the central source of truth, and this aligns with the principle of solving business problems first.

The domain will not import anything from the other layers. Its API is used almost exclusively by the services.

### Services <a name="services"></a>

Sometimes called use cases, activities, domain services.

Responsibility: encapsulate application-specific use cases and rules

Services add activity to the [domain module](#domainLayer). They are the affordances of the application, i.e. the "what can you do" with the app. They cover both the customer-facing and administrative activities.

The services also encapsulate the *business rules* relevant to activities.

For example, the booking app above will contain a `BookingService` class with a method to create a booking. This method then encapsulates the validation and describes the side effects (save booking, send notifications), and it should be highly declarative and read like a recipe.

Services are best implemented as classes because they hold dependencies in the state.

Moving every interaction and rule into a single layer makes them visible, explicit and discoverable. When changes are requested, we know where to implement them.

We know what the rules and activities are at any moment because they come from a single source of truth and not sprinkled across different layers and random modules.

A user-facing layer trying to "do" something within the app must go through domain services. Only services are allowed to use a repository directly. There are - or will be - rules attached to entity manipulations, and these belong to services. Authorization is also a business rule by the way - hence it too belongs to services.

Services know nothing about the world around them. They need tools - via dependency injection - to get their job done. These tools are, for example, repositories to load and persist entity data, email sending service, etc.

Service modules will import only from the Domain modules. The service API is not exposed to end-users directly. Only interfaces like GraphQL use it.

#### Services and dependencies

It is the service itself that describes the expected API of any dependency, i.e. the persistence layer. Your persistence modules will fulfil these contracts and not the other way around.

### Persistence <a name="persistence"></a>

Responsibility: persist entities to a database/storage and retrieve them later. Often called a repository.

A few important rules apply to the persistence layer:

* they contain zero business logic
* their input is one or multiple domain entity or a domain-specific change description
* their output is one or multiple domain entity or a command feedback

Persistence modules do not create entities, that is the role of [Services](#services). There should be no "create" method in them. Persistence only stores things.

The API interface of persistence modules talks the language of the domain and never mention storage-specific information.

It is fine to use a framework, library or 3rd party service as a repository. But they have to remain hidden behind a bespoke, domain-driven API. Looking at the interface should reveal nothing about the technology behind the storage layer.

<a name='persistenceApi'></a> Add an explicit API interface type to your codebase. This way, different implementations can exist at the same time. A "real" one can save to the database of choice, another to memory for [testing](#integrationTests).

Keep in mind that the [domain services](#services) define the API contract for repository modules. There is an obvious shortcut: import the definitions from the services into persistence.

No business logic exists in the persistence layer. These modules are storage boxes. They can store an entity and later return it in a pristine state, but they will never mutate it. The way they achieve this goal is an implementation detail.

There is no validation implemented in the repository - that is business logic. The incoming payload is validated by the [services](#services).

Suggestion: use an ID generated by the domain services instead of an auto-generated one from the DB. Your IDs decouple you from database-specific ID formats. The entity shapes will be more straightforward (they always have an ID). There will be no need to change the ID field names either (from `_id` to `id` etc.).

The Persistence API is not public and will import only from the domain or service modules.

The general rules about API and separation of concerns apply to any other IO modules we need to inject into the services: email or SMS sending, logging, and so on.

### HTTP <a name="http"></a>

Responsibility: translate public API calls into domain service calls.

HTTP is the public API of the web application, the module that enables the outside world to interact with the application services.

The HTTP layer does the translation from the outside world to the domain sphere and back. If we had multiple public APIs, they would do the same job for different flavours of input (CLI, queue, etc.). It's important to move any other responsibility to the appropriate layers.

The users of our application will not interact directly with any other domain/service modules.

As this module is the gateway between the inside and outside, it will solve the injection of the [environment specific configuration](#configuration) into the application: credentials, addresses, etc.

The HTTP modules will import from all of the above layers: domain, services, persistence. During bootstrapping, repository instances (and other IO services) are created. These and remaining configuration are injected into the domain services.

GraphQL is one solution to the HTTP problem. It is not a hard requirement but offers many benefits over REST in our context. Hence HTTP will usually mean GraphQL in this document.

Unless there is an important reason to do otherwise, the type definitions of the graph layer should conform to the domain definitions. There are [tools that can help](https://typegraphql.ml/) with that, resulting in type-safe requests and responses. It's usually not possible or desirable to have the exact same types in each case, but that should be the default approach.

It's worth stressing that the types and interfaces of the HTTP layer derive from the domain models and not the other way around. In other words: the HTTP module conforms to the domain interfaces, the domain is defined first. Changes in the domain can change HTTP definitions but not the other way around. You can generate HTTP definitions from domain entities, but domain entities, by definition, can not be derived from HTTP types.

### UI

From an architectural point of view, the user interface is a client to the public HTTP layer - and mostly talks the language of the HTTP layer.

#### UI and HTTP

It is OK to import domain modules into the UI layer (but not others, obviously).

Once GraphQL is introduced into the stack, the graph types might be slightly different from the domain types, causing annoying issues. For this reason, it's best to stick to the graph's language here.

Use a [type generator](https://github.com/dotansimha/graphql-code-generator) to minimise effort. These can turn graph types into static type definitions for the UI, resulting in end-to-end type safety.

#### Declarative UI

There are many great UI libraries and frameworks today. React is suggested here because it's a *declarative* and *functional* solution.

See the [thekarel/best-practices-example](https://github.com/thekarel/best-practices-example) for examples.