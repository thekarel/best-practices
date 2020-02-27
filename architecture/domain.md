## Domain

Responsibility: encapsulate business entities and core rules

The domain is the gravitational core of the ecosystem you're building, pulling in every bit of information that is not specific to a representational interface. In truth, very little - if any - knowledge about the business is coded elsewhere.

The domain contains two kinds of definitions.

First, it represents the business entity shapes as classes or types. To avoid sounding too abstract, let's imagine we're building a restaurant booking app. In this case, our business entities would be the restaurants, bookings, payments, invoices, ratings. The shape of these things will be defined only once throughout the application - here in the domain module.

Entity names start in uppercase in code: `type Restaurant`, `interface Booking`, `class Payment`. Use `interface` if you're not sure.

Second, the domain contains rules that apply to these things, no matter where they are or where actions take place (e.g. online and telephone booking). Examples for rules would be: bookings are valid only for future dates, at most three months in advance; the maximum number of guests can not exceed a limit.

At the most basic level, you can implement rules as pure functions: they take the entities and the arguments for the action. In the above example, we could send a `Booking` and the current date to a rule function, and it would return `false` if the time is too far in the future.

When looking at the domain module one needs to be able to understand what kind of "things" are there in the system, how they look like, what kind of properties they can have.

The domain module is the central source of truth, and this aligns with the principle of [solving business problems first](../principles/business.md).

The domain will not import anything from the other layers. Its API is used almost exclusively by the services.
