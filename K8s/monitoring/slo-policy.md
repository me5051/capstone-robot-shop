# Robot Shop Availability SLO and Error-Budget Policy

## 1. Objective

Maintain reliable access to the Robot Shop customer-facing web application while detecting and responding to service degradation.

## 2. Service-Level Indicator (SLI)

**Availability SLI:** the proportion of eligible HTTP requests to the Robot Shop web application that return successful responses.

- Good event: an eligible request returns HTTP 2xx or 3xx.
- Bad event: an eligible request returns HTTP 5xx or fails because the service is unavailable.
- Exclusions: health checks, synthetic monitoring requests, and requests intentionally aborted by the client.
- Measurement source: Prometheus request metrics, once a reliable instrumentation and scraping path is configured.

The exact metric name and query must be confirmed against the metrics actually exposed by the application before alert rules are enabled.

## 3. Service-Level Objective (SLO)

**Target:** 99.5% availability over a rolling 30-day window.

This is a proposed target for the project, not a claim about current production availability.

## 4. Error Budget

For a 99.5% availability target:

- Allowed failure ratio: 0.5%.
- Error budget: 0.5% of eligible requests over the rolling 30-day window.
- At a constant request rate, 0.5% corresponds to approximately 3 hours and 36 minutes of equivalent downtime in 30 days.

Request-based error budget consumption must be calculated from eligible good and bad events, rather than assuming that every minute has the same traffic.

## 5. Alerting Policy

Proposed thresholds, to be implemented after the SLI query is validated:

- Warning: more than 50% of the rolling error budget consumed.
- Critical: more than 80% of the rolling error budget consumed.
- Incident trigger: the service is unavailable, or the validated availability SLI breaches its agreed threshold.

Alerts should identify the affected service, summarize the observed condition, link to the Grafana dashboard, and include a runbook link.

Slack delivery is not considered configured until a test notification has been received successfully.

## 6. Error-Budget Policy

- Below 50% budget consumed: continue planned work while monitoring reliability.
- At 50% or more: review recent incidents and prioritize reliability fixes.
- At 80% or more: pause non-essential risky releases until the service stabilizes and the team agrees on mitigation.
- After a major incident: document the impact, root cause, remediation, and follow-up actions in a blameless postmortem.

## 7. Incident Response

1. Acknowledge the alert and assess customer impact.
2. Check the service dashboard, application logs, and available traces.
3. Mitigate the immediate impact using a safe rollback, restart, or traffic-management action as appropriate.
4. Verify recovery using the same SLI used to detect the incident.
5. Record a timeline, root cause, contributing factors, and preventive actions.
6. Link the postmortem and follow-up work from the incident record.

## 8. Runbook

Runbook location: `K8s/monitoring/runbook.md`

The runbook will document dashboard checks, log queries, trace investigation, mitigation steps, and recovery verification.

## 9. Validation Status

- SLO target and error-budget policy: proposed.
- Prometheus metric and SLI query: pending validation against actual metrics.
- Alert rules and Slack delivery: pending implementation and test.
- Dashboard and runbook links: pending runtime verification.
