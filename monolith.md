## Monolith

Start building a monolith and not a microservice ecosystem. A microservice setup is an optimization, with its inherent costs, complexity and [some hard problems](https://www.youtube.com/watch?v=STKCRSUsyP0). Leave it to later, when there is a real need. You might never get to that point.

It is much easier to carve out truly independent slices once the team knows more about the system. Having a domain-driven, layered architecture makes this transition more comfortable by the way and can answer questions about responsibilities.

In case a service is extracted later, it should adhere to all the present recommendations: it will be an application in its own right.
