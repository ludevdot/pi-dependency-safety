---
name: npm-dependency-safety
description: "Trigger: dependency safety, npm package audit, package add/update, package.json or lockfile changes. Review Node package risk before dependency work."
license: Apache-2.0
compatibility: Pi coding agent; Node.js projects using npm, pnpm, yarn, bun, or another npm-registry-compatible package manager.
metadata:
  author: ludevdot
  version: "0.1.0"
  copyright: "Copyright 2026 ludevdot"
---

# npm-dependency-safety

## Activation Contract

Use before dependency-impacting Node.js work: project initialization, package add/update/remove/replace, package-manager migration, dependency warning/CVE response, or edits to `package.json`, lockfiles, npm/pnpm/yarn/bun config, or workspace dependency policy.

## Hard Rules

- Do not install, update, migrate, pin, unpin, rewrite lockfiles, or edit dependency config until the user has seen the safety report and explicitly approved the change.
- If the user asked only for analysis, produce the report and stop.
- Preserve the current package manager unless the user asks to evaluate alternatives or a security/policy reason justifies asking.
- The parent/orchestrator owns user questions, approvals, and subagent delegation. Non-orchestrator agents must report that delegation is needed; they must not launch orchestration.
- Do not ask the user to run audit, registry, metadata, bugs, or issue-tracker commands when the agent can run them.
- Keep raw lockfiles, full manifests, raw audit JSON, and long registry/issue output out of the main conversation.

## Decision Gates

| Situation | Action |
| --- | --- |
| Private repo, private package, or unknown network policy | Ask approval before external audit, registry, Socket.dev, web, GitHub issue, or advisory lookups that may disclose package names, dependency graphs, or repo signals. |
| Existing Dependabot, Renovate, audit, or CI security automation is detected | Use it as evidence, not a substitute. Do not skip pre-change review for new direct dependencies, package-manager changes, substantial lockfile changes, lifecycle scripts, low-reputation/newly published packages, large transitive expansion, or unknown CI coverage. |
| Dependency change requested | Inspect local package-manager evidence, produce a safety report, then ask explicit approval before edits. |
| Broad research/review needed | Parent may delegate focused inspection/research/review; non-orchestrators report the need. |
| pnpm hardening requested | Load `references/pnpm-hardening.md`; verify setting support for the pinned pnpm version before recommending changes. |
| yarn or bun project | Load manager guidance; treat support as basic checks unless project/version-specific policy is verified. |

## Execution Steps

1. Detect package manager, workspaces, package-manager pin, lockfiles, config, scripts, dependency sections, overrides/resolutions/catalogs, and requested dependency impact.
2. Apply the privacy/network gate before any external lookup in private or unknown-policy contexts.
3. Detect existing dependency-security automation and summarize what it covers without treating it as proof of safety for new dependency risk.
4. Run non-mutating native audit and metadata checks when available; include lifecycle-script, `bin`, dependency, optional, and peer-dependency signals.
5. Check advisories, supply-chain signals, issue trackers, package-manager policy, and package-install/build script controls only as needed for the requested change.
5. Return one decision: `approved`, `approved-with-caution`, `needs-human-review`, or `blocked`.
6. Ask the approval question before any dependency or package-manager edit.

## Output Contract

Return a concise dependency safety report with scope, package manager, requested change, candidates, vulnerabilities, supply-chain signals, policy questions, recommendation, missing evidence, and required approval.

## References

- `references/orchestration.md` — parent/subagent boundaries and context-budget details.
- `references/package-policy.md` — preferences, migration, version pinning, automation evidence, and npm/pnpm/yarn/bun policy notes.
- `references/package-manager-checks.md` — audit, metadata, lifecycle-script, install/build-script controls, and issue-tracker checks.
- `references/risk-review.md` — CVE/advisory research, Socket.dev checks, and decision levels.
- `references/pnpm-hardening.md` — pnpm setting verification and hardening proposal format.
- `references/report-format.md` — concise report template and approval gate.
