# AGENTS.md

为 **Hermes Agent** 优化的项目指令。追加到你的项目 AGENTS.md 末尾，或设为项目级默认指令。

**适用**: Windows 10/11 · Hermes Desktop · 中文用户 · Godot/ComfyUI 创作者

---

## 环境声明

这些声明帮 Hermes 快速进入正确的上下文，避免反复纠正：

```
## 环境
- OS: Windows 10/11
- 终端: git-bash (MSYS2)，使用 POSIX 语法 (ls, grep, /c/...)
- 包管理: uv (Python), npm (Node)
- Godot: C:\Users\<user>\Projects\Godot\engine\Godot_v*_win64.exe
- 用户主目录: C:\Users\<用户名>
- 工作目录: 项目根目录
```

---

## 路径规则 (最重要)

**永远不要在 write_file / read_file / patch 中使用 MSYS 路径。**

```bash
# ✅ 对 — 用 Windows 原生路径
write_file "C:\Users\yushi\Projects\my-game\main.gd" "..."

# ❌ 错 — MSYS 路径传给工具会创建 C:\c\ 影子目录
write_file "/c/Users/yushi/Projects/my-game/main.gd" "..."
```

**唯一安全使用 MSYS 路径的场景**: terminal 工具内部的 shell 命令。

```bash
# ✅ terminal 里用 MSYS 路径安全
terminal("ls /c/Users/yushi/Projects/")
```

---

## 工具链速查

```bash
# Python — 用 uv，不要用 python3 (不存在)
uv pip install <包名>
uv run python <脚本>

# 图片处理 Python 解释器 (有 rembg/scipy)
C:\Users\<用户>\AppData\Local\Programs\Python\Python311\python.exe

# Node — npm/npx 直接用
npm install
npx <包名>

# Godot 4 引擎
C:\Users\<用户>\Projects\Godot\engine\Godot_v4.*-stable_win64.exe
```

---

## 沟通风格

- **默认中文**，除非用户用英文开头
- 简洁直接，不寒暄
- 不确定就问，不要猜测
- Push back when warranted — 用户的方案不好时直接说

---

## 技能系统

Hermes 有技能系统。工作中遇到以下场景时，先检查相关技能：

```
- 生图 → comfyui / image-compositing / image_gen 技能
- 游戏开发 → ai-assisted-game-dev / godot-web-deploy
- GitHub → github-pr-workflow / github-auth
- 海报/设计 → baoyu-infographic / image-compositing
- 网页 → web-project-design
- B站/抖音 → agency-bilibili-strategist / agency-douyin-strategist
```

---

## 记忆系统

- 用户偏好和环境配置 → 存 memory
- 工作流程和经验 → 存 skill
- 任务进度和临时 TODO → 不存 memory（用 session_search 回溯）
- 7 天后会过时的信息 → 不存 memory

---

## 验证循环

复杂任务使用这个模板：

```
1. [步骤] → 验证: [检查点]
2. [步骤] → 验证: [检查点]
3. [步骤] → 验证: [检查点]
```

每一步验证通过再继续，不要跳步。

---

## 完成标准

- 交付物是**可工作的结果**，不是方案描述
- 代码必须实际**运行过**，不是只写出来
- 报告真实输出，不编造数据
- 阻塞就报告阻塞，不瞎凑结果

---

## 常见陷阱

1. **不要用 python3** → 用 python 或 uv
2. **不要用 PowerShell 命令** → terminal 走 bash，用 grep/ls/sed
3. **不要 delivery 到桌面** → 放项目目录
4. **npm 发布需要 2FA** → 用户有 Chrome 认证器扩展
5. **生图时 image_gen.provider 留空**
