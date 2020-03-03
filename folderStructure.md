## Folder structure

### The context

According to [our first principle](principles/business.md), the codebase should primarily reflect the business domain. Frameworks and libraries are secondary considerations - implementation details.

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

* It needs to be done manually, which is a drain on [attention](principles/attention.md) and energy (no matter how small it appears)
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
