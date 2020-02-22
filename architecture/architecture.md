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

The layers are [represented as modules in the workspace](../monorepo.md).

Architecture is not the same as [folder structure](../folderStructure.md).
