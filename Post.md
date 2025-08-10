## 🛠 Cursor as an AI SRE

Every week brings a new “AI SRE.” Some are magic; some are marketing. The real win today is simpler: let AI work with the same CLI tools you already trust — inside your editor — with a clear plan and your approval at every step.

### TL;DR

- Use a `.cursorrules` file to make Cursor act as a safe, transparent SRE copilot
- It discovers tools (`kubectl`, `helm`, `argocd`, `istioctl`, …), proposes a Diagnostic Plan, and waits for approval
- It runs read‑only diagnostics first, then suggests mitigations with rollback plans
- You stay in control; the AI just moves faster

### Why do this inside the editor?

- Keep the investigation alongside code and infra definitions
- Reuse your PATH tools and kube context without new bots or agents
- See the AI’s reasoning and audit each command before it runs

### What is `.cursorrules`?

It’s an always‑on instruction file that defines behavior, tools, and safety rails. Ours encodes a production‑style incident flow:

1. Clarify context (cluster/context, namespace, service/workload, timeframe)
2. Detect available tools (Helm, ArgoCD, Istio, etc.)
3. Produce a Diagnostic Plan before any command
4. Approval Gate #1 (no diagnostics without consent)
5. Execute step‑by‑step with ≤5‑line summaries, secrets redacted
6. Propose mitigations with rollback plan, prefer tool‑native rollbacks
7. Approval Gate #2 (no changes without consent)
8. Execute and verify rollout/health
9. Close out with a brief post‑mortem and follow‑ups

### The Diagnostic Plan

- Service & endpoint health
- Routing / traffic checks (Ingress, Istio, etc.)
- Workload status (deployments, pods, restarts, resources)
- Logs & recent events
- Recent changes (Helm, ArgoCD, GitOps, config drift)
- Mitigation options (safe first, rollback included)

### Audit block before each command

```json
{
  "action": "exec",
  "tool": "helm",
  "reason": "check recent release changes",
  "ns": "prod",
  "cmd": "helm history payments -n prod"
}
```

### Live demo: fixing CrashLoopBackOff

We used this flow to find a bad secret value causing repeated restarts. The copilot:

- Detected 424 restarts over 35 hours
- Pulled logs and inspected the related secret
- Found `APP_MODE=broken` where the app requires `APP_MODE=safe`
- Proposed three options (patch secret, create new secret, or temp deployment override) with rollback

Read the transcript in `chat.md`.

### Try it yourself

1. Open this repo in Cursor
2. Ensure `.cursorrules` is at the repo root (it is)
3. Start a chat and describe the incident (namespace, timeframe, context)
4. Review the Diagnostic Plan and approve to proceed

Tips:

- Include `--context` and `-n` when known; the copilot will reuse them
- Prefer GitOps‑aligned actions (Helm/ArgoCD rollback/redeploy) over manual edits

### Safety guardrails

- Read‑only diagnostics first; uses `dry-run` where possible
- Two approval gates: diagnostics and changes
- No destructive commands unless explicitly requested
- Concise summaries; secrets/tokens redacted

### Extend it for your stack

Add checks for your ingress, service mesh, observability, or platform tools. The plan stays the same; the commands evolve.

### Closing thought

This isn’t about replacing on‑call. It’s about speeding up the boring parts while keeping you in charge.
