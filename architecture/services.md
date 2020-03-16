## Services

Sometimes called use cases, activities, domain services.

Responsibility: encapsulate application-specific use cases and rules

Services add activity to the [domain module](domain.md). They are the affordances of the application, i.e. the "what can you do" with the app. They cover both the customer-facing and administrative activities.

The services also encapsulate the *business rules* relevant to activities.

For example, the booking app above will contain a `BookingService` class with a method to create a booking. This method then encapsulates the validation and describes the side effects (save booking, send notifications), and it should be [highly declarative](../declarative.md) and read like a recipe.

Services are best implemented as classes because they hold dependencies in the state.

Moving every interaction and rule into a single layer makes them visible, explicit and discoverable. When changes are requested, we know where to implement them.

We know what the rules and activities are at any moment because they come from a single source of truth and not sprinkled across different layers and random modules.

A user-facing layer trying to "do" something within the app must go through domain services. Only services are allowed to use a repository directly. There are - or will be - rules attached to entity manipulations, and these belong to services. Authorization is also a business rule by the way - hence it too belongs to services.

Services know nothing about the world around them. They need tools - via dependency injection - to get their job done. These tools are, for example, repositories to load and persist entity data, email sending service, etc.

Service modules will import only from the Domain modules. The service API is not exposed to end-users directly. Only interfaces like GraphQL use it.

### Services and dependencies

It is the service itself that describes the expected API of any dependency, i.e. the persistence layer. Your persistence modules will fulfil these contracts and not the other way around.
