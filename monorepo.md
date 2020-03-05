## Monorepo

Start with a single code repository for the whole project (not the entire company). It offers many benefits over a multi-repo setup and should be the default for the following reasons.

There is only a single repo to know. Only one address, one `git clone`, one `git pull`, one `git push`, one `yarn install`, one README to read, one place to raise PRs, ... and so on - it's [simpler](./principles/simple.md), quicker, easier for everyone.

The whole stack lives in one place. When you need to change [multiple layers](./architecture/architecture.md) at the same time, they are all there, and they can change at once. The codebase never gets out of sync.

No need to bother about [version numbers](versioning.md), things keep working together.

You can solve tooling at once, adding new modules later will instantly take advantage of it, no extra steps required. Check out how [this example](https://github.com/thekarel/best-practices-example) handles Jest testing, Prettier and tsc checks.

<a name='manyRepoDisadvantage'></a> On the other hand, multiple repos  seem to present some disadvantages.

Developers need to chaperon multiple repos locally, which is the source of bugs (it works on my machine) and drag on development (why can't I run it locally).

Setting up new repos is an extra effort: you need to set up tooling, copy configurations, create the folder structure, set up git permissions, publish the new address, etc.

It becomes difficult to share standard development configuration (linters, etc.). You can create *another* repo to hold these, but that just makes things even more complicated.

You set up CI/CD services multiple times: potentially adding env variables repeatedly, setting permissions, keys, and so on.

When multiple layers change, you need to organize a chain of PRs. First, add a change somewhere, bump the version number, raise a PR, review, wait for a CI run, merge, deploy. Next, you change one consumer's reference, rinse and repeat. It's a long and complicated process requiring [human attention](principles/attention.md) all the way. Imagine a five repo change: the chance for mistakes, oversights, merge conflicts - and time spent - raise exponentially.

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

### Workspaces

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
