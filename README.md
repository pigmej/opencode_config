# OpenCode Agent Orchestration Workflow

This document outlines the complete workflow for using OpenCode's agent orchestration system to develop features through a structured three-stage process.

## Overview

The OpenCode workflow consists of three main stages:
1. **Issue Creation** (`/auto_issue`) - Create structured issue files from discussions
2. **Planning** (`/auto_plan`) - Generate comprehensive development plans with architectural analysis
3. **Orchestration** (`/orch`) - Execute the plan using coordinated agents

## Stage 1: Issue Creation (`/auto_issue`)

### Purpose
Creates a structured issue file based on discussion context, transforming conversation into actionable development requirements.

### Usage
```
/auto_issue [additional suggestions and ideas]
```

### Process
1. **Issue ID Generation**: Finds the highest numbered file in `./.issue/` directory and adds 10
2. **File Naming**: Creates `{new-issue-id}-{short-issue-title}.md` using kebab-case (max 7 words)
3. **Content Structure**:
   - User feedback from discussion
   - Problem Statement
   - Requirements
   - Expected Outcome
   - Additional suggestions (from arguments)
   - Other important agreements (architectural decisions, direction, project context)

### Output
- Issue file in `./.issue/` directory
- Clear file path confirmation

### Example
After discussing authentication improvements:
```
/auto_issue "Consider OAuth2 integration and multi-factor authentication"
```
Creates: `./.issue/0030-user-authentication-system.md`

## Stage 2: Planning (`/auto_plan`)

### Purpose
Generates comprehensive development plans with architectural analysis and implementation details.

### Usage
```
/auto_plan ./issue/[filename].md grok sonnet
```

### Process Flow

#### Phase 1: Architectural Analysis
- **Agent**: `architect`
- **Input**: Issue file
- **Output**: `./.plan/arch_[filename].md`
- **Activities**:
  - Web research for best practices
  - Technology recommendations
  - System architecture design
  - Security and scalability considerations
  - Integration patterns
  - Performance implications

#### Phase 2: Implementation Planning
- **Agent**: First specified agent (`grok`)
- **Input**: Issue file + architectural analysis
- **Output**: `./.plan/[filename].md`
- **Activities**:
  - Component details and data structures
  - API design following architectural patterns
  - User interaction flows
  - Testing strategy
  - Development phases
  - Dependencies identification

#### Phase 3: Dual Validation
**3a: Implementation Review**
- **Agent**: Second specified agent (`sonnet`)
- **Scoring**: Implementation feasibility (60%) + Technical soundness (40%)
- **Criteria**: Plan detail, scope adherence, testing strategy, technical approach

**3b: Architectural Validation**
- **Agent**: `architect`
- **Scoring**: Technology validation (30%) + Pattern integrity (30%) + Quality attributes (40%)
- **Criteria**: Alignment with recommendations, pattern application, scalability/security preservation

#### Phase 4: Refinement Loop (if needed)
- **Trigger**: Overall score < 90%
- **Process**: Targeted fixes based on specific feedback
- **Maximum**: 7 iterations
- **Agents**: Both `architect` and first agent make targeted improvements

#### Phase 5: Completion
- **Trigger**: 90%+ overall compliance
- **Output**: Comprehensive summary with quality metrics

### Output Files
- `./.plan/arch_[filename].md` - Architectural analysis
- `./.plan/[filename].md` - Implementation plan

### Example
```
/auto_plan ./issue/0030-user-authentication-system.md grok sonnet
```

## Stage 3: Orchestration (`/orch`)

### Purpose
Executes the development plan using coordinated agents with quality validation.

### Usage
```
/orch ./issue/[filename].md ./.plan/[filename].md grok sonnet
```

### Process Flow

#### Phase 1: Plan Execution
- **Agent**: First specified agent (`grok`)
- **Input**: Issue file + implementation plan
- **Activities**:
  - Read and understand requirements
  - Implement exactly as specified in plan
  - No deviations or extra features
  - No commits (code changes only)

#### Phase 2: Implementation Review
- **Agent**: Second specified agent (`sonnet`)
- **Input**: Issue + plan + git diff
- **Evaluation Criteria**:
  - Plan compliance (how many items implemented)
  - Approach adherence (follows plan's methodology)
  - Code quality and best practices
  - Security and performance considerations
  - Test coverage (if applicable)

**Scoring System**:
- 90-100%: Excellent execution, minor/no deviations
- 70-89%: Good execution with some issues
- 50-69%: Significant deviations or incomplete work
- <50%: Major failure to follow plan

#### Phase 3: Iterative Improvement (if needed)
- **Trigger**: Score < 90%
- **Process**: 
  1. Extract specific issues from review
  2. First agent fixes only identified issues
  3. Second agent re-reviews
  4. Repeat until 90%+ compliance

#### Phase 4: Completion
- **Output**: Final summary with compliance score and implementation details
- **No commits**: User decides when to commit changes

### Example
```
/orch ./issue/0030-user-authentication-system.md ./.plan/0030-user-authentication-system.md grok sonnet
```

## Complete Workflow Example

### 1. Discussion and Issue Creation
```bash
# After discussing user authentication improvements
/auto_issue "Add OAuth2 support and consider security best practices"
# Output: ./issue/0030-user-authentication-system.md
```

### 2. Planning with Architecture
```bash
/auto_plan ./issue/0030-user-authentication-system.md grok sonnet
# Output: 
# - ./.plan/arch_0030-user-authentication-system.md
# - ./.plan/0030-user-authentication-system.md
```

### 3. Development Execution
```bash
/orch ./issue/0030-user-authentication-system.md ./.plan/0030-user-authentication-system.md grok sonnet
# Output: Implemented code changes (not committed)
```

## Agent Recommendations

### Primary Development Agents
- **grok**: General coding and planning
- **sonnet**: General coding and planning  
- **supernova**: General coding and planning
- **qwen3**: General coding and planning

### Specialized Agents
- **architect**: Architectural guidance and design decisions
- **security-auditor**: Security audits and vulnerability identification
- **review**: Code quality and best practices review

## Key Benefits

1. **Structured Process**: Clear separation between planning and execution
2. **Quality Assurance**: Dual validation at each stage ensures high-quality output
3. **Architectural Integrity**: Research-backed architectural decisions
4. **Iterative Improvement**: Automatic refinement until quality thresholds are met
5. **Traceability**: Clear documentation trail from issue to implementation
6. **Separation of Concerns**: Architecture, planning, and implementation are handled separately

## Best Practices

1. **Issue Creation**: Be specific about requirements and include architectural considerations
2. **Agent Selection**: Use different agents for planning and execution to get diverse perspectives
3. **Quality Standards**: The 90% compliance threshold ensures high-quality deliverables
4. **Documentation**: Each stage produces comprehensive documentation for future reference
5. **No Premature Commits**: Review all changes before committing to maintain code quality

## File Structure

```
project/
├── .issue/
│   └── [id]-[title].md           # Issue files
├── .plan/
│   ├── arch_[filename].md        # Architectural analysis
│   └── [filename].md             # Implementation plans
└── [your code files]             # Implementation results
```

This workflow ensures systematic, high-quality feature development through coordinated agent collaboration while maintaining architectural integrity and code quality standards.