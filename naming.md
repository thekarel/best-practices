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
