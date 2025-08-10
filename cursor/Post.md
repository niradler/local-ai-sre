# 🛠 Cursor as AI SRE

Every week some new “AI SRE” thing shows up in my feed.
Some look magical, some… let’s just say _marketing_ is working overtime.

This isn’t that.
This is me making Cursor behave like a helpful SRE buddy who uses **my tools, in my terminal, from inside my editor**, and who asks permission before doing anything dumb.

---

### TL;DR

- Stick a `.cursorrules` file in your repo so Cursor knows how to “behave”
- It figures out what CLI tools you’ve got (`kubectl`, `helm`, `argocd`, `istioctl`, …), writes up a Diagnostic Plan, and _waits_
- Runs read-only stuff first, then suggests fixes with rollback ideas
- You’re still the human in charge — AI just does the boring typing

---

## Why Inside the Editor?

Because it’s fun having incident digging happen right next to the YAML and code that caused it.

Also:

- No new bots, agents, or random SaaS logins — it just uses what’s already in your PATH
- You can literally watch it “think” and veto any nonsense before it runs

---

## `.cursorrules`: The Secret Sauce

It’s just a file that says “Here’s how you behave, dear AI.”
Mine basically encodes my usual production incident flow:

1. Figure out the context (cluster, namespace, service, time range)
2. See what toys we have (Helm, ArgoCD, Istio, etc.)
3. Write a Diagnostic Plan _before_ doing anything
4. **Approval Gate #1** – no poking without my say-so
5. Run diagnostics step-by-step, keep output short, redact secrets
6. Suggest fixes with a rollback plan
7. **Approval Gate #2** – no changes unless I click “yes”
8. Apply fix, check health
9. Wrap up with a quick post-mortem

---

## The Diagnostic Plan Covers…

- Service & endpoint health
- Routing/traffic checks (Ingress, Istio, etc.)
- Workload status (pods, restarts, resources)
- Logs & events
- Recent changes (Helm/ArgoCD/GitOps/config drift)
- Fix options that won’t nuke prod

---

## Every Command Comes With an Audit Card

Before anything runs, it dumps a little JSON like:

```json
{
  "action": "exec",
  "tool": "helm",
  "reason": "check recent release changes",
  "ns": "prod",
  "cmd": "helm history payments -n prod"
}
```

So you can say “Yes” or “LOL no” before it even touches the keyboard.

---

## Example: Chasing a CrashLoopBackOff

The setup found a sneaky bad secret value:

- 424 restarts in 35 hours
- Logs + secret check → `APP_MODE=broken` (should’ve been `APP_MODE=safe`)
- Gave me 3 fix options (patch secret, make a new one, or override in deployment) — all with rollback steps

Full nerd transcript in `chat.md`.

---

## Wanna Try?

1. Open this repo in Cursor
2. `.cursorrules` is already in the root
3. Start a chat, tell it the namespace + timeframe + what’s broken
4. Approve the Diagnostic Plan → profit

**Pro tips:**

- Use `--context` and `-n` so it stays in the right cluster/namespace
- GitOps rollbacks/redeploys > random manual edits

---

## Built-In Safety Nets

- Always read-only first, with `dry-run` where possible
- Two “are you sure?” stops — diagnostics and changes
- Won’t blow stuff away unless you literally tell it to
- Short summaries, no secret leaks

---

## Hack It for Your Stack

Add checks for whatever else you’ve got — ingress controllers, service mesh, weird observability tools.
The steps stay the same; the commands are yours to mess with.

---

## Final Thought

This isn’t replacing the on-call human.
It’s just my way of making the on-call human (me) do less copy-pasting at 2 AM.
