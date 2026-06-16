# AGENTS.md · Hermes Agent 增强指令

为 **Hermes Agent** 优化的项目指令。追加到你的项目 AGENTS.md 末尾。

**适用**: Windows 10/11 · Hermes Desktop · 中英双语用户 · Godot/ComfyUI

> Append to your project's AGENTS.md. Optimized for Windows + Hermes Desktop.

---

## 环境声明 / Environment

```yaml
## 环境
- OS: Windows 10/11
- Shell: git-bash (MSYS2) — POSIX syntax (ls, grep, /c/...)
- Package: uv (Python), npm (Node)
- Godot: C:\Users\<user>\Projects\Godot\engine\Godot_v*_win64.exe
- Home: C:\Users\<username>
- Python (image): C:\Users\<user>\AppData\Local\Programs\Python\Python311\python.exe
```

---

## 路径规则 / Path Rules 🔴

**文件工具 (read_file, write_file, patch, search_files) 必须用 Windows 原生路径。**

> File tools MUST use Windows native paths. MSYS paths will create ghost directories.

```bash
# ✅ CORRECT — Windows native
write_file("C:\\Users\\yushi\\Projects\\my-game\\main.gd", "...")

# ❌ WRONG — MSYS path creates C:\c\ shadow directory
write_file("/c/Users/yushi/Projects/my-game/main.gd", "...")
```

**terminal 工具内部可以用 MSYS 路径**（shell 自己会转换）。

> Inside terminal(), MSYS paths are safe — the shell handles translation.

```bash
terminal("ls /c/Users/yushi/Projects/")  # ✅ OK
```

---

## 工具链 / Toolchain

```bash
# Python — use uv, NOT python3
uv pip install <package>
uv run python <script>

# Image processing (has rembg/scipy)
C:\Users\<user>\AppData\Local\Programs\Python\Python311\python.exe

# Node
npm install && npx <package>

# Godot 4
C:\Users\<user>\Projects\Godot\engine\Godot_v4.*-stable_win64.exe
```

---

## 沟通 / Communication

- **默认中文**，用户用英文则回英文
- 简洁直接，不寒暄
- 不确定就问，不猜测
- 用户的方案不好时直接说 — push back when warranted

> Default to Chinese. If user writes in English, reply in English. Be concise, ask when uncertain.

---

## 技能系统 / Skills

遇到以下场景，先 `skill_view()` 加载技能，不要裸调：

> Load skills before working — don't raw-call tools:

```
生图 → comfyui / image-compositing / image_gen
游戏 → ai-assisted-game-dev / godot-web-deploy
GitHub → github-pr-workflow / github-auth
海报 → baoyu-infographic / image-compositing
网页 → web-project-design
B站/抖音 → agency-bilibili-strategist / agency-douyin-strategist
Windows → windows-system-diagnostics
npm → npm-publish-flow
```

---

## 记忆系统 / Memory

| 存什么 / What to save | 工具 / Tool |
|------------------------|-------------|
| 用户偏好、环境配置 | `memory` |
| 工作流程、经验 | `skill_manage(action='create')` |
| 任务进度、TODO | ❌ 不存 → 用 `session_search` |

> Save preferences to memory, workflows to skills. Don't save task progress — search past sessions instead.

---

## 验证循环 / Verification Loop

```
1. [步骤] → 验证: [检查点]
2. [步骤] → 验证: [检查点]
3. [步骤] → 验证: [检查点]
```

每步验证通过再继续，不跳步。> Verify each step before moving on.

---

## 完成标准 / Done Means

- ✅ 可工作的结果，不是方案描述 → working artifact, not a plan
- ✅ 代码实际运行过，不是只写出来 → code was actually executed
- ✅ 报告真实输出，不编造数据 → report real output, never fabricate
- ✅ 阻塞就报告阻塞 → report blockers honestly

---

## 常见陷阱 / Common Pitfalls

1. ❌ `python3` → ✅ `python` or `uv run python`
2. ❌ PowerShell commands → ✅ bash: `ls`, `grep`, `sed`
3. ❌ 交付到桌面 → ✅ 放项目目录
4. ❌ npm publish 死循环 → ✅ PTY + desktop browser for 2FA
5. ❌ 生图 `image_gen.provider` 随便填 → ✅ 留空
