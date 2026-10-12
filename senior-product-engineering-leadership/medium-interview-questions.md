# Senior Product Engineering: Medium-Level Interview Q&A

## Discovery and prioritization

### Q1. A stakeholder asks for a large feature but cannot explain the user pain. What do you do first?

**Answer:** Ask who has the problem, how often it occurs, what they do today, and what a successful change would look like. Check support cases or usage data before estimating the full solution.

**Example:** Example: Prototype the most common workflow with a few users before committing to a multi-month rewrite.

### Q2. How do you choose a success metric for a new feature?

**Answer:** Tie one primary metric to the intended user outcome, define its population and time window, and add guardrails for quality, support burden, latency, and abuse.

### Q3. The feature adoption metric rises but customer complaints also rise. How do you read that?

**Answer:** Do not call it a win from adoption alone. Segment the complaints and users, check the full workflow and guardrails, and decide whether to pause, fix, or narrow the rollout.

### Q4. How would you prioritize two valuable requests with limited capacity?

**Answer:** Compare user impact, urgency, confidence, effort, dependencies, and risk. State what will be delayed and revisit the choice when evidence changes.

### Q5. How do you find a useful MVP boundary?

**Answer:** Keep the smallest end-to-end journey that delivers the promised outcome. Defer optional paths, but retain privacy, security, accessibility, and recovery behavior users need.

### Q6. How do you handle a requirement that conflicts with an existing workflow?

**Answer:** Map both workflows and identify who would be harmed. Offer options with costs, get a decision from the accountable product owner, and document the chosen behavior.

### Q7. When is an experiment a poor way to decide?

**Answer:** When the change is unsafe to expose randomly, the sample is too small, or the result would not change the decision. Use staged rollout, usability research, or a guardrail instead.

### Q8. How do you estimate an unfamiliar project?

**Answer:** Break it into discovery, implementation, integration, migration, and rollout. Estimate ranges, list assumptions, and reserve time for the riskiest unknowns.

### Q9. How do you communicate scope reduction without surprising stakeholders?

**Answer:** Show the original outcome, what the first slice will cover, what is deferred, and the evidence or date that will trigger the next decision.

### Q10. A product manager asks for a deadline before requirements are clear. What is a useful response?

**Answer:** Give a range tied to assumptions, then propose a short discovery step that will narrow it. Avoid presenting an early guess as a commitment.

## Design and delivery

### Q11. What belongs in a design proposal for a cross-team change?

**Answer:** Problem and constraints, proposed design, alternatives, API/data ownership, operational risks, rollout, open questions, and who needs to decide what.

### Q12. When should a decision be recorded in an ADR?

**Answer:** When the choice has meaningful long-term consequences or future engineers are likely to ask why it was made. Keep the note short and update its status if superseded.

### Q13. How do you make an API change that old clients can tolerate?

**Answer:** Prefer additive changes, preserve existing semantics, publish a deprecation path, observe client usage, and remove old behavior only after owners have a migration window.

### Q14. How do you coordinate a feature that depends on another team?

**Answer:** Agree on the contract and owner early, use a stub or compatible interim behavior, track the dependency explicitly, and have a fallback if the date slips.

### Q15. A design review has strong disagreement. How do you move it forward?

**Answer:** Ask which requirement or risk each option optimizes. Compare evidence and reversibility, capture the decision owner, and time-box unresolved exploration.

### Q16. How do you add a feature flag safely?

**Answer:** Give it an owner, purpose, default behavior, rollout plan, and cleanup date. Test both flag states and ensure the disabled path is safe.

### Q17. What should a canary rollout measure?

**Answer:** Use user-facing success and latency signals plus error rate, resource saturation, and a comparison group or baseline. Expand only when the agreed guardrails hold.

### Q18. How do you decide between rollback and roll-forward?

**Answer:** Rollback when the prior version is safe and compatible; roll forward when rollback risks data loss or incompatibility. First stop user harm, then choose the lowest-risk recovery.

### Q19. What makes a good service-level objective?

**Answer:** It measures an important user journey over a defined window, uses a clear good-event definition, and is ambitious enough to protect users without pretending failures are impossible.

### Q20. How do you explain an error budget to a product stakeholder?

**Answer:** It is the amount of unreliability the agreed SLO permits in a period. Spending it quickly signals that reliability work may be more valuable than another risky release.

## Production reliability and quality

### Q21. An alert fires every day but users are not affected. What would you do?

**Answer:** Check whether the alert predicts user harm or an actionable risk. Tune thresholds, add context, or remove it; frequent unactionable alerts train responders to ignore them.

### Q22. How do you structure an incident update for non-engineers?

**Answer:** State user impact, what is known, current mitigation, next update time, and any action needed. Separate confirmed facts from hypotheses.

### Q23. What is the difference between a mitigation and a root-cause fix?

**Answer:** A mitigation reduces current harm, such as disabling a feature. A root-cause fix removes or contains the underlying failure; do both in that order when needed.

### Q24. What makes a post-incident review useful?

**Answer:** A shared timeline, user impact, contributing conditions, what helped or slowed recovery, and a small set of owned actions with dates. Avoid blaming the last person who touched code.

### Q25. How do traces help when an API is slow but the database query is fast?

**Answer:** A trace breaks the request into timed spans. It can expose time in serialization, lock waits, connection-pool waits, network calls, or queueing between the query and response.

### Q26. A release increases error rate only in one region. What do you compare?

**Answer:** Compare version, configuration, traffic mix, dependency health, and resource saturation by region. Pause rollout there, preserve evidence, and test the suspected regional difference.

### Q27. How do you choose a testing mix for a user-facing feature?

**Answer:** Unit-test business rules, integration-test boundaries with the real kind of dependency, contract-test service interfaces, and keep a small end-to-end test for the critical journey.

### Q28. A browser end-to-end test is flaky. How do you fix it?

**Answer:** Find whether the cause is timing, shared state, unstable selectors, network dependence, or test data. Wait on observable state and isolate data; do not just increase every timeout.

### Q29. How do you plan a database schema migration with zero downtime?

**Answer:** Use expand-and-contract: add compatible schema, deploy code that can work with both shapes, backfill in bounded batches, switch reads/writes, then remove old fields after verification.

### Q30. What telemetry would you add to a new checkout flow?

**Answer:** Measure successful completion and failures by safe dimensions, trace payment and inventory calls, and log request identifiers and error categories without sensitive card or identity data.

## Leadership and launch

### Q31. How would you investigate a slow frontend interaction?

**Answer:** Measure on representative devices, identify whether the delay is JavaScript, rendering, network, or API time, then profile and address the largest user-visible cost.

### Q32. How do you give feedback in a code review when you would have designed it differently?

**Answer:** Separate correctness or risk from preference. Explain the concrete consequence, ask the author to walk through the choice, and accept a sound alternative.

### Q33. How do you delegate work without losing ownership?

**Answer:** Agree on the outcome, boundaries, decision freedom, support points, and check-in timing. The delegate owns implementation; the lead remains accountable for unblocking and integration.

### Q34. A junior engineer is stuck and asks you to take over. What do you do?

**Answer:** Clarify the blocker, pair on the next step, or reduce the scope. Let them keep ownership where possible and follow up with a concrete learning point.

### Q35. How do you report a delivery risk early?

**Answer:** Name the impact, evidence, confidence, options, and decision deadline. Bring a recovery plan instead of waiting until the date is already missed.

### Q36. How do you handle a production request that bypasses normal review?

**Answer:** Assess the actual urgency and user harm, use the smallest reversible change, get the required second set of eyes if possible, and document the follow-up review.

### Q37. How do you distinguish useful tech-debt work from cleanup for its own sake?

**Answer:** Connect the debt to a recurring cost or risk: slower delivery, incidents, scaling limits, or security exposure. Estimate the payback and choose a proportionate fix.

### Q38. What does a strong project deep dive include?

**Answer:** The user problem, constraints, your decisions, alternatives, rollout and failure handling, the measured result, and the parts you would change with new evidence.

### Q39. How do you know a feature is ready to launch?

**Answer:** The critical path works, risks have owners, monitoring and rollback are ready, support knows what changes, and the team has agreed on launch and stop criteria.

### Q40. A feature ships but its success metric does not move. What next?

**Answer:** Verify instrumentation and exposure first. Then inspect the user journey and segments, talk to users, and decide whether to iterate, reposition, or stop investing.

---

## Quick revision

For senior-level answers, connect the user problem to the decision, show the risks and rollout, and explain what evidence will tell you whether it worked.
