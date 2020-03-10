## The only problem to solve is the one we have today

It is a mistake to solve potential future difficulties: things we *might* need, features that *might* be required.

What you need to solve are the problems you have *now*: dedicate [proper focus](attention.md) to these. Produce a correct solution for the task at hand and move on.

Worrying about what might happen in an imaginary scenario leads you down into rabbit holes. It is one of the reasons why people start many things but finish few.

Another reason to refrain from "preemptive" solutions is the lack of information. You will know more about the problem domain later and will be able to come up with a better solution "just in time".

Finally, you limit your future choices by making sub-optimal decisions now. Don't paint yourself into a corner by making uninformed judgments.

If you keep your application [as simple as possible](simple.md), you'll have a much higher chance to adapt to future changes successfully.

You can't predict the future, and surprises will happen. What you have planned might never arrive, something completely different probably will.

Keep YAGNI in mind: [You aren't going to need it](https://www.martinfowler.com/bliki/Yagni.html).

Avoiding unnecessary investments applies to developer tools also. You could spend all your time improving the tooling and processes, but are you inefficient to begin with? Are these real improvements or just cool toys?

Not catering for potential features today has the advantage of keeping the codebase more flexible. If you do not commit to a solution today, you'll have more wiggle room tomorrow. Keep your options open.

## Optimisation

The [First Rule Of Optimisation](https://wiki.c2.com/?RulesOfOptimization): Don't do it!

Avoid micro-optimisations. Don't take DRY (Don't Repeat Yourself) for example too seriously. Repeating the same code a few times might be acceptable for now. You can come back and clean up once you have a final solution and know more.

Many developers fall into the trap of haphazard optimisation. One can spend enormous amounts of time and energy while making a system "better" in some way - while not adding any [real value](business.md). Not all applications have to run at the highest imaginable speed or on the most sophisticated storage solutions.

You need two things to start optimising: an actual performance issue that is causing real problems, and automated, objective measurements (a baseline) of the problematic feature.

Given these two, you should still focus on solving the first (the real problem) as a priority. There might be ways around the problematic parts that cost orders of magnitude less than the "real" fix. As an example, it might be enough to add an index instead of rethinking the database structure. A cache can mask slow services, and this might be sufficient in many cases.

The actual decision about going ahead with an optimisation - or any change - is ultimately made by the ones [who have skin in the game](business.md).