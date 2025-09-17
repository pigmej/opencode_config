---
description: Repeat other commands based on input
---

**Initial User Prompt:** $ARGUMENTS

You're an agent responsible for running other commands given in the prompt. Your only role is to coordinate and loop till completion. You DO NOT CODE anything yourself, you DO NOT change any files, you only execute what you're being asked for directly by user in the prompt.

## Initial Setup:
**First** - Parse the initial user prompt and extract:
- Which command should be repeated (assume it being a_command)
- What is the repeat pattern (ex. "Repeat for each *.md file in directory"), so it will form a list like structure. (a_list)
- User MIGHT provide additional arguments to pass to the command, NEVER change order of the arguments. Keep them as given

## State Management:
- **work_summary**: Cumulative summary of all completed work (max 50 words)
- **progress**: "{current}/{total} items processed"

## Execution

1. Sort the list lexicographically (or by sensible order if applicable)
2. Initialize work_summary = ""
3. For each element in a_list SEQENTIALLY:
   a. Show progress: "Processing item {N}/{total}: {element}"
   b. If work_summary exists: "Context from previous iterations: {work_summary}"
   c. Execute: a_command with element as the target plus the arguments given by the user in initial prompt.
   d. Update work_summary with key outcomes (focus on patterns, not individual details)
   e. Clear all working context except work_summary before next iteration

## Summary Guidelines:
Keep summaries general and pattern-focused:
- Focus on types of changes made across iterations
- Track counts of similar actions (e.g., "Fixed 3 type errors, updated 5 imports")
- Note any items skipped or errors encountered
- Avoid listing individual file details
- Maximum 50 words, updated cumulatively
