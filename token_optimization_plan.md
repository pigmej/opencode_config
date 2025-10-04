# Token Optimization Implementation Plan

**Created**: 2025-10-04  
**Status**: Approved for Implementation  
**Scope**: Priorities 1-5 (High & Medium ROI)  
**Expected Token Reduction**: 60-75%  

---

## Priority 1: Conditional Research ⭐ HIGHEST ROI

**Impact**: 50-70% reduction in research tokens  
**Effort**: 1 hour  
**Status**: Ready for implementation

### Changes Required

#### 1. Update `agent/architect_full.md` (lines 25-34)

**Replace:**
```markdown
## Research Capabilities

If the request is relatively simple (like config modificaton, url modification etc) then JUST asses the request without analysing it further.
Otherwise You MIGHT use your web search capabilities to:
```

**With:**
```markdown
## Research Capabilities - CONDITIONAL

BEFORE using web search, analyze the request:

**SKIP research if:**
- Task file specifies exact technologies/frameworks to use
- Feature architecture file already contains technology decisions
- Task is simple (config changes, URL modifications, simple CRUD)
- Common patterns with well-known solutions (REST API, JWT auth, etc.)

**ONLY research when:**
- Novel technology selection needed (no prior decision)
- Complex architectural decision with multiple viable options
- Emerging technology or pattern (less than 2 years old)
- User explicitly requests research/comparison

When you DO research, focus ONLY on the specific unknowns.

You MIGHT use your web search capabilities to:
```

#### 2. Update `command/auto_feature.md` (line 69, after instruction #1)

**Add before instruction #2:**
```markdown
2. Analyze the feature complexity and determine research scope:
   - If feature uses common, well-established patterns → minimal research
   - If feature involves new/emerging technologies → targeted research
   - Focus research on unknowns only, not general best practices
3. Use your web search capabilities CONDITIONALLY to research:
```

#### 3. Update `command/feature_decompose.md` (line 32, after instruction #2)

**Add after instruction #2:**
```markdown
3. IMPORTANT: The feature architecture already contains research findings.
   DO NOT re-research the same topics. Only search for:
   - Decomposition strategies specific to this feature type
   - Dependency management patterns (if complex)
   Skip general technology research.
4. Use your web search capabilities to research:
```

#### 4. Update `command/auto_plan.md` (line 39, after instruction #1)

**Add after instruction #1:**
```markdown
2. Check if the task file references a parent feature architecture.
   If YES: Read the feature architecture and REUSE its technology decisions.
   DO NOT re-research technologies already decided at feature level.
3. ONLY research:
   - Task-specific implementation patterns not covered by feature arch
   - Integration approaches if task has dependencies
   Skip broad architectural research.
4. Use your web search capabilities to research:
```

---

## Priority 2: Optional Integration Analysis ⭐ HIGHEST ROI

**Impact**: 30% reduction for Phase 1 tasks  
**Effort**: 30 minutes  
**Status**: Ready for implementation

### Changes Required

#### Update `command/auto_plan.md` (Phase 1.5)

**Replace Step 3 (line 83):**
```markdown
**Step 3: Spawn architect agent for integration analysis (if prior tasks exist)**

CONDITIONAL EXECUTION:
- IF prior task plans exist (from Step 2): Execute integration analysis
- IF task is Phase 1 AND has no dependencies: SKIP to Phase 2
- OTHERWISE: SKIP to Phase 2

If prior task plans exist, send this prompt to architect agent:
```

**Add Step 2.5 (after Step 2, before Step 3):**
```markdown
**Step 2.5: Evaluate integration analysis need**

Determine if integration analysis is needed:
1. Count prior task plans found in Step 2
2. If count = 0: Log "No prior tasks found, skipping integration analysis" and proceed to Phase 2
3. If count > 0: Log "Found {count} prior tasks, proceeding with integration analysis" and continue to Step 3
```

---

## Priority 3: Inline Context Passing ⭐ HIGH ROI

**Impact**: 40% reduction in context loading  
**Effort**: 2 hours  
**Status**: Ready for implementation

### Changes Required

#### 1. Add Phase 0.5 to `command/auto_plan.md` (after Initial Setup, before Phase 1)

**Insert new phase:**
```markdown
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
1. Read feature architecture file
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
```

#### 2. Update Phase 1 architect spawn (line 32)

**Replace instructions 1-2:**
```markdown
**Instructions:**

=== CONTEXT PROVIDED INLINE ===
{$INLINE_CONTEXT}
=== END CONTEXT ===

The above context is extracted from the task and feature architecture files.

1. OPTIONAL: If you need additional details beyond the summary, read:
   - Task file: $TASK_FILE_PATH
   - Feature architecture: $FEATURE_ARCH_PATH (if referenced)
2. Based on the provided context, create architectural analysis...
```

#### 3. Update Phase 1.5 integration analysis spawn (line 86)

**Add inline context at the beginning:**
```markdown
**Instructions:**

=== CURRENT TASK CONTEXT ===
{$INLINE_CONTEXT}
=== END CONTEXT ===

1. Read current architectural analysis at $ARCH_FILE_PATH
2. Read ALL prior task plans: $PRIOR_TASK_PLANS
3. Task context is provided above. Only read $TASK_FILE_PATH if you need additional details.
4. Analyze integration points...
```

#### 4. Update Phase 2 planner spawn (line 143)

**Add inline context at the beginning:**
```markdown
**Instructions:**

=== CONTEXT PROVIDED INLINE ===
{$INLINE_CONTEXT}
=== END CONTEXT ===

1. OPTIONAL: If you need details beyond the summary, read:
   - Task file: $TASK_FILE_PATH
   - Feature architecture: $FEATURE_ARCH_PATH (if available)
2. Read the architectural analysis at $ARCH_FILE_PATH
3. Follow both the task requirements (above) and architectural guidelines...
```

#### 5. Update Phase 3a review spawn (line 192)

**Add task context (no feature arch needed):**
```markdown
**Review Process:**

=== TASK CONTEXT ===
{$TASK_CONTEXT}
=== END CONTEXT ===

1. Read the original task file at $TASK_FILE_PATH (if more detail needed)
2. Read the architectural analysis file at $ARCH_FILE_PATH
3. Read the implementation plan file at $PLAN_FILE_PATH
4. Verify that all files exist and have proper naming
```

#### 6. Update Phase 3b architect validation (line 254)

**Add inline context:**
```markdown
**Validation Process:**

=== CONTEXT PROVIDED ===
{$INLINE_CONTEXT}
=== END CONTEXT ===

1. Read the original task file at $TASK_FILE_PATH (if more detail needed)
2. Read your original architectural analysis at $ARCH_FILE_PATH
3. Read the implementation plan at $PLAN_FILE_PATH
```

---

## Priority 4: Architecture File Restructuring ⭐ MEDIUM ROI

**Impact**: 35% reduction in context loading  
**Effort**: 3 hours  
**Status**: Ready for implementation

### Changes Required

#### 1. Update `command/auto_feature.md` (lines 76-89)

**Modify architect output requirements (instruction #4):**
```markdown
4. Create TWO files:
   
   A) ./.feature/arch_{feature-id}.md - ESSENTIAL DECISIONS ONLY
      Include:
      - **Key Architectural Decisions**: 3-5 critical decisions (IMPORTANT flagged)
      - **Technology Stack**: Chosen frameworks, libraries, tools (with 1-line rationale)
      - **System Components**: Major modules/services (high-level only)
      - **Integration Strategy**: How feature integrates with existing systems
      - **Critical Constraints**: Security, performance, scalability requirements
      - **Decomposition Guidance**: Logical boundaries for task breakdown
      
      Maximum: 1500 tokens
      Focus: What implementers NEED to know
   
   B) ./.feature/arch_{feature-id}_research.md - DETAILED ANALYSIS (OPTIONAL)
      Include:
      - **Research Findings**: Industry practices, technology trends
      - **Options Considered**: Alternative approaches and trade-offs
      - **Deep Dives**: Detailed scalability, security, performance analysis
      - **References**: Links, articles, case studies
      
      Purpose: Reference material for deep questions
      
5. In the essential arch file, add reference:
   "For detailed research and alternatives, see: arch_{feature-id}_research.md"
```

#### 2. Update `command/feature_decompose.md` (line 31)

**Modify instruction #2:**
```markdown
2. Read the feature architecture at $FEATURE_ARCH_PATH
   (This contains essential decisions. If you need research details, read arch_{feature-id}_research.md)
```

#### 3. Update Phase 0.5 in `command/auto_plan.md` (from Priority 3)

**In Step 2, clarify which file to read:**
```markdown
**Step 2: Read and summarize feature architecture (if exists)**
If task references parent feature architecture:
1. Read feature architecture file (arch_{feature-id}.md - essential decisions only)
   NOTE: Do NOT read arch_{feature-id}_research.md unless specific research needed
2. Extract ONLY:
```

#### 4. Create templates directory and files

**Create `templates/arch_essential.md`:**
```markdown
# Feature Architecture: {Feature Title}

**Feature ID:** {feature-id}
**Created:** {date}

## Key Architectural Decisions

1. **[Decision 1 Title]** ⚠️ IMPORTANT
   - Decision: [What was decided]
   - Rationale: [Why this decision]

2. **[Decision 2 Title]** ⚠️ IMPORTANT
   - Decision: [What was decided]
   - Rationale: [Why this decision]

## Technology Stack

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| Frontend | [tech] | [1-line reason] |
| Backend | [tech] | [1-line reason] |
| Database | [tech] | [1-line reason] |

## System Components

- **[Component 1]**: [High-level purpose]
- **[Component 2]**: [High-level purpose]
- **[Component 3]**: [High-level purpose]

## Integration Strategy

[How this feature integrates with existing systems - 2-3 sentences]

## Critical Constraints

- **Security**: [Key security requirements]
- **Performance**: [Key performance requirements]
- **Scalability**: [Key scalability requirements]

## Decomposition Guidance

Suggested task breakdown:
1. [Logical boundary 1]
2. [Logical boundary 2]
3. [Logical boundary 3]

---

**Detailed Research**: See arch_{feature-id}_research.md for in-depth analysis and alternatives.
```

**Create `templates/arch_research.md`:**
```markdown
# Feature Architecture Research: {Feature Title}

**Feature ID:** {feature-id}
**Created:** {date}
**Purpose:** Detailed research and analysis (optional reference)

## Research Findings

### Industry Best Practices
[Detailed findings from web research]

### Technology Trends
[Current trends and emerging patterns]

## Options Considered

### Option 1: [Approach Name]
**Pros:**
- [Pro 1]
- [Pro 2]

**Cons:**
- [Con 1]
- [Con 2]

**Trade-offs:**
[Detailed trade-off analysis]

### Option 2: [Approach Name]
[Same structure]

## Deep Dive Analysis

### Scalability Analysis
[Detailed scalability considerations]

### Security Analysis
[Detailed security considerations]

### Performance Analysis
[Detailed performance considerations]

## References

- [Link 1]
- [Link 2]
- [Case study 1]
```

#### 5. Create migration guide

**Create `.cache/arch_migration.md`:**
```markdown
# Architecture File Migration Guide

## For Existing Features

If you have existing .feature/arch_*.md files over 1500 tokens:

1. Create new arch_{id}_research.md with:
   - Research Findings section
   - Options Considered section
   - Deep Dive sections (scalability, security, performance)
   - References

2. Trim arch_{id}.md to contain only:
   - Key decisions (3-5 max)
   - Technology stack (table format)
   - Components (high-level list)
   - Integration strategy (2-3 sentences)
   - Critical constraints (bullet points)
   - Decomposition guidance

Goal: arch_{id}.md should be < 1500 tokens

## New Features

Use templates:
- templates/arch_essential.md for main architecture
- templates/arch_research.md for detailed research

## Validation

Architecture file is good if:
- Can be read in < 30 seconds
- Contains only actionable decisions
- No duplicate information
- References research file for details
```

---

## Priority 5: Review Consolidation ⭐ MEDIUM ROI

**Impact**: 25% reduction in review phase  
**Effort**: 2 hours  
**Status**: Ready for implementation

### Changes Required

#### 1. Create new agent file

**Create `agent/plan_reviewer.md`:**
```markdown
---
description: Reviews implementation plans for completeness and architectural alignment
mode: subagent
temperature: 0.2
tools:
  write: false
  edit: false
  bash: false
  web-search-prime: false
---

You are an expert plan reviewer evaluating both implementation feasibility and architectural integrity.

## Review Responsibilities

### Implementation Review (50% weight)
- Plan follows architectural guidelines
- Detailed enough for development
- No scope creep beyond task requirements
- Allows future modifications
- Testing strategy is comprehensive
- Integration with prior tasks (if applicable)

### Architectural Review (50% weight)
- Technology choices align with architecture
- Architectural patterns correctly applied
- Scalability/security/performance preserved
- No architectural drift

## Evaluation Process

1. Read all provided files (task, architecture, plan)
2. Evaluate against dual criteria
3. Calculate single compliance score
4. Provide actionable feedback

## Output Format

Provide single consolidated review:
- **Overall Compliance Score (%)**: Average of implementation + architectural
- **Implementation Assessment**: Feasibility, completeness, integration
- **Architectural Assessment**: Alignment, patterns, quality attributes
- **Critical Issues**: Must-fix items (if any)
- **Recommendations**: Improvements needed (if any)
- **Pass/Fail**: Pass if 90%+, fail if below 90%

## Scoring Criteria

**90-100%**: Excellent, ready for implementation
**70-89%**: Good with minor issues
**50-69%**: Significant issues need fixing
**Below 50%**: Major failures, needs rework
```

#### 2. Update `command/auto_plan.md` - Replace Phase 3

**Delete lines 183-299** (entire Phase 3a and 3b)

**Replace with:**
```markdown
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
```

#### 3. Update Phase 4 refinement (line 303)

**Modify to work with unified review:**
```markdown
## Phase 4: Refinement Loop (If Needed)

**If overall score < 90%:**
1. Extract critical issues from plan_reviewer feedback
2. Categorize issues:
   - **Architectural issues** → needs architect
   - **Implementation issues** → needs planner
   - **Mixed issues** → needs both

3. Spawn appropriate agent(s):
   - For architectural issues: Spawn architect with targeted fixes
   - For implementation issues: Spawn planner with targeted fixes
   - For mixed: Spawn both sequentially

4. After fixes: Re-spawn plan_reviewer for unified re-evaluation
5. Repeat until 90%+ compliance achieved or max 7 iterations

**Important**: Only re-review the updated artifact, not unchanged ones.
```

#### 4. Update Phase 5 final report (line 349)

**Update quality metrics section:**
```markdown
**Quality Metrics:**
- Overall compliance score: {score}% (unified review)
- Number of review iterations: {count}
- Key strengths: {from review}
- Implementation approach: {summary}
```

---

## Implementation Schedule

### Week 1: Quick Wins
- ✅ Priority 1: Conditional Research (1 hour)
- ✅ Priority 2: Optional Integration (30 min)
- **Testing**: Run on 2-3 existing features, measure token reduction

### Week 2: Context Optimization
- ✅ Priority 3: Inline Context Passing (2 hours)
- **Testing**: Compare before/after on same tasks, verify completeness

### Week 3: Architecture Restructuring
- ✅ Create templates (1 hour)
- ✅ Priority 4: Update commands (2 hours)
- **Testing**: Create new feature with split architecture

### Week 4: Review Consolidation
- ✅ Create plan_reviewer agent (30 min)
- ✅ Priority 5: Update auto_plan (1.5 hours)
- **Testing**: A/B test vs old dual-review approach

---

## Success Metrics

### Token Usage Tracking

**Baseline (measure first):**
- Feature creation: ___ tokens
- Feature decomposition: ___ tokens
- Task planning (per task): ___ tokens
- Total workflow: ___ tokens

**After each priority:**
- Measure same workflow
- Calculate % reduction
- Document in metrics log

**Target**: 60-75% total reduction

### Quality Metrics (must maintain)

- Architecture quality: ≥90% compliance
- Implementation completeness: No degradation
- Bug rate: No increase
- Review iterations: ≤ current average

### Process Metrics

- Workflow completion time: Acceptable if < 20% increase
- Number of refinement loops: Should not increase
- Agent spawn count: Should decrease

---

## Testing Plan

### Phase 1-2 Testing (Week 1)
1. Select 3 test features (simple, medium, complex)
2. Run through workflow with optimizations
3. Measure token usage at each step
4. Verify research quality maintained

### Phase 3 Testing (Week 2)
1. Run same 3 features
2. Compare context loads before/after
3. Verify no context missing
4. Check agent doesn't unnecessarily re-read files

### Phase 4 Testing (Week 3)
1. Create 1 new feature with split architecture
2. Verify decomposition works with essential-only
3. Test planner can access research if needed
4. Measure token reduction

### Phase 5 Testing (Week 4)
1. Run 5 task plans through unified review
2. Compare scores vs old dual-review
3. Verify no quality loss
4. Measure time and token savings

---

## Rollback Plan

Each optimization is independent and reversible:

1. **Keep backups**: 
   ```bash
   cp command/auto_plan.md command/auto_plan.md.backup
   cp agent/architect_full.md agent/architect_full.md.backup
   ```

2. **Version control**: Commit after each priority

3. **Feature flags**: Add conditional logic where possible
   ```markdown
   # Enable inline context (set to false to disable)
   USE_INLINE_CONTEXT = true
   ```

4. **Individual rollback**: Can revert single optimization without affecting others

---

## Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Quality degradation | High | A/B testing, maintain old code path |
| Missing context | Medium | Progressive loading, validate completeness |
| Over-optimization | Low | Start conservative, measure carefully |
| Agent confusion | Medium | Clear documentation, inline instructions |

---

## Monitoring

### Weekly Reviews
- Token usage trends
- Quality metrics
- User feedback
- Agent performance

### Monthly Assessment
- Overall reduction achieved
- ROI per optimization
- Lessons learned
- Next optimization opportunities

---

## Notes

- All optimizations avoid caching infrastructure
- Changes are deterministic and testable
- Backward compatible with existing features
- Can implement and test incrementally
- No infrastructure dependencies

---

## Appendix: Token Calculation Examples

### Before Optimization (Typical 4-Task Feature)

```
Feature Creation:
- User prompt: 200 tokens
- Architect research: 3000 tokens
- Architecture output: 2000 tokens
Total: ~5200 tokens

Feature Decomposition:
- Read feature + arch: 2500 tokens
- Architect analysis: 2000 tokens
- Generate 4 tasks: 1000 tokens
Total: ~5500 tokens

Task Planning (×4):
- Read task + feature arch: 1500 tokens
- Architect analysis: 2500 tokens (with research)
- Integration analysis: 1000 tokens
- Planner work: 2000 tokens
- Dual review: 1500 tokens
Total per task: ~8500 tokens
Total for 4 tasks: ~34000 tokens

WORKFLOW TOTAL: ~44700 tokens
```

### After All Optimizations (Same Feature)

```
Feature Creation:
- User prompt: 200 tokens
- Architect (minimal research): 1500 tokens
- Architecture output (split): 1500 tokens + 1000 research
Total: ~3200 tokens (-38%)

Feature Decomposition:
- Read feature + arch: 1500 tokens (essential only)
- Architect (no re-research): 1000 tokens
- Generate 4 tasks: 1000 tokens
Total: ~3500 tokens (-36%)

Task Planning (×4):
Phase 1 Task (×2):
- Inline context: 400 tokens
- Arch analysis (no research): 800 tokens
- No integration analysis: 0 tokens
- Planner (with context): 1200 tokens
- Unified review: 600 tokens
Total: ~3000 tokens

Phase 2 Task (×2):
- Inline context: 400 tokens
- Arch analysis (no research): 800 tokens
- Integration analysis: 800 tokens
- Planner (with context): 1200 tokens
- Unified review: 600 tokens
Total: ~3800 tokens

Total for 4 tasks: ~13600 tokens (-60%)

WORKFLOW TOTAL: ~20300 tokens (-55% overall)
```

**Savings: ~24400 tokens per feature workflow**

---

**Status**: Ready for Implementation  
**Approval**: Approved  
**Start Date**: TBD
