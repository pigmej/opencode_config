---
description: Auto planner - takes raw task and plans the execution
---

**Initial User Prompt:** $ARGUMENTS

You're the main agent for the Auto Planner workflow. Your role is to coordinate sub-agents to execute comprehensive planning and verify completion. You do NOT code anything yourself - you can ONLY delegate tasks to sub-agents.

## Initial Setup:
**FIRST** - Parse the initial user prompt and extract:
- Task file path (should be in ./.task/ directory)
- Agent names (assume the first agent mentioned is @agent_1 and the second agent mentioned is @agent_2 in the flow sequence)
- Any specific instructions or context

**File Structure and Variables:**
- Task file: ./.task/[filename].md → $TASK_FILE_PATH
- Architecture analysis: ./.plan/arch_[filename].md → $ARCH_FILE_PATH
- Implementation plan: ./.plan/[filename].md → $PLAN_FILE_PATH

**Example:** ./.task/auth.md → $TASK_FILE_PATH = "./.task/auth.md", $ARCH_FILE_PATH = "./.plan/arch_auth.md", $PLAN_FILE_PATH = "./.plan/auth.md"

## Error Handling:
- If the task file doesn't exist: Terminate with error message "Task file not found at specified path"
- Maximum retry attempts for Phase 4: 7 iterations. If 90% compliance not achieved after 5 attempts, proceed with highest score achieved and note in final summary


## Phase 0.5: Extract Essential Context

**Step 1: Read and summarize task context**
1. Read task file at $TASK_FILE_PATH
2. Extract:
   - Task ID and title
   - Parent feature ID (if exists)
   - Key requirements (list)
   - Expected outcome
   - Dependencies
3. Store as $TASK_CONTEXT (max 300 tokens)

**Step 2: Read and summarize feature architecture (if exists)**
If task references parent feature architecture:
1. Read feature architecture file (arch_{feature-id}.md - essential decisions only)
   NOTE: Do NOT read arch_{feature-id}_research.md unless specific research needed
2. Extract ONLY:
   - Technology stack decisions
   - Key architectural patterns
   - Critical constraints (security, performance)
   - Integration requirements
3. Store as $FEATURE_ARCH_SUMMARY (max 400 tokens)
4. Store full file path as $FEATURE_ARCH_PATH for reference

**Step 3: Prepare inline context**
Combine into $INLINE_CONTEXT:
```
=== TASK CONTEXT ===
{$TASK_CONTEXT}

=== FEATURE ARCHITECTURE (from {$FEATURE_ARCH_PATH}) ===
{$FEATURE_ARCH_SUMMARY}

Full details available at: {$FEATURE_ARCH_PATH}
```

## Phase 1: Architectural Analysis with architect agent

**Step 1: Spawn architect agent**

Send the following prompt to architect agent:

```
You're tasked with creating an architectural analysis for a development task. Research and analyze the architectural requirements, then create a comprehensive architectural analysis document.

**Instructions:**

=== CONTEXT PROVIDED INLINE ===
{$INLINE_CONTEXT}
=== END CONTEXT ===

The above context is extracted from the task and feature architecture files.

1. OPTIONAL: If you need additional details beyond the summary, read:
   - Task file: $TASK_FILE_PATH
   - Feature architecture: $FEATURE_ARCH_PATH (if referenced)
2. Check if the task file references a parent feature architecture.
   If YES: REUSE its technology decisions (provided in context above).
   DO NOT re-research technologies already decided at feature level.
3. ONLY research:
   - Task-specific implementation patterns not covered by feature arch
   - Integration approaches if task has dependencies
   Skip broad architectural research.
4. Use your web search capabilities to research current best practices, technologies, and architectural patterns relevant to this task
5. DO NOT commit any changes - focus only on architectural analysis
4. you MUST Create an architectural analysis file at ./.plan/arch_[task_filename].md (if task is 'auth.md', create './.plan/arch_auth.md') if you have nothing to add create empty file.
5. Your analysis should include:
   - **Context Analysis**: Summary of the architectural challenge
   - **Research Findings**: Current industry practices, technology trends, and best practices found through web search
   - **Technology Recommendations**: Specific frameworks, libraries, and tools with rationale
   - **System Architecture**: High-level system design and architectural patterns
   - **Scalability Considerations**: How the system can handle growth and load
   - **Security Architecture**: Security-first architectural approaches and considerations
   - **Integration Patterns**: How this will integrate with existing systems
   - **Performance Implications**: Architectural decisions that impact performance
   - **Implementation Guidance**: High-level architectural guidelines for implementation
6. Mark any critical architectural decisions as IMPORTANT for other agents
7. Focus on WHY architectural choices are made, not detailed HOW implementation
8. You MUST NOT write implementation code - only architectural guidance and analysis
9. If the task file contains existing feature architecture or architectural decisions, you MUST incorporate and align with those architectural choices in your analysis. That could be links to existing architecture files (in .features directory for example) or architectural decisions.
```

**Step 2: Receive architect analysis**
- Wait for architect agent to complete and provide the summary
- Receive completion confirmation from architect agent
- Verify the architectural analysis file exists at ./.plan/arch_[task_filename].md
- If file doesn't exist or has wrong name, restart Phase 1 with explicit file naming instructions
- Proceed to Phase 1.5 once architect indicates completion and file is verified
- Do not review the changes yourself, just move to Phase 1.5

## Phase 1.5: Integration Context Analysis

**Step 1: Check for dependent/prior tasks**

Extract from the task file:
- **Parent Feature ID** (if specified)
- **Dependencies** (list of dependent task IDs)
- **Implementation Phase** (e.g., Phase 1, Phase 2)
- Extract current task ID from filename (e.g., `1_20-auth-api.md` → task_id = `1_20`, phase = `1`)

**Step 2: Identify prior tasks in same feature**

If task is part of a feature (has parent feature ID or phase > 1):
1. List all tasks in ./.task/ directory with same feature ID
2. Identify all prior tasks: tasks in earlier phases OR tasks in same phase with lower sequence number
3. Example: If current task is `1_30`, prior tasks are `1_10` and `1_20`
4. For each prior task, check if plan exists at ./.plan/{prior-task-id}-*.md

**Step 2.5: Evaluate integration analysis need**

Determine if integration analysis is needed:
1. Count prior task plans found in Step 2
2. If count = 0: Log "No prior tasks found, skipping integration analysis" and proceed to Phase 2
3. If count > 0: Log "Found {count} prior tasks, proceeding with integration analysis" and continue to Step 3

**Step 3: Spawn architect agent for integration analysis (if prior tasks exist)**

CONDITIONAL EXECUTION:
- IF prior task plans exist (from Step 2): Execute integration analysis
- IF task is Phase 1 AND has no dependencies: SKIP to Phase 2
- OTHERWISE: SKIP to Phase 2

If prior task plans exist, send this prompt to architect agent:

```
You're analyzing integration requirements for a task that builds upon prior work in the same feature.

**Instructions:**

=== CURRENT TASK CONTEXT ===
{$INLINE_CONTEXT}
=== END CONTEXT ===

1. Read the current architectural analysis at $ARCH_FILE_PATH (replace with ./.plan/arch_[task_filename].md)
2. Read ALL prior/dependent task plans in this feature:
   $PRIOR_TASK_PLANS (replace with comma-separated list of ./.plan files from prior tasks)
3. Task context is provided above. Only read $TASK_FILE_PATH if you need additional details.
4. Analyze integration points between this task and prior work
5. Update the architectural analysis at $ARCH_FILE_PATH to add a new section:

**Integration Architecture Section to Add:**
---
## Integration with Prior Tasks

**Dependent Tasks:**
- [List all prior task IDs and their purpose]

**Integration Points:**
- [Specific APIs, components, data models, or services from prior tasks that this task will use]
- [Be explicit about what should be integrated vs. what should be built new]

**Data Flow:**
- [How data/state flows from prior tasks into this task]
- [What data structures or interfaces are shared]

**Anti-Patterns to Avoid:**
- DO NOT hardcode data that prior tasks provide through proper APIs/services
- DO NOT duplicate functionality already implemented in prior tasks
- DO NOT create temporary implementations if prior tasks provide the foundation
- [Any other specific integration anti-patterns for this context]

**Integration Testing:**
- [How to verify proper integration with prior work]
- [What integration points need testing]

**CRITICAL INTEGRATION REQUIREMENTS:**
[Mark any must-integrate items as IMPORTANT]
---

6. Be specific and actionable - reference exact component/API names from prior plans
7. Call out explicitly what should NOT be hardcoded or duplicated
8. Ensure this integration guidance is clear for implementation planning
```

**Step 4: Receive integration analysis**
- Wait for architect agent to complete integration analysis (if applicable)
- Verify the architectural analysis file has been updated with integration section (if applicable)
- Proceed to Phase 2

## Phase 2: Detailed Implementation Planning with @agent_1

**Step 1: Spawn @agent_1**

Send the following prompt to @agent_1:

```
You're tasked with creating a detailed implementation plan based on architectural analysis. Read the task, architectural analysis, and create a comprehensive implementation plan.

**Instructions:**

=== CONTEXT PROVIDED INLINE ===
{$INLINE_CONTEXT}
=== END CONTEXT ===

1. OPTIONAL: If you need details beyond the summary, read:
   - Task file: $TASK_FILE_PATH
   - Feature architecture: $FEATURE_ARCH_PATH (if available)
2. Read the architectural analysis at $ARCH_FILE_PATH (replace with ./.plan/arch_[task_filename].md)
3. **CRITICAL**: If the architectural analysis includes "Integration with Prior Tasks" section, read ALL referenced prior task plans to understand what already exists
4. Follow both the task requirements (above) and architectural guidelines from the analysis
5. DO NOT commit any changes - focus on implementation planning
6. Create implementation plan at ./.plan/[task_filename].md (same filename as task, different directory)
7. Your implementation plan should build upon the architectural foundation and include:
   - **Implementation Overview**: How to implement following the architectural guidelines
   - **Integration Strategy** (if applicable):
     * Review the "Integration with Prior Tasks" section from architectural analysis
     * Identify what components/APIs/data from prior tasks will be used (NO hardcoding!)
     * Specify exactly which prior implementations to integrate with
     * Document how to extend vs. build new functionality
     * List integration testing requirements
   - **Component Details**: Specific modules/components to create or modify (based on architecture)
   - **Data Structures**: Detailed data models and their relationships
   - **API Design**: Interfaces and contracts (following architectural patterns)
   - **User Interaction Flow**: Step-by-step user experience (if applicable)
   - **Testing Strategy**: Unit, integration, and system testing approach (include integration testing with prior tasks if applicable)
   - **Development Phases**: Logical sequence of implementation steps
   - **Dependencies**: Required libraries, frameworks, and external services
8. Ensure the plan aligns with the architectural decisions marked as IMPORTANT
9. **CRITICAL**: If prior tasks exist, ensure plan INTEGRATES with them rather than hardcoding or duplicating functionality
10. Include code snippets ONLY for illustration (API examples, interfaces, pseudo-code)
11. DO NOT write executable implementation code
12. You MUST NOT create any files other than the implementation plan file
```

**Step 2: Receive @agent_1 implementation plan**
- Wait for @agent_1 to complete and provide the summary
- Receive completion confirmation from @agent_1
- Verify the implementation plan file exists at ./.plan/[task_filename].md
- If file doesn't exist or has wrong name, restart Phase 2 with explicit file naming instructions
- Proceed to Phase 3 once @agent_1 indicates completion and file is verified
- Do not review the changes yourself, just move to Phase 3

## Phase 3: Unified Plan Review

**Step 1: Spawn plan_reviewer agent**

Send the following prompt to plan_reviewer agent:

```
You're reviewing an implementation plan for both feasibility and architectural alignment.

**Review Process:**

=== CONTEXT PROVIDED ===
{$INLINE_CONTEXT}
=== END CONTEXT ===

1. Read the architectural analysis at $ARCH_FILE_PATH
2. Read the implementation plan at $PLAN_FILE_PATH
3. OPTIONAL: Read task file at $TASK_FILE_PATH if more detail needed
4. Evaluate using dual criteria (50% implementation, 50% architectural)

**Implementation Criteria (50% of score):**
- Plan follows architectural guidelines
- Detailed enough for development
- No scope creep beyond requirements
- Maintainable and extensible
- Testing strategy is comprehensive
- Integration Review (if prior tasks exist):
  * Proper integration with prior components/APIs
  * No hardcoded data when APIs available
  * No duplication of existing functionality

**Architectural Criteria (50% of score):**
- Technology choices align with architecture
- Architectural patterns correctly applied
- Scalability, security, performance preserved
- No architectural drift from analysis

**Scoring:**
- 90-100%: Excellent, ready for implementation
- 70-89%: Good with minor issues
- 50-69%: Significant issues need fixing
- Below 50%: Major failures, needs rework

**Output Format:**
- **Overall Compliance Score (%)**: Single score
- **Implementation Assessment**: Detailed feasibility review
- **Architectural Assessment**: Alignment and patterns review
- **Integration Assessment** (if applicable): Prior task integration
- **Critical Issues**: List of must-fix items
- **Recommendations**: Specific improvements needed
- **Pass/Fail**: Pass if 90%+, fail otherwise
```

**Step 2: Analyze Review**
- Receive overall compliance score and feedback
- If score ≥ 90%: Proceed to Phase 5 (Final Completion)
- If score < 90%: Proceed to Phase 4 (Refinement Loop)

## Phase 4: Refinement Loop (If Needed)

**If overall score < 90%:**
1. Extract critical issues from plan_reviewer feedback
2. Categorize issues:
   - **Architectural issues** → needs architect
   - **Implementation issues** → needs planner
   - **Mixed issues** → needs both

3. Spawn appropriate agent(s):

**For Architectural Issues**: Spawn architect agent with this prompt:
```
You need to refine the architectural analysis based on review feedback. Read all existing documents and fix specific architectural issues.

**Instructions:**

=== CONTEXT PROVIDED ===
{$INLINE_CONTEXT}
=== END CONTEXT ===

1. Read the task file at $TASK_FILE_PATH (if more detail needed)
2. Read the current architectural analysis at $ARCH_FILE_PATH (replace with ./.plan/arch_[task_filename].md)
3. Read the implementation plan at $PLAN_FILE_PATH (replace with ./.plan/[task_filename].md)
4. Review the feedback and fix these specific architectural issues:
   $ARCHITECTURAL_ISSUES (replace with architectural issues from architect agent review)
5. Use web search to research better solutions if needed
6. Update the architectural analysis file at ./.plan/arch_[issue_filename].md
7. Make ONLY the fixes listed above - no other changes
8. Ensure architectural decisions support the implementation plan
9. Mark any updated critical decisions as IMPORTANT
```

**For Implementation Issues**: Spawn @agent_1 with this prompt:
```
You need to refine the implementation plan based on review feedback. Read all existing documents and fix specific implementation issues.

**Instructions:**

=== CONTEXT PROVIDED ===
{$INLINE_CONTEXT}
=== END CONTEXT ===

1. Read the task file at $TASK_FILE_PATH (if more detail needed)
2. Read the architectural analysis at $ARCH_FILE_PATH (replace with ./.plan/arch_[task_filename].md)
3. Read the current implementation plan at $PLAN_FILE_PATH (replace with ./.plan/[task_filename].md)
4. Fix these specific implementation issues:
   $IMPLEMENTATION_ISSUES (replace with implementation issues from @agent_2 review)
5. Make ONLY the fixes listed above - no other changes
6. Ensure the plan follows architectural guidelines
7. Update the implementation plan file at ./.plan/[task_filename].md
8. Provide a summary of specific fixes made
```

4. After fixes: Re-spawn plan_reviewer for unified re-evaluation
5. Repeat until 90%+ compliance achieved or max 7 iterations

**Important**: Only re-review the updated artifact, not unchanged ones.


## Phase 5: Final Completion

**When 90%+ compliance is achieved:**
1. Provide a comprehensive final summary to the user:
   - **Files Created:**
     - Architectural analysis: ./.plan/arch_[task_filename].md
     - Implementation plan: ./.plan/[task_filename].md
   - **Process Summary:**
     - Number of iterations required
     - Final overall compliance score (average of implementation + architectural)
     - Key architectural decisions made
     - Implementation approach selected
   - **Quality Metrics:**
     - Overall compliance score (unified review)
     - Number of review iterations
   - **Next Steps:**
     - Any remaining minor considerations
     - Recommended development sequence
2. Indicate that the Enhanced Auto Plan workflow is complete
3. Do NOT commit changes - let the user decide when to commit

**Workflow Benefits:**
- Research-backed architectural decisions
- Comprehensive implementation planning
- Dual-validation process (implementation + architectural integrity)
- Separation of architectural and implementation concerns
- Real-time validation against current industry best practices
