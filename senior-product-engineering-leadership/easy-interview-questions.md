# Senior Product Engineering: Easy Interview Q&A

## Product thinking and scope

### Q1. What does product-minded engineering mean?

**Answer:** Start with the user's problem and the result the product needs. Then choose the smallest safe technical change that can deliver and measure that result.

**Example:** Example: Improve checkout completion, not merely ship a new checkout button.

### Q2. Who is a user, and who is a stakeholder?

**Answer:** A user works with the product. A stakeholder is affected by its results or decisions; stakeholders can include support, security, finance, and engineering.

### Q3. How is a user problem different from a requested solution?

**Answer:** A problem describes what is difficult for the user; a solution is one proposed fix. Ask what evidence led to the solution before committing to it.

### Q4. What is the difference between an output and an outcome?

**Answer:** An output is something the team ships. An outcome is a change for users or the business that happens because of it.

### Q5. What makes a useful success metric?

**Answer:** It measures the user or business result the feature is meant to improve and can be checked before and after launch.

**Example:** Example: Measure successful self-service completion, with support-contact rate as a guardrail.

### Q6. What is an MVP?

**Answer:** A minimum viable product is the smallest complete slice that lets real users get value and lets the team learn. It is not a reason to skip security or quality.

### Q7. What is a non-functional requirement?

**Answer:** A constraint on how the system behaves, such as latency, availability, accessibility, privacy, or security, rather than a feature it provides.

### Q8. What are acceptance criteria?

**Answer:** Observable conditions that show a story is complete. Good criteria cover the normal path and important failure or boundary cases.

### Q9. Why talk to users or support teams before coding?

**Answer:** They can reveal the real workflow, workarounds, and pain points that a ticket may not explain. That often changes the scope before it becomes expensive.

### Q10. What should an engineer do when product requirements are unclear?

**Answer:** Write down the uncertainty, ask focused questions, and agree on an initial behavior and a way to verify it before implementation.

## Ownership and delivery

### Q11. What does end-to-end ownership mean for a senior engineer?

**Answer:** It means helping shape the problem, designing and delivering the change, supporting its launch, and following its results in production.

### Q12. Why split a large feature into thin vertical slices?

**Answer:** Each slice delivers a usable path through UI, API, and data. The team gets feedback sooner and limits the size of risky changes.

### Q13. What is a dependency risk?

**Answer:** A part of delivery that relies on another team, service, decision, or vendor being ready. Identify it early and agree on an owner and fallback.

### Q14. What is a design proposal used for?

**Answer:** It makes the problem, options, trade-offs, and rollout plan visible so teammates can find mistakes and agree before implementation is costly.

### Q15. What is an architecture decision record?

**Answer:** A short note capturing a decision, context, alternatives, and consequences. It helps future maintainers understand why the system looks this way.

### Q16. What is the purpose of code review?

**Answer:** Catch defects, improve clarity, and share context. Review the behavior and risks, not just formatting or personal style.

### Q17. Why are estimates different from commitments?

**Answer:** An estimate is a forecast based on current information. A commitment is an agreement to deliver a scope or outcome; make uncertainty visible in both.

### Q18. What makes a useful handoff?

**Answer:** A clear status, owner, next step, important risks, and links to the relevant design or incident notes. Avoid handing off hidden assumptions.

### Q19. What is technical debt?

**Answer:** A shortcut or design choice that makes future change more costly. Record its impact and pay it down when the risk or cost justifies the work.

### Q20. How can a senior engineer help a teammate grow?

**Answer:** Give context, ask them to reason through options, agree on a manageable challenge, and provide timely feedback instead of taking the task back.

## Reliability and operations

### Q21. What is an SLI?

**Answer:** A service level indicator is a measurement of user-visible service behavior, such as the share of requests that succeed or meet a latency target.

### Q22. What is an SLO?

**Answer:** A service level objective is a target for an SLI over a stated window. It gives a team a measurable reliability goal.

### Q23. What is an SLA?

**Answer:** A service level agreement is a commitment to customers, often with remedies if it is missed. An internal SLO is usually a team target.

### Q24. What is an error budget?

**Answer:** The allowed unreliability under an SLO for a period. It helps teams discuss how much risk remains for changes.

### Q25. What is an incident?

**Answer:** An unplanned event that harms or threatens users or service operation and needs coordinated response, mitigation, and follow-up.

### Q26. What is a runbook?

**Answer:** A practical guide for a known operational task or alert: what to check, safe mitigations, and when to escalate.

### Q27. What is a rollback?

**Answer:** Returning a release to a previously known working version or configuration when the new change is causing harm.

### Q28. What is a canary release?

**Answer:** Sending a change to a small share of traffic or users first, watching agreed signals, and expanding only if it behaves well.

### Q29. What is a feature flag?

**Answer:** A runtime switch that controls whether a feature is available. It can support gradual rollout, but stale flags need owners and cleanup.

### Q30. What does observability help an engineer do?

**Answer:** Use telemetry to understand system behavior and investigate problems that were not anticipated in advance.

### Q31. What do logs, metrics, and traces tell you?

**Answer:** Logs record events, metrics summarize measurements over time, and traces show work across a request path through components.

**Example:** Example: A latency graph finds the spike; a trace locates the slow dependency; logs explain its error.

## Quality and collaboration

### Q32. What is a unit test?

**Answer:** A fast test of a small unit of behavior, usually with controlled dependencies.

### Q33. What is an integration test?

**Answer:** A test that checks whether multiple parts work together, such as application code with a real database or message broker.

### Q34. What is an end-to-end test?

**Answer:** A test of a user-visible workflow across the system's boundaries, often through a browser or public API.

### Q35. What is a contract test?

**Answer:** A check that a consumer and provider agree on the shape and behavior of an interface they rely on.

### Q36. What does backward compatibility mean?

**Answer:** An existing client can keep working after a change. Additive changes are often safer than silently changing or removing existing behavior.

### Q37. Why use a staged database migration?

**Answer:** It lets old and new application versions overlap safely while the schema changes, instead of requiring one risky all-at-once deployment.

### Q38. Why does accessibility matter to product engineering?

**Answer:** It lets more people use the product and reduces interaction barriers. Keyboard access, labels, focus, and readable contrast are practical basics.

### Q39. What is a performance budget?

**Answer:** A limit for an experience or service metric that helps teams catch regressions before users feel them.

**Example:** Example: Set a page-load target and an API p95 target, then check them in production.

### Q40. How should you describe a project in an interview?

**Answer:** Explain the user problem, your contribution, the decision you made, what changed for users, and what you would improve next.

**Example:** Example: Use a STAR structure, but include a measurable result and your own role.

---

## Quick revision

For senior-level answers, connect the user problem to the decision, show the risks and rollout, and explain what evidence will tell you whether it worked.
