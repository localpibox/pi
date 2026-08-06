# Contributing to LocalPibox Pi Fork

This fork adds Qwen reasoning support to Pi. This document explains how to
contribute — whether by improving the LocalPibox patches, forking for your own
stack, or feeding changes back upstream.

## The Patch Model

All LocalPibox changes are kept as a **single squashed commit** on top of
upstream. This keeps the delta clean and makes rebasing straightforward.

```
upstream release ──→ [v0.83.0] ──┐
                                  │
lpb branch       ──→ [lpb patch]──┘
```

### Working on a patch

1. Fork or clone `localpibox/pi`
2. Check out the `lpb` branch
3. Make your changes in a feature branch
4. Squash into one commit: `git commit -S -s --squash`
5. Push and open a PR against the `lpb` branch

### Rebasing onto a new upstream release

```bash
# Fetch latest upstream
git fetch https://github.com/earendil-works/pi.git release:upstream-release

# Rebase the lpb patch
git checkout lpb
git rebase upstream-release

# Resolve conflicts if any, then force-push
git push --force-with-lease origin lpb
```

### Patch boundaries

- **In scope:** Changes that make reasoning/thinking work correctly with Qwen
  models (and potentially other local models)
- **Out of scope:** Pi core features, UI changes, or unrelated fixes — those
  go to upstream directly

## Forking for Your Own Stack

If you want to personalize this fork for your own setup:

1. **Fork** `localpibox/pi` to your own GitHub account
2. **Customize** the patches — adjust reasoning parameters, compaction settings,
   or add support for other models
3. **Repoint** your stack's `lpb.stack.env` at your fork:
   ```bash
   export LPB_PI_FORK=https://github.com/<you>/pi.git
   export LPB_PI_REF=main
   ```
4. **Rebuild** your devstack image (or update extensions at runtime)

See the
[Forking & Repointing guide](https://github.com/localpibox/devstack#forking--repointing)
for the full procedure.

## Feeding Back Upstream

If your patch proves useful beyond the LocalPibox stack:

1. **Open an issue** on `earendil-works/pi` describing the problem and your
   approach
2. **Split your patch** — ensure it's clean, focused, and not tied to
   LocalPibox-specific configuration
3. **Submit a PR** against the upstream `release` branch
4. **Follow up** — if upstream merges, fold it into the LocalPibox patch set

### What goes upstream

- ✅ Generally useful features (Qwen reasoning support, overflow detection)
- ✅ Bug fixes that apply regardless of model choice
- ✅ Core improvements that benefit all Pi users

### What stays local

- ❌ LocalPibox-specific configuration (model URLs, hardware-specific tuning)
- ❌ Workarounds that conflict with upstream design direction
- ❌ Opinionated defaults specific to this stack

## Reporting Issues

- **Pi core issues** → [earendil-works/pi/issues](https://github.com/earendil-works/pi/issues)
- **LocalPibox-specific patches** → [localpibox/pi/issues](https://github.com/localpibox/pi/issues)
- **Stack configuration** → [localpibox/devstack/issues](https://github.com/localpibox/devstack/issues)

## Communication

- [Pi Discord](https://discord.com/invite/3cU7Bz4UPx) — upstream community
- Issues and PRs on GitHub — preferred for technical discussions
