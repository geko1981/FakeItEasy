### Issue labels

All issues should default to being labelled **0 - Backlog** (see [[workflow labels|Issue labels#workflow]]) and **P2** as the default priority level (see Priorities below), as well as appropriate [[general labels|Issue labels#general]]. This should happen ASAP after an issue is created. As the issue progresses, the labels should be kept up to date to reflect any insights gained. An issue must have appropriate labels assigned before it can be moved into **1 - Ready** (see Huboard below).

Unfortunately GitHub does not provide any history of label changes so, where practical, when changing the labels on issue, add a comment to the issue recording the change and the reasoning.

### Priorities

Issues should default to priority level 2 (label **P2**). If an issue is judged to be important, e.g. a major bug, a new feature with great value, etc. then it should be promoted to **P1**. An issue which is a nice to have but less important than others should be demoted to **P3**. If there are very few **P2** issues open then issues may be selectively promoted from **P3** to **P2**.

In Huboard (see below) an issue cannot be moved from 'Backlog' into 'Ready' until it has a priority assigned.

Generally, higher priority issues should be addressed before lower priority issues.

### Huboard

New issues should be immediately placed in the 'Backlog' column. For an explanation of the columns, see [[Huboard]].

As soon as work can commence on an issue, it should be moved into 'Ready'.

As soon as work begins on an issue, it should be moved into 'Working'. This should be done in conjunction with assigning the issue (see Assignment).

As soon as all work is finished on an issue and no further work is outstanding then the issue can be moved into 'Done'. At this point it OK to close the issue.

Issues can be moved across the [board](https://huboard.com/FakeItEasy/FakeItEasy) by dragging them. This updates the [[workflow labels|Issue-labels#workflow]] accordingly) and owners can be assigned to issues by dragging their gravatar onto them. The beta version of Huboard also allows closed issues to be seen in the final column and for issues to be closed from the Huboard UI.

Issues which are currently in the 'Working' column should generally be addressed (implementation, raising/reviewing PRs) before any which are currently in the 'Backlog' or 'Ready' columns. This helps to keep the 'Working' column lean so that changes can be released early and rebases with conflicts are kept to a minimum.

### Assignment

If an owner decides to work on an issue then they can assign themselves to it, either via the GitHub UI or by dragging their gravatar onto the issue in Huboard. This should usually be done in conjunction with moving the issue from **Ready** to **Working**.

If a contributor has volunteered to take on an issue and you feel it is suitable for them, add the **taken** label to the issue and add a comment to the issue indicating who it has been taken by. Move the issue from **Ready** to **Working**. Generally we should be generous in assigning issues to contributors. More complex issues should have a design discussed and agreed up front. Changes to the public API should also have the agreement of at least two owners.

If a contributor has been assigned an issue and becomes unresponsive then the **taken** label should be removed and the issue should be moved back into **Ready**. A comment should be added indicating the issue is no longer taken.

### Milestones

A milestone relates directly to a release. As soon as an issue is moved into **Working** it should be added to the next milestone in the assumption that it will form part of that release. Note that this should only be done for issues which will form part of the release notes for that milestone. Issues which are of internal concern only, e.g. refactoring, build improvements, documentation (outside of code), etc. do not form part of the release notes for a package and therefore do not form part of a milestone.