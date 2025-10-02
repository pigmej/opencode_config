I want to extend the flow that I have in my .plan and .task.

I'd like to have also .feature directory.

The idea would be that FEATURE is decomposited into multiple implementable TASKS and then each of the TASKS has it's own PLAN.

I need an AI generated feature command that would take care about it. Make sure that it's adjusted for tech / programming / architecture tasks. BUT focus on breaking down complex feature into smaller pieces, not necessarily. I think we should have ONE architecture plan though for FEATURE. And then every TASK / PLAN should be aware of that ARCH part.


So to summarize I need you:
1. To create a feature command that will create a new feature in .feature directory, with an description from USER input. Base it on @command/auto_task.md
2. After execution of that command, an AI agent (up to you to decide) needs to break it down to smaller and deliverable tasks (kinda similar to @command/auto_task.md executed on each of the splitted chunks).

So as a result we should have one FEATURE file and one OR multiple TASK files created from single FEATURE.
