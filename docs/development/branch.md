# Branching

<!-- markdown-toc -i branch.md -->
<!-- marked <filename>.md -o <filename>.html >
<!-- toc -->

  * [Example](#example)
  * [Branch Name/Commit Messages/Pull Requests](#branch-namecommit-messagespull-requests)
  * [References](#references)
  * [Definitions](#definitions)

<!-- tocstop -->




## Branching Strategy

Proposing the `git-flow` for branch management.  This is not necessary for all repos, but should be targeted for those that have greater collaboration or complexity.  Pronghorn for example has enough files and enough collaborators that it would make sense to never want to damage `master`.  Arbiter is simple enough that working against `master` from a feature branch is straightforward enough to not introduce additional branching strategies.

This involves 4 main branches

1. __master__ - The root, default branch in any repo.  This will branch to develop initially, and then only get updated whenever we have finished a weekly code release.  The idea is that `master` is kept pristine, and is _ALWAYS_ in a shippable state.
2. __develop__ - Originally branched from `master`, accepts incoming pull-requests from all branches except `master`.  `develop` represents work-in-progress, or work that is complete, code reviewed and awaiting test: verification and validation (V&V).  Note: merging to develop is "blocked" or "disallowed" after the code cutoff and before the release is "cut" for V&V.
3. __feature/bug__ - Branches tied directly to a JIRA ticket, i.e. `feature/WITS-2389` or `bug/WITS-1899`.  When work in these branches are code complete and code reviewed, they are merged back into `develop` awaiting V&V and a subsequent "cut" to `release` when validation is complete. In the case of merge conflicts with `develop`, engineers will need to merge `develop` _into_ their respective feature branch to resolve the conflict.  Rebasing should not be necessary in most cases, unless the code base changed significiantly and requires a replay of the changes.
4. __release__ - A snapshot of develop at the end of a development cycle that represents a set of features to be tested and validated.  When complete, a release is tagged and merged back to _BOTH_ `master` and `develop`.
5. __hotfix__ - In the case when we have bundled the software in a `release`, and merged back to `master`, but a fix is necessary without delay (i.e. without waiting another development cycle).  In this case, we create a `hotfix` and complete development and V&V in a shorter cycle in order to return `master` to a known-good state.  When a `hotfix` is complete, we merge into `master` and into `develop`.

The overall idea is to "protect" `master` from getting messed up and also allow `master` to always be a shippable version of code. `develop` is WIP and should mostly represent the influx between `Implement Done` and `Verification Open`.

### Example 

<img src="../images/branching.png">

### Branch Name/Commit Messages/Pull Requests

Any branch for a ticket should be named after its ticket idendifier, i.e. `feature/WITS-2321`. Commit messages should begin with the ticket name in brackets, i.e. "[WITS-2321] _Commit Message_".  Finally, a PR title should be named with the ticket, [WITS-2321].

This way, JIRA will pick-up on the respective hooks to find relevant work related to a ticket.  This will be beneficial when 

### References

See:
- [Gitflow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- [A Successful Git Branching Model](https://nvie.com/posts/a-successful-git-branching-model/)

### Definitions

- __SET__ - Software Engineer in Test
- __Verification__ - process of checking that the software meets the specification
- __Validation__ - process of checking wether the specification captures the customer's needs
- __Workstream__ - a topic, or area of development, in which tickets are bucketed.  Could also be a larger feature, or release.
