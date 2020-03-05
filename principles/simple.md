## Simplicity is the best ally in the long run

You need to embrace simplicity if you want to achieve results today and stay successful tomorrow. Long term product quality, performance and development speed all rest on the simplicity of a system.

The typical problems associated with software projects, cost overruns and delays are all closely related to the disregard for simplicity.

There are two kinds of complexity in any project: the inherent complications of the [problem domain](business.md) and the unnecessary, "incidental" or "accidental" complexity introduced by developers. The domain complexity is more or less fixed and has to be accepted as is. Dealing with it is the primary role of a developer.

Accidental complexity, on the other hand, has to be avoided and minimised at all cost.  We're focusing only on the developer induced complications here - or preferably on how to avoid it as much as possible.

Be frugal with code, processes and infrastructure. Everything you add today will have to be looked at and understood by you or someone else in the future. The more complicated your system is, the more resources people dedicate to learning it instead of implementing improvements and new features.

One aspect of simplicity is the "number of things" one has to deal with. Using one programming language is simpler than two. A [single codebase](../monorepo.md) is simpler than managing multiple. A [monolith](../monolith.md) is simpler than running microservices. One database, one server, one UI - all simpler than having multiple.

Another dimension of simplicity is how monotonic, homogeneous the system is. How many variations or answers you have for the same problem? Using the same solution every time is simple. Sticking to the agreed [architecture](../architecture/architecture.md) is simple. Using consistent [naming](../naming.md) makes things simpler. Following consistent [file structure](../folderstructure.md), having the same code style and [formatting](../codeformatting.md) throughout the codebase is simple.

Following the conventions keeps things manageable: respect the pattern!

Finally, consider how many steps something takes from planning to execution and deployment. Do you need all those meetings? Do you need all those people? Do you need all the ceremony that goes into development? Are there unproductive, repetitive tasks? Is your deployment a chore or a non-event?

Simple might seem boring at first - but it's the opposite: it's liberating. Predictable, easy to understand solutions free up your mental resources, allowing you to be creative and solve the problems that matter.

Over-complicated systems are greedy and force you to work on them for their own sake: they get fatter and more convoluted with time until the situation becomes hopeless, and maintainers ask for a rewrite.

Sometimes developers bury the project in complexity: the solutions are over-engineered. I can see two obvious reasons for this: they weren't sure how to move on to the next step, or they were bored. Teams can address both problems with discipline, reflection and guidance from leads.

Keep in mind: we need to do as much as it takes to tackle the domain problems and the inherent complexity. But ideally, we never go further. Quite literally, every line of unnecessary code comes at a cost: the time and money it takes writing it, then the time and money of reading and changing it in the future. The same is true for every minute sacrificed for some unproductive ritual or the maintenance of over-complicated infrastructure.

Adding more - code, infrastructure - is not always the answer. The best code is the one that doesn't exist. The infrastructure you did not add will never cause problems. When planning out features, try to find the way that achieves the results with the least amount of programming and future maintenance.

Simplicity frees up [attention](attention.md), so you can focus on what matters: [solving business problems](business.md). Simple software is easier to read and easier to maintain. Simple code is easier to understand, so you will make fewer mistakes and create fewer bugs.

Teams don't introduce complexity with a big bang: it creeps into the codebase bit by bit. Complications are built up one piece at a time, by small acts of carelessness and indifference.

The default reaction to any addition should be sceptical. Question every change, be it a new kind of solution, library, piece of infrastructure. My rule of thumb is this: don't change until the situation is painful. Then you will know what to change, and it will be evident if you've managed to solve the problem.

Run with the minimum that achieves the [business goals](business.md).

Explore your options: the first idea is often not the best or only one. Demand precise requirements from stakeholders so you can discover better solutions. Communicate, find ways to simplify and explain the cost and benefits of different approaches.

Do the minimum to achieve the required features and functionality. Everything else is a waste of present and future resources. Doing the minimum does not mean creating a low-quality or primitive product. It means creating only what the stakeholders demand and not spending precious time and energy on made-up requirements.

You can always evolve a simple system and increase its performance or turn it to something different. This is especially true if your code is simple because it's consistent. On the other hand, complexity makes changes harder.

At this point, we can see how simplicity helps minimise cost overruns and uncertainties about timelines. An uncomplicated structure allows both developers and management to plan the required work more precisely.  Changes are predictable as people understand the interplay of different parts. Unintended consequences are rare, so the team does less fire fighting or fixing costly surprises.

The key is understanding. When you understand the codebase, it stops being a liability and becomes a solution.

Simple leads to results today and tomorrow. Simplicity is a developer's best friend.
