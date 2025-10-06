# OpenCode Agent Orchestration Workflow

This document outlines the complete workflow for using OpenCode's agent orchestration system to develop features through a structured process.

> **🚀 Token Optimized**: This workflow has been optimized to reduce token usage by 60-75% while maintaining quality.

## Overview

OpenCode supports two workflow types:

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

> **⚡ Token Optimized**: Conditional research, inline context passing, and unified reviews reduce token usage by ~60%.

### Usage
```
/auto_plan ./.task/[filename].md @architect @sonnet
```

### Process Flow

#### Phase 0.5: Extract Essential Context 🆕
- **Purpose**: Extract and prepare context once for all agents
- **Activities**:
  - Read and summarize task file (max 300 tokens)
  - Extract feature architecture summary if exists (max 400 tokens)
  - Prepare inline context for all subsequent phases
- **Benefit**: Eliminates redundant file reads (40% token savings)

#### Phase 1: Architectural Analysis
- **Agent**: `architect`
- **Input**: Inline context + task file reference
- **Output**: `./.plan/arch_[filename].md`
- **Token Optimizations**:
  - ✅ **Conditional Research**: Skips research if tech already specified or pattern is common
  - ✅ **Reuses Feature Architecture**: No re-research of parent feature decisions
  - ✅ **Inline Context**: Receives summarized context instead of reading full files
- **Activities**:
  - Targeted web research (only for unknowns)
  - Task-specific architecture patterns
  - Integration approach if dependencies exist

#### Phase 1.5: Integration Analysis (Conditional) 🆕
- **Agent**: `architect`
- **When**: Only runs if prior tasks exist
- **Token Optimization**: Phase 1 tasks skip this entirely (30% savings)
- **Activities**:
  - Analyze integration with prior task plans
  - Identify reusable components/APIs
  - Define integration requirements

#### Phase 2: Implementation Planning
- **Agent**: First specified agent (`sonnet`)
- **Input**: Inline context + architectural analysis
- **Output**: `./.plan/[filename].md`
- **Token Optimizations**:
  - ✅ **Inline Context**: No re-reading of task/architecture files
- **Activities**:
  - Component details and data structures
  - API design following architectural patterns
  - Integration strategy with prior tasks
  - Testing strategy
  - Development phases

#### Phase 3: Unified Plan Review 🆕
- **Agent**: `plan_reviewer` (new unified agent)
- **Scoring**: Implementation (50%) + Architecture (50%)
- **Token Optimization**: Single review instead of dual review (25% savings)
- **Criteria**:
  - Implementation: Feasibility, completeness, integration, testing
  - Architecture: Pattern alignment, tech choices, quality attributes

#### Phase 4: Refinement Loop (if needed)
- **Trigger**: Overall score < 90%
- **Process**: Targeted fixes based on specific feedback
- **Maximum**: 7 iterations
- **Token Optimization**: Only spawns needed agent (architect OR planner, not both)

#### Phase 5: Completion
- **Trigger**: 90%+ overall compliance
- **Output**: Comprehensive summary with quality metrics

### Output Files
- `./.plan/arch_[filename].md` - Architectural analysis
- `./.plan/[filename].md` - Implementation plan

### Token Savings Summary
- **Phase 0.5**: Inline context reduces redundant reads by ~40%
- **Phase 1**: Conditional research reduces unnecessary research by ~50-70%
- **Phase 1.5**: Skipped for Phase 1 tasks saves ~30%
- **Phase 3**: Unified review saves ~25%
- **Overall**: ~60% token reduction per task

### Example
```
/auto_plan ./.task/110-jwt-token-service.md @architect @sonnet
```

## Stage 3: Orchestration (`/orch`)

### Purpose
Executes the development plan using coordinated agents with quality validation.

> **⚡ Token Optimized**: Inline context passing and incremental diff tracking reduce token usage by ~50%.

### Usage
```
/orch ./.task/[filename].md ./.plan/[filename].md grok sonnet
```

### Process Flow

#### Phase 0.5: Extract Essential Context 🆕
- **Purpose**: Extract context once for all agents
- **Activities**:
  - Read and summarize task file (max 200 tokens)
  - Read and summarize plan file (max 300 tokens)
  - Prepare inline context for all phases
- **Benefit**: Eliminates redundant file reads (50% token savings)

#### Phase 1: Plan Execution
- **Agent**: First specified agent (`grok`)
- **Input**: Inline context (task + plan summary)
- **Token Optimization**: Receives 500-token summary instead of 2500-token full files
- **Activities**:
  - Implement exactly as specified in plan summary
  - Track modified files for incremental review
  - No deviations or extra features
  - No commits (code changes only)

#### Phase 2: Implementation Review
- **Agent**: Second specified agent (`sonnet`)
- **Input**: Inline context + git diff of modified files only
- **Token Optimization**: Only diffs modified files, not entire codebase
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
- **Token Optimization**: Uses inline context, only reviews newly changed files
- **Process**: 
  1. Extract specific issues from review
  2. First agent fixes only identified issues (with inline context)
  3. Update modified files list
  4. Second agent re-reviews (only changed files)
  5. Repeat until 90%+ compliance

#### Phase 4: Completion
- **Output**: Final summary with compliance score and implementation details
- **No commits**: User decides when to commit changes

### Token Savings Summary
- **Phase 0.5**: Context extracted once, passed inline (~50% reduction)
- **Phase 1**: 500-token summary vs 2500-token full files (80% reduction)
- **Phase 2**: Incremental diff of modified files only (30-50% reduction)
- **Phase 3**: Inline context + incremental review per iteration (50% reduction)
- **Overall**: ~50% token reduction per execution

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
- **plan_reviewer**: Unified plan review (implementation + architecture) 🆕
- **security-auditor**: Security audits and vulnerability identification
- **review**: Code quality and best practices review

## Key Benefits

1. **Structured Process**: Clear separation between planning and execution
2. **Quality Assurance**: Validation at each stage ensures high-quality output
3. **Architectural Integrity**: Research-backed architectural decisions
4. **Iterative Improvement**: Automatic refinement until quality thresholds are met
5. **Traceability**: Clear documentation trail from task to implementation
6. **Separation of Concerns**: Architecture, planning, and implementation are handled separately
7. **Token Efficiency**: 60-75% token reduction through smart optimizations 🆕

## Best Practices

1. **Task Creation**: Be specific about requirements and include architectural considerations
2. **Agent Selection**: Use different agents for planning and execution to get diverse perspectives
3. **Quality Standards**: The 90% compliance threshold ensures high-quality deliverables
4. **Documentation**: Each stage produces comprehensive documentation for future reference
5. **No Premature Commits**: Review all changes before committing to maintain code quality
6. **Feature vs Task**: Use feature workflow for complex projects with 3+ related tasks

## Token Optimization Features 🚀

This workflow has been optimized to reduce token usage by **60-75%** while maintaining quality:

### 1. Conditional Research
- Architects skip research when tech is already specified
- No research for common patterns (REST API, JWT, CRUD, etc.)
- Focus only on novel tech or complex architectural decisions
- **Savings**: 50-70% reduction in research tokens

### 2. Optional Integration Analysis
- Phase 1.5 only runs when prior tasks exist
- Phase 1 tasks skip integration analysis entirely
- **Savings**: ~30% tokens for early-phase tasks

### 3. Inline Context Passing
- Context extracted once in Phase 0.5
- All agents receive inline summaries
- Eliminates redundant file reads (6+ reads → 1 read)
- **Savings**: ~40% reduction in context loading

### 4. Architecture File Restructuring
- Essential architecture (`arch_{id}.md`) - max 1500 tokens
- Optional research file (`arch_{id}_research.md`) - detailed analysis
- Agents read only what they need
- **Savings**: ~35% reduction in architecture reads

### 5. Unified Review Process
- Single `plan_reviewer` agent replaces dual review
- One review instead of two sequential reviews
- Maintains same quality with fewer tokens
- **Savings**: ~25% reduction in review phase

See [token_optimization_plan.md](token_optimization_plan.md) for detailed optimization strategies.

## File Structure

```
project/
├── .feature/                      # Feature workflow
│   ├── {id}-{title}.md           # Feature specifications
│   ├── arch_{id}.md              # Essential architecture (1500 tokens max) 🆕
│   ├── arch_{id}_research.md     # Detailed research (optional) 🆕
│   └── {id}-decomposition.md     # Task breakdown
├── .task/
│   └── {id}-{title}.md           # Individual tasks
├── .plan/
│   ├── arch_{id}.md              # Task-specific architecture
│   └── {id}.md                   # Implementation plans
├── .cache/                        # Optimization support 🆕
│   └── arch_migration.md         # Migration guide
├── templates/                     # Architecture templates 🆕
│   ├── arch_essential.md         # Essential arch template
│   └── arch_research.md          # Research template
└── [your code files]             # Implementation results
```

## Performance Metrics

### Token Usage Comparison (4-Task Feature)

**Before Optimization:**
- Feature Creation: ~5,200 tokens
- Feature Decomposition: ~5,500 tokens
- Task Planning (×4): ~34,000 tokens
- **Total**: ~44,700 tokens

**After Optimization:**
- Feature Creation: ~3,200 tokens (-38%)
- Feature Decomposition: ~3,500 tokens (-36%)
- Task Planning (×4): ~13,600 tokens (-60%)
- **Total**: ~20,300 tokens

**Savings: ~24,400 tokens per feature (55% reduction)**

---

This workflow ensures systematic, high-quality feature development through coordinated agent collaboration while maintaining architectural integrity and code quality standards—now with significantly reduced token usage.
