# Hermes Agent Guide

Project-level AGENTS.md optimized for **Hermes Agent** — the multi-tool desktop AI assistant by Nous Research.

**Who this is for**: Windows users, Godot/ComfyUI creators, Chinese-speaking developers, and anyone who wants Hermes to actually understand their workflow.

---

## What This Solves

Hermes is powerful, but out of the box it doesn't know:
- That you're on Windows with MSYS2 bash
- That `python3` doesn't exist but `uv` does
- That MSYS paths break file operations
- That npm publish needs 2FA via desktop browser
- That you communicate in Chinese by default

This guide is a battle-tested AGENTS.md template that fixes all of that.

---

## Quick Start

```bash
# Download into your project root
curl -o AGENTS.md https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md
```

Or append to existing AGENTS.md:

```bash
curl https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md >> AGENTS.md
```

Then customize the `## 环境` section for your machine.

---

## The 4 Principles (already in Hermes)

Hermes ships with Karpathy's principles built into its system prompt. This guide operationalizes them for real-world use:

| Principle | Hermes Challenge | This Guide |
|-----------|-----------------|------------|
| **Surface Assumptions** | MSYS vs Windows path confusion | Explicit path rules |
| **Simplicity First** | Over-engineering simple tasks | Toolchain defaults |
| **Surgical Changes** | Drive-by refactoring, style drift | Project style lock |
| **Finish the Job** | Plans without execution | Verification loop template |

---

## What's Inside

| File | Purpose |
|------|---------|
| `AGENTS.md` | Core instruction file — add to your project root |
| `EXAMPLES.md` | Real-world patterns: right vs. wrong |
| `README.md` | This guide (Chinese) |
| `README.en.md` | English version |

---

## License

MIT
