# Module 33 — Senior Product Engineering and Technical Leadership

> **Module guide:** Practice the work expected of a senior engineer on a product team: understand the user problem, make sound technical trade-offs, deliver safely, and take responsibility for what happens after launch.

This module complements the subject guides. It focuses on applying them while working with Product, Design, Data, Security, Operations, and other engineering teams.

## Learning outcomes

- Turn an unclear request into a problem statement, user outcome, and measurable success criteria.
- Break broad work into useful slices and explain scope, estimates, risks, and trade-offs.
- Make architecture and API decisions that account for compatibility, operations, and future change.
- Plan releases with observability, safety checks, rollback or recovery, and clear ownership.
- Lead through design reviews, mentoring, incident response, and cross-team collaboration.

## Topic map

- **Product thinking:** users, discovery, prioritization, outcome metrics, experiments, and MVP boundaries.
- **Technical direction:** design proposals, ADRs, system boundaries, API evolution, cost, and technical debt.
- **Delivery:** slicing work, estimation, dependency management, feature flags, canaries, and launch readiness.
- **Production ownership:** SLI/SLOs, error budgets, on-call, incident leadership, postmortems, and observability.
- **Engineering quality:** testing strategy, migrations, performance, accessibility, security, and privacy.
- **Senior collaboration:** mentoring, delegation, code review, disagreement, stakeholder updates, and measurable impact.

## Question sets

- [40 easy questions](easy-interview-questions.md)
- [40 medium questions](medium-interview-questions.md)
- [40 hard questions](hard-interview-questions.md)
- [30 tricky questions](tricky-interview-questions.md)

Each set starts numbering at 1. The levels move from clear definitions to applied decisions, production scenarios, and common senior-level traps.

## Project deep-dive checklist

For one project you know well, prepare to explain:

1. Who had the problem, and what evidence showed it mattered?
2. What did you personally own, and which teams or systems did you depend on?
3. What were the important constraints and alternatives?
4. How did the API, data model, and frontend fit together?
5. What could fail, and how did the system degrade or recover?
6. How did you test, secure, observe, and roll out the change?
7. What changed for users or the business, and how did you measure it?
8. What would you change with the evidence you have now?

A useful answer shape is: **user need → options → decision → safety plan → result**. Be specific about your contribution and honest about uncertainty.

## Related modules

- [System Design and Architecture](../system-design-architecture/README.md)
- [Scenario-Based Interview Questions](../scenario-based-interview-questions/README.md)
- [Logging, Monitoring and Observability](../logging-monitoring-observability/README.md)
- [Testing](../testing/README.md)
- [HR and Managerial Interviews](../hr-managerial-interviews/README.md)
- [Angular and Frontend Testing](../angular-testing/README.md)
- [Java Coding Interview Practice](../coding-interview-practice/README.md)

## References

- [DORA: User-centric focus](https://dora.dev/capabilities/user-centric-focus/) — connect engineering work to user needs and outcomes.
- [Google SRE: Error budget policy](https://sre.google/workbook/error-budget-policy/) — use reliability targets to guide release and stability decisions.
- [Google SRE: Monitoring](https://sre.google/workbook/monitoring/) — choose monitoring signals that support both detection and diagnosis.
- [OpenTelemetry: Observability primer](https://opentelemetry.io/docs/concepts/observability-primer/) — understand telemetry, SLIs/SLOs, logs, spans, and traces.
