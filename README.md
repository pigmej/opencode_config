# OpenCode Agent Orchestration Framework

A structured framework for developing software features through coordinated agent collaboration, architectural analysis, and systematic implementation.

## Overview

OpenCode provides two workflow types for different development needs:

### Simple Workflow (Tasks)
1. **Task Creation** (`/auto_task`) - Create structured task files from discussions
2. **Planning** (`/auto_plan`) - Generate comprehensive development plans with architectural analysis
3. **Orchestration** (`/orch`) - Execute the plan using coordinated agents

### Feature Workflow (Complex Projects)
1. **Feature Creation** (`/auto_feature`) - Create feature specification and architecture
2. **Feature Decomposition** (`/feature_decompose`) - Break feature into implementable tasks
3. **Task Planning** (`/auto_plan`) - Create detailed plans for each task
4. **Orchestration** (`/orch`) - Execute each task plan

See [FEATURE_WORKFLOW.md](FEATURE_WORKFLOW.md) for detailed feature workflow documentation.

## Stage 1: Task Creation (`/auto_task`)

### Purpose
Creates a structured task file based on discussion context, transforming conversation into actionable development requirements.

### Usage
```
/auto_task [additional suggestions and ideas]
```

### Process
1. **Task ID Generation**: Finds the highest numbered file in `./.task/` directory and adds 10
2. **File Naming**: Creates `{new-task-id}-{short-task-title}.md` using kebab-case (max 7 words)
3. **Content Structure**:
   - User feedback from discussion
   - Problem Statement
   - Requirements
   - Expected Outcome
   - Additional suggestions (from arguments)
   - Other important agreements (architectural decisions, direction, project context)

### Output
- Task file in `./.task/` directory
- Clear file path confirmation

### Example
After discussing authentication improvements:
```
/auto_task "Consider OAuth2 integration and multi-factor authentication"
```
Creates: `./.task/0030-user-authentication-system.md`

## Stage 2: Planning (`/auto_plan`)

### Purpose
Generates comprehensive development plans with architectural analysis and implementation details.

### Usage
```
/auto_plan ./.task/[filename].md @architect @sonnet
```

### Process Flow

#### Phase 0: Setup & Context Extraction
- **Purpose**: Validate input and extract context for all agents
- **Activities**:
  - Validate task file exists
  - Extract filename components and set file paths
  - Read and summarize task file (max 300 tokens)
  - Extract feature architecture summary if exists (max 400 tokens)
  - Prepare inline context for all subsequent phases
- **Reliability**: Clear error handling and termination conditions

#### Phase 1: Architectural Analysis
- **Agent**: `architect`
- **Retry Limit**: 3 attempts
- **Input**: Inline context + task file reference
- **Output**: `./.plan/arch_[basename].md`
- **Activities**:
  - Targeted web research (when needed)
  - Task-specific architecture patterns
  - Integration approach if dependencies exist
- **Reliability**: Automatic retry with clear failure handling

#### Phase 2: Implementation Planning
- **Agent**: First specified agent (`@agent_1`)
- **Retry Limit**: 3 attempts
- **Input**: Inline context + architectural analysis
- **Output**: `./.plan/[filename].md`
- **Activities**:
  - Component details and data structures
  - API design following architectural patterns
  - Integration strategy with prior tasks
  - Testing strategy
  - Development phases
- **Reliability**: Automatic retry with clear failure handling

#### Phase 3: Review
- **Agent**: Second specified agent (`@agent_2`)
- **Scoring System**: 
  - Implementation Feasibility (40%)
  - Architectural Alignment (30%)
  - Completeness (20%)
  - Integration Quality (10%)
- **Decision Logic**: 90%+ = PASS, <90% = NEEDS REFINEMENT
- **Activities**:
  - Read architectural analysis and implementation plan
  - Provide detailed percentage-based feedback
  - Generate specific improvement recommendations
- **Output**: Overall score + detailed breakdown + feedback

#### Phase 4: Refinement Loop
- **Trigger**: Overall score < 90%
- **Maximum Iterations**: 3 total
- **Process**: 
  - Categorize issues (architectural vs implementation)
  - Spawn appropriate agents (@agent_2 for architectural, @agent_1 for implementation)
  - Use actual review feedback (not placeholders)
  - Re-evaluate until 90%+ achieved or max iterations reached
- **Reliability**: Clear iteration limits and graceful completion

#### Phase 5: Final Completion
- **Trigger**: 90%+ score OR max refinement iterations reached
- **Output**: Comprehensive summary with quality metrics
- **Quality Metrics**: 
  - Final overall score with detailed breakdown
  - Iteration counts and improvement tracking
  - File paths and architectural decisions summary

### Output Files
- `./.plan/arch_[filename].md` - Architectural analysis
- `./.plan/[filename].md` - Implementation plan

### Reliability Features 🛡️
- **Retry Limits**: 3 attempts per phase with clear failure handling
- **Linear Flow**: Predictable Phase 0→1→2→3→4→5 progression
- **No Infinite Loops**: Eliminated "repeat till file exists" patterns
- **Proper Variable Substitution**: Real feedback content instead of placeholders
- **Graceful Degradation**: Best effort completion when limits reached

### Example
```
/auto_plan ./.task/110-jwt-token-service.md @architect @sonnet
```

## Stage 3: Orchestration (`/orch`)

### Purpose
Executes the development plan using coordinated agents with quality validation.

### Usage
```
/orch ./.task/[filename].md ./.plan/[filename].md grok sonnet
```

### Process Flow

#### Phase 0: Setup & Context Extraction
- **Purpose**: Validate input and extract context for all agents
- **Activities**:
  - Read and summarize task file (max 300 tokens)
  - Read and summarize plan file (max 400 tokens)
  - Prepare inline context for all phases

#### Phase 1: Plan Execution
- **Agent**: First specified agent (`grok`)
- **Input**: Inline context (task + plan summary)
- **Activities**:
  - Implement exactly as specified in plan summary
  - Track modified files for incremental review
  - No deviations or extra features
  - No commits (code changes only)

#### Phase 2: Implementation Review
- **Agent**: Second specified agent (`sonnet`)
- **Input**: Inline context + git diff of modified files only
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
  2. First agent fixes only identified issues (with inline context)
  3. Update modified files list
  4. Second agent re-reviews (only changed files)
  5. Repeat until 90%+ compliance

#### Phase 4: Completion
- **Output**: Final summary with compliance score and implementation details
- **No commits**: User decides when to commit changes

### Example
```
/orch ./.task/0030-user-authentication-system.md ./.plan/0030-user-authentication-system.md grok sonnet
```

## Complete Workflow Example

### Simple Task Workflow
```bash
# 1. Create task from discussion
/auto_task "Add OAuth2 support and consider security best practices"
# Output: ./.task/0030-user-authentication-system.md

# 2. Generate plan with architecture
/auto_plan ./.task/0030-user-authentication-system.md @architect @sonnet
# Output: 
# - ./.plan/arch_0030-user-authentication-system.md
# - ./.plan/0030-user-authentication-system.md

# 3. Execute plan
/orch ./.task/0030-user-authentication-system.md ./.plan/0030-user-authentication-system.md grok sonnet
# Output: Implemented code changes (not committed)
```

### Feature Workflow (Complex Projects)
```bash
# 1. Create feature with architecture
/auto_feature Implement user authentication system with JWT and OAuth2

# 2. Decompose into tasks
/feature_decompose ./.feature/100-user-authentication-system.md

# 3. Plan each task
/auto_plan ./.task/100_1_10-jwt-service.md @architect @sonnet
/auto_plan ./.task/100_1_20-oauth2-integration.md @architect @sonnet

# 4. Execute each task
/orch ./.task/100_1_10-jwt-service.md ./.plan/100_1_10-jwt-service.md grok sonnet
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
2. **Quality Assurance**: Percentage-based validation ensures high-quality output
3. **Architectural Integrity**: Research-backed architectural decisions
4. **Iterative Improvement**: Automatic refinement until 90%+ quality achieved
5. **Traceability**: Clear documentation trail from task to implementation
6. **Separation of Concerns**: Architecture, planning, and implementation are handled separately
7. **Reliability**: Retry limits, error handling, and no infinite loops 🛡️
8. **Flexibility**: Dynamic agent selection with customizable combinations

## Best Practices

1. **Task Creation**: Be specific about requirements and include architectural considerations
2. **Agent Selection**: Use different agents for planning (@agent_1) and review (@agent_2) to get diverse perspectives
3. **Quality Standards**: The 90% score threshold ensures high-quality deliverables
4. **Documentation**: Each stage produces comprehensive documentation for future reference
5. **No Premature Commits**: Review all changes before committing to maintain code quality
6. **Feature vs Task**: Use feature workflow for complex projects with 3+ related tasks
7. **Error Handling**: Monitor retry counts and check for graceful failure handling
8. **Quality Tracking**: Review percentage breakdowns to understand specific improvement areas

## File Structure

```
project/
├── .feature/                      # Feature workflow
│   ├── {id}-{title}.md           # Feature specifications
│   ├── arch_{id}.md              # Essential architecture
│   ├── arch_{id}_research.md     # Detailed research (optional)
│   └── {id}-decomposition.md     # Task breakdown
├── .task/
│   └── {id}-{title}.md           # Individual tasks
├── .plan/
│   ├── arch_{id}.md              # Task-specific architecture
│   └── {id}.md                   # Implementation plans
├── .cache/                        # Support files
│   └── arch_migration.md         # Migration guide
├── templates/                     # Architecture templates
│   ├── arch_essential.md         # Essential arch template
│   └── arch_research.md          # Research template
└── [your code files]             # Implementation results
```

---

This framework ensures systematic, high-quality feature development through coordinated agent collaboration while maintaining architectural integrity and code quality standards.