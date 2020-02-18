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

Only multi-repos can provide a genuinely atomic deployment experience, but they come with [a lot more problems of their own](monorepo.md).