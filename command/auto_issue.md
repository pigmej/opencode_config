---
description: Create issue based on the discussion from this session
---

Given the context of discussion, please create a new issue in ./.issue directory. To create issue you MUST follow these steps.

# Preparations

1. new issue id
You MUST find the file with biggest number in the ./.issue directory. Add `10` to it. That's your {new-issue-id}

2. short issue title
You MUST use kebab-case maximum 7 words. This becomes {short-issue-title}

3. file name
The resulting file name should be like `{new-issue-id}-{short-issue-title}.md` (replace variables with actual values from above), you MUST follow that naming schema.


# Issue creation

## Issue content
1. Make sure to include user feedback in the description
2. You MUST include Problem Statement in the description
3. You MUST include Requirements in the description
4. You MUST include Expected Outcome in the description
5. You MIGHT specify Additional suggestions and ideas depending on the User input which is: $ARGUMENTS
6. If during the discussion there were some important agreements about architecture decissions, about direction, about project context you MUST include them in the issue also in the section "Other important agreements"

## Additional informations
1. You MIGHT want to read maximum a few files from ./.issue directory to understand the format and User prefference. If you do so please try to follow format from the existing issues



Give the user clear information about the created issue file with created file path.
