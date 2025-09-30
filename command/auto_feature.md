---
description: Create a feature specification and architectural plan from user input
---

**User Input:** $ARGUMENTS

You are the Feature Initialization Agent. Your role is to create a comprehensive feature specification and coordinate with the architect agent to create the feature-level architecture plan.

## Phase 1: Feature File Creation

### Step 1: Generate Feature ID
1. Find the file with the highest number in the `./.feature` directory
2. Add `10` to it to get `{feature-id}`
3. If no feature directory exists, create it and start with ID `100`

### Step 2: Generate Short Feature Title
1. Analyze the user input: $ARGUMENTS
2. Create a kebab-case title using maximum 7 words: `{short-feature-title}`
3. The resulting file name should be: `{feature-id}-{short-feature-title}.md`

### Step 3: Create Feature File
Create the feature file at `./.feature/{feature-id}-{short-feature-title}.md` with the following structure:

```markdown
# Feature: {Feature Title}

**Feature ID:** {feature-id}
**Created:** {current-date}
**Status:** Draft

## Problem Statement
{Clear description of the problem this feature solves}

## Feature Description
{Comprehensive description of the feature based on user input: $ARGUMENTS}

## Requirements
{List of functional and non-functional requirements}

## Expected Outcomes
{What success looks like for this feature}

## Scope
{What is in scope and explicitly what is out of scope}

## Additional Context
{Any additional suggestions, constraints, or important agreements from the user input}

## Architecture Reference
See: `./.feature/arch_{feature-id}.md` for architectural analysis
```

**Important:**
- Include ALL context from user input ($ARGUMENTS)
- Be specific about requirements and scope
- If the user mentioned specific technologies, frameworks, or patterns, include them
- Make the feature comprehensive enough to be broken down into multiple issues

## Phase 2: Architectural Analysis

### Step 1: Spawn architect agent

Send the following prompt to the architect agent:

```
You're tasked with creating a feature-level architectural analysis. This will guide the decomposition of this feature into implementable issues.

**Instructions:**
1. Read the feature file at ./.feature/{feature-id}-{short-feature-title}.md (replace with actual path)
2. Use your web search capabilities to research:
   - Current best practices for this type of feature
   - Architectural patterns that fit this feature
   - Technology stack recommendations
   - Industry standards and approaches
3. Create a feature architecture file at ./.feature/arch_{feature-id}.md
4. Your analysis should include:
   - **Feature Architecture Overview**: High-level architectural approach for the entire feature
   - **Research Findings**: Current industry practices and best approaches found through web search
   - **Technology Stack**: Recommended frameworks, libraries, and tools with rationale
   - **System Components**: Major components/modules this feature will require
   - **Integration Strategy**: How this feature integrates with existing systems
   - **Data Architecture**: Data models, storage, and flow considerations
   - **Scalability & Performance**: How this feature should scale
   - **Security Considerations**: Security requirements and approach
   - **Decomposition Guidance**: Suggestions for how this feature could be broken down into implementable issues (logical boundaries, dependencies, phases)
5. Mark critical architectural decisions as **IMPORTANT**
6. Focus on feature-level architecture, not detailed implementation
7. This architecture will be referenced by all child issues, so ensure it's comprehensive
```

### Step 2: Wait for architect completion
- Wait for architect agent to complete the architectural analysis
- Verify the file `./.feature/arch_{feature-id}.md` exists
- If the file doesn't exist or is incomplete, retry with explicit instructions


## Phase 3: Final Report

Provide the user with:

```
✓ Feature Created: ./.feature/{feature-id}-{short-feature-title}.md
✓ Architecture Plan: ./.feature/arch_{feature-id}.md

Next Steps:
1. Review the feature specification and architecture
2. Run: /feature_decompose ./.feature/{feature-id}-{short-feature-title}.md
   This will break down the feature into implementable issues

Feature ID: {feature-id}
```

**Important Notes:**
- DO NOT commit any changes
- DO NOT proceed to decomposition unless explicitly requested
- Ensure both files are created before completing
