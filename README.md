## Agent Treasury Policy Linter — GitHub Action

[![CI](https://github.com/Amara-ops/policy-linter-action/actions/workflows/ci.yml/badge.svg)](https://github.com/Amara-ops/policy-linter-action/actions/workflows/ci.yml)

One-line CI gate for agent-treasury safety. Fails your workflow if guardrails are missing:
- selector+chainId allowlists
- spend caps (hour/day)
- timelock + quorum for escalations
- pause + logging

This Action fetches the compiled CLI from the main repo and runs it on your policy.json.

Links
- Main linter (docs, samples, cookbook): https://github.com/Amara-ops/agent-guardrails-policy-linter

## Usage
```yaml
jobs:
  policy_lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: Amara-ops/policy-linter-action@v0
        with:
          policy: path/to/policy.json
          report: policy.report.json     # optional (default: policy.report.json)
          strict: 'true'                 # optional, treats warnings as errors
          # version: v0                  # optional, linter tag/branch (defaults to v0)
```

## Inputs
- policy (required): path to policy JSON
- report (optional): path to write JSON report (default: policy.report.json)
- strict (optional): 'true' to fail on warnings (default: 'false')
- version (optional): linter ref in main repo (default: v0)
- sarif (optional): path to write SARIF output
- artifact (optional): repo-relative path to anchor SARIF findings (defaults to policy path)

## GitHub Code Scanning (SARIF)
What it is
- GitHub Code Scanning ingests SARIF and shows alerts in Security → Code scanning alerts and on PRs.
- Public repos: free. Private repos: requires GitHub Advanced Security.

When to use
- If you want policy issues visible in GitHub’s Security UI/PR checks, not just as a CI failure.

How to enable with this Action
```yaml
name: Policy Lint
on: [push, pull_request, workflow_dispatch]

permissions:
  contents: read
  security-events: write  # required to upload SARIF

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      # Run the linter and emit SARIF
      - uses: Amara-ops/policy-linter-action@v0
        with:
          policy: policies/agent/policy.json
          report: policy.report.json
          sarif: policy.sarif
          artifact: policies/agent/policy.json   # repo-relative path recommended
          strict: 'false'                        # set 'true' to block on warnings
          # version: v0                          # optional; defaults to v0

      # Upload SARIF to Code Scanning
      - uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: policy.sarif
```
Notes
- artifact sets which file the alert is attached to; defaults to the policy input. Use repo-relative paths for best results.
- If you don’t need Code Scanning, omit sarif/artifact and just use report.

## License
MIT
