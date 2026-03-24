# Spec Template

Use this template for Phase 2 specs. Not every section applies to every feature — omit sections that aren't relevant, but justify the omission.

---

## [Feature/Service Name]

### Problem Statement
What problem does this solve? For whom? Why now?

### Success Criteria
How do we know this worked? Be specific. Prefer measurable criteria.
- [ ] Criterion 1
- [ ] Criterion 2

### Proposed Solution

#### Overview
One paragraph summary of the approach.

#### Design

Describe the solution. Include:
- Architecture / component diagram (mermaid or prose)
- API contracts (endpoints, request/response shapes)
- Data model changes (new tables, schema migrations, field additions)
- Key algorithms or business logic

#### Tradeoffs
What alternatives were considered? Why was this approach chosen over them?

| Approach | Pros | Cons | Verdict |
|----------|------|------|---------|
| Option A | ... | ... | Chosen because... |
| Option B | ... | ... | Rejected because... |

### Dependencies
What does this depend on? What depends on this?
- Upstream: services, APIs, data sources this consumes
- Downstream: services, consumers, users affected by this change
- Infrastructure: new resources, config changes, permissions

### Risks and Open Questions
What could go wrong? What don't we know yet?

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| ... | ... | ... | ... |

### Out of Scope
What are we explicitly NOT doing? Restate from Phase 1 framing and add any new exclusions discovered during spec writing.

### Rollout Plan
How does this get to users?
- Feature flag strategy
- Migration plan (if applicable)
- Rollback plan

### Observability
How do we know this is working in production?
- Key metrics / SLIs
- Traces and spans
- Alerts and dashboards
- Log patterns to watch for
