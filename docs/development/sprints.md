
# Sprints/Cycles
<!-- markdown-toc -i branch.md -->
<!-- marked <filename>.md -o <filename>.html >
<!-- toc -->
* [Sprints](#sprints)
* [Sprint Planning/CCB](#sprint-planningccb)
* [Deadlines](#deadlines)
* [Releases](#releases)
* [Demos/Retrospective](#demosretrospective)
* [WIP Limits](#wip-limits)
<!-- topstop -->

## Sprints

Sprints don't exist.  Thing of the past.  In an agile environment, we move fast, we break things. The expectation is that we will be able to fix/cure the system before the next "release", or these changes can be caught by QA/Automated Tests.  The weekly development cycle includes SW development AND QA efforts, or the entire development and test team.  

The engineering and QA efforts are aligned, continuous, and serial. They are not concurrent.  In other words, development work will complete for a week and allow QA the following week to complete verification and validation on that work and complete any paperwork to accompany that release.  Repeat.

_Note: Without sprints, we eliminate overburden wastes: meetings (for most team members) and sprint plannings and sprint retrospectives._

## Sprint Planning/CCB

Sprint Planning is not a function of engineering.  QMS and SET along with Product and Engineering Managers meet to groom the backlog and "Pitch" ideas for respective Engineering workstreams.

The idea is to identify items for the top of the backlog that will be pulled in as soon as resources are availble.

## Deadlines

In this recurring, weekly cycle, the expectation is that development is completed for QA by weeks end.  In fact, tickets requiring a larger QA manual effort should be code complete, code reviewed and merged to [`develop`] by end of day Thursday (or the second to last day of the week/cycle).  Tickets that are code-review only, with no manual verification, should be code complete, code reviewed and merged to [`develop`] by end of day Friday (or the last day of the week/cycle).

Items that do not make these deadlines are simply left on the board for the following week's deadline for QA. 

_Note: This continuous cycle eliminates Mura (unevenness)_

### Releases

We can release to customer sites a version that is feature complete.  That is, when we've reached completion on all features/bugs/tickets that need to be ushered into the customer release.

### Demos/Retrospective

It is still important to share engineering features we develop them, and schedule time to do so.  It certainly doesn't have to be weekly, or even bi-weekly.  

We also will, from time to time, need to reflect on the latest release or even development cycle to evaluate misses or larger mistakes, i.e. introducing bugs.  Again, scheduled as needed.

### WIP Limits

Each individual should carry no more than 1.5 tickets. A ticket, regardless of scope is only one unit. It refers to one topic/story/task and multiple tickets represent context switching (muda).  

At a team level, this means that the collective WIP Limit is the product of each user and 1.5.  i.e. A team of 4 engineers should have a WIP of 6.  WIP is imposed on the Implement Column/Swimlane.  Some engineers have greater than 1, but all should be less than or equal to 2 in order to satisfy the WIP Limit.
