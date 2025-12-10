---
name: vLLM Slack Summary
description: Generate slack summaries of vLLM CI SIG Slack channel activity for the RHAIIS midstream release team
---

# vLLM slack Summary Skill

Automates generating slack summaries of the vLLM CI SIG Slack channel for the Red Hat AI Inference Server (RHAIIS) team.

## Prerequisites

- **Python 3.12+** (or a compatible recent Python 3 version)
- **slackdump** installed and authenticated with vLLM workspace
  - Install: <https://github.com/rusq/slackdump>
  - Auth: Run `slackdump workspace add`

## Usage

```bash
./scripts/generate_transcript.py                    # Last 7 days (default)
./scripts/generate_transcript.py --days 14          # Custom date range
./scripts/generate_transcript.py --channel C07R5PAL2L9  # Override Slack channel ID
./scripts/generate_transcript.py --output-dir out       # Custom output directory
```

## Context & Scope

When summarizing the transcript, treat this as a **Red Hat–centric midstream CI brief** for upstream vLLM:

- **Scope**: Only surface items that matter for **Red Hat’s midstream goals around upstream vLLM CI**. Prefer:
  - **Channels/threads** involving SIG CI, Midstream Upstream, hardware/infra channels, and release/CI-governance threads.
  - **Messages from or about Red Hat engineers** or that clearly affect Red Hat’s CI, packaging, or supported hardware, even if discussed by others.

- **Per‑thread summary format (for any non‑trivial thread or decision)**:
  - **What happened / is proposed**: 1–2 sentences, explicitly referencing one or more **Red Hat tags** (see below).
  - **Why it matters for Red Hat midstream**: e.g. affects AMD gating, changes revert policy, shortens Docker builds, alters test selection.
  - **Who is involved**: call out **Red Hat folks explicitly** and add SIGs / working groups when mentioned.
  - **Timing** if stated: e.g. “after 0.12 release”, “targeting next PyTorch release”, “by end of November”.

- **Red Hat–specific tags**: tag each extracted item with one or more of:
  - `rh-ci-health` – trunk/main/nightly status, treehugger/on‑call rotation, revert vs fix‑forward decisions, any change to what “green” means.
  - `rh-build-perf` – Docker/build changes affecting CI time or image size (ECR/buildkit, kernel split, slimming, caching).
  - `rh-tests-selection` – pipeline generator, CI shadow, test‑selection logic, unit vs e2e vs nightly coverage.
  - `rh-tests-ownership` – SIG mappings, codeowners, test categorization by area/SIG, flake/soft‑fail decisions.
  - `rh-platform-matrix` – AMD/ROCm, CPU, TPU, H100/B200/IBM clusters, L4; any change to their CI pipelines or gating policy.
  - `rh-deps-releases` – dependency pinning/unpinning; PyTorch, CUDA, flash‑infer/flash‑attn, exformers; vLLM + PyTorch release coordination.
  - `rh-infra-metrics` – dashboards, alerts, CI cost, infra donations, runners added/removed, capacity constraints that impact CI.
  - `rh-build-system` – custom build backend, setuptools/pyproject changes, CPU/TPU build logic, deterministic builds and packaging.

- **Prioritization when choosing what to include**:
  1. **Changes to gating and release criteria** (`rh-ci-health`, `rh-platform-matrix`, `rh-deps-releases`).
  2. **CI performance or infra changes** that affect developer speed or cost (`rh-build-perf`, `rh-infra-metrics`).
  3. **Platform/hardware issues & fixes** on AMD/CPU/TPU/H100/B200 that could surprise midstream (`rh-platform-matrix`, `rh-build-system`).
  4. **Structural test/ownership changes** that change how failures are triaged (`rh-tests-selection`, `rh-tests-ownership`).

## Output & Formats

The skill creates `vllm_slack_summary/` containing:

```text
vllm_slack_summary/
├── slack_export/                               # Raw Slack export
├── transcript.md                               # Markdown transcript
└── slack_summary_YYYY-MM-DD_to_YYYY-MM-DD.md  # Summary report
```

After generating the transcript, analyze it and create one of the following, depending on cadence:

- **Daily / per‑incident note (short form)**:
  - List only the **top 1–3 items** that hit **priorities 1–3** above.
  - For each item, give:
    - 1–2 sentence summary of what happened, including **tag(s)** in‑line, and a **link to the originating Slack thread**.
    - 1 short sentence on **why it matters for Red Hat midstream**.

- **Weekly digest (grouped for Red Hat consumption)**:
  - Use headings and include only the **2–3 highest‑impact bullets** under each, with tags and thread links:
    - **CI health & treehugger** (`rh-ci-health`)
    - **Docker & build performance** (`rh-build-perf`)
    - **Tests, coverage & ownership** (`rh-tests-selection`, `rh-tests-ownership`)
    - **Hardware/platform matrix** (`rh-platform-matrix`)
    - **Deps & releases** (`rh-deps-releases`)
    - **Infra, dashboards & cost** (`rh-infra-metrics`, `rh-build-system`)
  - Each bullet should briefly state:
    - What changed / is proposed and the **tag(s)**.
    - Why it matters for Red Hat (gating, performance, hardware surprise, etc.).
    - Who is involved and any stated timing.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| slackdump not found | Ensure slackdump is in PATH: `which slackdump` |
| Authentication failed | Run `slackdump workspace add` |
| No messages found | Check date range; channel may have no messages in that period |

## Quick Reference

- **Channel ID**: C07R5PAL2L9 (vLLM CI SIG)
- **Transcript**: `vllm_slack_summary/transcript.md`
- **Summary**: `vllm_slack_summary/slack_summary_YYYY-MM-DD_to_YYYY-MM-DD.md`
