# Kanban Board

<!-- markdown-toc -i branch.md -->
<!-- marked <filename>.md -o <filename>.html >
<!-- toc -->
* [Columns/Swimlanes](#columnsswimlanes)
* [Physical Kanban Board](#physical-kanban-board)
* [Standup](#standup)
<!-- tocstop -->

## Columns/Swimlanes

The idea is to completely simplify the steps.  Four (4) basic steps, with no additional intermediary steps for added confusion.

1. Specify (Open/Done) - This is largely the backlog, but it is really a step above the worklog.  Features/Bugs or Epics identified in the next release should be Specify Open, such that an engineer can notify Product/Engineering Management that they have capacity and identify the next item to pull into their workstream.  Then, the primary task, if not already completed, is gathering specifications, complimenting the ticket description, adding any additional comments and moving to `Specify Done`.
2. Implement (Open/Done) -  WIP.  A ticket is considered `Implement Open` when work has begun.  Until the ticket has been code reviewed and merged into `master`/`develop` it is considered `Open`.**
3. Verify (Open/Done) - Individual tickets that have been marked `Implement Done` can be pulled into Verification and tested in the develop environment.  Tickets that pass the Verification Steps are moved to `Done`.
4. Validate (Open/Done) - Once all tickets for a development cycle are ushered into a "release", the validation process begins on a test or staging environment to validate each ticket one at a time.


|                 | Specify        |                      | Implement             |                               | Verify            |               | Validate        |                  |
| --------------- | -------------- | -------------------- | --------------------- | ----------------------------- | ----------------- | ------------- | --------------- | ---------------- |
| __Name__        | __Open__   | __Done__         | __Open__        | __Done__                | __Open__       | __Done__   | __Open__   | __Done__    |
| __Description__ | Backlog        | Ticket Description   | Active Development    | Code Complete (with Review)   | QA Verification   |               | QA Validation   |                  |

** **Implement Track** - An additional lane could be added when a work has begun on a ticket, but it is actively blocked awaiting a decision, a client, a server, etc.  Something is blocking the ticket.  This lane exists between `Implement Open` and `Implement Done` and draws attention to the ticket, realizing that there may be no update.

## Physical Kanban Board

Whatever is online, should be duplicated on a physical board.  Swimlanes are taped to the wall, and then tickets are printed and posted to the wall in the actual swimlanes.  Tickets in the backlog that have been prioritized can also be printed and compartmentalized per discipline or expertise.  i.e. All the tickets pertaining to Pronghorn could go in one folder, likewise for any DICOM work.  Engineer's names are written clearly on each ticket for the purpose of identification.

## Standup

Each day (or each day we decide to stand-up), we are only talking about the tickets on the board.  Standup should typically be run by a "scrum-master", which is either the lead QA or the lead engineer/engineering manager.  Working from RIGHT to LEFT, we discuss tickets in `Validate` > `Verify` > `Implement` > (and maybe, typically not) `Specify`.

Each ticket owner will speak to the status of a ticket and describe what is needed to complete that ticket, or describe any blockers (meetings, process, decisions, etc) for completing that work.

After tickets are reviewed, there are typically a few standard questions?

* _Is there anything not on the board that we should talk about?_ - This engages individuals to comment on either a ticket that is not on the board, an effort that has not been captured within a ticket, OR an opportunity to create shared awareness around a problem or a solution.
* _Is anyone looking for work?_ - If any individual is under-utilized, they should be able to speak up and ask for more work.  This way, we can identify important work as a group and select ticket(s) to pull in.
* _Is anyone blocked or looking for help?_ - We need to move forward as a team, so asking for help is a good thing.  A volunteer from the team may be able to help unblock the individual and go back to his/her own tickets.
