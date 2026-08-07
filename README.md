<p align="center">
  <a href="https://pi.dev">
    <img alt="pi logo" src="https://pi.dev/logo-auto.svg" width="128">
  </a>
</p>
<p align="center">
  <a href="https://discord.com/invite/3cU7Bz4UPx"><img alt="Discord" src="https://img.shields.io/badge/discord-community-5865F2?style=flat-square&logo=discord&logoColor=white" /></a>
  <a href="https://www.npmjs.com/package/@earendil-works/pi-coding-agent"><img alt="npm" src="https://img.shields.io/npm/v/@earendil-works/pi-coding-agent?style=flat-square" /></a>
</p>

> New issues and PRs from new contributors are auto-closed by default. Maintainers review auto-closed issues daily. See [CONTRIBUTING.md](CONTRIBUTING.md).

# Pi Agent Harness — LocalPibox Fork

This fork of [`earendil-works/pi`](https://github.com/earendil-works/pi) adds targeted support for **Qwen models with reasoning (thinking)** for local [Lemonade](https://github.com/lemonade-sdk/lemonade) backends (llama.cpp). All LocalPibox changes are kept as **small surgical edits on top of upstream `v0.84.1`** so the delta is minimal and rebasing onto new releases is straightforward.

## Base: upstream v0.84.1 ✅

These features are **already in upstream v0.84.1** and require no LocalPibox patches:

| Feature | Location | What it does |
|---|---|---|
| `qwen-chat-template` thinking format | `ai/src/api/openai-completions.ts` | Sends `chat_template_kwargs.enable_thinking` + `preserve_thinking` for Qwen/llama.cpp |
| `reasoning_effort` mapping | `ai/src/api/openai-completions.ts` | Sends top-level `reasoning_effort` for various providers |
| vLLM `supportsThinkingTokenBudget` | `ai/src/api/openai-completions.ts` | Caps reasoning via top-level `thinking_token_budget` (vLLM servers) |
| `compact()` method | `agent/src/harness/agent-harness.ts` | Agent harness compaction support |
| Compaction for Qwen thinking windows | `agent/src/harness/compaction/compaction.ts` | Passes `reasoning`/thinking during compaction |

## Added by LocalPibox on top of v0.84.1 🔧 (4 files, +29 lines)

These are the **only** changes this fork adds on top of v0.84.1:

| File | Change | Why |
|---|---|---|
| `ai/src/utils/overflow.ts` | **Case 4** overflow detection | Qwen/Llama.cpp reasoning blocks silently consume the output budget → `stopReason "length"` + output > 0 + input ≥ 90% of window. Without it, Pi treats overflow as a dead session after compaction. |
| `ai/src/types.ts` | `reasoningBudgetTokens` compat field | Lets a provider (e.g. the lemonade plugin) set a Qwen thinking budget (0 = soft-capped, positive = token limit, -1 = unbounded). |
| `ai/src/api/openai-completions.ts` | `qwen-chat-template` sends `reasoning_effort` mapping + `reasoning_budget_tokens` soft-cap | Reads `compat.reasoningBudgetTokens` and emits the `reasoning_budget_tokens` llama.cpp sampler param to prevent runaway thinking. |
| `coding-agent/src/config.ts` | `LOCALPIB_VERSION` from `LPB_VERSION` env | Version tracking for the LocalPibox stack. |

**Intentionally NOT included:** The `SessionTreeEntry`/`fromHook` refactor, compaction comment-only changes, and the `baseten` / `qwen-token-plan-individual` provider removals. None are required for Lemonade/Qwen support, and they are incompatible with the v0.84.1 codebase (they were written against an older v0.83 base).

## Why these changes?

The target hardware (Ryzen AI Max+ 395, 128 GB unified memory) runs Qwen3.6-35B locally via Lemonade. The lemonade-pi-plugin sets `reasoningBudgetTokens` (soft-cap) and `thinkingFormat: "qwen-chat-template"` on Qwen models. This fork makes core Pi:
- Send the `reasoning_budget_tokens` soft-cap so Qwen's thinking doesn't exhaust `max_tokens`
- Detect Qwen reasoning-driven context overflow (Case 4) instead of leaving the session dead

## Upstream mapping

| LocalPibox | ← | Upstream |
|---|---|---|
| `localpibox/pi` (branch `lpb`) | ← | `earendil-works/pi` |

## Reporting Issues

- **Pi core issues** → [earendil-works/pi/issues](https://github.com/earendil-works/pi/issues)
- **LocalPibox patches** → [localpibox/pi/issues](https://github.com/localpibox/pi/issues)
- **Stack config** → [localpibox/devstack/issues](https://github.com/localpibox/devstack/issues)

## Development

```bash
npm install --ignore-scripts  # Install all dependencies without running lifecycle scripts
npm run build         # Refresh model data, then build all packages
npm run check         # Lint, format, and type check
./test.sh            # Run tests (skips LLM-dependent tests without API keys)
```

## License

See the [upstream license](https://github.com/earendil-works/pi/blob/release/LICENSE). LocalPibox patches inherit the same license.

<p align="center">
  <a href="https://pi.dev">pi.dev</a> domain graciously donated by
  <br /><br />
  <a href="https://exe.dev"><img src="packages/coding-agent/docs/images/exy.png" alt="Exy mascot" width="48" /><br />exe.dev</a>
</p>
