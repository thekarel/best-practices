# BEST PRACTICES

> Pragmatic recommendations based on clean coding principles for teams working on TypeScript/JavaScript web application.

📖 Read the GitBook: https://thekarel.gitbook.io/best-practices/

📌 An example implementation is available at [thekarel/best-practices-example](https://github.com/thekarel/best-practices-example)

This is a collection of recommendations, best practices and concepts about efficiently developing web applications with TypeScript/JavaScript. These ideas can serve as a solid baseline for teams interested in product quality, timely delivery and sustainable development.

At the basic level, this document can serve as a blueprint for groups and leads to establish their bespoke ways of working. I firmly believe that successful development teams have a comprehensive set of best-practices from the very beginning. A solid baseline provides confidence and productivity.

Even without tailoring to your particular situation, these ideas can make software development more efficient and more enjoyable.

The [basic principles](./principles/principles.md) can guide you when making day to day decisions. You can assess how changes align with the business goals, the social nature of software development, the protection of your team's attention capital and the need for simplicity.

This document promotes a very business-domain centric view of software. Explicitly separating parts of your codebase to follow the business thinking will help with maintainability immensely. Such [architecture](./architecture/architecture.md) aligns nicely with the separation of concerns too. Writing and reading your source code will become easier.

There are many ways to solve a problem, and this is especially true in today's flourishing JavaScript ecosystem. At the same time, I believe that not all solutions are equally beneficial to your team or the product you're building. I've collected various ideas about structural decisions (such as monorepo, monolith), the source code (formatting, folder structure, and so on) and some more specific tools (logging, git). These ideas were all selected and adjusted to align with the basic principles mentioned before.

There is a separate section dedicated to [testing](./testing/purpose.md). The aim, once again, is to keep the testing effort aligned with the basic considerations: creating and releasing better products.

The ideas presented here [are not all new](resources.md). What I've tried to do is to show them pragmatically, in the context of modern enterprise web app development.

Finally, this document is not a replacement for knowledge of programming languages. The assumption is that you are a competent JavaScript developer and are comfortable using the tools and techniques mentioned here.

## The Big Picture

* [Principles](principles/principles.md)
    * [Software engineers solve business problems](principles/business.md)
    * [Programming is a social activity](principles/social.md)
    * [Attention is the most precious resource in a project](principles/attention.md)
    * [Simplicity is the best ally in the long run](principles/simple.md)
    * [The only problem to solve is the one we have today](principles/today.md)
* [Architecture](architecture/architecture.md)
    * [Domain](architecture/domain.md)
    * [Services](architecture/services.md)
    * [Persistence](architecture/persistence.md)
    * [HTTP](architecture/http.md)
    * [UI](architecture/ui.md)
* [Naming](naming.md)

## Constraints

* [TypeScript](typescript.md)
* [Monorepo](monorepo.md)
* [Monolith](monolith.md)
* [Versioning](versioning.md)

## Source code

* [The README](theReadme.md)
* [Modules](modules.md)
* [Folder structure](folderStructure.md)
* [Code formatting](codeFormatting.md)
* [Code comments](comments.md)

## The Outside World

* [Runtime configuration](configuration.md)
* [Logging](logging.md)

## Testing

* [The purpose of testing](testing/purpose.md)
    * [End-to-end tests](testing/e2e.md)
    * [Integration tests](testing/integration.md)
    * [Unit tests](testing/unit.md)

## Tools

* [Git](git.md)

## Resources

* [Resources](resources.md)

[CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)
