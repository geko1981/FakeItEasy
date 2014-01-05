Pull requests must satisfy the following criteria in order to be merged:

* the PR must be from a branch in a users fork which is a straight line of commits from master (no merges from other branches, including master)
* no micro commits. Any branches with micro commits should be squished to a sensible level so that each commit acheives a recognisable goal, e.g. a refactoring, the addition of a failing test, the implementation of the feature, etc.
* any functional change must have a corresponding issue and the commits should reference that issue in their commits using the #commitnumber syntax. Non-functional changes, e.g. refactorings, removal of dead code, etc. do not need a corresponding issue.
* an owner cannot merge their own PR. This must be done by another owner.
* the PR must merge automatically. If it doesn't then the PR needs rebasing and the PR sender should be informed of this.
* the pull request must build. Status is normally reported back to the PR from the build server. If this does not happen, then the build server should be checked manually.
* all other points mentioned in https://github.com/FakeItEasy/FakeItEasy/blob/master/CONTRIBUTING.md should have been followed
* remember to thank contributors! They are the life blood of the project ;-).