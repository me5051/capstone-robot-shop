# Robot Shop Incident Response Runbook

## 1. Confirm the Incident

Check pod health, recent events, and service endpoints:

    kubectl get pods -n robot-shop
    kubectl get events -n robot-shop --sort-by='.lastTimestamp'
    kubectl get svc,endpoints -n robot-shop

Look for unavailable pods, repeated restarts, failed readiness probes, scheduling failures, and missing endpoints.

## 2. Investigate Application Logs

Inspect the affected deployment:

    kubectl logs -n robot-shop deployment/catalogue --tail=100

Replace `catalogue` with the affected deployment name.

For a restarting container:

    kubectl logs -n robot-shop <pod-name> --previous --tail=100

Look for dependency connection errors, application exceptions, and repeated failures.

## 3. Investigate Traces

Once OpenTelemetry Collector and Jaeger are operational:

1. Open Jaeger using the configured access method.
2. Select the affected service and an appropriate time range.
3. Find traces with errors or unusually long durations.
4. Inspect spans and dependency calls to locate the failure or latency.

Do not assume tracing works until a real trace is visible in Jaeger.

## 4. Check Capacity and Storage

    kubectl get nodes
    kubectl describe nodes
    kubectl get pvc -n robot-shop
    kubectl get events -n robot-shop --sort-by='.lastTimestamp'

Look for insufficient memory, pod-capacity limits, unscheduled workloads, and pending persistent volume claims.

Do not delete persistent volume claims or stateful workloads as a routine troubleshooting step.

## 5. Mitigate Safely

- Application regression: consider rolling back to a known-good revision.
- Dependency failure: restore the dependency before restarting dependent services.
- Capacity exhaustion: reduce unnecessary workload consumption or arrange approved additional capacity.
- Storage failure: restore the configured provisioner and validate volume binding before restarting dependent workloads.

Inspect the current state before making changes. Avoid affecting unrelated namespaces or services.

## 6. Verify Recovery

1. Confirm affected pods become Ready.
2. Confirm service endpoints are populated.
3. Test the application through its normal access path.
4. Review logs for recurring errors.
5. Check the validated availability SLI.

## 7. Document the Incident

Record the incident timeline, customer impact, detection method, mitigation, recovery verification, root cause, contributing factors, and follow-up actions.

Use a blameless approach focused on system improvements.

## 8. Outstanding Validation

- Prometheus scraping and availability metric query
- Grafana dashboard data
- Fluent Bit ingestion and searchable Loki logs
- OpenTelemetry trace delivery to Jaeger
- Slack alert delivery
- Verified dashboard links and access methods
