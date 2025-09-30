# Feature Workflow Documentation

This document explains the feature decomposition workflow that breaks down complex features into implementable issues.

## Overview

The feature workflow consists of three main stages:

```
FEATURE → ARCHITECTURE → ISSUES → PLANS
```

1. **Feature Creation** (`/auto_feature`) - Creates feature specification and architecture
2. **Feature Decomposition** (`/feature_decompose`) - Breaks feature into implementable issues
3. **Issue Planning** (`/auto_plan`) - Creates detailed plans for each issue

## Directory Structure

```
.feature/
  ├── {id}-{title}.md              # Feature specification
  ├── arch_{id}.md                 # Feature-level architecture
  └── {id}-decomposition.md        # Decomposition summary

.issue/
  └── {id}-{title}.md              # Individual issues (reference parent feature)

.plan/
  ├── arch_{issue-id}.md           # Issue-specific architecture
  └── {issue-id}.md                # Issue implementation plan
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

### Step 2: Decompose Feature into Issues

```bash
/feature_decompose ./.feature/100-user-authentication-system.md
```

**What happens:**
1. Architect agent analyzes the feature and architecture
2. Creates decomposition plan with logical issue breakdown
3. Generates multiple issue files in `.issue/` directory
4. Each issue includes:
   - Reference to parent feature
   - Link to feature architecture
   - Specific requirements
   - Dependencies
   - Implementation phase

**Output:**
```
✓ Feature Decomposed Successfully

Issues Created (4 total):
  • 110: jwt-token-service
  • 120: oauth2-integration
  • 130: user-session-management
  • 140: authentication-api-endpoints

Summary: ./.feature/100-decomposition.md
```

### Step 3: Plan Each Issue

```bash
/auto_plan ./.issue/110-jwt-token-service.md @architect @sonnet
```

**What happens:**
1. Creates issue-specific architecture (`.plan/arch_110.md`)
2. Creates detailed implementation plan (`.plan/110.md`)
3. Both reference and align with parent feature architecture

**Repeat for each issue:**
```bash
/auto_plan ./.issue/120-oauth2-integration.md @architect @sonnet
/auto_plan ./.issue/130-user-session-management.md @architect @sonnet
/auto_plan ./.issue/140-authentication-api-endpoints.md @architect @sonnet
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
- Issues follow component boundaries
- Each issue is independently deliverable
- Dependencies are tracked explicitly
- Implementation phases guide execution order

### 3. Hierarchical Structure
```
FEATURE (high-level architecture)
  ↓
ISSUES (implementation chunks, aware of feature arch)
  ↓
PLANS (detailed implementation, aligned with feature arch)
```

### 4. Incremental Delivery
- Issues can be implemented and tested independently
- Follow implementation phases for logical ordering
- Dependencies ensure proper sequencing

## File Relationships

```
.feature/100-user-auth.md
    ↓ (parent)
.feature/arch_100.md ←──────────┐
    ↓ (guides)                  │
    ├─ .issue/110-jwt.md ───────┤
    │    ↓ (detailed)           │ (references)
    │    ├─ .plan/arch_110.md ──┤
    │    └─ .plan/110.md ────────┤
    │                            │
    ├─ .issue/120-oauth.md ──────┤
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
- Decomposes feature into implementable issues
- Input: Path to feature file (e.g., `./.feature/100-user-auth.md`)
- Output: Multiple issue files + decomposition summary
- Does NOT automatically create plans

### `/auto_plan [issue-file-path] @architect @sonnet`
- Creates detailed implementation plan for an issue
- Input: Path to issue file
- Output: Architecture + implementation plan
- References parent feature architecture

## Best Practices

### When to Use Features
- Complex functionality requiring multiple components
- Cross-cutting concerns affecting multiple areas
- New major system capabilities
- Features that need coordinated architecture

### When to Use Direct Issues
- Small, isolated changes
- Bug fixes
- Simple enhancements
- Single-component modifications

### Decomposition Guidelines
- Aim for 2-8 issues per feature
- Each issue should be completable in reasonable time
- Follow natural component boundaries
- Consider dependencies and order

### Architecture Consistency
- Always review feature architecture before implementing issues
- Ensure issue plans align with feature architecture
- Use consistent technology stack across issues
- Maintain architectural patterns throughout

## Example: Full Workflow

```bash
# 1. Create feature
/auto_feature Build a real-time notification system with WebSocket support, email fallback, and user preferences

# Review: .feature/200-notification-system.md and .feature/arch_200.md

# 2. Decompose feature
/feature_decompose ./.feature/200-notification-system.md

# Output:
# - .issue/210-websocket-server.md
# - .issue/220-email-service.md
# - .issue/230-user-preferences.md
# - .issue/240-notification-api.md

# 3. Plan each issue
/auto_plan ./.issue/210-websocket-server.md @architect @sonnet
/auto_plan ./.issue/220-email-service.md @architect @sonnet
/auto_plan ./.issue/230-user-preferences.md @architect @sonnet
/auto_plan ./.issue/240-notification-api.md @architect @sonnet

# 4. Implement in order (check .feature/200-decomposition.md for sequence)
```

## Tips

1. **Start with clear feature descriptions** - Better input = better decomposition
2. **Review architecture before decomposition** - Ensure it aligns with your needs
3. **Follow implementation phases** - Respect dependencies
4. **Keep issues focused** - Each should have clear boundaries
5. **Reference architecture frequently** - Maintain consistency

## Troubleshooting

**Too many issues generated (>10)?**
- Feature is too large, break into multiple features

**Too few issues (1)?**
- Feature is too simple, use `/auto_issue` instead

**Issues have circular dependencies?**
- Review feature architecture, may need restructuring

**Architecture doesn't match needs?**
- Edit `.feature/arch_{id}.md` before decomposing
- Or edit feature description and regenerate