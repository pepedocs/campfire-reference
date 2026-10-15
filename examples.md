# Examples

These examples assume Alice, Bob, Charlie, and Diana are team members
working across multiple OpenShift clusters.

---

## Reporting

### New incident

> `/incident CrashLoopBackOff on payment-api pods in prod-east/payments`

Agent: *How would someone verify this?*

> `oc get pods -n payments -l app=payment-api`

Agent writes to `incidents.md`, commits, then asks: *push?*

```markdown
- **Incident 1** (Alice): CrashLoopBackOff on payment-api pods in prod-east/payments.
  Validate: `oc get pods -n payments -l app=payment-api`
```

### New issue

> `/issue etcd disk latency above 100ms on prod-west control plane`

Agent: *How would someone verify this?*

> `check grafana etcd dashboard for prod-west`

```markdown
- **Issue 1** (Bob): etcd disk latency above 100ms on prod-west control plane.
  Validate: check grafana etcd dashboard for prod-west.
```

### New upgrade

> `/upgrade OCP 4.14 to 4.15 planned for prod-east next Tuesday`

Agent: *How would someone verify this?*

> `oc get clusterversion on prod-east`

```markdown
- **Upgrade 1** (Diana): OCP 4.14 → 4.15 planned for prod-east next Tuesday.
  Validate: `oc get clusterversion` on prod-east.
```

### New note

> `/note AWS us-east-1 reporting degraded EBS performance, may affect prod-east PVs`

```markdown
- **Note 1** (Charlie): AWS us-east-1 reporting degraded EBS performance, may affect prod-east PVs.
  Validate: check AWS status page for us-east-1 EBS.
```

---

## Updating

### Updating an entry

> `/update Incident 1 mitigated, scaled replicas to 8, error rate dropping`

Agent edits the existing line in `incidents.md`:

```markdown
- **Incident 1** (Alice): CrashLoopBackOff on payment-api pods in prod-east/payments. Mitigated — scaled replicas to 8, error rate dropping.
  Validate: `oc get pods -n payments -l app=payment-api`
```

### A different person updates

> `/update Incident 1 root cause identified — OOM, need to bump memory limits`

Agent adds Charlie's name since he's not the original reporter:

```markdown
- **Incident 1** (Alice, Charlie): CrashLoopBackOff on payment-api pods in prod-east/payments. Root cause identified — OOM, need to bump memory limits.
  Validate: `oc get pods -n payments -l app=payment-api`
```

### Resolving an entry

> `/resolve Incident 1 pods healthy, bumped memory limits to 2Gi`

```markdown
- **Incident 1** (Alice, Charlie): Resolved. CrashLoopBackOff on payment-api pods in prod-east/payments. Root cause was OOM, bumped memory limits to 2Gi.
  Validate: `oc get pods -n payments -l app=payment-api` — zero restarts in 24h.
```

---

## Querying

### Finding related entries

> `/relate I'm going to install a cluster to test UDR`

Agent searches all files and returns:

> - **Issue 1** (Jose): Kubernetes v10.1 bug is causing a UDR incident in OpenShift.
>   Validate: Confirm OCP is using Kubernetes > v10.2.
>
> ⚠️ *These entries reflect what the team has reported. They may be incomplete, stale, or inaccurate. Verify before acting.*

### Checking status

> `/status`

Agent reads all files and summarizes:

> **Incidents** (1 entry)
> - **Incident 1** (Alice, Charlie): Resolved. Payment-api OOM in prod-east.
>
> **Issues** (1 entry)
> - **Issue 1** (Bob): etcd disk latency on prod-west. ⚠️ *9 days since last update.*
>
> **Upgrades** (1 entry)
> - **Upgrade 1** (Diana): OCP 4.14 → 4.15 for prod-east next Tuesday.
>
> **Notes** (1 entry)
> - **Note 1** (Charlie): AWS us-east-1 degraded EBS performance.

### Checking a specific entry

> `/status Incident 1`

Agent shows the entry and its git history:

> **Incident 1** (Alice, Charlie): Resolved. CrashLoopBackOff on payment-api pods in prod-east/payments.
> Validate: `oc get pods -n payments -l app=payment-api` — zero restarts in 24h.
>
> **History:**
> - Charlie (2026-08-24): resolve Incident 1: pods healthy, root cause was OOM
> - Charlie (2026-08-23): update Incident 1: root cause identified, OOM
> - Alice (2026-08-23): update Incident 1: mitigated, scaled replicas to 8
> - Alice (2026-08-22): report Incident 1: CrashLoopBackOff on payment-api pods

### Viewing full history

> `/history Incident 1`

Agent shows the complete git log with diffs for that entry.

---

## What Git captures

Every action above produces a commit. The full trail is always available:

```
$ git log --oneline

a1b2c3d Charlie: resolve Incident 1: pods healthy, root cause was OOM
d4e5f6g Charlie: update Incident 1: root cause identified, OOM
h7i8j9k Alice: update Incident 1: mitigated, scaled replicas to 8
l0m1n2o Alice: report Incident 1: CrashLoopBackOff on payment-api pods
p3q4r5s Diana: report Upgrade 1: OCP 4.14 to 4.15 for prod-east
t6u7v8w Bob: report Issue 1: etcd disk latency on prod-west
x9y0z1a Charlie: report Note 1: AWS us-east-1 degraded EBS
```

Who said what, when they said it, and exactly what changed — all auditable.
