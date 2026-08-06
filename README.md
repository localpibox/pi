<div align="center">

![Pi](https://pi.dev/logo-auto.svg)

# LocalPibox Pi Fork

**Qwen reasoning support + context overflow fixes for [Pi](https://pi.dev)**

[![Upstream: earendil-works/pi](https://img.shields.io/badge/upstream-earendil--works/pi-blue)](https://github.com/earendil-works/pi)
[![Fork branch: lpb](https://img.shields.io/badge/fork-lpb-green)](https://github.com/localpibox/pi)

</div>

> **⚡ [← Back to LocalPibox](https://github.com/localpibox/localpibox)** — project overview, architecture, and the full stack.

---

## What this fork adds

This fork of [`earendil-works/pi`](https://github.com/earendil-works/pi) adds
targeted support for **Qwen models with reasoning (thinking)** — the changes
needed to make `reasoning_effort`, thinking budgets, and context overflow
detection work correctly with Qwen/Llama.cpp backends.

**All LocalPibox work is kept as a single squashed commit** on top of upstream,
so the delta is always one clean patch.

### Patches

| Patch | What it does | Files |
|---|---|---|
| **`reasoning_effort`** | Send `reasoning_effort` (high/medium/low) for Qwen models via the `qwen` / `qwen-chat-template` thinking formats | `packages/ai/src/api/openai-completions.ts` |
| **`reasoning_budget_tokens`** | Add reasoning-budget token support/typing for Qwen to prevent runaway thinking | `packages/ai/src/types.ts`, `generate-models.ts`, AI tests |
| **Case 4 context overflow** | Add Case 4 to `isContextOverflow`: Qwen/Llama.cpp reasoning overflow (`stopReason=length` + `output>0` + input ≥ 90% window) | `packages/ai/src/utils/overflow.ts` |
| **Compaction tuning** | Adjust compaction for Qwen thinking windows | `packages/agent/src/harness/compaction/compaction.ts` |
| **Reasoning wiring** | `reasoning_effort` field plumbing in coding-agent config | `packages/coding-agent/src/config.ts` |

### Upstream mapping

| LocalPibox | → | Upstream |
|---|---|---|
| `localpibox/pi` (fork) | ← | `earendil-works/pi` (releases) |

- **Upstream latest:** v0.83.0
- **Update policy:** rebase the `lpb` patch onto upstream **on releases only**
- **Branch strategy:** `lpb` branch carries LocalPibox changes; `main` tracks upstream

## Why these patches?

The target hardware (Ryzen AI Max+ 395, 128 GB unified memory) runs Qwen3.6-35B
locally via [Lemonade](https://github.com/lemonade-sdk/lemonade). Without these
patches, Pi cannot:

- Control thinking depth (`reasoning_effort`) for Qwen models
- Detect when Qwen's reasoning block triggers context overflow
- Properly compact sessions with thinking tokens
- Enforce reasoning budgets to prevent runaway token consumption

## Upstreaming

These patches are **candidate upstream contributions**. The goal is to submit
them to `earendil-works/pi` when they prove clean and generally useful. Qwen
reasoning support would benefit any Pi user running local models, not just this
stack.

## Building

Same as upstream:

```bash
npm install --ignore-scripts
npm run build
npm run check
./test.sh
```

See [earendil-works/pi#CONTRIBUTING](https://github.com/earendil-works/pi/blob/release/CONTRIBUTING.md).

## License

See the [upstream license](https://github.com/earendil-works/pi/blob/release/LICENSE).
LocalPibox patches inherit the same license.
