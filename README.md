# BEST PRACTICES

> Pragmatic recommendations based on clean coding principles for teams working on TypeScript/JavaScript web application.

A Git repository template implementing these recommendations is available at [thekarel/project-template](https://github.com/thekarel/project-template/)

## Principles <a name="principles"></a>

The principles underpinning these recommendations are:

* Software engineers solve business problems, code is a means not a goal
* Programming is a social activity and source code is communication
* Attention is the most expensive resource in a project
* Simplicity is the best ally in the long run.
* The only problem to solve is the one we have today

## Software Architecture <a name='architecture'></a>

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

Implementing a clean architecture is the same effort as implementing a convoluted one. It's a habit that is easy to pick up. [Use a template based on these concepts](https://github.com/thekarel/project-template).

The layers are [represented as modules](#workspace) in the codebase.

Architecture is not the same as [folder structure](#folderStructure).

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

### UI <a name="ui"></a>

From an architectural point of view, the user interface is a client to the public HTTP layer - and mostly talks the language of the HTTP layer.

#### UI and HTTP

It is OK to import domain modules into the UI layer (but not others, obviously).

Once GraphQL is introduced into the stack, the graph types might be slightly different from the domain types, causing annoying issues. For this reason, it's best to stick to the graph's language here.

Use a [type generator](https://github.com/dotansimha/graphql-code-generator) to minimise effort. These can turn graph types into static type definitions for the UI, resulting in end-to-end type safety.

#### Declarative UI

There are many great UI libraries and frameworks today. React is suggested here because it's a *declarative* and *functional* solution.

See the [project template](https://github.com/thekarel/project-template) for examples.

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

Type safety only works in our codebase. Validating runtime data is the responsibility of the services. If there is end-to-end type-safety, the Graph also plays a part.

## Folder structure <a name="folderStructure"></a>

### The context

According to our first principle, the codebase should primarily reflect the business domain. Frameworks and libraries are secondary considerations - implementation details.

Folders and files are organized first around the entities that are manipulated. Proper names convey the purpose of the application: what can I do with it?

One common problem is to structure code according to a framework or library's requirements. It is almost always a mistake. Such solutions mask the real purpose of the module or application, while also couple the whole codebase to a specific technology.

Another mistake is to organize source files by type, for example, to have a dedicated folder for all images, another for CSS, components, resolvers, etc.

Every module should reflect the entities it works with as top-level folders - this is the work context. When there is a hierarchy, we use subfolders. When there is no more domain-driven context to talk of, a technology-driven grouping can happen.

The above also means that context-free group names like "utils" or "helpers" are pointless and are strongly discouraged. They are not domain-driven, not technology-driven and don't convey meaning. There is always a better name than these.

Another consequence of context-driven grouping is that the test, mock and all supporting files live next to the source files (co-location). Co-locating makes clear if there is a test for a module and avoids creating a shadow folder-tree.

More importantly, all resources are at hand when working in a specific context. The developer can see the source, the tests and any relevant assets. There is no need to open files from multiple, far-away directories, and there is no guessing. It is obvious where to implement changes.

A regular question is where to store UI components that are shared across all domain types, for example, buttons, typography. Since these don't have a domain context, they can live in a group named `base`, `baseComponents`. `components` has the danger of luring *every* component in, even context-specific ones, so it's not the best choice.

### Index files

Do not add `index` files to folders. This practice has several drawbacks:

* It needs to be done manually, which is a drain on attention and energy (no matter how small it appears)
* It's just another thing to remember
* Makes refactoring harder, as you'll need to edit the re-exports
* Makes moving modules around harder
* It seems like a cosmetic change
* It is a code smell

Making refactoring harder usually means it never happens, which is far from ideal.

In any case, when using correct grouping, the full path to a module is usually pretty short.

The only exception to this rule is when creating a local or public npm package: the module's top-level exports are best exposed using an index file so clients can import without mentioning a path (which is an implementation detail to be hidden). For this reason, our domain, service etc. modules *will* have a top-level index file, but only one and it is used only from the outside.

### Example

Here is an example for confusing and a clear grouping of source files:

```sh
# Do not do this
src/
  __tests__
    date.test.ts
  components/
    index.ts
    LaptopList.tsx
  css/
    Laptop.css
  images/
    keyboard.png
  utils/
    sortMarbles.ts
    date.ts
```

A good example

```sh
  src/
   laptop/
     LaptopList.tsx
     Laptop.css
     keyboard.png
   date/
     date.test.ts
     dateFormat.ts # Maybe this is a domain module?
   sortMarbles.ts # "1 or more"
```

Hint: you can use the "1 or more" rule to decide if something needs to go into a subfolder. If there is only one "thing", the file can live where it is (e.g. `sortMarbles.ts` above). If there is two or more, put them in a subfolder (e.g. `marble/sortMarbles.ts`)

## README <a name="readme"></a>

Create a top-level README and make it the entry point for developers, so they can quickly get the system up and running.

Ask new starters to begin with reading this README, and in turn, it should provide enough information to get started.

Write as little as possible. People don't read, they scan, and very few are interested in reading a README (yes, I see the irony :). Developers will look for keywords and code blocks to copy. Don't let important information get lost in the sea of words. Don't waste attention.

Few things scream neglect as an outdated README. The less you write, the less will become obsolete tomorrow.

When you need to write a lot of instructions, consider automating the complicated steps. Solve the problem once instead of letting everyone after you struggle with it.

What to include in a README?

* Instructions to get the project working in a dev environment so people can start being productive fast
* Brief notes on quirks, known issues, peculiar things in the project that would surprise others.

What to leave out?

* List of technologies in use, they should be evident from the codebase or  `package.json`.
* Do not copy or link to 3rd party documentation that is a Google search away.
* In general, omit everything that is obvious or can easily be found out from the codebase, or is not relevant to writing software.

Making others read one README is an achievement in itself. By default, I'd avoid adding separate READMEs to the individual workspace modules.

## Code formatting

The visual formatting of the source code is probably the least important aspect of a successful project, as long as it is consistent. There is no technical or practical difference, for example, between having or not having semicolons or using spaces or tabs to indent lines - these are mostly personal preferences.

Still, these questions need to be settled once, preferably early and for good.

The issues that can suck away valuable time are:

* surprises due to inconsistencies (derails attention, frustrates some people)
* endless debates about formatting questions
* manual code formatting exercises

The way to avoid surprises is to enforce standard code formatting rules and automate the local formatting of source code. The rules should live in the codebase, and every pull request should check against them in the CI step (not a git hook which can be easily sidestepped and is redundant).

The way to avoid debates is to decide on formatting at the beginning of the project. These should not change unless there are serious technical reasons. Use a [template](https://github.com/thekarel/project-template) to speed things up.

Formatting rule changes should be global: the whole codebase needs to change in one go (one PR). Otherwise, it just increases the inconsistency.

Currently, popular formatting tools are:

[EditorConfig](https://editorconfig.org/) - ensures the same end-of-line, final newline, indent style etc is used. Usually needs an IDE extension.

[Prettier](https://prettier.io/) - formats and validates.

Code formatting should be completely automated (IDEs, coupled with the above tools are very good at it). Asking developers to manually format source code is a vast time-sink -- but obviously, everyone is responsible for keeping the system tidy and readable.

If you have custom requirements, then use a custom plugin for the above tools. Try to keep things simple.

## Naming

Name things for humans, not for computers. People care, computers don't.

Use descriptive names for types, classes, variables, etc.: they should be short, to the point and precise.

Name things as they are in the business language. It makes coming up with names and communicating with stakeholders easier.

Ask others for their opinion to develop a shared language.

Add the units, technology etc. at the end of the module (and file) name: (`weightKgs`, `volumePints`, `FoodRepositoryMemory`, `FoodRepositoryMongoDb`), so files are listed according to the name of the entity.

Be consistent in how you name an entity - avoid synonyms, different spellings or slight differences (a great way to confuse everyone).

Use the same casing: designate a rule - camelCase seems to be the norm nowadays - for *everything* so there is no need to think each time. There will be only a few exceptions: classes and types/interfaces need to start with `Uppercase`. Others, like constants, environment variables, variables, methods, file and folder names etc. can all follow the general rule and be camelCased (simple rule, easy to follow and makes a pretty file list).

Make it clear what type of thing the variable refers to: is it an ID (`userId` vs `user`), an entity or instance (`User` vs `user`), a service (`UserService` vs `User`), a response (`InvoiceCreatedResponse` vs `Created`), etc.

No need to state the obvious though: the context will often explain what is what. People will know when something is a (React) component, for example, no need to add it to the name.

For this reason, interfaces and types do not need a particular prefix/suffix added to them: `CoffeeMug` is perfectly natural and readable while `ICoffeeMug` is not. It will be evident from the usage that it is a type definition (you don't add `class` or `function` to the name either).

Make the units visible. Say when an amount is in seconds (`timeSeconds` vs `time`), for example.

Don't make the names too long (hard to type) or too short (hard to read and meaningless).

The rule of thumb is: write it down in English then camelCase it.

### File names

As the default rule, use the same name for the file as the named export contained in the file: `bananaResolver.ts`, `User.ts`. File lists then become very easy to read and understand.

If - as suggested - one file contains one module, the file names become predictable. Do not add special affixes like `.class.ts`.

By looking at a folder or file, it must be evident what it does or contains. `DatePicker.tsx` is pretty good, for the same component `Date.tsx` is not so much. Another example would be `dateFromTimestamp.ts` vs `date.ts`.

Use the same case as the export (camelCase by default, unless there is a special rule: type, class, React component)

Folder names can always start with a lowercase letter.

## Modules

Use named module exports only.

```typescript
// Good
export const example = 1

// Bad
export default 1
```

It is the way modules are supposed to work: you have to decide what you call an exported entity. Calling something "default" is like saying "I can't decide what the name is, and don't care".

There are only benefits to named exports:

* the name of the module will be used consistently across the codebase.
* You can change all occurrences at once (refactoring in IDE).
* You get reliable auto-imports in IDEs, which is an enormous productivity boost.

On the other hand, there are no benefits to using `default`.

Naming exports is linked to file names too: once you decide the name of the module, the filename becomes obvious.

By default, a file is named as the export, and there is only one export in one file. It's fine to add a couple of small related exports to a module as long as they are not too long (we don't want big files). You can always refactor - with named exports moving code is easy.

## Monorepo

Start with a single code repository for the whole project (not the entire company). It offers many benefits over a multi-repo setup and should be the default for the following reasons.

There is only a single repo to know. Only one address, one `git clone`, one `git pull`, one `git push`, one `yarn install`, one README to read, one place to raise PRs, ... and so on - it's simpler, quicker, easier for everyone.

The whole stack lives in one place. When you need to change [multiple layers](#architecture) at the same time, they are all there, and they can change at once. The codebase never gets out of sync.

No need to bother about [version numbers](#versioning), things keep working together.

You can solve tooling at once, adding new modules later will instantly take advantage of it, no extra steps required. Check out how [the template](https://github.com/thekarel/project-template) handles Jest testing, Prettier and tsc checks for example.

<a name='manyRepoDisadvantage'></a> On the other hand, multiple repos  seem to present some disadvantages.

Developers need to chaperon multiple repos locally, which is the source of bugs (it works on my machine) and drag on development (why can't I run it locally).

Setting up new repos is an extra effort: you need to set up tooling, copy configurations, create the folder structure, set up git permissions, publish the new address, etc.

It becomes difficult to share standard development configuration (linters, etc.). You can create *another* repo to hold these, but that just makes things even more complicated.

You set up CI/CD services multiple times: potentially adding env variables repeatedly, setting permissions, keys, and so on.

When multiple layers change, you need to organize a chain of PRs. First, add a change somewhere, bump the version number, raise a PR, review, wait for a CI run, merge, deploy. Next, you change one consumer's reference, rinse and repeat. It's a long and complicated process requiring human attention all the way. Imagine a five repo change: the chance for mistakes, oversights, merge conflicts - and time spent - raise exponentially.

Best to nip this in the bud. Use a monorepo unless there is a very substantial technical reason not to.

### package.json

Add shared dev-dependencies like Jest, Prettier, TypeScript and such to the top-level package.json (`yarn add -WD`).

Have a single command that starts the local development environment - any number of dev servers, compilers - and verifies any runtime dependencies. It should be enough to run `yarn dev` to start working.

I suggest adding short workspace-alias commands to the top-level package.json to simplify workspace commands:

```
{
  "commands": {
    "web": "yarn workspace @project/web-ui"
    "graph": "yarn workspace @project/graph"
  }
}
```

With these in place, commands run from the root folder, without having to `cd` into subfolders.

### Workspaces <a name="workspace"></a>

Using Yarn workspaces is the recommended way to deal with monorepos. A workspace enables your packages to import each other as if they were from NPM, without any tricks or hacks. Plus a single `yarn` installs every dependency; there's only one lockfile, package installs are faster.

The top-level package name can be a single word.

For the workspace modules, I suggest adding a short namespace, so they are easily identifiable throughout the codebase: `@project/domain`.
The folder names of workspace modules are the same names without namespace: `src/modules/domain`.

The workspace folder is named `modules` not `packages` to remove a shell completion annoyance (have only one thing starting with "package" in the root folder).

The folder structure will look something like this:

```sh
modules/
  domain/
    package.json # name: @project/domain
  admin-ui/
    package.json # name: @project/admin-ui
package.json # name: project
```

Workspaces present one annoying problem, unfortunately. The top-level `node_modules` is shared and will contain the installed packages. The one next to your server-side apps is not guaranteed actually to contain the runtime dependencies. This issue trips up the tooling for AWS Lambdas and Firebase Functions (Serverless.com included) for example. The solution is to build a bundle before deploying to these providers. Webpack works nicely. It seems to be an unavoidable complexity at the moment.

## Versioning

When it comes to version numbers, there seem to be two kinds of products to think of: libraries and applications.

Libraries are used in code by 3rd parties (external or internal). They need semantic version numbers, so consumers know what kind of change they are getting (bugfix, new feature or breaking change) plus being able to pin the dependency to a particular version.

Applications are tools people use to get something done and usually don't care about version numbers, as long as the thing works. With web apps user don't even get to choose the version: they get the latest release. Mobile apps are quite similar. Semantic versioning is not meaningful in this context.

Since this document is about application development and a monorepo is preferred, I suggest to

* use the same version number in all package.json files in the monorepo and never change it (literally set everything to `1.0.0` and forget about it)
* use the git hash (plus maybe a build number) to identify which version is being used by clients

You won't need to manually change version numbers or have a script to bump them.

A couple of worries are usually raised regarding this solution.

> How will you be able to use different versions of your local libraries without version numbers?

Without publishing packages to NPM, you won't be able to use multiple versions straight from a monorepo. Doing so would be an anti-pattern anyway, in my opinion. Evolve the whole codebase at once, avoid fragmentation, use the same version everywhere.

> How will the CD service know what changed and what it needs to deploy?

By default, it won't know and will re-deploy everything. You should not worry about this too much.
There are some ways to avoid that, but I can't recommend a wholly automated and reliable solution.

Some CD services can run pipelines based on file path changes. You could prevent re-deploying package X if no files changed inside the relevant folder. But this is tricky, as dependencies in other layers could warrant a re-deployment, e.g. due to a change in domain rules.

Also, deployments should be idempotent (and fast), if they are not, that is the real problem to solve first.

Only multi-repos can provide a genuinely atomic deployment experience, but they come with [a lot more problems of their own](#manyRepoDisadvantage).

## Monolith

Start building a monolith and not a microservice ecosystem. A microservice setup is an optimization, with its inherent costs, complexity and [some hard problems](https://www.youtube.com/watch?v=STKCRSUsyP0). Leave it to later, when there is a real need. You might never get to that point.

It is much easier to carve out truly independent slices once the team knows more about the system. Having a domain-driven, layered architecture makes this transition more comfortable by the way and can answer questions about responsibilities.

In case a service is extracted later, it should adhere to all the present recommendations: it will be an application in its own right.

## Runtime configuration <a name='configuration'></a>

The runtime configuration should be picked up [from the environment](https://12factor.net/config) by the public interface instance - the HTTP layer in our example.

The entry point has to declare the list of configurations keys that it requires to work. The app throws an exception if any required values are missing since it won't be able to function correctly. Better to refuse to start than to throw an exception in the middle of a user journey.

Only the entry point should look at the environment; this is to ensure we define the required config keys in a single place. Picking up env vars in random places is a sure way to introduce problems that only happen when they really shouldn't (in production).

The same rule applies to the UI instances, whereas they pick up their config from environment variables, but they usually do that at *build time* since they are mostly static.

## Git

Be pragmatic; find a productive workflow. Source control should be a tool, not a chore.

Use the master branch to deploy into different environments and use feature branches to prepare PRs.

Resist having any long-running branches apart from master: they are the source of confusion and frustration. The branch list should reveal the current work effort (not the past). Long-running branches get out of sync quickly anyway.

No change can enter the master (production) branch without a PR.

Create small PRs: they are easier to create, so the code quality is better, and they are easier to review, so more mistakes will be caught.

Use feature branches that live at most two days: longer ones either get too big (impossible to review) or will introduce merge conflicts.

To help code reviews make sure the feature branch history is readable.

Use squash when merging PRs:

* it produces clean git history
* requires no extra effort from the developer
* flattens the PR into a single commit, hiding individual commits you made during the PR

Other merge strategies either result in a messy history or require the developer to manually tinker with git or prepare each commit with great care. That amount of effort is always better spent elsewhere.

When PRs are small, the individual commits in the feature branch should not matter: what matters is the result, the complete change that goes into master.

Make sure merged feature branches are deleted, keep a tidy work environment.

Do not keep deprecated code in HEAD: delete it, you can always go back and find it.

## Logging

Log into JSON: it's rich, structured and you can parse messages even if the structure changes somewhat. [Log to the standard output](https://12factor.net/logs) and let the execution environment pick it up.

It is generally discouraged to import the same 3rd party library in many different locations. A good practice to hide the dependency behind a local proxy, so you can change the implementation as you see fit.

The same is valid for logging. Pick a library - if you even need a library at all - and set it up behind your facade, so your modules keep importing `@you/logger`.

Try to use [two log levels](https://dave.cheney.net/2015/11/05/lets-talk-about-logging): debug and info. Debug is for developers only, do not enable it in production. Info is for analytics and auditing.

## Code comments

You don't need comments in the code.

Shapes, classes, call signatures are self-documenting in TypeScript. Clear names explain what is what. Declarative code is easy to read and reveal the flow of control and data without the prose.

There is always a better way, then adding comments. Comments rarely solve problems.

The *only exception* to this rule is to add comments to a piece of code that is unexpected or misleading but not fixable for technical reasons (for example it's 3rd party API). If it's your code, fix it.

Not writing comments doesn't mean you don't share [helpful information](#readme) in its appropriate place.

## Testing

The purpose of testing is to ensure the product works according to its business purposes. It must work even after adding or removing functionality or refactoring the codebase.

Testing is crucial. It is also a convoluted topic and can quickly turn into a counterproductive time sink.

Keeping the goal of testing and the [core principles](#principles) in mind, I suggest the following pragmatic approach.

Implement three types of automated tests, in order of importance:

* Complete end-to-end (browser/app) tests that verify the user-facing features
* Integration tests that verify the machine facing APIs
* A few developer tests (aka unit tests)

Sacrifice the lower priority tests if your resources are limited.

Treat tests as proper code, do not take shortcuts: be declarative, type-safe, lint - stick to the good practices.

Tests will usually require similar data across multiple test instances and types. Maintain a single source for the different types of test data, so that you can update it in one go. For anything non-trivial a type-safe factory is suggested (for example [factory-ts](https://www.npmjs.com/package/factory.ts)).

### End-to-end tests

E2E validates the user interface.

Put an automated, end-to-end test in place right at the beginning; make sure it validates PRs. The initial setup is the most difficult; adding or changing things will be more comfortable moving forward.

The e2e test should also be able to validate that a *deployed* version of the app works as expected: this verifies the environment, configuration and integration. You can use this before promoting code to a new environment.

Most importantly: e2e proves that the app does what it supposed to do - no other form of testing will give you that kind of confidence.

It is OK to run automated tests on production, where confidence is the most important - using dedicated test users is one way.

E2E tests should cover only high-level features derived from business requirements. These are the user journeys that interest the stakeholders and include such things as being able to register, log in, look at things in different ways and buy them.

These tests run in a browser or mobile app, without mocks.

Suggestion: put e2e tests in the UI project, next to the `src` folder:

```sh
ui/
  src/
  e2e/ # <-- here
```

### Integration tests <a name="integrationTests"></a>

Integration tests have the same aim as e2e tests: to verify the [use cases](#services) are working by executing an API (REST, GraphQL, etc.).

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

I suggest to cover the [persistence modules](#persistence) with dedicated integration tests, to gain confidence in data integrity.
The tests should prove that the specific implementation of the [persistence API](#persistenceApi) handles CRUD commands as expected. The benefit of having a single API is that the same test can run against multiple implementations (just inject the test subject).

### Developer tests (unit tests) <a name="unitTests"></a>

Domain logic and rules deserve high test coverage.

As for other unit tests, my advice is to use them only as aides when working on a tricky problem (hence the name "developer tests"). Test complicated algorithms, data transformations, complex logic that would be very time consuming to test using the UI only.

Do not write tests for trivial things or ones that are covered by the static checkers. Do not test 3rd party libraries.

Units of the UI are always implementation details, and we don't want to tests those (see the goal of testing). Covering UI components in unit tests (including snapshots) is very time consuming but provides little proof that the app is working, let alone it looks as it should. Fry the bigger fish first (e2e, integration).

A highly recommended tool for UI development is [Storybook](https://storybook.js.org/). It lets you work on components in isolation and verify functionality plus look and feel in various states (not an automated test in itself though).

If your tests fail, but the app is working: delete the tests. If your tests pass, but the app is not working: you're testing the wrong thing.

Code coverage is a trap. What you need is *feature coverage* but that is not coming from unit tests.

Suggestion: put unit tests (and Storybook stories) right next to the source files, so they are easy to find and add:

```sh
addRumToTea.ts
addRumToTea.test.ts
DrinkTea.tsx
DrinkTea.story.tsx
```

## Recommended resources <a name='sources'></a>

All valuable ideas mentioned here come from the great books, talks and websites listed below - and from the many great engineers I had the pleasure to work with during my career - thank you! 🙏

### Books

[Clean Code: A Handbook of Agile Software Craftsmanship by Robert C. Martin](https://www.amazon.co.uk/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/).

[The Mythical Man-Month by Frederick Brooks](https://www.amazon.co.uk/Mythical-Man-Month-Anniversary-Software-Engineering-ebook/dp/B00B8USS14)

[The Pragmatic Programmer by Andrew Hunt, David Thomas](https://www.amazon.co.uk/Pragmatic-Programmer-Journeyman-Master-ebook/dp/B003GCTQAE)

[The Simplicity Cycle: A Field Guide to Making Things Better Without Making Them Worse by Dan Ward](https://www.amazon.co.uk/Simplicity-Cycle-Making-Things-Without-ebook/dp/B00MTS3LUC)

[Maintainable Javascript by Nicholas C. Zakas](https://www.amazon.co.uk/Maintainable-JavaScript-Nicholas-C-Zakas/dp/1449327680)

### Talks, videos

[Robert "Uncle Bob" Martin - Architecture: The Lost Years](https://www.youtube.com/watch?v=HhNIttd87xs)

[Ian Cooper - TDD, Where Did It All Go Wrong](https://www.youtube.com/watch?v=EZ05e7EMOLM)

[Drew Colthorp - Understanding TypeScript's Structural Type System](https://www.youtube.com/watch?v=MbZoQlmQaWQ)

[Rich Hickey - Simple Made Easy](https://www.youtube.com/watch?v=oytL881p-nQ)

[Martin Fowler - The Many Meanings of Event-Driven Architecture](https://www.youtube.com/watch?v=STKCRSUsyP0)

### Websites

[Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

[The Twelve-Factor App](https://12factor.net)

---

[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)