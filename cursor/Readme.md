## Cursor SRE Copilot

Turn Cursor into a safe, structured AI SRE that works with the same Kubernetes tools you already use: `kubectl`, `helm`, `argocd`, `istioctl`, and more. This repo is an experiment and a blog companion showing how a simple `.cursorrules` file can make AI a reliable SRE copilot inside your editor — fast at diagnostics, explicit about reasoning, and always human‑approved before acting.

### What you get

- A ready‑to‑use `.cursorrules` that defines the SRE copilot behavior: discovery, planning, safe execution, and audit logs
- A write‑up explaining the approach (`Post.md`)
- An example incident transcript (`chat.md`) demonstrating the structured workflow on a CrashLoopBackOff issue

### Core behavior (baked into `.cursorrules`)

1. Clarify context: cluster/context, namespace, affected service/workload, timeframe (default last 30m)
2. Detect available tools: `helm`, `argocd`, `istioctl`, etc., in addition to `kubectl`
3. Produce a Diagnostic Plan before any command:
   - Service & endpoint health
   - Routing / traffic checks (Ingress, Istio, etc.)
   - Workload status (deployments, pods, restarts, resources)
   - Logs & recent events
   - Recent changes (Helm, ArgoCD, GitOps, config drift)
   - Proposed mitigations (safe by default, rollback included)
4. Approval Gate #1: wait for your approval before running diagnostics
5. Execute step‑by‑step with ≤5‑line summaries per step, secrets redacted
6. Propose mitigations with rollback plan; prefer tool‑native rollbacks
7. Approval Gate #2: wait for approval before any change action
8. Execute and verify rollouts/health
9. Close out with a concise post‑mortem summary and follow‑ups

### Quick start

1. Open this repository in Cursor
2. Ensure `.cursorrules` is present at the repo root (it is)
3. Start a chat and describe your incident, e.g.:

   "Payment service is timing out in production. Namespace `prod`. Last 30 minutes."

4. The copilot will:
   - Detect tools available in your PATH
   - Show a numbered Diagnostic Plan
   - Ask for approval before executing any command
   - Summarize findings after each step

Tip: Include `--context` and `-n` when you know them. The copilot will retain and reuse them.

### Safety & auditability

- Two approval gates: one before diagnostics, one before changes
- Read‑only by default; uses `dry-run` where applicable
- Non‑destructive unless explicitly approved
- Emits a small JSON audit block before each command, e.g.:

```json
{
  "action": "exec",
  "tool": "helm",
  "reason": "check recent release changes",
  "ns": "prod",
  "cmd": "helm history payments -n prod"
}
```

### Demo

See `chat.md` for a real transcript troubleshooting a CrashLoopBackOff caused by a bad secret value. The copilot identified the faulty key, proposed three safe fixes with a rollback plan, and waited for approval before any change.

### Try it against your cluster

- Make sure your kube context is set and tools you use are in PATH
- Start small in a sandbox namespace
- Prefer GitOps‑aligned actions (Helm/ArgoCD rollback/redeploy) over manual edits

### Repo layout

- `.cursorrules`: The SRE copilot behavior
- `Post.md`: Blog‑style write‑up of the approach
- `chat.md`: Example incident session

### Notes

- This is an experiment to showcase how far you can go with conventions and a single rule file — not a replacement for your on‑call playbooks
- Extend the Diagnostic Plan to match your stack (e.g., service mesh, ingress controller, observability backends)

## Cursor SRE Copilot

Turn Cursor into a safe, structured AI SRE that works with the same Kubernetes tools you already use: `kubectl`, `helm`, `argocd`, `istioctl`, and more. This repo is an experiment and a blog companion showing how a simple `.cursorrules` file can make AI a reliable SRE copilot inside your editor — fast at diagnostics, explicit about reasoning, and always human‑approved before acting.

### What you get

- A ready‑to‑use `.cursorrules` that defines the SRE copilot behavior: discovery, planning, safe execution, and audit logs
- A write‑up explaining the approach (`Post.md`)
- An example incident transcript (`chat.md`) demonstrating the structured workflow on a CrashLoopBackOff issue

### Core behavior (baked into `.cursorrules`)

1. Clarify context: cluster/context, namespace, affected service/workload, timeframe (default last 30m)
2. Detect available tools: `helm`, `argocd`, `istioctl`, etc., in addition to `kubectl`
3. Produce a Diagnostic Plan before any command:
   - Service & endpoint health
   - Routing / traffic checks (Ingress, Istio, etc.)
   - Workload status (deployments, pods, restarts, resources)
   - Logs & recent events
   - Recent changes (Helm, ArgoCD, GitOps, config drift)
   - Proposed mitigations (safe by default, rollback included)
4. Approval Gate #1: wait for your approval before running diagnostics
5. Execute step‑by‑step with ≤5‑line summaries per step, secrets redacted
6. Propose mitigations with rollback plan; prefer tool‑native rollbacks
7. Approval Gate #2: wait for approval before any change action
8. Execute and verify rollouts/health
9. Close out with a concise post‑mortem summary and follow‑ups

### Quick start

1. Open this repository in Cursor
2. Ensure `.cursorrules` is present at the repo root (it is)
3. Start a chat and describe your incident, e.g.:

   "Payment service is timing out in production. Namespace `prod`. Last 30 minutes."

4. The copilot will:
   - Detect tools available in your PATH
   - Show a numbered Diagnostic Plan
   - Ask for approval before executing any command
   - Summarize findings after each step

Tip: Include `--context` and `-n` when you know them. The copilot will retain and reuse them.

### Safety & auditability

- Two approval gates: one before diagnostics, one before changes
- Read‑only by default; uses `dry-run` where applicable
- Non‑destructive unless explicitly approved
- Emits a small JSON audit block before each command, e.g.:

```json
{
  "action": "exec",
  "tool": "helm",
  "reason": "check recent release changes",
  "ns": "prod",
  "cmd": "helm history payments -n prod"
}
```

### Demo

See `chat.md` for a real transcript troubleshooting a CrashLoopBackOff caused by a bad secret value. The copilot identified the faulty key, proposed three safe fixes with a rollback plan, and waited for approval before any change.

### Try it against your cluster

- Make sure your kube context is set and tools you use are in PATH
- Start small in a sandbox namespace
- Prefer GitOps‑aligned actions (Helm/ArgoCD rollback/redeploy) over manual edits

### Repo layout

- `.cursorrules`: The SRE copilot behavior
- `Post.md`: Blog‑style write‑up of the approach
- `chat.md`: Example incident session

### Notes

- This is an experiment to showcase how far you can go with conventions and a single rule file — not a replacement for your on‑call playbooks
- Extend the Diagnostic Plan to match your stack (e.g., service mesh, ingress controller, observability backends)
