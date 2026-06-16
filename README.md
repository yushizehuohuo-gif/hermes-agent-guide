# Hermes Agent 指南 · Hermes Agent Guide

[中文](#中文) | [English](#english)

A single `AGENTS.md` file to improve Hermes Agent behavior on Windows. Battle-tested across Godot game dev, ComfyUI art pipelines, and npm publishing workflows.

---

## 中文

### 这是什么

一份经过实战验证的**项目级 AGENTS.md 模板**，解决 Hermes Agent 在 Windows 上的高频痛点：

- 🗂️ MSYS 路径 vs Windows 路径混用 → 文件操作出 `C:\c\` 影子目录
- 🔧 工具链选择混乱 → `python3` 不存在、`uv` vs `pip` 打架
- 🎨 生图/设计跳过技能系统 → 裸调工具效率低
- 🌐 中英混合沟通 → 反复纠正语言偏好
- 📦 npm 2FA 发布 → 死循环重试

### 四原则落地

Hermes 系统提示词内置了 Karpathy 四原则。这份指南把它们变成具体规则：

| 原则 | Windows 特有问题 | 本指南解法 |
|------|-----------------|-----------|
| **先亮假设再写代码** | MSYS vs Windows 路径混用 | 文件工具用原生路径，terminal 用 MSYS |
| **简洁优先** | 过度工程化简单任务 | 项目级工具链声明，省去每次解释 |
| **外科手术式修改** | 顺带重构、风格漂移 | 匹配现有风格，只动目标代码 |
| **完成工作** | 只给方案不执行 | 验证循环模板 + 真实输出验证 |

### 快速开始

```bash
# 下载到项目根目录
curl -o AGENTS.md https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md
```

追加到已有 AGENTS.md：

```bash
curl https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md >> AGENTS.md
```

然后修改 `## 环境` 部分，填上你的用户名和路径。

### 核心内容速览

| 章节 | 内容 |
|------|------|
| 环境声明 | OS、终端、包管理、引擎路径 |
| 路径规则 | 文件工具用 Windows 路径、terminal 用 MSYS 路径 |
| 工具链速查 | Python (uv)、Node (npm)、Godot、生图管线 |
| 沟通风格 | 默认中文、简洁直接、不确定就问 |
| 技能系统 | 生图→comfyui、游戏→godot、GitHub→pr-workflow |
| 记忆系统 | 偏好存 memory、流程存 skill、进度不存 |
| 验证循环 | 步骤→验证→继续，不跳步 |
| 常见陷阱 | python3 不存在、不要 PowerShell、npm 2FA |

### 效果验证

这份指南生效的标志：

- ✅ 文件操作不再产生 `C:\c\` 幽灵目录
- ✅ 不再出现 `python3: command not found`
- ✅ npm 发布一次通过，不循环重试
- ✅ 生图/设计任务自动加载相关技能
- ✅ 沟通语言和风格保持稳定

### 目录

```
hermes-agent-guide/
├── README.md          # 本文件（中英双语）
├── AGENTS.md          # 核心指令文件
├── EXAMPLES.md        # 6 个实战案例（中英双语）
└── LICENSE            # MIT
```

---

## English

### What This Is

A battle-tested `AGENTS.md` template that fixes Hermes Agent's most common pain points on Windows — the equivalent of [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills) but for Hermes.

### The Problems It Solves

| Problem | Without This Guide | With This Guide |
|---------|-------------------|-----------------|
| MSYS path confusion | Writes to `C:\c\` ghost directory | Files always land in the right place |
| Toolchain chaos | `python3` not found, wrong pip | `uv` + correct Python path |
| npm 2FA loops | 5 attempts, all fail | PTY + desktop browser, one shot |
| Skill system ignored | Raw tool calls, slow results | Auto-loads relevant Hermes skills |
| Language drift | Switches Chinese/English randomly | Stable, predictable communication |

### Quick Start

```bash
curl -o AGENTS.md https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md
```

Customize the `## 环境` section for your machine, then Hermes reads it automatically on every session.

### The Four Principles, Operationalized

Hermes ships with Karpathy's principles in its system prompt. This guide makes them concrete for Windows workflows:

1. **Surface Assumptions** → Explicit path rules (file tools vs terminal)
2. **Simplicity First** → Declared toolchain, no re-explaining every time
3. **Surgical Changes** → Match existing style, touch only target code
4. **Finish the Job** → Verification loops with real output validation

### Signs It's Working

- No more `C:\c\` ghost directories from path confusion
- No `python3: command not found` errors
- npm publish completes in one shot
- Skills auto-load for image/design tasks
- Communication language stays consistent

### See Also

- [EXAMPLES.md](./EXAMPLES.md) — 6 real-world patterns with ❌/✅ comparisons
- [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills) — the original inspiration (176k ⭐)

### License

MIT
