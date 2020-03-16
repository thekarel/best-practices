## Declarative programming

Maintainable code is [simple](principles/simple.md), [discoverable](folderstructure.md) and easy to understand.

How do we write code that others understand easily? By stating our intentions in short, abstract, human language-like expressions and moving the technical details into lower-level modules.

Consider the following code snippet. It is obvious what's supposed to happen when running this code. We don't need to know the details of how this implemented, but it's also obvious where to look to understand more.

```
const buyTicket = async ({repository: Repository}, {userId: UserId, concertId: ConcertId}): Promise<Ticket> => {
    const [concert, user] = await Promise.all([
        repository.getConcert(concertId),
        repository.getUser(userId)
    ])

    if (!ticketsAvailableFor(concert)) {
        throw new TicketsNotAvailableError()
    }

    if (!hasFundsFor(user, concert)) {
        throw new NotEnoughFundsError()
    }

    const ticket = new Ticket(concert, user)

    await repository.recordTransaction(user, ticket)

    return ticket
}
```

This code more or less reads like a recipe in English: get the user and the concert, check if tickets and the money are available - if so, deduct the money and hand over a ticket.

Production code would be more complicated than this example, but the pattern is quite clear:

* State your intentions in expressive, human-friendly language
* Stay on one level of abstraction inside [a module](modules.md)
* Start from high-level, [domain](architecture/architecture.md) language and move to low-level, implementation-specific details

Writing declarative code results in small building blocks, but works from top to bottom: you state the most complex, most significant problem first, then break it down to smaller bits, then go and break down these again and again. At some point down this road, you find ready-made solutions, reuse your existing code or 3rd party modules.

You can think of declarative programming as writing a specification first (top-level function calls), then implementing these features one by one. It's easy to imagine that the snippet above started as a list of requirements to implement:

```
const buyTicket = () => {
    // get user
    // get concert

    // check if tickets are still available
    // check if the user has funds

    // create a Ticket
    // create sale transaction

    //return ticket
}
```

While details in a declarative codebase are not immediately visible, they are still easily accessible. When you need to understand specifics, you should be able to "jump to definition" in your IDE - or open the relevant file, which is straightforward with [consistent naming](naming.md).

The definition of the lower-level code block must also follow the declarative principles - so it's also easy to understand. This pattern repeats like a fractal, from top to bottom, from very abstract to the lowest level.

One function (method) must deal with one level of abstraction. Sticking to the rule of increasing specificity makes working with the code straightforward, as readers don't have to jump between contexts, which is very distracting and drains [attention](principles/attention.md).

Declarative code builds up complex structures from smaller building blocks. In this regard, it's very similar to the compositional nature of functional programming. But using FP is not a requirement; you can use this technique in object-oriented programming or any other/mixed paradigm.

The main goal here is to write something easy to understand for others - and ourselves in the future. But declarative programming has other benefits too: refactoring is more manageable because you've extracted implementation details, so changing "how" we do something doesn't affect the "what". Also, finding the code responsible for a piece of functionality is almost trivial - you might have to go through several modules (more indirection), but the path to follow is always evident. You might have to walk a bit more, but you're guaranteed to get there - and this is key in a product-oriented environment.

The expressive strategy described here works well outside of programming too. You are encouraged to be declarative when recording requirements, writing documentation, describing systems, creating tests, scripts, CI/CD pipelines.

Finally, consider how the opposite of declarative code looks like: everything stares at you at once, from technical details to high-level concepts, from counters and indexes to business entities, all mixed. Such source code is confusing and difficult to follow - and precisely because of that becomes a breeding ground for bugs.
