# Agent Treasury Policy Linter — GitHub Action

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
          version: v0.1.0                # optional, linter tag/branch (defaults to main)
```

## Inputs
- policy (required): path to policy JSON
- report (optional): path to write JSON report (default: policy.report.json)
- strict (optional): 'true' to fail on warnings (default: 'false')
- version (optional): linter ref in main repo (default: main)

## Notes
- Some sample policies in the main repo intentionally emit educational warnings (e.g., anomaly block off, low timelock, missing slippage). In non-strict mode, these still pass; in `--strict`, they fail.
- Pin `version` to a tag (e.g., v0.1.0) for reproducible builds.

## License
MIT
