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
