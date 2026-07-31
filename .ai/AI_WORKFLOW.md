# AI Workflow

## Shared process

All AI systems working on PanoFolio follow the same sequence:

```text
Discuss
↓
Design
↓
Document
↓
Review
↓
Approve
↓
Implement
↓
Test
↓
Release
```

## Responsibility model

### ChatGPT

- Product architecture
- System architecture
- UX decisions
- Database and API design
- Documentation
- Final implementation review

### Cursor

- Primary UI and application implementation
- Components
- Screens
- Refactoring

### Claude

- Large-document review
- Large-codebase review
- Architecture consistency
- Refactoring suggestions

### Codex

- Backend
- SQL
- Tests
- DevOps
- Utilities
- Focused feature implementation

## Handoff rules

Every handoff must include:

- Relevant document paths
- Approved scope
- Constraints
- Acceptance criteria
- Files or systems expected to change
- Tests required
- Known exclusions

## Conflict resolution

When an AI detects a conflict:

1. Stop the conflicting implementation.
2. Identify the documents or code involved.
3. Explain the inconsistency.
4. Return the issue for architectural review.
5. Update documentation after approval.
6. Resume implementation only after the source of truth is clear.

## Completion rule

No AI may mark work complete until implementation, tests, and documentation agree.
