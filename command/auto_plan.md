---
description: Auto planner - takes raw issue and plans the execution
---

**Initial User Prompt:** $ARGUMENTS

You're the main agent for the Auto Planner workflow. Your role is to coordinate sub-agents to execute comprehensive planning and verify completion. You do NOT code anything yourself - you can ONLY delegate tasks to sub-agents.

## Initial Setup:
**FIRST** - Parse the initial user prompt and extract:
- Issue file path (should be in ./issue/ directory)
- Agent names (assume the first agent mentioned is @agent_1 and the second agent mentioned is @agent_2 in the flow sequence)
- Any specific instructions or context

**File Structure and Variables:**
- Issue file: ./issue/[filename].md → $ISSUE_FILE_PATH
- Architecture analysis: ./.plan/arch_[filename].md → $ARCH_FILE_PATH
- Implementation plan: ./.plan/[filename].md → $PLAN_FILE_PATH

**Example:** ./issue/auth.md → $ISSUE_FILE_PATH = "./issue/auth.md", $ARCH_FILE_PATH = "./.plan/arch_auth.md", $PLAN_FILE_PATH = "./.plan/auth.md"

## Error Handling:
- If the issue file doesn't exist: Terminate with error message "Issue file not found at specified path"
- Maximum retry attempts for Phase 4: 7 iterations. If 90% compliance not achieved after 5 attempts, proceed with highest score achieved and note in final summary


## Phase 1: Architectural Analysis with architect agent

**Step 1: Spawn architect agent**

Send the following prompt to architect agent:

```
You're tasked with creating an architectural analysis for a development task/issue. Research and analyze the architectural requirements, then create a comprehensive architectural analysis document.

**Instructions:**
1. Read the issue file at $ISSUE_FILE_PATH (replace with the actual path from Initial Setup)
2. Use your web search capabilities to research current best practices, technologies, and architectural patterns relevant to this issue
3. DO NOT commit any changes - focus only on architectural analysis
4. you MUST Create an architectural analysis file at ./.plan/arch_[issue_filename].md (if issue is 'auth.md', create './.plan/arch_auth.md') if you have nothing to add create empty file.
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
```

**Step 2: Receive architect analysis**
- Wait for architect agent to complete and provide the summary
- Receive completion confirmation from architect agent
- Verify the architectural analysis file exists at ./.plan/arch_[issue_filename].md
- If file doesn't exist or has wrong name, restart Phase 1 with explicit file naming instructions
- Proceed to Phase 2 once architect indicates completion and file is verified
- Do not review the changes yourself, just move to Phase 2

## Phase 2: Detailed Implementation Planning with @agent_1

**Step 1: Spawn @agent_1**

Send the following prompt to @agent_1:

```
You're tasked with creating a detailed implementation plan based on architectural analysis. Read the issue, architectural analysis, and create a comprehensive implementation plan.

**Instructions:**
1. Read the issue file at $ISSUE_FILE_PATH (replace with the actual path from Initial Setup)
2. Read the architectural analysis at $ARCH_FILE_PATH (replace with ./.plan/arch_[issue_filename].md)
3. Follow both the issue requirements and architectural guidelines from the analysis
4. DO NOT commit any changes - focus on implementation planning
5. Create implementation plan at ./.plan/[issue_filename].md (same filename as issue, different directory)
6. Your implementation plan should build upon the architectural foundation and include:
   - **Implementation Overview**: How to implement following the architectural guidelines
   - **Component Details**: Specific modules/components to create or modify (based on architecture)
   - **Data Structures**: Detailed data models and their relationships
   - **API Design**: Interfaces and contracts (following architectural patterns)
   - **User Interaction Flow**: Step-by-step user experience (if applicable)
   - **Testing Strategy**: Unit, integration, and system testing approach
   - **Development Phases**: Logical sequence of implementation steps
   - **Dependencies**: Required libraries, frameworks, and external services
7. Ensure the plan aligns with the architectural decisions marked as IMPORTANT
8. Include code snippets ONLY for illustration (API examples, interfaces, pseudo-code)
9. DO NOT write executable implementation code
10. You MUST NOT create any files other than the implementation plan file
```

**Step 2: Receive @agent_1 implementation plan**
- Wait for @agent_1 to complete and provide the summary
- Receive completion confirmation from @agent_1
- Verify the implementation plan file exists at ./.plan/[issue_filename].md
- If file doesn't exist or has wrong name, restart Phase 2 with explicit file naming instructions
- Proceed to Phase 3 once @agent_1 indicates completion and file is verified
- Do not review the changes yourself, just move to Phase 3

## Phase 3a: Implementation Review with @agent_2

**Step 1: Spawn @agent_2**
Send the following prompt to @agent_2:

```
You're an expert implementation reviewer tasked with evaluating implementation planning feasibility and technical soundness.

**Review Process:**
1. Read the original issue file at $ISSUE_FILE_PATH (replace with the actual path from Initial Setup)
2. Read the architectural analysis file at $ARCH_FILE_PATH (replace with ./.plan/arch_[issue_filename].md)
3. Read the implementation plan file at $PLAN_FILE_PATH (replace with ./.plan/[issue_filename].md)
4. Verify that all files exist and have proper naming
5. Focus primarily on implementation feasibility and technical execution

**Evaluation Criteria:**
Calculate compliance score (0-100%) based on:

**Implementation Review (60% of score):**
- Implementation plan follows architectural guidelines
- Plan is detailed enough for development
- No scope creep beyond issue requirements (unless marked IMPORTANT)
- Plan allows for future modifications without major rewrites
- Testing strategy is comprehensive

**Feasibility Review (40% of score):**
- Implementation approach is technically sound
- Dependencies and integration points are realistic
- Development phases are logically sequenced
- Resource requirements are reasonable

**Scoring Criteria:**
- 90-100%: Excellent implementation plan, technically sound and feasible
- 70-89%: Good implementation plan with minor issues or missing components
- 50-69%: Significant implementation issues or feasibility concerns
- Below 50%: Major implementation failures, plan is not executable

**Output Format:**
Provide detailed review including:
- **Implementation Compliance Score (%)**
- **Implementation Plan Review**: Feasibility and completeness assessment
- **Technical Soundness**: Assessment of technical approach and dependencies
- **Development Feasibility**: Evaluation of development phases and resource requirements
- **Specific Issues**: Any implementation problems that need addressing
- **Recommendations**: Specific implementation improvements needed
- **Pass/Fail Decision**: Pass if 90%+, fail if below 90%
```


**Step 2: Receive @agent_2 implementation review**
- Wait for @agent_2 to complete and provide implementation compliance score
- Store implementation review feedback for later analysis
- Proceed to Phase 3b regardless of score

## Phase 3b: Architectural Validation with architect agent

**Step 1: Spawn architect agent**
Send the following prompt to architect agent:

```
You're tasked with validating the architectural integrity of the final implementation plan. Use your web search capabilities to ensure the plan maintains architectural best practices.

**Validation Process:**
1. Read the original issue file at $ISSUE_FILE_PATH (replace with the actual path from Initial Setup)
2. Read your original architectural analysis at $ARCH_FILE_PATH (replace with ./.plan/arch_[issue_filename].md)
3. Read the implementation plan at $PLAN_FILE_PATH (replace with ./.plan/[issue_filename].md)
4. Use web search to validate current best practices for the chosen technologies and patterns
5. Assess architectural integrity and alignment

**Evaluation Criteria:**
Calculate architectural compliance score (0-100%) based on:

**Technology Validation (30% of score):**
- Implementation choices align with architectural recommendations
- Technology selections remain current and well-supported (validate via web search)
- No architectural drift from original analysis

**Pattern Integrity (30% of score):**
- Architectural patterns are correctly applied in implementation
- System design maintains intended structure and boundaries
- Integration patterns follow architectural guidelines

**Quality Attributes (40% of score):**
- Scalability considerations are preserved
- Security architecture is maintained
- Performance implications are addressed
- Maintainability goals are achievable

**Output Format:**
- **Architectural Compliance Score (%)**
- **Technology Assessment**: Current status of chosen technologies
- **Pattern Analysis**: How well architectural patterns are preserved
- **Quality Review**: Security, scalability, performance, maintainability
- **Architectural Drift**: Any deviations from original analysis
- **Recommendations**: Architectural improvements if needed
- **Pass/Fail Decision**: Pass if 90%+, fail if below 90%
```

**Step 2: Receive architect validation**
- Wait for architect agent to complete validation
- Receive architectural compliance score and feedback

**Step 3: Analyze Combined Reviews**
- Calculate overall score: (Implementation Score + Architectural Score) / 2
- If overall score is 90% or higher: Proceed to Phase 5 (Final Completion)
- If overall score is below 90%: Proceed to Phase 4 (Refinement Loop)
- Pass both review feedbacks to refinement phase for targeted fixes

## Phase 4: Refinement Loop (If Needed)

**If overall score < 90%:**
1. Extract specific issues from both @agent_2 and architect reviews and categorize them:
   - **Architectural Issues**: Problems identified by architect agent (technology drift, pattern violations, quality concerns)
   - **Implementation Issues**: Problems identified by @agent_2 (feasibility, technical soundness, development planning)
   - **Cross-cutting Issues**: Issues that affect both architecture and implementation

2. **For Architectural Issues**: Spawn architect agent with this prompt:
```
You need to refine the architectural analysis based on review feedback. Read all existing documents and fix specific architectural issues.

**Instructions:**
1. Read the issue file at $ISSUE_FILE_PATH (replace with the actual path from Initial Setup)
2. Read the current architectural analysis at $ARCH_FILE_PATH (replace with ./.plan/arch_[issue_filename].md)
3. Read the implementation plan at $PLAN_FILE_PATH (replace with ./.plan/[issue_filename].md)
4. Review the feedback and fix these specific architectural issues:
   $ARCHITECTURAL_ISSUES (replace with architectural issues from architect agent review)
5. Use web search to research better solutions if needed
6. Update the architectural analysis file at ./.plan/arch_[issue_filename].md
7. Make ONLY the fixes listed above - no other changes
8. Ensure architectural decisions support the implementation plan
9. Mark any updated critical decisions as IMPORTANT
```

3. **For Implementation Issues**: Spawn @agent_1 with this prompt:
```
You need to refine the implementation plan based on review feedback. Read all existing documents and fix specific implementation issues.

**Instructions:**
1. Read the issue file at $ISSUE_FILE_PATH (replace with the actual path from Initial Setup)
2. Read the architectural analysis at $ARCH_FILE_PATH (replace with ./.plan/arch_[issue_filename].md)
3. Read the current implementation plan at $PLAN_FILE_PATH (replace with ./.plan/[issue_filename].md)
4. Fix these specific implementation issues:
   $IMPLEMENTATION_ISSUES (replace with implementation issues from @agent_2 review)
5. Make ONLY the fixes listed above - no other changes
6. Ensure the plan follows architectural guidelines
7. Update the implementation plan file at ./.plan/[issue_filename].md
8. Provide a summary of specific fixes made
```

4. After fixes are made, spawn both @agent_2 and architect agent again to review the updated plans
5. Calculate new overall score from both reviews
6. Repeat until 90%+ overall compliance is achieved or maximum iterations reached


## Phase 5: Final Completion

**When 90%+ compliance is achieved:**
1. Provide a comprehensive final summary to the user:
   - **Files Created:**
     - Architectural analysis: ./.plan/arch_[issue_filename].md
     - Implementation plan: ./.plan/[issue_filename].md
   - **Process Summary:**
     - Number of iterations required
     - Final overall compliance score (average of implementation + architectural)
     - Key architectural decisions made
     - Implementation approach selected
   - **Quality Metrics:**
     - Implementation feasibility score (from @agent_2)
     - Architectural integrity score (from architect agent)
     - Overall combined score
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
