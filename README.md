# Hermes Agent Guide · Hermes Agent 使用指南

<p align="center">
  <img src="https://img.shields.io/github/stars/yushizehuohuo-gif/hermes-agent-guide?style=flat-square&color=B31E3D" alt="Stars">
  <img src="https://img.shields.io/github/license/yushizehuohuo-gif/hermes-agent-guide?style=flat-square&color=B31E3D" alt="License: MIT">
  <img src="https://img.shields.io/badge/platform-Windows%2010%2F11-blue?style=flat-square" alt="Windows">
  <img src="https://img.shields.io/badge/Hermes%20Agent-v2.x-blue?style=flat-square" alt="Hermes Agent">
  <img src="https://img.shields.io/badge/language-EN%2FZH-brightgreen?style=flat-square" alt="Bilingual">
</p>

<p align="center">
  <b>Make Hermes Agent work perfectly on Windows — on the first try, every time.</b><br>
  <b>让 Hermes Agent 在 Windows 上完美工作 — 一次配置，次次省心。</b>
</p>

---

> **English** | [中文](#chinese-中文)

## English

### Table of Contents

- [What This Is](#what-this-is)
- [The Problems It Fixes](#the-problems-it-fixes)
- [Why You Need This](#why-you-need-this)
- [Quick Start (30 Seconds)](#quick-start-30-seconds)
- [Detailed Usage Guide](#detailed-usage-guide)
  - [Step 1: Download AGENTS.md](#step-1-download-agentsmd)
  - [Step 2: Customize for Your Machine](#step-2-customize-for-your-machine)
  - [Step 3: Verify It's Working](#step-3-verify-its-working)
  - [Step 4: Add to All Your Projects](#step-4-add-to-all-your-projects)
- [What You Get](#what-you-get)
- [How It Works](#how-it-works)
- [Signs It's Working](#signs-its-working)
- [Project Structure](#project-structure)
- [FAQ](#faq)
- [See Also](#see-also)
- [Contributing](#contributing)
- [License](#license)

---

### What This Is

A **single `AGENTS.md` file** that you drop into any project root. Hermes Agent reads it automatically at the start of every session — no plugins, no config changes, no complex setup.

It's the Hermes equivalent of [Cursor Rules](https://docs.cursor.com/context/rules-for-ai) or [GitHub Copilot Instructions](https://code.visualstudio.com/docs/copilot/copilot-customization) — a plain-text instruction file that tells the AI exactly how your environment works, so it stops guessing and starts getting things right.

**Battle-tested** across:
- 🎮 Godot 4 game development (FPS card game, interactive fiction, management sim)
- 🎨 ComfyUI AI art pipelines (image generation, compositing, poster design)
- 📦 npm package publishing (2FA flows, GitHub Pages deployments)
- 🌐 Next.js web projects (portfolio sites, editorial designs)
- 📊 Data & automation workflows (cron jobs, email reports, social media bots)

### The Problems It Fixes

| # | Problem | Before (Without Guide) | After (With Guide) |
|---|---------|----------------------|-------------------|
| 1 | **MSYS path confusion** | Files land in `C:\c\Users\…` ghost directory; original files untouched | Files always go to the correct Windows path |
| 2 | **Toolchain chaos** | `python3` command not found; `pip` installs to wrong Python; `uv` vs `pip` confusion | Right tool, right version, right path — every time |
| 3 | **npm 2FA loops** | 5 OTP attempts all fail; npm publish deadlocks forever | PTY mode + desktop browser = one-shot publish |
| 4 | **Skill system ignored** | Raw `image_generate()` calls produce mediocre results; manual post-processing takes 30 minutes | Skills auto-load; polished poster in 3 minutes |
| 5 | **Language drift** | Switches between Chinese and English randomly; you correct it 5+ times per session | Language choice is stable and predictable |
| 6 | **Over-engineering** | 200 lines for a one-line fix; 15 files changed for a single feature | Surgical changes — only the lines that need to change |
| 7 | **PowerShell confusion** | `Get-ChildItem`, `Select-String` commands fail in git-bash | Hermes knows the shell is bash, not PowerShell |
| 8 | **Verification skipped** | Hermes says "done" but never actually ran the code | Every step is verified with real tool output |

### Why You Need This

Hermes Agent ships with excellent system prompts (Karpathy principles, tool-use enforcement, etc.), but it doesn't know **your specific Windows environment**. Without AGENTS.md, you spend every session correcting the same mistakes:

- "No, use `python` not `python3`"
- "No, the file is at `C:\Users\...` not `/c/Users/...`"
- "Please speak Chinese, not English"
- "Don't refactor unrelated code"
- "Actually run it, don't just describe what you'd do"

With AGENTS.md, these corrections happen **zero times** — Hermes reads the rules once and follows them forever.

### Quick Start (30 Seconds)

```bash
# 1. Download the AGENTS.md to your project
curl -o AGENTS.md https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md

# 2. Edit the environment section for your machine
#    (Change username, paths, Godot version, etc.)

# 3. Done! Hermes reads AGENTS.md automatically.
```

Or append to your existing AGENTS.md:

```bash
curl https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md >> AGENTS.md
```

### Detailed Usage Guide

#### Step 1: Download AGENTS.md

There are three ways to get AGENTS.md into your project:

**Option A: Fresh project (recommended)**

```bash
cd /c/Users/<your-username>/Projects/<your-project>/
curl -o AGENTS.md https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md
```

**Option B: Append to existing AGENTS.md**

If your project already has an AGENTS.md with project-specific instructions:

```bash
cd /c/Users/<your-username>/Projects/<your-project>/
curl https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md >> AGENTS.md
```

Then manually merge conflicting sections (like `## Environment`).

**Option C: Manual copy**

Open [AGENTS.md](https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md) in your browser, copy the content, and paste it into your project's `AGENTS.md`.

> **💡 Pro tip:** Use Option A for new projects, Option B for existing projects with their own AGENTS.md.

#### Step 2: Customize for Your Machine

Open AGENTS.md and find the `## Environment / 环境` section. Replace the placeholders:

```yaml
## Environment / 环境

- **OS:** Windows 10/11
- **Shell:** git-bash (MSYS2) — POSIX syntax: `ls`, `grep`, `/c/Users/…`
- **Package managers:** `uv` (Python), `npm` (Node)
- **Home directory:** C:\Users\YOUR_USERNAME        # ← CHANGE THIS
- **Godot engine:** C:\Users\YOUR_USERNAME\Projects\Godot\engine\Godot_v4.Y.Z-stable_win64.exe  # ← CHANGE THIS
- **Python (image processing):** C:\Users\YOUR_USERNAME\AppData\Local\Programs\Python\Python311\python.exe  # ← CHANGE THIS
```

**Quick way to find your paths:**

```bash
# Find your Godot engine location
ls /c/Users/$USERNAME/Projects/Godot/engine/Godot_v*-stable_win64.exe

# Find your Python installation
which python
python --version

# Check if you have uv installed
uv --version
```

> **💡 Pro tip:** The `YOUR_USERNAME` placeholder appears in 3 places. Use your text editor's find-and-replace (Ctrl+H) to replace all at once.

#### Step 3: Verify It's Working

After adding AGENTS.md, start a new Hermes session and test it:

**Test 1: Path handling**

Ask Hermes to create a test file:
> "Create a test file at C:\Users\YOUR_USERNAME\Projects\hermes-test.txt with the content 'hello'"

If AGENTS.md is working, the file will be created at the exact path. If not, it might appear at `C:\c\Users\YOUR_USERNAME\…` (the ghost directory problem).

**Test 2: Language consistency**

Send a message in Chinese. Hermes should reply in Chinese. Send one in English and it should switch.

**Test 3: Tool choice**

Ask Hermes to install a Python package. It should use `uv pip install`, not `pip install` or `python3 -m pip install`.

**Test 4: Verification loops**

Give Hermes a multi-step task. It should follow the pattern:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
```

Not skip verification steps or declare "done" without actually running the code.

**Test 5: No ghost directories**

```bash
# Check if the ghost directory exists
ls /c/c/ 2>/dev/null && echo "❌ Ghost directory found" || echo "✅ Clean"
```

If you see `C:\c\` appear after file operations, AGENTS.md is not being loaded. Check that:
1. The file is named exactly `AGENTS.md` (case-sensitive on some systems)
2. It's in your project root directory
3. You started a **new** Hermes session after adding it (`/reset` in CLI, or restart)

#### Step 4: Add to All Your Projects

Once you've verified AGENTS.md works on one project, add it to your other projects:

```bash
# Copy your customized version to other projects
cp /c/Users/$USERNAME/Projects/project1/AGENTS.md /c/Users/$USERNAME/Projects/project2/AGENTS.md
```

Or create a template in your home directory for new projects:

```bash
# Save a clean template
cp /c/Users/$USERNAME/Projects/project1/AGENTS.md /c/Users/$USERNAME/AGENTS.template.md

# Use for new projects
cp /c/Users/$USERNAME/AGENTS.template.md /c/Users/$USERNAME/Projects/new-project/AGENTS.md
```

### What You Get

When AGENTS.md is active, Hermes Agent gains these capabilities automatically:

| Feature | How It Works |
|---------|-------------|
| **Correct paths** | File tools use Windows native paths (`C:\Users\…`); terminal commands use MSYS paths (`/c/Users/…`) |
| **Right tools** | `uv` for Python, `npm` for Node, direct path for Godot — no guessing |
| **Skill auto-load** | Image tasks → loads `comfyui` skill; game tasks → loads `ai-assisted-game-dev`; npm → loads `npm-publish-flow` |
| **Language stability** | Defaults to Chinese; switches to English only when the user writes in English |
| **Surgical edits** | Touches only the code that needs changing; no drive-by refactoring |
| **Real verification** | Actually runs code and reports results; never fabricates output |
| **Blocking honesty** | Reports errors and blockers plainly instead of inventing results |
| **Memory discipline** | Saves preferences to memory, workflows to skills, NOT task progress |

### How It Works

Hermes Agent follows a hierarchy of instructions:

```
System Prompt (built-in)
  ├── Karpathy principles (surface assumptions, simplicity, surgical, finish the job)
  ├── Tool-use enforcement rules
  └── Agent persona definition
      │
      ▼
AGENTS.md (your project)
  ├── Environment: OS, shell, paths, toolchain
  ├── Path rules: file tools vs terminal
  ├── Communication: language, tone, style
  ├── Skills: which skills to load for which tasks
  ├── Memory: what to save, what not to save
  └── Verification: steps and checkpoints
      │
      ▼
Session Memory (accumulated)
  ├── User preferences
  ├── Past corrections
  └── Environment quirks
```

AGENTS.md sits between the system prompt and session memory — it provides stable, project-specific context that doesn't change session to session.

### Signs It's Working

You'll know AGENTS.md is active when:

- ✅ File operations never create `C:\c\` ghost directories
- ✅ `python3: command not found` never appears
- ✅ npm publish completes in one attempt (no 2FA loop)
- ✅ Image generation and design tasks automatically load relevant skills
- ✅ Communication language stays consistent (Chinese by default)
- ✅ Code changes are surgical — only target files touched
- ✅ Every step includes a verification checkpoint
- ✅ Hermes reports blockers honestly instead of fabricating results

### Project Structure

```
hermes-agent-guide/
├── 📄 README.md          ← You're reading this (EN/ZH bilingual)
├── 📄 AGENTS.md           ← Core instruction file (drop into your project)
├── 📄 EXAMPLES.md         ← 8 real-world examples with ❌/✅ comparisons
└── 📄 LICENSE             ← MIT — use freely, modify freely
```

### FAQ

<details>
<summary><b>Q: Do I need to restart Hermes after adding AGENTS.md?</b></summary>

Yes. Start a new session (`/reset` in CLI, or close and reopen the app). AGENTS.md is read at session start, not mid-session.
</details>

<details>
<summary><b>Q: Can I have project-specific and global rules?</b></summary>

Yes. Put project-specific rules in `AGENTS.md` in the project root. Hermes reads it automatically when you open that project directory. For global rules that apply everywhere, use Hermes's built-in memory system (`memory` tool) instead.
</details>

<details>
<summary><b>Q: What if my team uses different operating systems?</b></summary>

AGENTS.md supports multiple environments. Add separate environment blocks:

```yaml
## Environment / 环境

### Windows (yushi)
- OS: Windows 11, Shell: git-bash (MSYS2), Home: C:\Users\yushi

### macOS (alice)
- OS: macOS 14, Shell: zsh, Home: /Users/alice
```

Hermes will match the running OS and use the correct configuration.
</details>

<details>
<summary><b>Q: Does this work with other AI coding agents (Claude Code, Codex, Cursor)?</b></summary>

The core principles (environment declaration, path rules, verification loops) are universal and work with any AI agent that reads AGENTS.md or similar instruction files. However:

- **Path rules** are Windows/MSYS-specific
- **Skill references** are Hermes-specific (comfyui, ai-assisted-game-dev, etc.)
- **Toolchain commands** (`uv`, `npm`) are cross-agent

For non-Hermes agents, remove the `## Skills` section and keep the rest.
</details>

<details>
<summary><b>Q: How do I update AGENTS.md when my environment changes?</b></summary>

Edit the `## Environment` section in your project's AGENTS.md. Changes take effect at the next session start. For frequent environment changes (switching between machines), consider using Hermes's memory system to store machine-specific paths.
</details>

<details>
<summary><b>Q: Will AGENTS.md conflict with Hermes's built-in prompts?</b></summary>

No. AGENTS.md supplements the system prompt, it doesn't replace it. Hermes reads both and merges them — your AGENTS.md rules add Windows-specific context that the general-purpose system prompt can't provide.
</details>

<details>
<summary><b>Q: Can I use AGENTS.md without Git?</b></summary>

Yes. Just place the file in your project root directory. Hermes reads it from the filesystem regardless of Git status.

However, adding AGENTS.md to Git (`git add AGENTS.md && git commit`) is recommended so your team members also benefit from it.
</details>

<details>
<summary><b>Q: The ghost directory C:\c\ still appears even with AGENTS.md. What's wrong?</b></summary>

Three common causes:

1. **AGENTS.md not loaded** — Hermes needs a session restart (`/reset`) to pick up the file
2. **AGENTS.md in wrong location** — Must be in the project root directory (where you run `hermes`)
3. **MSYS path in file tools** — `write_file("/c/Users/…")` creates the ghost directory. Must use `write_file("C:\Users\…")`

To clean up the ghost directory:
```bash
# From MSYS2 / git-bash
cmd.exe //c "rmdir /s /q C:\\c"
```
</details>

### See Also

- [EXAMPLES.md](./EXAMPLES.md) — 8 real-world patterns with ❌/✅ comparisons
- [Hermes Agent Official Docs](https://hermes-agent.nousresearch.com/docs/) — Complete Hermes documentation
- [Hermes Agent GitHub](https://github.com/NousResearch/hermes-agent) — Source code and issues
- [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills) — The original inspiration (Karpathy's skill system for Claude Code)

### Contributing

Found a bug? Have a new pattern to share? PRs welcome!

1. Fork the repo
2. Add your pattern to `EXAMPLES.md`
3. Submit a PR with a clear description of the problem and solution

Or just open an issue with your scenario — I'll add it.

### License

MIT — use it, modify it, share it. Credit appreciated but not required.

---

## Chinese · 中文

### 目录

- [这是什么](#这是什么)
- [它解决的问题](#它解决的问题)
- [为什么你需要它](#为什么你需要它)
- [快速开始（30 秒）](#快速开始30-秒)
- [详细使用指南](#详细使用指南)
  - [第一步：下载 AGENTS.md](#第一步下载-agentsmd)
  - [第二步：为你的机器定制](#第二步为你的机器定制)
  - [第三步：验证是否生效](#第三步验证是否生效)
  - [第四步：添加到所有项目](#第四步添加到所有项目)
- [你能得到什么](#你能得到什么)
- [工作原理](#工作原理)
- [生效的标志](#生效的标志)
- [项目结构](#项目结构)
- [常见问题](#常见问题)
- [参考资源](#参考资源)
- [贡献](#贡献)
- [许可证](#许可证)

---

### 这是什么

一个**单独的 `AGENTS.md` 文件**，放到任何项目根目录即可生效。Hermes Agent 在每个会话启动时自动读取 — 不需要安装插件、不需要改配置、不需要复杂设置。

它是 Hermes 版本的 [Cursor Rules](https://docs.cursor.com/context/rules-for-ai) 或 [GitHub Copilot Instructions](https://code.visualstudio.com/docs/copilot/copilot-customization) — 一份纯文本指令文件，告诉 AI 你的环境如何运行，让它不再猜测，直接正确执行。

**实战验证**覆盖：
- 🎮 Godot 4 游戏开发（FPS 卡牌游戏、互动小说、经营模拟）
- 🎨 ComfyUI AI 绘图流程（生图、合成、海报设计）
- 📦 npm 包发布（2FA 认证、GitHub Pages 部署）
- 🌐 Next.js 网页项目（个人作品集、编辑风格设计）
- 📊 数据与自动化工作流（定时任务、邮件报告、社交媒体机器人）

### 它解决的问题

| # | 问题 | 之前（没指南） | 之后（有指南） |
|---|------|--------------|--------------|
| 1 | **MSYS 路径混淆** | 文件写到 `C:\c\Users\…` 幽灵目录；原文件纹丝不动 | 文件总是落到正确的 Windows 路径 |
| 2 | **工具链混乱** | `python3` 命令不存在；`pip` 装到错误的 Python；分不清 `uv` 和 `pip` | 正确的工具、正确的版本、正确的路径 |
| 3 | **npm 2FA 死循环** | 5 次 OTP 全部失败；npm publish 无限重试 | PTY 模式 + 桌面浏览器 = 一次通过 |
| 4 | **技能系统被忽略** | 裸调 `image_generate()` 效果一般；手动后期处理耗时 30 分钟 | 自动加载技能；3 分钟出精美海报 |
| 5 | **语言漂移** | 中英文随机切换；每个会话纠正 5+ 次 | 语言选择稳定可预测 |
| 6 | **过度工程化** | 一行修改写 200 行代码；一个功能改 15 个文件 | 外科手术式修改 — 只改需要改的 |
| 7 | **PowerShell 混淆** | `Get-ChildItem`、`Select-String` 在 git-bash 中报错 | Hermes 知道要用 bash 命令 |
| 8 | **跳过验证** | Hermes 说"完成"但代码从未运行 | 每步都用真实工具输出验证 |

### 为什么你需要它

Hermes Agent 内置了优秀的系统提示（Karpathy 四原则、工具使用强制等），但它不知道**你具体的 Windows 环境**。没有 AGENTS.md，你每个会话都要纠正同样的错误：

- "别用 `python3`，用 `python`"
- "文件在 `C:\Users\...` 不是 `/c/Users/...`"
- "请说中文"
- "别重构无关代码"
- "实际运行一下，别光描述"

有了 AGENTS.md，这些纠正发生**零次** — Hermes 读一次规则，永远遵守。

### 快速开始（30 秒）

```bash
# 1. 下载 AGENTS.md 到你的项目
curl -o AGENTS.md https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md

# 2. 编辑环境部分，改成你的用户名和路径

# 3. 完成！Hermes 自动读取 AGENTS.md
```

或者追加到已有的 AGENTS.md：

```bash
curl https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md >> AGENTS.md
```

### 详细使用指南

#### 第一步：下载 AGENTS.md

三种方式获取：

**方式 A：新项目（推荐）**

```bash
cd /c/Users/<你的用户名>/Projects/<你的项目>/
curl -o AGENTS.md https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md
```

**方式 B：追加到已有 AGENTS.md**

如果项目已有 AGENTS.md，里面包含项目特定指令：

```bash
cd /c/Users/<你的用户名>/Projects/<你的项目>/
curl https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md >> AGENTS.md
```

然后手动合并冲突的部分（如 `## Environment`）。

**方式 C：手动复制**

在浏览器打开 [AGENTS.md](https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md)，复制内容，粘贴到你的项目的 `AGENTS.md` 中。

> **💡 建议：** 新项目用方式 A，已有 AGENTS.md 的老项目用方式 B。

#### 第二步：为你的机器定制

打开 AGENTS.md，找到 `## Environment / 环境` 部分，替换占位符：

```yaml
## Environment / 环境

- **OS:** Windows 10/11
- **Shell:** git-bash (MSYS2) — POSIX 语法：`ls`、`grep`、`/c/Users/…`
- **包管理:** `uv`（Python）、`npm`（Node）
- **主目录:** C:\Users\你的用户名        # ← 改这里
- **Godot 引擎:** C:\Users\你的用户名\Projects\Godot\engine\Godot_v4.版本号_stable_win64.exe  # ← 改这里
- **Python（图像处理）:** C:\Users\你的用户名\AppData\Local\Programs\Python\Python311\python.exe  # ← 改这里
```

**快速查找路径：**

```bash
# 查找 Godot 引擎位置
ls /c/Users/$USERNAME/Projects/Godot/engine/Godot_v*-stable_win64.exe

# 查看 Python 安装位置
which python
python --version

# 检查 uv 是否安装
uv --version
```

> **💡 建议：** `你的用户名` 占位符出现 3 处，用编辑器的查找替换（Ctrl+H）一次性替换。

#### 第三步：验证是否生效

添加 AGENTS.md 后，开启新的 Hermes 会话，测试：

**测试 1：路径处理**

让 Hermes 创建测试文件：
> "在 C:\Users\你的用户名\Projects\hermes-test.txt 创建文件，内容写 'hello'"

如果 AGENTS.md 生效，文件精确创建在指定路径。如果没生效，可能会出现在 `C:\c\Users\你的用户名\…`（幽灵目录问题）。

**测试 2：语言一致性**

发中文消息，Hermes 应该回中文。发英文则切换英文。

**测试 3：工具选择**

让 Hermes 安装 Python 包，应该用 `uv pip install`，而不是 `pip install` 或 `python3 -m pip install`。

**测试 4：验证循环**

给 Hermes 一个多步骤任务，应该遵循：
```
1. [步骤] → 验证: [检查]
2. [步骤] → 验证: [检查]
```

不跳过验证步骤，不声称"完成"却没实际运行。

**测试 5：无幽灵目录**

```bash
# 检查幽灵目录
ls /c/c/ 2>/dev/null && echo "❌ 发现幽灵目录" || echo "✅ 干净"
```

如果文件操作后出现 `C:\c\`，说明 AGENTS.md 未加载。检查：
1. 文件名是精确的 `AGENTS.md`（大小写敏感）
2. 放在项目根目录
3. 添加后开启了**新**的 Hermes 会话（CLI 用 `/reset`，或重启应用）

#### 第四步：添加到所有项目

一个项目验证成功后，添加到其他项目：

```bash
# 复制定制版到其他项目
cp /c/Users/$USERNAME/Projects/project1/AGENTS.md /c/Users/$USERNAME/Projects/project2/AGENTS.md
```

或者在主目录创建模板，用于新项目：

```bash
# 保存干净模板
cp /c/Users/$USERNAME/Projects/project1/AGENTS.md /c/Users/$USERNAME/AGENTS.template.md

# 用于新项目
cp /c/Users/$USERNAME/AGENTS.template.md /c/Users/$USERNAME/Projects/new-project/AGENTS.md
```

### 你能得到什么

AGENTS.md 生效后，Hermes Agent 自动获得以下能力：

| 功能 | 实现方式 |
|------|---------|
| **正确路径** | 文件工具用 Windows 原生路径（`C:\Users\…`）；终端命令用 MSYS 路径（`/c/Users/…`） |
| **正确工具** | Python 用 `uv`，Node 用 `npm`，Godot 用完整路径 — 不再猜测 |
| **技能自动加载** | 图像任务 → 加载 `comfyui`；游戏任务 → 加载 `ai-assisted-game-dev`；npm → 加载 `npm-publish-flow` |
| **语言稳定** | 默认中文；用户写英文才切换英文 |
| **外科手术式编辑** | 只改需要改的代码；不顺手重构 |
| **真实验证** | 实际运行代码并报告结果；不编造输出 |
| **诚实阻塞** | 遇到错误和阻塞直接报告，不捏造结果 |
| **记忆规范** | 偏好存 memory，流程存 skill，不存任务进度 |

### 工作原理

Hermes Agent 遵循指令层级：

```
系统提示（内置）
  ├── Karpathy 四原则（先亮假设、简洁优先、外科手术、完成工作）
  ├── 工具使用强制规则
  └── Agent 角色定义
      │
      ▼
AGENTS.md（你的项目）
  ├── 环境：OS、终端、路径、工具链
  ├── 路径规则：文件工具 vs 终端
  ├── 沟通：语言、语气、风格
  ├── 技能：什么任务加载什么技能
  ├── 记忆：存什么、不存什么
  └── 验证：步骤和检查点
      │
      ▼
会话记忆（累积）
  ├── 用户偏好
  ├── 历史纠正
  └── 环境细节
```

AGENTS.md 位于系统提示和会话记忆之间 — 提供稳定、项目特定的上下文，不随会话变化。

### 生效的标志

以下迹象表明 AGENTS.md 已生效：

- ✅ 文件操作不再产生 `C:\c\` 幽灵目录
- ✅ 不再出现 `python3: command not found`
- ✅ npm publish 一次通过（不再 2FA 死循环）
- ✅ 生图/设计任务自动加载相关技能
- ✅ 沟通语言保持稳定（默认中文）
- ✅ 代码修改精准 — 只触及目标文件
- ✅ 每步都包含验证检查点
- ✅ Hermes 阻塞时诚实报告，不捏造结果

### 项目结构

```
hermes-agent-guide/
├── 📄 README.md          ← 你正在读的文件（中英双语）
├── 📄 AGENTS.md           ← 核心指令文件（放入你的项目）
├── 📄 EXAMPLES.md         ← 8 个实战案例，含 ❌/✅ 对比
└── 📄 LICENSE             ← MIT — 自由使用，自由修改
```

### 常见问题

<details>
<summary><b>Q：添加 AGENTS.md 后需要重启 Hermes 吗？</b></summary>

需要。开启新会话（CLI 用 `/reset`，或关闭重开应用）。AGENTS.md 在会话启动时读取，不在会话中途热加载。
</details>

<details>
<summary><b>Q：可以有项目特定规则和全局规则吗？</b></summary>

可以。把项目特定规则放在项目根目录的 `AGENTS.md`。Hermes 会在打开该目录时自动读取。全局规则用 Hermes 内置的记忆系统（`memory` 工具）。
</details>

<details>
<summary><b>Q：团队用不同操作系统怎么办？</b></summary>

AGENTS.md 支持多环境。添加独立的环境块：

```yaml
## Environment / 环境

### Windows (小明)
- OS: Windows 11, Shell: git-bash (MSYS2), Home: C:\Users\xiaoming

### macOS (小红)
- OS: macOS 14, Shell: zsh, Home: /Users/xiaohong
```

Hermes 会匹配运行中的操作系统，用正确的配置。
</details>

<details>
<summary><b>Q：AGENTS.md 会和 Hermes 内置提示冲突吗？</b></summary>

不会。AGENTS.md 是补充，不替代系统提示。Hermes 读取两者并合并 — 你的 AGENTS.md 规则添加了通用系统提示无法提供的 Windows 特定上下文。
</details>

<details>
<summary><b>Q：幽灵目录 C:\c\ 即使有 AGENTS.md 还是出现？</b></summary>

三个常见原因：

1. **AGENTS.md 未加载** — Hermes 需要会话重启（`/reset`）才能识别
2. **AGENTS.md 位置不对** — 必须在项目根目录
3. **文件工具用了 MSYS 路径** — `write_file("/c/Users/…")` 会创建幽灵目录，必须用 `write_file("C:\Users\…")`

清理幽灵目录：
```bash
# 在 MSYS2 / git-bash 中
cmd.exe //c "rmdir /s /q C:\\c"
```
</details>

### 参考资源

- [EXAMPLES.md](./EXAMPLES.md) — 8 个实战案例，含 ❌/✅ 对比
- [Hermes Agent 官方文档](https://hermes-agent.nousresearch.com/docs/) — 完整 Hermes 文档
- [Hermes Agent GitHub](https://github.com/NousResearch/hermes-agent) — 源码和问题反馈
- [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills) — 灵感来源

### 贡献

发现 bug？有新的模式想分享？欢迎 PR！

1. Fork 仓库
2. 把你的模式加到 `EXAMPLES.md`
3. 提交 PR，附上清晰的问题和解决方案描述

或者直接提 issue 描述你的场景 — 我来加。

### 许可证

MIT — 随意使用、修改、分享。署名感谢，不署名也没关系。

---

<p align="center">
  <sub>Made with ❤️ by <a href="https://github.com/yushizehuohuo-gif">HuoHuoOvO</a> · Battle-tested on Windows · 实战验证</sub>
</p>
