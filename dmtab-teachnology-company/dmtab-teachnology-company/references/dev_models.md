# Development Models Reference

## Overview

The development model determines how stages are organized at the project level. Each feature
within a cycle still follows the standard waterfall stage sequence internally.

## Models

### Waterfall

```
Requirements → Design → Architecture → Database → Dev → Test → Deploy
```

- **When to use**: Small projects (< 4 weeks), requirements are clear and stable, one-time delivery
- **Cycle structure**: One single pass through all stages
- **User involvement**: Heavy at start (requirements) and end (acceptance), lighter in middle
- **Risk**: Late discovery of issues — changes are expensive after architecture is set

### Incremental

```
Increment 1: [Req → Design → Arch → DB → Dev → Test → Deploy] → Deliver Module A
Increment 2: [Req → Design → Arch → DB → Dev → Test → Deploy] → Deliver Module B
Increment 3: [Req → Design → Arch → DB → Dev → Test → Deploy] → Deliver Module C
```

- **When to use**: Large system that decomposes into independent modules, need early value delivery
- **Cycle structure**: Each increment covers one feature module end-to-end
- **Planning**: Full requirements upfront, but design and dev per increment
- **Advantage**: Early delivery, easier to manage scope per increment

### Iterative

```
Iteration 1: [Req → Design → Arch → Dev → Test] → Version 0.1 (basic functionality)
Iteration 2: [Req → Design → Dev → Test] → Version 0.5 (refined features)
Iteration 3: [Req → Design → Dev → Test → Deploy] → Version 1.0 (production ready)
```

- **When to use**: Requirements are not fully known upfront, user feedback will shape the product
- **Cycle structure**: Each iteration expands and refines the same scope
- **Key difference from Incremental**: Same features get refined vs. new features added
- **Advantage**: Adapts to changing understanding, early user feedback

### Spiral

```
Cycle 1: [Risk Assessment → Req → Design → Proto → Review] → Risk resolved
Cycle 2: [Risk Assessment → Req → Design → Dev → Test → Review] → More risk resolved
Cycle N: [Risk Assessment → Final Dev → Test → Deploy] → Production
```

- **When to use**: High-risk projects, novel technology, large budget, compliance requirements
- **Cycle structure**: Each cycle starts with risk assessment before proceeding
- **Key addition**: Explicit risk analysis and prototyping phase per cycle
- **Advantage**: Risk mitigation, suits projects where technical uncertainty is high

### Scrum

```
Sprint 1 (2 weeks): [Planning → Dev → Test → Review → Retro] → Potentially shippable increment
Sprint 2 (2 weeks): [Planning → Dev → Test → Review → Retro] → Increment
Sprint N: ...
```

- **When to use**: Rapidly changing requirements, strong user feedback loop, experienced team
- **Cycle structure**: Fixed-length sprints, each containing a mini-waterfall for selected stories
- **Key practices**: Sprint planning, daily standups (role status updates), sprint review, retro
- **Adaptation in this simulation**: Each sprint still flows through relevant roles sequentially
- **Advantage**: Maximum flexibility, frequent delivery

## Model Selection Decision Tree

```
Are requirements clear and stable?
├── Yes → Is the project small (< 4 weeks)?
│   ├── Yes → Waterfall
│   └── No → Can features be independently delivered?
│       ├── Yes → Incremental
│       └── No → Iterative
└── No → Is there significant technical risk?
    ├── Yes → Spiral
    └── No → Scrum
```

## Switching Models Mid-Project

If during development it becomes clear the current model isn't working:
1. Project Manager raises the concern to the user
2. Explain why the current model is problematic
3. Propose an alternative with rationale
4. After user approval, update PROJECT_STATE.md and adjust the remaining plan

## Per-Feature Internal Flow

Regardless of the project-level model, each individual feature within a cycle/iteration/sprint
follows this internal sequence:

```
1. Product Manager refines the feature into user stories
2. UI Designer creates interface mockups for the feature
3. Architect reviews the feature's technical approach
4. DB Engineer adjusts schema if the feature needs it
5. Backend Engineer implements the feature's server-side logic
6. Frontend Engineer implements the feature's UI
7. Test Engineer verifies the feature works correctly
8. Security Engineer checks for vulnerabilities
```

This internal waterfall ensures each feature is properly thought through by all relevant roles.
