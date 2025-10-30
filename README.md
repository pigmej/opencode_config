# OpenCode Agent Orchestration Framework

A developer-focused framework for building software features through coordinated AI agent collaboration.

Requires opencode `v0.15.29+` because of positional arguments for across agent compatibility.

## Quick Start

### Prerequisites
- Git repository initialized
- OpenCode CLI installed

### Basic Usage
```bash
# Create a task from your current discussion
/auto_task "Add OAuth2 support to our authentication service"

# Plan the implementation with architectural analysis
/auto_plan ./.task/0030-add-oauth2-support.md @architect @sonnet

# Execute the plan with code generation
/orch ./.task/0030-add-oauth2-support.md ./.plan/0030-add-oauth2-support.md grok sonnet
```

## Core Commands

### `/auto_task` - Task Creation
Transforms conversation into structured development tasks.

**Usage:**
```bash
/auto_task [task description]
```

**What it does:**
- Creates numbered task files in `./.task/` directory
- Generates kebab-case filenames (max 7 words)
- Structures requirements and expected outcomes
- **Uses the current conversation context** as input when called after discussing requirements

**Example:**
```bash
# After discussing authentication requirements:
/auto_task "Implement JWT token refresh mechanism with Redis caching"
# Creates: ./.task/0040-implement-jwt-token-refresh.md
```

### `/auto_feature` - Feature Specification
Creates comprehensive feature specifications with architectural planning.

**Usage:**
```bash
/auto_feature [feature description]
```

**What it does:**
- Creates feature files in `./.feature/` directory
- Generates feature architecture documentation
- **Uses the current conversation context** as input when called after architectural discussions
- Breaks down complex features into manageable components

**Example:**
```bash
# After discussing system architecture:
/auto_feature "Implement user authentication system with JWT and OAuth2"
# Creates: ./.feature/100-user-authentication-system.md
```

### `/auto_plan` - Planning & Architecture
Generates comprehensive development plans with architectural analysis.

**Usage:**
```bash
/auto_plan ./.task/[filename].md @architect @agent_type
```

**File outputs:**
- `./.plan/arch_[filename].md` - Architectural analysis
- `./.plan/[filename].md` - Implementation plan

**Example:**
```bash
/auto_plan ./.task/0040-implement-jwt-token-refresh.md @architect @sonnet
```

### `/orch` - Code Execution
Executes development plans with quality validation.

**Usage:**
```bash
/orch ./.task/[filename].md ./.plan/[filename].md agent_1 agent_2
```

**Process:**
1. First agent implements the plan
2. Second agent reviews implementation quality
3. Iterative improvement until 90%+ compliance
4. Tracks modified files without automatic commits

**Example:**
```bash
/orch ./.task/0040-implement-jwt-token-refresh.md ./.plan/0040-implement-jwt-token-refresh.md grok sonnet
```

## Advanced Workflows

### Feature Workflow (Complex Projects)
For multi-task features requiring architectural coordination.

```bash
# 1. Create feature specification
/auto_feature "Implement user authentication system with JWT and OAuth2"

# 2. Decompose into individual tasks
/feature_decompose ./.feature/100-user-authentication-system.md

# 3. Plan each task
/auto_plan ./.task/100_1_10-jwt-service.md @architect @sonnet
/auto_plan ./.task/100_1_20-oauth2-integration.md @architect @sonnet

# 4. Execute each task
/orch ./.task/100_1_10-jwt-service.md ./.plan/100_1_10-jwt-service.md grok sonnet
```

### Simple Task Workflow
For straightforward development tasks.

```bash
# 1. Create task
/auto_task "Add input validation to user registration endpoint"

# 2. Generate plan
/auto_plan ./.task/0050-add-input-validation.md @architect @sonnet

# 3. Execute implementation
/orch ./.task/0050-add-input-validation.md ./.plan/0050-add-input-validation.md grok sonnet
```

## Agent Selection Guide

### Primary Development Agents
- **grok**: General coding and planning
- **sonnet**: General coding and planning
- **supernova**: General coding and planning
- **qwen3**: General coding and planning

### Specialized Agents
- **architect**: Architectural guidance, design patterns, system integration
- **security-auditor**: Security reviews, vulnerability identification
- **review**: Code quality, best practices, performance optimization

### Recommended Combinations
- **Planning**: `@architect @sonnet` (architecture + implementation)
- **Execution**: `grok sonnet` (implementation + review)
- **Security-Critical**: `@architect @security-auditor` (architecture + security)

## Quality Assurance

### Planning Quality (90%+ threshold)
- **Implementation Feasibility** (40%)
- **Architectural Alignment** (30%)
- **Completeness** (20%)
- **Integration Quality** (10%)

### Execution Quality (90%+ threshold)
- **Plan compliance** - follows specified implementation
- **Approach adherence** - uses planned methodology
- **Code quality** - follows best practices
- **Security considerations** - addresses vulnerabilities
- **Test coverage** - includes appropriate tests

## File Structure

```
project/
├── .task/                       # Individual tasks
│   └── {id}-{title}.md         # Task specifications
├── .plan/                       # Development plans
│   ├── arch_{id}.md            # Architecture analysis
│   └── {id}.md                 # Implementation plans
├── .feature/                    # Feature specifications (complex projects)
│   ├── {id}-{title}.md         # Feature requirements
│   ├── arch_{id}.md            # Feature architecture
│   └── {id}-decomposition.md   # Task breakdown
└── .cache/                      # Support files
    └── arch_migration.md       # Migration documentation
```

## Best Practices

### Task Creation
- Be specific about requirements and constraints
- Include architectural considerations when known
- Use clear, descriptive task titles
- **Discuss requirements first**, then call `/auto_task` to capture the conversation context

### Feature Creation
- **Discuss architecture and scope first**, then call `/auto_feature` to capture the conversation context
- Include integration points and dependencies
- Define clear success criteria

### Agent Selection
- Use different agents for planning and review to get diverse perspectives
- Match agent specialties to task requirements
- Consider security-auditor for authentication, data handling, or network code

### Quality Standards
- The 90% score threshold ensures high-quality deliverables
- Review percentage breakdowns to understand specific improvement areas
- Monitor retry counts for potential issues

### Development Workflow
- No premature commits - review all changes before committing
- Use feature workflow for complex projects with 3+ related tasks
- Track modified files for incremental review and testing

## Error Handling & Reliability

- **Retry Limits**: 3 attempts per phase with clear failure handling
- **Linear Flow**: Predictable progression through phases
- **No Infinite Loops**: Eliminated problematic "repeat till file exists" patterns
- **Graceful Degradation**: Best effort completion when limits reached

## Examples in Practice

### Adding Database Migration
```bash
/auto_task "Create database migration for user profile schema changes"
/auto_plan ./.task/0060-create-user-profile-migration.md @architect @sonnet
/orch ./.task/0060-create-user-profile-migration.md ./.plan/0060-create-user-profile-migration.md grok sonnet
```

### Implementing API Rate Limiting
```bash
/auto_task "Add rate limiting to public API endpoints using Redis"
/auto_plan ./.task/0070-add-api-rate-limiting.md @architect @sonnet
/orch ./.task/0070-add-api-rate-limiting.md ./.plan/0070-add-api-rate-limiting.md grok sonnet
```

### Security Audit Integration
```bash
/auto_task "Review authentication service for security vulnerabilities"
/auto_plan ./.task/0080-security-audit-auth-service.md @architect @security-auditor
/orch ./.task/0080-security-audit-auth-service.md ./.plan/0080-security-audit-auth-service.md grok sonnet
```

---

This framework provides a structured approach to AI-assisted development with clear separation between planning and execution, ensuring high-quality, architecturally sound software implementation.

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
