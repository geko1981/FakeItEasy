We use [Huboard](https://huboard.com/FakeItEasy/FakeItEasy) for a Kanban style view of the issues and their progress. Issues can be moved across columns by dragging them (this updates the [[workflow labels|Issue-labels#workflow]] accordingly) and owners can be assigned to issues by dragging their gravatar onto them. The beta version of Huboard also allows closed issues to be seen in the final column and for issues to be closed from the Huboard UI.

### Columns

* **Backlog** - the default placement of all new issues. Issues in the backlog are yet to be understood or require analysis and/or discussion with regard to functionality, implementation, etc.
* **Ready** - the issue is understood, labelled correctly and work can commence immediately.
* **Working** - the issue is currently being worked on.
* **Done** - work has finished on the issue and it can be closed. In the case of a code change, this means the work has been merged to master and the functionality is complete. In the case of a question, the question has been answered and the user asking the question has acknowledged it. Other issues types as appropriate.
