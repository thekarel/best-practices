## Code formatting

The visual formatting of the source code is probably the least important aspect of a successful project, as long as it is consistent. There is no technical or practical difference, for example, between having or not having semicolons or using spaces or tabs to indent lines - these are mostly personal preferences.

Still, these questions need to be settled once, preferably early and for good.

The issues that can suck away valuable time are:

* surprises due to inconsistencies ([derails attention](principles/attention.md), frustrates some people)
* endless debates about formatting questions
* manual code formatting exercises

The way to avoid surprises is to enforce standard code formatting rules and automate the local formatting of source code. The rules should live in the codebase, and every pull request should check against them in the CI step (not a git hook which can be easily sidestepped and is redundant).

The way to avoid debates is to decide on formatting at the beginning of the project. These should not change unless there are serious technical reasons. Use an [example](https://github.com/thekarel/best-practices-example) to speed things up.

Formatting rule changes should be global: the whole codebase needs to change in one go (one PR). Otherwise, it just increases the inconsistency.

Currently, popular formatting tools are:

[EditorConfig](https://editorconfig.org/) - ensures the same end-of-line, final newline, indent style etc is used. Usually needs an IDE extension.

[Prettier](https://prettier.io/) - formats and validates.

Code formatting should be completely automated (IDEs, coupled with the above tools are very good at it). Asking developers to manually format source code is a vast time-sink -- but obviously, everyone is responsible for keeping the system tidy and readable.

If you have custom requirements, then use a custom plugin for the above tools. [Try to keep things simple](./principles/simple.md).
