## Kanban
<!-- markdown-toc -i branch.md -->
<!-- marked <filename>.md -o <filename>.html >
<!-- toc -->

* [Watch](#watch)
* [What is Kanban?](#what-is-kanban)
* [Main Objectives, eliminate the three M's (waste)](#main-objectives-eliminate-the-three-ms-waste)
* [What is Muri?](#what-is-muri)
* [What is Mura?](#what-is-mura)
* [What is Muda?](#what-is-muda)
* [Pull Systems vs. Push Systems](#pull-systems-vs-push-systems)
<!-- tocstop -->

### What is Kanban?

#### Watch

<iframe width="560" height="315"  style="min-width:560px !important;" src="https://www.youtube.com/embed/CD0y-aU1sXo" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

[Eric Brechner: "Agile Project Management with Kanban" | Talks at Google](https://www.youtube.com/watch?v=CD0y-aU1sXo)

Is a scheduling system for lean manufacturing and just-in-time manufacturing (JIT) developed by Taiichi Ohno, and Industrial Engineer at Toyota to improve efficiency.

### Main Objectives, eliminate the three M's (waste)

1. Remove overburden/unreasonable asks (muri)
2. Remove inconsistency/unevenness (mura)
3. Remove waste (muda)

### What is Muri?

Overburden would be too much work for the given resource(s), such that the expectation of [quality] output is not conceivable. 

In other words, you create Muri every time you put your team under stress by demanding unreasonable or unnecessary work that exceeds their capacity.

Muri can drastically decrease productivity and efficiency.  Translates to extra working hours and burnouts. Negatively affects team's morale.

### What is Mura?

Mura is the waste of unevenness or inconsistency.  

Example: "Hockey Stick" at the end of a sprint:

> 
> For example, a software development team practicing Scrum plans to release 10 new features within their next sprint, which is let’s say 4 weeks long. They fill the backlog and start processing task after task. Knowing that they’ve got plenty of time ahead, at first, they start working casually, picking up the pace as days roll by. By the beginning of the last week, the team realizes that they are falling behind schedule.
>
> As a result, in the next few days each member puts in an extra effort and at the cost of hours overtime and a lot of stress, they manage to finish the sprint successfully (in the best case scenario).
> __Due to the fatigue that they’ve accumulated during the fourth week, at the beginning of the next sprint, they start slowly again and the cycle repeats.__

### What is Muda?

Traditionally, there are 7 wastes, plus an additional modern work environment waste that is known as DOWNTIME

- __D__ efects - Simply, making mistakes.
- __O__ verproduction - Asking employees to do too much work.
- __W__ aiting - Due to interruptions, context switching, or blocking further output.
- __N__ ot using talent - The new waste*, not using an employee's talents reduces their motivation and engagement.
- __T__ ransportation - Moving, typically within a product line. 
- __I__ nventory excess - Or building the wrong product.   
- __M__ otion - Related to Transportation, but within the organization In software, this translates more to devops.  i.e. setting up an environment, scripting/automating repetitive tasks
- __E__ xcess Processing / Overprocessing - Happens when work is not organized well or lack of documentation to repeat tasks.

In terms of software development, waste is introduced in several forms: context switching, blockers, and any wait states/buffer columns.

### Pull Systems vs. Push Systems

A pull system is a Lean technique for reducing the waste of any production process. Applying a pull system allows you to start new work only when there is a customer demand for it. This gives you the opportunity to reduce overhead.

Kanban is a PULL system.  Meaning when a engineer/QA/SET brings work into their workstream when they have capacity. Rather than assign tickets to an engineer to be completed in the next week or two.
