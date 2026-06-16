# AGENTS.md · Hermes Agent Enhanced Instructions

**For:** Hermes Agent on Windows 10/11 · Chinese-English bilingual users · Godot / ComfyUI / npm workflows

**为：** Windows 10/11 上的 Hermes Agent · 中英双语用户 · Godot / ComfyUI / npm 工作流

> This file lives in your project root. Hermes reads it automatically at every session start.
> Append it to your existing AGENTS.md or use it standalone.

> 此文件放在项目根目录。Hermes 每个会话启动时自动读取。
> 可以追加到已有的 AGENTS.md 或独立使用。

---

## Environment / 环境

Declare your machine's configuration here. Hermes uses this instead of guessing.

在这里声明你的机器配置。Hermes 以此为准，不再猜测。

```yaml
## Environment / 环境

- **OS:** Windows 10/11
- **Shell:** git-bash (MSYS2) — uses POSIX syntax: `ls`, `grep`, `sed`, `/c/Users/…`
- **Package managers:** `uv` (Python), `npm` (Node.js)
- **Home directory:** C:\Users\<YOUR_USERNAME>
- **Godot engine:** C:\Users\<YOUR_USERNAME>\Projects\Godot\engine\Godot_v4.*-stable_win64.exe
- **Python (image processing, has rembg/scipy):** C:\Users\<YOUR_USERNAME>\AppData\Local\Programs\Python\Python311\python.exe
- **Python (general, no rembg):** use `python` or `uv run python` — NOT `python3`
```

**Note:** `python3` does NOT exist on this Windows machine. The `python3` command is a macOS/Linux convention. Always use `python` or `uv run python`.

**注意：** `python3` 在这台 Windows 机器上**不存在**。`python3` 是 macOS/Linux 的惯例。始终使用 `python` 或 `uv run python`。

---

## Path Rules / 路径规则 🔴

This is the **most important rule** in this file. Violating it causes silent file loss.

这是本文件**最重要的规则**。违反它会导致文件操作静默失败。

### Rule / 规则

| Tool | Path Format | Example |
|------|------------|---------|
| `read_file` | Windows native | `read_file("C:\\Users\\yushi\\Projects\\game\\main.gd")` |
| `write_file` | Windows native | `write_file("C:\\Users\\yushi\\Projects\\game\\main.gd", ...)` |
| `patch` | Windows native | `patch("C:\\Users\\yushi\\Projects\\game\\main.gd", ...)` |
| `search_files` | Windows native | `search_files(pattern="...", path="C:\\Users\\yushi\\Projects")` |
| `terminal` | MSYS (either) | `terminal("ls /c/Users/yushi/Projects/")` — MSYS is safe here |

### Why / 为什么

MSYS2 paths (`/c/Users/...`) passed to non-MSYS tools (file operations) get mistranslated and create a shadow directory at `C:\c\Users\...`. The original file is never touched. This is invisible until you notice your changes aren't there.

MSYS2 路径（`/c/Users/...`）传给非 MSYS 工具（文件操作）时会被错误转换，在 `C:\c\Users\...` 创建影子目录。原文件纹丝不动。直到你发现修改没生效才会察觉。

### Examples / 示例

```bash
# ✅ CORRECT — File tools: Windows native path
read_file("C:\\Users\\yushi\\Projects\\my-game\\scenes\\main.tscn")
write_file("C:\\Users\\yushi\\Projects\\my-game\\scripts\\player.gd", "extends CharacterBody2D\n")

# ✅ CORRECT — Terminal: MSYS path is fine (shell handles conversion)
terminal("cd /c/Users/yushi/Projects/my-game && git status")

# ❌ WRONG — File tool with MSYS path → creates C:\c\ ghost directory
read_file("/c/Users/yushi/Projects/my-game/main.gd")     # Reads from C:\c\Users\... instead!
write_file("/c/Users/yushi/Projects/my-game/main.gd", ...) # Writes to C:\c\Users\... !!!
```

### Ghost Directory Cleanup / 幽灵目录清理

If you accidentally created the `C:\c\` ghost directory, clean it up:

```bash
# Check if it exists
ls /c/c/ 2>/dev/null && echo "Ghost directory found!" || echo "Clean"

# Remove it
cmd.exe //c "rmdir /s /q C:\\c"
```

---

## Toolchain Reference / 工具链速查

### Python

```bash
# Install packages — use uv, NOT pip
uv pip install <package>
uv pip install -r requirements.txt

# Run scripts
uv run python <script.py>          # General Python
python <script.py>                 # Also works if python is in PATH

# Image processing (this Python has rembg, scipy, Pillow)
C:\Users\<YOUR_USERNAME>\AppData\Local\Programs\Python\Python311\python.exe <script.py>

# Check what's installed
uv pip list
```

### Node.js / npm

```bash
# Install dependencies
npm install

# Run scripts
npx <package>

# npm publish — use npm-publish-flow skill for 2FA
# IMPORTANT: npm publish requires desktop browser 2FA.
# Use PTY mode + cmd.exe //c start "" "URL" for auth.
# Never loop-retry npm publish — it will always fail without browser auth.
```

### Godot Engine

```bash
# Run a Godot project
C:\Users\<YOUR_USERNAME>\Projects\Godot\engine\Godot_v4.*-stable_win64.exe --path C:\Users\<YOUR_USERNAME>\Projects\<project> &

# Run a specific scene
C:\Users\<YOUR_USERNAME>\Projects\Godot\engine\Godot_v4.*-stable_win64.exe C:\Users\<YOUR_USERNAME>\Projects\<project>\scenes\main.tscn &

# Export to HTML5 (use godot-web-deploy skill)
```

### Git / GitHub

```bash
# Use gh CLI for GitHub operations (gh pr create, gh issue list, etc.)
# Auth: HTTPS token configured

# Common operations
git status
git add <files>
git commit -m "message"
git push origin main
```

---

## Communication Rules / 沟通规则

### Language / 语言

- **Default: Chinese (中文)** — All explanations, descriptions, and conversation in Chinese
- **Switch to English** ONLY when the user writes in English
- **Code, commands, API names, npm package names, GitHub repo names** — always in English, never translate

### When to Use Chinese vs English

| Content Type | Language |
|-------------|----------|
| Conversation, explanations, descriptions | Chinese (default) |
| Code, commands, API names | English (always) |
| File names, package names, repo names | English (always) |
| Error messages, logs | Original language (don't translate) |
| User writes in English | Reply in English |

### Style / 风格

- **Concise** — Substance over filler. No greetings, no "sure!", no "let me help you with that"
- **Direct** — Push back when something is a bad idea. Say "this won't work because..." 
- **Honest** — Admit uncertainty. Say "I'm not sure about X, let me check" instead of guessing
- **Action-oriented** — Use tools to take action, don't end with a promise to act

### Anti-patterns to Avoid / 避免的反模式

- ❌ "当然可以！让我来帮你..." → ✅ Just do it
- ❌ "这是一个很好的问题！" → ✅ Answer directly
- ❌ 把代码里的变量名翻译成中文 → ✅ Code stays in English
- ❌ 不确定的事情猜测 → ✅ 说"我不确定，让我查一下"

---

## Skills System / 技能系统

Hermes has 50+ specialized skills. **Always load the relevant skill before working on a task** — it takes 2 seconds and saves 20 minutes of trial and error.

Hermes 有 50+ 专项技能。**处理任务前先加载相关技能** — 花 2 秒加载，省 20 分钟试错。

### Skill-to-Task Mapping / 技能-任务对照表

| When user asks for... / 用户要求... | Load this skill first / 先加载 |
|-------------------------------------|-------------------------------|
| 🎨 Generate images, AI art | `comfyui` — full pipeline |
| 🖼️ Create posters, banners, composites | `image-compositing` — layer and compose |
| 🎨 Design HTML artifacts, landing pages | `claude-design` — one-off HTML |
| 🎮 Godot game development | `ai-assisted-game-dev` — Godot workflows |
| 🌐 Deploy Godot to web | `godot-web-deploy` — HTML5 export + GitHub Pages |
| 📝 Write game design docs | `agency-game-designer` — GDD authoring |
| 🗺️ Design levels | `agency-level-designer` — layout and pacing |
| 🔀 GitHub PRs, code review | `github-pr-workflow`, `github-code-review` |
| 🐛 Debug systematically | `systematic-debugging` — 4-phase root cause |
| 🧪 Test-driven development | `test-driven-development` — RED-GREEN-REFACTOR |
| 📦 Publish to npm | `npm-publish-flow` — 2FA browser auth |
| 🌐 Build web projects | `web-project-design` — Next.js, editorial |
| 📊 Create infographics | `baoyu-infographic` — 21 layouts × 21 styles |
| 🎬 Bilibili / Douyin content | `agency-bilibili-strategist` / `agency-douyin-strategist` |
| 🖥️ Windows system diagnostics | `windows-system-diagnostics` — disk, memory, processes |
| 📋 Plan before building | `plan` — actionable plan in `.hermes/plans/` |
| 🧪 Spike / experiment | `spike` — throwaway validation |
| 🎨 ASCII art | `ascii-art` — pyfiglet, cowsay |
| 🔍 Web scraping (anti-bot) | `scrapling` — Cloudflare bypass |

### How to Load Skills / 如何加载技能

```bash
# In conversation: use skill_view
skill_view(name="comfyui")

# At CLI startup: preload with -s flag
hermes -s comfyui -s ai-assisted-game-dev

# In AGENTS.md: list frequently-used skills here for auto-load
```

---

## Memory System / 记忆系统

Hermes has persistent memory across sessions. Use it correctly.

Hermes 有跨会话持久记忆。正确使用它。

### What to Save (and Where) / 存什么（存哪里）

| Save this / 存这个 | Use / 用 | Because / 因为 |
|-------------------|---------|---------------|
| User preferences / 用户偏好 | `memory` | Prevents re-correcting every session |
| Environment details / 环境细节 | `memory` | Saves 5 minutes of env discovery per session |
| Recurring corrections / 反复纠正 | `memory` | Stops the same mistake from repeating |
| Proven workflows / 验证过的工作流 | `skill_manage(action='create')` | Reusable across projects |
| Bug fixes learned / 学到的修复经验 | `skill_manage(action='create')` | Next time it happens, you're ready |

### What NOT to Save / 不要存

| Don't save this / 别存 | Use instead / 改用 | Because / 因为 |
|------------------------|-------------------|---------------|
| Task progress / 任务进度 | Nothing — it's session-scoped | Stale in 2 hours |
| TODO items / 待办事项 | Nothing — use `todo` tool | Stale in 1 session |
| "Completed X today" / "今天完成了X" | Nothing | Stale instantly |
| PR numbers, commit SHAs / PR号、提交哈希 | Nothing | Stale within days |
| File lists, counts / 文件列表、数量 | Nothing | Changes every commit |

### Memory Examples / 记忆示例

```
✅ GOOD memory entries:
  "User prefers concise Chinese responses"
  "Project uses pytest with -n 0 on Windows"
  "npm account: huohuoovo, email: yushizehuohuo@gmail.com, 2FA: auth-and-writes"

❌ BAD memory entries:
  "Fixed bug in player.gd — added gravity damping"
  "PR #42 merged — feature complete"
  "Session 2026-06-16: rewrote README"
```

---

## Verification Loop / 验证循环

Every multi-step task must follow this pattern. No skipping verification.

每个多步骤任务必须遵循此模式。不跳过验证。

### Template / 模板

```
1. [Action step] → verify: [concrete check]
2. [Action step] → verify: [concrete check]
3. [Action step] → verify: [concrete check]
```

### Example: Adding a feature to a Godot game

```
1. Read existing player script → verify: understand current state
2. Add new function → verify: no syntax errors (check with Godot --check-only)
3. Test in scene → verify: function executes without errors in Godot output
```

### Example: npm publish

```
1. Bump version in package.json → verify: version updated in file
2. npm login in PTY mode → verify: captured auth URL from PTY output
3. Open browser: cmd.exe //c start "" "URL" → verify: user confirms 2FA complete
4. npm publish --access public → verify: package appears on npmjs.com
```

### Done Means / 完成的标准

- ✅ **Working artifact**, not a plan or description — 可工作的产物，不是方案描述
- ✅ **Code was actually executed**, not just written — 代码实际运行过，不是只写出来
- ✅ **Real output reported**, never fabricated — 报告真实输出，不编造数据
- ✅ **Blockers reported honestly** — 遇到阻塞就报告阻塞

---

## Surgical Changes / 外科手术式修改

When editing existing code, follow these constraints strictly.

编辑已有代码时，严格遵守以下约束。

### Rules / 规则

1. **Touch only what you must** — Don't "improve" adjacent code
2. **Clean up only your own mess** — If your change creates orphan imports/variables, remove them
3. **Match existing style** — Even if you'd do it differently
4. **Don't refactor** — It's not broken, don't fix it
5. **Mention dead code, don't delete it** — "I noticed unused X, want me to remove it?"

### Godot-Specific / Godot 特定

- Changes should be **scene-scoped**, not engine-wide
- One scene + one script = ideal scope for a feature
- Use existing AnimationPlayers, don't create new systems
- Search for existing patterns before writing new code

### Example: Adding a card draw animation

```
❌ WRONG: Create AnimationSystem.gd, TweenManager.gd, modify 15 files
✅ RIGHT:
  1. search_files("AnimationPlayer") → find existing pattern
  2. Add animation to existing AnimationPlayer in .tscn
  3. Bind trigger in the existing .gd script
  4. Test in scene
  → Files changed: 2. Nothing else touched.
```

---

## npm 2FA Publishing / npm 双因素认证发布

This is a special workflow that requires precise steps. The npm CLI redacts auth URLs to `***` in normal terminal output — you MUST use PTY mode.

这是一个需要精确步骤的特殊工作流。npm CLI 会在普通终端输出中将认证 URL 遮蔽为 `***` — 必须用 PTY 模式。

### Correct Workflow / 正确流程

```
1. Start npm login in PTY mode:
   terminal("npm login", pty=true, timeout=60)

2. Watch PTY output for the auth URL:
   https://www.npmjs.com/auth/cli/<uuid>

3. Open the URL in the desktop browser:
   cmd.exe //c start "" "https://www.npmjs.com/auth/cli/<uuid>"

4. User completes 2FA in Chrome Authenticator extension

5. Wait for login to complete in PTY session

6. Publish:
   terminal("npm publish --access public", timeout=30)
```

### Common Mistakes / 常见错误

- ❌ `terminal("npm login")` without `pty=true` → Auth URL is redacted, you see `***`
- ❌ Looping `npm publish` on EOTP error → Will always fail without browser auth
- ❌ Using `npm login --auth-type=legacy` → Deprecated, won't work with 2FA

---

## Delivery Rules / 交付规则

- **Don't deliver files to the Desktop** — Put project-related files in the project directory
- **Temporary/report files** → Put in `C:\Users\<YOUR_USERNAME>\Projects\` or user-specified location
- **Don't clutter the home directory** — Everything organized by project

不要将交付文件放在桌面上。项目相关文件放到对应项目目录。临时/报告类文件放到 Projects 下或用户指定的位置。

---

## Common Pitfalls / 常见陷阱

### 1. `python3` is fake on Windows

```bash
❌ python3 --version          # command not found
❌ python3 -m pip install ... # command not found
✅ python --version           # Python 3.11.9
✅ uv run python script.py    # Recommended
```

### 2. PowerShell commands don't work

```bash
❌ Get-ChildItem              # Not in bash
❌ Select-String "pattern"    # Not in bash
❌ $env:USERPROFILE           # PowerShell syntax
✅ ls                          # bash
✅ grep "pattern" file         # bash
✅ $USERPROFILE                # bash
```

### 3. Image generation: leave provider empty

```bash
# image_gen.provider must be empty/null — the tool auto-detects
❌ image_generate(prompt="...", provider="openai")
✅ image_generate(prompt="...", aspect_ratio="portrait")
```

### 4. Don't reinvent skills

```bash
❌ Raw image_generate() call → manual processing → 30 minutes
✅ skill_view("image-compositing") → follow pipeline → 3 minutes
```

### 5. Gateway: iLink has daily quota limits

```bash
# WeChat iLink Bot has a daily message quota
# Batch non-urgent messages, prioritize critical ones
```

### 6. Network: OpenRouter is the proxy

```bash
# OpenRouter proxies all API calls (余额 $8.04)
# Primary model: gemini-2.5-flash
# Bypasses GFW for Claude/GPT access
```

---

## Quick Reference Card / 速查卡

| Situation | Do This |
|-----------|---------|
| File operation | Windows path: `C:\Users\...` |
| Terminal command | MSYS path OK: `/c/Users/...` |
| Python install | `uv pip install <pkg>` |
| Python run | `uv run python <script>` or `python <script>` |
| Image processing | `C:\Users\...\Python311\python.exe` |
| Godot run | Full path to Godot_v4.*-stable_win64.exe |
| npm publish | PTY mode + desktop browser for 2FA |
| Image generation | Load `comfyui` skill first, provider=empty |
| Language | Chinese by default, English if user writes English |
| Memory | Preferences → memory, workflows → skills, progress → don't save |
| Verification | Every step must have a concrete check |
| Editing code | Touch only target, match style, don't refactor |
| Blockers | Report honestly, don't fabricate |

---

## Configuration Template / 配置模板

Copy this and fill in your details. Replace every `<...>` placeholder.

复制这段并填入你的信息。替换每个 `<...>` 占位符。

```yaml
## My Environment / 我的环境

- OS: Windows <10 or 11>
- Shell: git-bash (MSYS2)
- Home: C:\Users\<your-username>
- Godot: C:\Users\<your-username>\Projects\Godot\engine\Godot_v<version>-stable_win64.exe
- Python (image): C:\Users\<your-username>\AppData\Local\Programs\Python\Python311\python.exe
- Node: npm (with 2FA, auth-and-writes)
- GitHub: <your-github-username>
- npm: <your-npm-username>

## My Preferences / 我的偏好

- Language: Chinese by default, English on request
- Style: Concise, direct, no filler
- Delivery: Project files in project dir, not Desktop
- Tools: uv for Python, npm for Node, full Godot path for engine
```

---

<p align="center">
  <sub>End of AGENTS.md · If you found this useful, ⭐ <a href="https://github.com/yushizehuohuo-gif/hermes-agent-guide">the repo</a></sub>
</p>
