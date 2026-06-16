# Hermes Agent 实战案例 / Real-World Examples

每个案例展示 ❌ 常见错误 vs ✅ 正确做法。

Each example shows what Hermes commonly does wrong and how to fix it.

---

## 1. 路径陷阱 / The Path Trap

**用户**: "帮我把 `config.json` 里的 `foo` 改成 `bar`"

### ❌ Wrong (MSYS path in file tool)

```bash
patch("/c/Users/yushi/project/config.json", "foo", "bar")
# → Creates C:\c\Users\yushi\project\config.json
# → Original file unchanged. User sees old content.
```

### ✅ Correct

```bash
# File tools → Windows native path
patch("C:\\Users\\yushi\\project\\config.json", "foo", "bar")

# Terminal → MSYS path is fine
terminal("sed -i 's/foo/bar/g' /c/Users/yushi/project/config.json")
```

> **Rule**: File tools = Windows paths. Terminal = MSYS paths OK.

---

## 2. 工具链混淆 / Toolchain Confusion

**用户**: "帮我装个 Python 图片处理包"

### ❌ Wrong

```bash
pip install pillow      # may install to wrong Python
python3 -c "..."         # python3 doesn't exist on this machine
```

### ✅ Correct

```bash
python --version         # check first
uv pip install pillow    # use uv

# For image processing (specific Python with rembg/scipy):
C:\Users\yushi\AppData\Local\Programs\Python\Python311\python.exe -c "..."
```

> **Rule**: `python3` is fake on Windows. Use `python` or `uv`.

---

## 3. 跳过技能系统 / Skipping Skills

**用户**: "帮我做一张宣传海报"

### ❌ Wrong

```
image_generate(prompt="...") → meh result
manual post-processing → slow and error-prone
```

### ✅ Correct

```bash
1. skill_view("image-compositing")  # Load the poster skill
2. Follow skill's step-by-step pipeline
3. Verify each step before continuing
# Result: polished poster in 3 minutes instead of 30
```

> **Rule**: Hermes has 50+ skills. Loading one takes 2 seconds, saves 20 minutes.

---

## 4. npm 2FA 死循环 / The npm 2FA Loop

**用户**: "帮我把这个包发到 npm"

### ❌ Wrong (5 attempts, all fail)

```bash
npm publish → EOTP error
npm login --auth-type=web → timeout
npm publish → EOTP error
# ... loops forever
```

### ✅ Correct (one shot)

```
1. Start npm login in PTY mode
2. Capture auth URL from output: https://www.npmjs.com/auth/cli/<uuid>
3. Open in desktop browser: cmd.exe //c start "" "URL"
4. User completes 2FA in Chrome authenticator extension
5. Wait for flow to complete
6. npm publish --access public → SUCCESS
```

> **Rule**: npm redacts auth URLs to `***` in logs. Use PTY mode to capture live output.

---

## 5. 游戏开发外科手术 / Surgical Game Dev

**用户**: "给我的 FPS 卡牌游戏加抽卡动画"

### ❌ Wrong (200 lines, 15 files)

```gdscript
# Creates: AnimationSystem, TweenManager, StateMachine, EventBus...
# Modifies: 15 files including unrelated systems
# Result: Game crashes, scope creep
```

### ✅ Correct (1 scene + 1 script)

```gdscript
1. search_files("AnimationPlayer") → find existing pattern
2. Add new animation to existing AnimationPlayer in .tscn
3. Bind trigger in .gd script
4. Test in scene
# Files changed: 2. No unrelated code touched.
```

> **Rule**: In Godot projects, changes should be scene-scoped, not engine-wide.

---

## 6. 中英混合 / Language Drift

**用户（中文）**: "帮我写个 README"

### ❌ Wrong

```
Hermes writes in English → User: "要中文"
Hermes rewrites in Chinese → But code is in Chinese too
User: "代码部分用英文" → back and forth...
```

### ✅ Correct

AGENTS.md 声明后，行为稳定：

```markdown
## 沟通 / Communication
- 默认中文，用户用英文则回英文
- 代码、API 名、命令保持原样不翻译
- npm 包名、GitHub 项目名不翻译
```

> **Rule**: Declare language preferences once in AGENTS.md. Never correct again.

---

## 反模式速查 / Anti-Patterns Quick Ref

| 场景 / Scenario | ❌ 反模式 | ✅ 正确 |
|-----------------|----------|---------|
| 文件操作 | MSYS 路径 | Windows 原生路径 |
| Python | `python3` / `pip` | `python` / `uv` |
| 生图/设计 | 裸调工具 | 先加载技能 |
| npm 发布 | 循环重试 | PTY + 桌面浏览器 |
| 游戏开发 | 大范围改动 | 场景文件级修改 |
| 代码修改 | 顺带重构 | 只动目标代码 |
| 工具选择 | 凭记忆 | 查 skills + memory |
| 任务完成 | 只给方案 | 实际运行验证 |
