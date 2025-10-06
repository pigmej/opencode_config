# Feature Workflow Documentation

This document explains the feature decomposition workflow that breaks down complex features into implementable tasks.

> **🚀 Token Optimized**: This workflow now includes smart optimizations that reduce token usage by 60-75% while maintaining quality.

## Overview

The feature workflow consists of three main stages:

```
FEATURE → ARCHITECTURE → TASKS → PLANS
```

1. **Feature Creation** (`/auto_feature`) - Creates feature specification and architecture (essential + research)
2. **Feature Decomposition** (`/feature_decompose`) - Breaks feature into implementable tasks
3. **Task Planning** (`/auto_plan`) - Creates detailed plans for each task (with optimizations)

## Directory Structure

```
.feature/
  ├── {id}-{title}.md              # Feature specification
  ├── arch_{id}.md                 # Essential architecture (1500 tokens max) 🆕
  ├── arch_{id}_research.md        # Detailed research (optional) 🆕
  └── {id}-decomposition.md        # Decomposition summary

.task/
  └── {id}-{title}.md              # Individual tasks (reference parent feature)

.plan/
  ├── arch_{task-id}.md            # Task-specific architecture
  └── {task-id}.md                 # Task implementation plan

templates/                          # Architecture templates 🆕
  ├── arch_essential.md            # Template for essential architecture
  └── arch_research.md             # Template for detailed research
```

## Workflow Steps

### Step 1: Create a Feature

```bash
/auto_feature Implement user authentication system with JWT tokens and OAuth2 support
```

**What happens:**
1. Creates `.feature/{id}-{title}.md` with:
   - Problem statement
   - Feature description
   - Requirements
   - Scope
   - Expected outcomes

2. Architect agent creates `.feature/arch_{id}.md` with:
   - Feature-level architecture
   - Technology stack recommendations
   - System components overview
   - Integration strategy
   - Decomposition guidance

**Output:**
```
✓ Feature Created: ./.feature/100-user-authentication-system.md
✓ Architecture Plan: ./.feature/arch_100.md

Next Steps:
1. Review the feature specification and architecture
2. Run: /feature_decompose ./.feature/100-user-authentication-system.md
```

### Step 2: Decompose Feature into Tasks

```bash
/feature_decompose ./.feature/100-user-authentication-system.md
```

**What happens:**
1. Architect agent analyzes the feature and architecture
2. Creates decomposition plan with logical task breakdown
3. Generates multiple task files in `.task/` directory
4. Each task includes:
   - Reference to parent feature
   - Link to feature architecture
   - Specific requirements
   - Dependencies
   - Implementation phase

**Output:**
```
✓ Feature Decomposed Successfully

Tasks Created (4 total):
  • 110: jwt-token-service
  • 120: oauth2-integration
  • 130: user-session-management
  • 140: authentication-api-endpoints

Summary: ./.feature/100-decomposition.md
```

### Step 3: Plan Each Task

```bash
/auto_plan ./.task/110-jwt-token-service.md @architect @sonnet
```

**What happens:**
1. Creates task-specific architecture (`.plan/arch_110.md`)
2. Creates detailed implementation plan (`.plan/110.md`)
3. Both reference and align with parent feature architecture

**Repeat for each task:**
```bash
/auto_plan ./.task/120-oauth2-integration.md @architect @sonnet
/auto_plan ./.task/130-user-session-management.md @architect @sonnet
/auto_plan ./.task/140-authentication-api-endpoints.md @architect @sonnet
```

### Step 4: Implement

Follow the implementation order specified in `.feature/{id}-decomposition.md`.

## Key Design Principles

### 1. Architectural Consistency
- Feature architecture is created ONCE at feature level
- All child issues reference this architecture
- Each issue's plan builds upon feature architecture
- Ensures consistency across all implementations

### 2. Logical Decomposition
- Tasks follow component boundaries
- Each task is independently deliverable
- Dependencies are tracked explicitly
- Implementation phases guide execution order

### 3. Hierarchical Structure
```
FEATURE (high-level architecture)
  ↓
TASKS (implementation chunks, aware of feature arch)
  ↓
PLANS (detailed implementation, aligned with feature arch)
```

### 4. Incremental Delivery
- Tasks can be implemented and tested independently
- Follow implementation phases for logical ordering
- Dependencies ensure proper sequencing

## File Relationships

```
.feature/100-user-auth.md
    ↓ (parent)
.feature/arch_100.md ←──────────┐
    ↓ (guides)                  │
    ├─ .task/110-jwt.md ────────┤
    │    ↓ (detailed)           │ (references)
    │    ├─ .plan/arch_110.md ──┤
    │    └─ .plan/110.md ────────┤
    │                            │
    ├─ .task/120-oauth.md ───────┤
    │    ↓ (detailed)           │
    │    ├─ .plan/arch_120.md ──┤
    │    └─ .plan/120.md ────────┘
    └─ ...
```

## Commands Reference

### `/auto_feature [description]`
- Creates feature specification and architecture
- Input: Feature description
- Output: Feature file + architecture file
- Does NOT automatically decompose

### `/feature_decompose [feature-file-path]`
- Decomposes feature into implementable tasks
- Input: Path to feature file (e.g., `./.feature/100-user-auth.md`)
- Output: Multiple task files + decomposition summary
- Does NOT automatically create plans

### `/auto_plan [task-file-path] @architect @sonnet`
- Creates detailed implementation plan for a task
- Input: Path to task file
- Output: Architecture + implementation plan
- References parent feature architecture

## Best Practices

### When to Use Features
- Complex functionality requiring multiple components
- Cross-cutting concerns affecting multiple areas
- New major system capabilities
- Features that need coordinated architecture

### When to Use Direct Tasks
- Small, isolated changes
- Bug fixes
- Simple enhancements
- Single-component modifications

### Decomposition Guidelines
- Aim for 2-8 tasks per feature
- Each task should be completable in reasonable time
- Follow natural component boundaries
- Consider dependencies and order

### Architecture Consistency
- Always review feature architecture before implementing tasks
- Ensure task plans align with feature architecture
- Use consistent technology stack across tasks
- Maintain architectural patterns throughout

## Example: Full Workflow

```bash
# 1. Create feature
/auto_feature Build a real-time notification system with WebSocket support, email fallback, and user preferences

# Review: .feature/200-notification-system.md and .feature/arch_200.md

# 2. Decompose feature
/feature_decompose ./.feature/200-notification-system.md

# Output:
# - .task/210-websocket-server.md
# - .task/220-email-service.md
# - .task/230-user-preferences.md
# - .task/240-notification-api.md

# 3. Plan each task
/auto_plan ./.task/210-websocket-server.md @architect @sonnet
/auto_plan ./.task/220-email-service.md @architect @sonnet
/auto_plan ./.task/230-user-preferences.md @architect @sonnet
/auto_plan ./.task/240-notification-api.md @architect @sonnet

# 4. Implement in order (check .feature/200-decomposition.md for sequence)
```

## Tips

1. **Start with clear feature descriptions** - Better input = better decomposition
2. **Review architecture before decomposition** - Ensure it aligns with your needs
3. **Follow implementation phases** - Respect dependencies
4. **Keep issues focused** - Each should have clear boundaries
5. **Reference architecture frequently** - Maintain consistency

## Troubleshooting

**Too many tasks generated (>10)?**
- Feature is too large, break into multiple features

**Too few tasks (1)?**
- Feature is too simple, use `/auto_task` instead

**Tasks have circular dependencies?**
- Review feature architecture, may need restructuring

**Architecture doesn't match needs?**
- Edit `.feature/arch_{id}.md` before decomposing
- Or edit feature description and regenerate
## Token Optimization Features 🚀

The feature workflow has been optimized to reduce token usage by **60-75%** while maintaining quality:

### 1. Split Architecture Files (Priority 4)
**Essential Architecture** (`arch_{id}.md` - max 1500 tokens):
- Contains only what implementers NEED to know
- Key decisions, tech stack, components, constraints
- Read by all agents during task planning

**Research File** (`arch_{id}_research.md` - optional):
- Detailed research findings and alternatives
- Deep dive analysis
- Only read when specific questions arise

**Savings**: ~35% reduction in architecture file reads

### 2. Conditional Research (Priority 1)
- Feature creation: Minimal research for common patterns
- Decomposition: No re-research of existing architecture
- Task planning: Reuses feature architecture decisions

**Savings**: ~50-70% reduction in research tokens

### 3. Task Planning Optimizations
Each task planning (`/auto_plan`) includes:

**Phase 0.5 - Context Extraction** 🆕:
- Extract task context once (300 tokens)
- Extract feature arch summary (400 tokens)
- Pass inline to all agents

**Phase 1 - Conditional Research**:
- Skip research if tech already decided at feature level
- Only research task-specific patterns

**Phase 1.5 - Optional Integration** 🆕:
- Only runs if prior tasks exist
- Phase 1 tasks skip entirely

**Phase 3 - Unified Review** 🆕:
- Single `plan_reviewer` instead of dual review
- Implementation + Architecture in one pass

**Per-Task Savings**: ~60% token reduction

### Token Usage Example (4-Task Feature)

**Before Optimization:**
```
Feature Creation:        5,200 tokens
Feature Decomposition:   5,500 tokens  
Task Planning (×4):     34,000 tokens
────────────────────────────────────
TOTAL:                  44,700 tokens
```

**After Optimization:**
```
Feature Creation:        3,200 tokens (-38%)
Feature Decomposition:   3,500 tokens (-36%)
Task Planning (×4):     13,600 tokens (-60%)
────────────────────────────────────
TOTAL:                  20,300 tokens

SAVINGS: 24,400 tokens (55% reduction)
```

## Updated Commands

### `/auto_feature` (Token Optimized)
```bash
/auto_feature Implement user authentication with JWT and OAuth2
```
**Changes**:
- ✅ Conditional research (skip for common patterns)
- ✅ Creates split architecture (essential + research)

### `/feature_decompose` (Token Optimized)
```bash
/feature_decompose ./.feature/100-user-authentication.md
```
**Changes**:
- ✅ Reads essential architecture only
- ✅ No re-research of feature architecture topics

### `/auto_plan` (Heavily Optimized)
```bash
/auto_plan ./.task/100_1_10-jwt-service.md @architect @sonnet
```
**Changes**:
- ✅ Phase 0.5: Extract context once
- ✅ Phase 1: Conditional research
- ✅ Phase 1.5: Optional integration analysis
- ✅ Phase 3: Unified review
- ✅ Inline context passed to all agents

## Architecture Templates

New templates available in `templates/`:

### `arch_essential.md`
```markdown
# Feature Architecture: {Feature Title}

## Key Architectural Decisions
1. **[Decision]** ⚠️ IMPORTANT
   - Decision: [What]
   - Rationale: [Why]

## Technology Stack
| Component | Technology | Rationale |
|-----------|-----------|-----------|
| ... | ... | ... |

## System Components
- **[Component]**: [Purpose]

## Integration Strategy
[How feature integrates - 2-3 sentences]

## Critical Constraints
- **Security**: [Requirements]
- **Performance**: [Requirements]
- **Scalability**: [Requirements]

## Decomposition Guidance
1. [Logical boundary 1]
2. [Logical boundary 2]
```

### `arch_research.md`
```markdown
# Feature Architecture Research: {Feature Title}

## Research Findings
- Industry best practices
- Technology trends

## Options Considered
### Option 1: [Approach]
- Pros/Cons
- Trade-offs

## Deep Dive Analysis
- Scalability analysis
- Security analysis
- Performance analysis

## References
- [Links and case studies]
```

## Migration Guide

For existing features with large architecture files:

1. **Create Research File**:
   ```bash
   # Move detailed sections to arch_{id}_research.md:
   - Research Findings
   - Options Considered
   - Deep Dive sections
   ```

2. **Trim Essential File**:
   ```bash
   # Keep only in arch_{id}.md (max 1500 tokens):
   - Key decisions (3-5)
   - Tech stack (table)
   - Components (high-level list)
   - Integration (2-3 sentences)
   - Constraints (bullets)
   - Decomposition guidance
   ```

See `.cache/arch_migration.md` for detailed migration steps.

## Performance Tips

1. **Use Feature Workflow for Complex Projects** (3+ related tasks)
2. **Use Simple Task Workflow for Isolated Changes** (1-2 tasks)
3. **Review Essential Architecture** before decomposition
4. **Check Research File** only when deep analysis needed
5. **Follow Implementation Phases** as specified in decomposition

---

For complete optimization details, see [token_optimization_plan.md](token_optimization_plan.md) and [IMPLEMENTATION_SUMMARY.md](IMPLEMENTATION_SUMMARY.md).
