## HTTP

Responsibility: translate public API calls into domain service calls.

HTTP is the public API of the web application, the module that enables the outside world to interact with the application services.

The HTTP layer does the translation from the outside world to the domain sphere and back. If we had multiple public APIs, they would do the same job for different flavours of input (CLI, queue, etc.). It's important to move any other responsibility to the appropriate layers.

The users of our application will not interact directly with any other domain/service modules.

As this module is the gateway between the inside and outside, it will solve the injection of the [environment specific configuration](../configuration.md) into the application: credentials, addresses, etc.

The HTTP modules will import from all of the above layers: domain, services, persistence. During bootstrapping, repository instances (and other IO services) are created. These and remaining configuration are injected into the domain services.

GraphQL is one solution to the HTTP problem. It is not a hard requirement but offers many benefits over REST in our context. Hence HTTP will usually mean GraphQL in this document.

Unless there is an important reason to do otherwise, the type definitions of the graph layer should conform to the domain definitions. There are [tools that can help](https://typegraphql.ml/) with that, resulting in type-safe requests and responses. It's usually not possible or desirable to have the exact same types in each case, but that should be the default approach.

It's worth stressing that the types and interfaces of the HTTP layer derive from the domain models and not the other way around. In other words: the HTTP module conforms to the domain interfaces, the domain is defined first. Changes in the domain can change HTTP definitions but not the other way around. You can generate HTTP definitions from domain entities, but domain entities, by definition, can not be derived from HTTP types.