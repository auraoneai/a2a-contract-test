# GitHub Action

The composite Action validates one A2A-style agent card plus a recorded task
lifecycle transcript. It reads local JSON files only and does not contact the
agent card endpoint.

Use immutable commit pins:

```yaml
name: a2a-contract

on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read

jobs:
  a2a-contract:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
      - uses: auraoneai/a2a-contract-test@0afe508dcfc0328d43949ca77498230fe9f25c8b # v0.1.5
        with:
          agent-card: contracts/agent-card.json
          transcript: contracts/contract-transcript.json
          output: a2a-contract-report.md
```

The report path's parent directory must already exist. The Action exits nonzero
when the result contains high or critical findings; medium findings remain
visible in the report but do not make the profile fail. The Action and PyPI
package are released as `0.1.5`.

The `transcript` input may be omitted only when a file named
`contract-transcript.json` is next to the agent card.
