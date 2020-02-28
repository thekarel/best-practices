## Programming is a social activity

Source code is communication.

Code is written for computers to run. But there is another, equally important audience: people who will read, change and maintain the codebase in the future—you and others.

Effective communication requires clear ideas. Understand and plan your work as a developer and as a team.

Use precise [language](business.md), [structure](../architecture/architecture.md), [grouping](../folderStructure.md), [names](../naming.md) and [interfaces](../typescript.md) to explain your intentions.

Provide clarity and demand it from others.

Write code that on the highest level of abstraction deals with the [domain entities](../architecture/domain.md). You make it easy for humans to explore and understand the codebase when they come across the same concepts in code as in other forms of communication.

It is surprising how quickly knowledge about a module fades from memory. The team soon forgets something that everyone knew and took for granted as they move on to solve a new problem.

The next time it's looked at the codebase has to speak for itself. It has to communicate what it tries to achieve, what kind of inputs it's expecting, and what are the outputs and side-effects. What is it doing internally? Why is the code there? Is it still required? Is it still working?

A maintainable codebase conveys its purpose, its internal logic, inputs and outputs to people. Make it a habit to write code this way. You can't retrofit clarity.

It's worth stressing that the information to understand the codebase has to come from the codebase itself. A separate, textual documentation is a weak substitute at best, unnecessary most of the time. Prose severed from the codebase - kept in a wiki for example - can become obsolete and misleading unnoticed, and this is even worse than no information.

Embrace the power of the [type system](../typescript.md): clear interface annotation eliminates the need for a whole suite of documentation that you wish to add separately.

Write declarative code that expresses the intent but hides the implementation details. Dedicate one module to one level of abstraction. Your program then becomes a handbook that is easy to read, understand and change.

Develop a shared language, use [names](../naming.md) that make sense to humans and ease communication.
