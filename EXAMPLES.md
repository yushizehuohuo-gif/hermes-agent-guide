# Hermes Agent Real-World Examples · 实战案例

Each example shows what Hermes commonly does wrong (❌) and how AGENTS.md fixes it (✅).
Use these as a reference when building your own AGENTS.md or debugging Hermes behavior.

每个案例展示 Hermes 常见的错误做法（❌）以及 AGENTS.md 如何修正（✅）。
在构建你自己的 AGENTS.md 或调试 Hermes 行为时用作参考。

---

## 1. The Path Trap / 路径陷阱

**User / 用户:** "Help me change `foo` to `bar` in `config.json`"
"帮我把 `config.json` 里的 `foo` 改成 `bar`"

**Context:** File is at `C:\Users\yushi\Projects\my-game\config.json`

### ❌ Wrong (MSYS path in file tool)

```python
# Hermes uses MSYS path with a file tool
patch("/c/Users/yushi/Projects/my-game/config.json", "foo", "bar")

# What actually happens:
# → Creates C:\c\Users\yushi\Projects\my-game\config.json (ghost directory!)
# → Original file at C:\Users\yushi\Projects\my-game\config.json is UNCHANGED
# → User sees old content and thinks Hermes failed
# → Hermes thinks it succeeded (no error from file write)
```

**Why this happens:** MSYS2 translates `/c/Users/...` to `C:\Users\...` for MSYS-native programs, but Hermes's file tools run as native Windows processes. They receive the path literally and create the directory structure under `C:\c\`.

### ✅ Correct

```python
# File tools MUST use Windows native paths
patch("C:\\Users\\yushi\\Projects\\my-game\\config.json", "foo", "bar")

# Terminal can use MSYS paths — the shell handles conversion
terminal("sed -i 's/foo/bar/g' /c/Users/yushi/Projects/my-game/config.json")
```

**Key takeaway / 要点:**
- `read_file`, `write_file`, `patch`, `search_files` → Windows native paths ONLY
- `terminal(...)` → MSYS paths are safe
- After any file operation, verify with `read_file` to confirm the change landed

---

## 2. Toolchain Confusion / 工具链混乱

**User / 用户:** "Install a Python package for image processing"
"帮我装个 Python 图片处理包"

### ❌ Wrong

```bash
# Hermes guesses the wrong tool
pip install pillow          # May install to wrong Python environment
python3 -c "print('hello')"  # python3 doesn't exist on Windows!
python3 -m pip install ...  # Same problem
```

**Why this happens:** Hermes's system prompt doesn't know your specific Python setup. It defaults to `python3` (macOS/Linux convention) and `pip` (may point to the wrong Python).

### ✅ Correct

```bash
# 1. Check what's available first
python --version              # → Python 3.11.9
uv --version                  # → uv is installed

# 2. Use uv for package management
uv pip install pillow         # Installs to the right environment
uv pip install rembg scipy    # For image processing deps

# 3. For the image-processing Python (has rembg/scipy pre-installed):
C:\\Users\\yushi\\AppData\\Local\\Programs\\Python\\Python311\\python.exe -c "
from rembg import remove
from PIL import Image
# ... image processing code ...
"

# 4. For general Python:
uv run python my_script.py
```

**Key takeaway / 要点:**
- `python3` is fake on Windows → use `python` or `uv run python`
- `uv pip install` is preferred over `pip install`
- Image processing has a dedicated Python with rembg/scipy
- Always check `python --version` before installing packages

---

## 3. Skipping the Skills System / 跳过技能系统

**User / 用户:** "Make me a promotional poster for my game"
"帮我做一张游戏宣传海报"

### ❌ Wrong (Raw tool calls)

```python
# Hermes tries to do everything manually
image_generate(prompt="game poster, futuristic style...")
# → Returns a raw 1024x1024 image, wrong aspect ratio

# Then tries to process manually
terminal("python -c 'from PIL import Image; ...'")  # Wrong Python!
# → Error: PIL not found in this Python

# Tries again with different approach
image_generate(prompt="...with text overlay...")
# → AI can't reliably generate text

# 30 minutes later...
# Result: mediocre image, wrong size, no text, user frustrated
```

**Why this happens:** Hermes has 50+ specialized skills but doesn't know to load them. Each skill contains proven workflows, specific commands, and pitfall avoidance that raw tool calls can't match.

### ✅ Correct

```python
# 1. Load the skill first (2 seconds)
skill_view(name="image-compositing")

# 2. Follow the skill's pipeline:
#    Step 1: Generate background → verify: 16:9 portrait, correct style
#    Step 2: Generate character art → verify: transparent background
#    Step 3: Composite layers → verify: correct positioning
#    Step 4: Add text overlays → verify: readable, properly styled

# 3. Each step uses the right tool with the right parameters
#    Skill already knows: correct Python path, correct image_gen settings,
#    correct compositing commands, verified output sizes

# Result: polished poster in 3 minutes
```

**Key takeaway / 要点:**
- Loading a skill takes 2 seconds, saves 20+ minutes
- Skills encode proven workflows for specific task types
- Always check `skills_list()` before starting a complex task
- The skill knows edge cases you'd discover through trial and error

---

## 4. npm 2FA Death Loop / npm 双因素认证死循环

**User / 用户:** "Publish this package to npm"
"帮我把这个包发到 npm"

### ❌ Wrong (5 attempts, all fail)

```bash
# Attempt 1
terminal("npm publish")
# → npm ERR! code EOTP
# → This operation requires a one-time password

# Hermes tries to login
terminal("npm login")
# → Opens web auth, but output is redacted in normal terminal
# → Hermes sees: "Visit https://www.npmjs.com/auth/cli/*** to authenticate"
# → Can't extract the URL!

# Attempt 2
terminal("npm login --auth-type=legacy")
# → Legacy auth deprecated, doesn't work with 2FA

# Attempts 3-5
# → Same pattern, different variations
# → User sees: "Enter OTP:" but Hermes can't respond
# → Deadlock
```

**Why this happens:** npm CLI redacts the full auth URL to `***` in non-PTY terminal output. Hermes can't capture the URL to open in a browser. Legacy auth is deprecated and can't handle 2FA.

### ✅ Correct (One shot)

```bash
# 1. Start npm login in PTY mode to capture the full URL
terminal("npm login", pty=true, timeout=60)

# PTY output shows the actual URL:
# "Visit https://www.npmjs.com/auth/cli/a1b2c3d4-e5f6-7890-abcd-ef1234567890 to authenticate"

# 2. Extract the URL and open in desktop browser
cmd.exe //c start "" "https://www.npmjs.com/auth/cli/a1b2c3d4-e5f6-7890-abcd-ef1234567890"

# 3. User completes 2FA in Chrome Authenticator extension
#    (Hermes waits for the PTY session to complete)

# 4. Once login is confirmed:
terminal("npm publish --access public", timeout=30)
# → + package-name@1.0.0
# → Published successfully!
```

**Key takeaway / 要点:**
- npm auth URLs are redacted in normal terminal → use PTY mode
- PTY = pseudo-terminal, captures raw output before npm redacts it
- Desktop browser auth is the only way with 2FA `auth-and-writes`
- Don't loop retry — fix the approach instead

---

## 5. Surgical Game Dev / 外科手术式游戏开发

**User / 用户:** "Add a card draw animation to my FPS card game"
"给我的 FPS 卡牌游戏加抽卡动画"

### ❌ Wrong (200 lines, 15 files)

```gdscript
# Hermes creates a whole new system:
# File 1: scripts/systems/AnimationSystem.gd (80 lines)
#   - Animation queue manager
#   - Tween pool
#   - Event bus integration
# File 2: scripts/systems/TweenManager.gd (60 lines)
#   - Tween lifecycle management
#   - Easing presets
# File 3: scripts/systems/StateMachine.gd (40 lines)
#   - Animation state transitions
# File 4-15: Modifies player.gd, card.gd, battle.gd, ui.gd, main.tscn...
#   - Adds signal connections to 10+ files
#   - "While I'm here, let me refactor the inventory system..."

# Result: Game crashes on startup
# → "Invalid get index 'animation_player' (on base: 'null instance')"
# → 15 files changed, need to revert 13 of them
```

**Why this happens:** Hermes defaults to engineering best practices (abstraction, reusability, separation of concerns) but game development often prefers scene-scoped implementations. The existing code already has patterns — Hermes just needs to follow them.

### ✅ Correct (1 scene + 1 script)

```gdscript
# 1. Search for existing patterns
search_files("AnimationPlayer", path="C:\\Users\\yushi\\Projects\\fps-card-roguelike")

# → Found: scenes/card.tscn already has an AnimationPlayer node
# → Found: scripts/card.gd already calls $AnimationPlayer.play("flip")

# 2. Add the new animation to the existing AnimationPlayer in card.tscn
#    (Add "draw_card" animation: scale 0→1, 0.3s, ease-out)

# 3. Bind the trigger in card.gd:
func draw_from_deck() -> void:
    # ... existing setup code ...
    $AnimationPlayer.play("draw_card")    # ← Only new line
    await $AnimationPlayer.animation_finished

# 4. Test in scene
#    Command: Godot_v4.*-stable_win64.exe scenes/card_test.tscn
#    Output: "Animation 'draw_card' played successfully"

# Files changed: 2 (card.tscn, card.gd)
# Lines added: 3
# Nothing else touched.
```

**Key takeaway / 要点:**
- Search for existing patterns before writing new code
- In Godot, changes should be scene-scoped, not engine-wide
- Use existing nodes (AnimationPlayer, Timer) instead of creating new systems
- One feature ≈ one scene + one script in most cases

---

## 6. Language Drift / 语言漂移

**User / 用户 (Chinese):** "帮我写个 README"
**User expects:** README in Chinese, code in English

### ❌ Wrong

```
Turn 1:
  User: "帮我写个 README"
  Hermes: writes entire README in English
  User: "要中文！"

Turn 2:
  Hermes: rewrites README in Chinese, but code comments and variable names are also in Chinese
  User: "代码用英文！"

Turn 3:
  Hermes: fixes code to English, but switches conversation to English
  User: "说中文！"

Turn 4:
  Hermes: switches back to Chinese, but translates npm package names
  User: "npm 包名不要翻译！"

... 15 minutes of corrections before any real work gets done
```

**Why this happens:** Without explicit language rules, Hermes defaults to mixing languages based on context. It doesn't know that code, package names, and API references should stay in English regardless of the conversation language.

### ✅ Correct (With AGENTS.md rules)

```markdown
# AGENTS.md includes:
## Communication / 沟通
- Default: Chinese (中文)
- Switch to English ONLY when user writes in English
- Code, commands, API names, npm packages, GitHub repos → always English
- Never translate: file names, package names, variable names, function names
```

After these rules are in AGENTS.md:

```
User: "帮我写个 README"
Hermes: writes README.md with:
  - Chinese descriptions and explanations
  - English code blocks, npm commands, package names
  - No language confusion
  - Done in one turn, zero corrections
```

**Key takeaway / 要点:**
- Declare language preferences once in AGENTS.md
- Separate rules for conversation language vs technical language
- Code, commands, API names, package names → always English
- Never translate technical identifiers

---

## 7. Verification Skipping / 跳过验证

**User / 用户:** "Create a Python script that fetches and saves the current weather"
"写一个获取并保存当前天气的 Python 脚本"

### ❌ Wrong (Declares done without running)

```
Hermes:
  1. write_file("weather.py", content) → "Done!"
  2. "I've created weather.py. It uses the OpenWeatherMap API
     to fetch current weather data and saves it to weather.json.
     Run it with: python weather.py"
  
  User: "Did you actually run it?"
  Hermes: "No, but the code looks correct."
  
  User runs it manually:
  $ python weather.py
  → ImportError: No module named 'requests'
  → The script never worked.
```

**Why this happens:** Hermes defaults to "describe what I would do" instead of "actually do it and report the result." The system prompt says "finish the job," but without AGENTS.md reinforcing it, Hermes often stops at writing.

### ✅ Correct

```
Hermes:
  1. Write weather.py → verify: file exists at correct path
     terminal("test -f /c/Users/yushi/Projects/weather.py && echo 'exists'")
     → exists ✓
  
  2. Check for requests library → verify: importable
     terminal("uv run python -c 'import requests; print(requests.__version__)'")
     → ModuleNotFoundError: No module named 'requests'
     → Installing: uv pip install requests
  
  3. Run the script → verify: actual output
     terminal("uv run python /c/Users/yushi/Projects/weather.py")
     → {"city": "Shanghai", "temp": 28.5, "humidity": 72}
     → weather.json created successfully ✓
  
  4. Read the output file to confirm
     read_file("C:\\Users\\yushi\\Projects\\weather.json")
     → Content verified ✓
  
  Report: Script works. First run needed `requests` installed.
  Output saved to weather.json.
```

**Key takeaway / 要点:**
- Every step must have a verification checkpoint
- Don't declare "done" until the code actually runs
- If something fails (like missing import), fix it and continue
- Report what actually happened, not what should happen

---

## 8. Image Generation Without Skills / 生图不加载技能

**User / 用户:** "Generate a character portrait for my game, anime style, 512x768, with transparent background"
"帮我生成游戏角色头像，日式风格，512x768，透明背景"

### ❌ Wrong (Raw image_generate calls)

```python
# Attempt 1
image_generate(prompt="anime character portrait, game asset...")
# → Returns: 1024x1024, square, solid background
# → Wrong size, wrong aspect ratio, not transparent

# Attempt 2
image_generate(prompt="anime character portrait, 9:16 aspect ratio...", aspect_ratio="portrait")
# → Returns: 832x1472, portrait, but still solid background
# → AI image gen can't do transparent backgrounds natively

# Attempt 3 — tries to remove background manually
terminal("python -c 'from rembg import remove; ...'")
# → Wrong Python! rembg is on the special image-processing Python
# → Error: ModuleNotFoundError

# Attempt 4 — finally finds the right Python
terminal("C:\\Users\\yushi\\...\\Python311\\python.exe -c 'from rembg import remove; ...'")
# → Works but took 15 minutes to get here
```

### ✅ Correct

```python
# 1. Load the image compositing skill
skill_view(name="image-compositing")

# 2. The skill provides a verified pipeline:
#    Step 1: Generate base character
#      image_generate(prompt="anime character, full body, clean lines...", aspect_ratio="portrait")
#      → verify: image received, correct aspect ratio
#
#    Step 2: Remove background using the correct Python
#      terminal("C:\\Users\\yushi\\AppData\\Local\\Programs\\Python\\Python311\\python.exe -c
#      \"from rembg import remove; from PIL import Image; ...\"")
#      → verify: transparent background confirmed
#
#    Step 3: Resize to exact dimensions
#      terminal("...Python311\\python.exe -c \"from PIL import Image; img=Image.open(...); 
#      img.resize((512,768)); img.save(...)\"")
#      → verify: dimensions = 512x768

# Total time: 3 minutes. First try correct.
```

**Key takeaway / 要点:**
- AI image generators can't do transparent backgrounds
- Background removal needs the correct Python (with rembg)
- Image compositing skills know the full pipeline
- Loading a skill saves the trial-and-error of finding the right approach

---

## Anti-Pattern Quick Reference / 反模式速查表

| Scenario / 场景 | ❌ Anti-Pattern / 反模式 | ✅ Correct / 正确 |
|-----------------|-------------------------|-------------------|
| File operation / 文件操作 | MSYS path (`/c/Users/...`) | Windows native (`C:\Users\...`) |
| Python / Python | `python3`, `pip` | `python`, `uv pip install`, `uv run` |
| Image gen / 生图 | Raw `image_generate()` calls | Load `comfyui` or `image-compositing` first |
| npm publish | Loop retry without PTY | PTY mode + desktop browser 2FA |
| Game dev / 游戏开发 | Create new systems, modify 15 files | Scene-scoped, use existing patterns |
| Code editing / 改代码 | Refactor adjacent code | Touch only target, match style |
| Tool selection / 工具选择 | Guess from memory | Check skills + memory first |
| Task completion / 完成任务 | Describe the plan | Run and verify with real output |
| Language / 语言 | Mix Chinese/English randomly | Chinese default, English on demand |
| npm package names / 包名 | Translate to Chinese | Keep in English |
| Error handling / 错误处理 | Fabricate results | Report blockers honestly |
| File delivery / 文件交付 | Save to Desktop | Save to project directory |

---

## How to Use These Examples / 如何使用这些案例

1. **When building your AGENTS.md:** Use these as a checklist — does your AGENTS.md prevent each ❌ scenario?

2. **When debugging Hermes behavior:** Match your issue to an example. If Hermes is doing the ❌ pattern, your AGENTS.md is missing that rule.

3. **When contributing:** Found a new ❌/✅ pattern? Add it here and submit a PR.

4. **Before each session:** Quick-scan the anti-pattern table to prime your memory on what to watch for.

### Quick Self-Check / 快速自检

After adding AGENTS.md to your project, ask Hermes these questions and check the responses:

| Test / 测试 | Expected / 期望 | If Wrong / 如果错了 |
|------------|-----------------|-------------------|
| "Create a test file" | File at exact Windows path | Add Path Rules section |
| "Install a Python package" | Uses `uv pip install` | Add Toolchain section |
| "Generate an image" | Loads `comfyui` skill first | Add Skills section |
| Write a message in Chinese | Replies in Chinese | Add Communication section |
| "Publish to npm" | Uses PTY mode for 2FA | Add npm Publishing section |
| Git status check | Uses `git` commands in terminal | Add Toolchain section |

---

<p align="center">
  <sub>More examples? Found a new pattern? <a href="https://github.com/yushizehuohuo-gif/hermes-agent-guide">Open an issue or PR</a></sub>
</p>
