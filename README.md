# a2a-contract-test

`a2a-contract-test` validates an A2A-style agent card and a recorded task
lifecycle transcript without contacting the declared agent endpoint.

## At a Glance

| | |
| --- | --- |
| Job | Gate a static agent card and recorded lifecycle against a practical contract profile. |
| Built for | Agent developers, integration engineers, and CI owners exchanging A2A-style tasks. |
| Differentiator | Offline, dependency-free checks with a redacted transcript in the report. |
| Produces | Markdown or JSON findings, pass state, and a replayable redacted transcript excerpt. |

## Install

```bash
python -m pip install "a2a-contract-test==0.1.5"
```

## Verified Quickstart

Run from a source checkout:

```bash
a2a-contract-test run \
  --agent-card examples/passing_agent/agent-card.json \
  --format json \
  --out /tmp/a2a-contract-report.json
```

When `--transcript` is omitted, the CLI reads `contract-transcript.json` next to
the agent card. The bundled passing fixture returns `passed: true` with zero
findings. The failing fixture returns exit code `1`.

## Contract Profile

The v1 profile checks:

- required agent-card fields, semantic-version-like version text, and HTTP(S)
  endpoint shape;
- capabilities, input/output modes, streaming declarations, and JSON content
  type disclosure;
- submitted, progress, and terminal lifecycle states;
- stable task IDs, JSON request/result shape, cancellation evidence, structured
  errors, and unsupported-capability negotiation.

The report is considered passing when no high or critical findings exist.
Medium findings remain visible and do not fail the profile.

## GitHub Action

Pin the published Action to its immutable release commit:

```yaml
- uses: auraoneai/a2a-contract-test@a8aeb3e6c2495a0e2b3b47e6c349c2b7628f620c # v0.1.5
  with:
    agent-card: contracts/agent-card.json
    transcript: contracts/contract-transcript.json
    output: a2a-contract-report.md
```

See [`docs/github-action.md`](docs/github-action.md) for a complete workflow and
input behavior.

## Runtime, Data, and Network Boundary

- The CLI reads local JSON files only. It does not send requests to the endpoint
  declared in the card.
- Transcript redaction masks secret-like keys and strings containing `sk-...` or
  `pypi-...`; it is not a comprehensive PII scrubber.
- Reports can preserve task payloads, results, IDs, and non-secret metadata, so
  sanitize recordings before use.
- Passing this profile does not establish interoperability with a live agent,
  protocol compliance, reliability, or security.

## Limitations

- The profile checks one practical offline contract shape, not the full space of
  A2A implementations or future protocol revisions.
- A passing report cannot prove that a live agent honors the same behavior once
  network transport, authentication, and runtime state are involved.

## Compatibility

The published `auraone-agent-studio-open` CLI declares
`a2a-contract-test>=0.1.0` as a runtime dependency and exposes the profile
through `agentstudio a2a`.

This project is not an official A2A compliance suite and is not affiliated with
the Linux Foundation or an A2A standards body.

## Publication Status

Verified on 2026-07-13:

- PyPI: [`a2a-contract-test==0.1.5`](https://pypi.org/project/a2a-contract-test/0.1.5/)
- GitHub Action release: [`v0.1.5`](https://github.com/auraoneai/a2a-contract-test/releases/tag/v0.1.5)
- The Action and PyPI package share release `0.1.5`; the Action installs the
  package source from its checked-out immutable commit.
- Bundled cards and transcripts are synthetic fixtures.

## Next Action

Capture one sanitized task lifecycle, store it beside the matching agent card,
and add the pinned Action as a pull request gate.
