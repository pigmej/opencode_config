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
