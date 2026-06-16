# Hermes Agent 实战案例

## 案例 1: 路径陷阱

**用户请求**: "帮我把这个文件里的 'foo' 改成 'bar'"

**❌ Hermes 常见错误 (MSYS 路径)**

```bash
# Hermes 内部用了 MSYS 路径
patch("/c/Users/yushi/project/config.json", "foo", "bar")
# → 创建了 C:\c\Users\yushi\project\config.json 这个影子文件
# → 原文件没改，用户看到的是旧内容
```

**✅ 正确做法**

```bash
# 所有文件工具都用 Windows 原生路径
patch("C:\\Users\\yushi\\project\\config.json", "foo", "bar")

# terminal 内部可以用 MSYS 路径
terminal("sed -i 's/foo/bar/g' /c/Users/yushi/project/config.json")
```

**关键**: 文件工具 (read_file, write_file, patch, search_files) 永远用 Windows 路径。
终端 (terminal) 里可以用 MSYS 路径，因为 shell 自己会处理。

---

## 案例 2: 工具链混淆

**用户请求**: "帮我装个 Python 包，用来处理图片"

**❌ 常见错误**

```bash
pip install pillow
python3 -c "from PIL import Image..."
# → pip 可能装到了错误的 Python
# → python3 不存在
```

**✅ 正确做法**

```bash
# 先确认 Python 环境
python --version
uv pip install pillow

# 如果有特定 Python（比如装了 rembg 的那个）
C:\Users\yushi\AppData\Local\Programs\Python\Python311\python.exe -c "..."
```

**关键**: Windows 上 python3 通常是假的。用 `python` 或 `uv`。

---

## 案例 3: 生图工作流

**用户请求**: "帮我做一张宣传海报"

**❌ 跳过技能，裸调工具**

```
直接调用 image_generate → 效果一般
然后手动拼接 → 费时费力
```

**✅ 先加载技能，再执行**

```
1. skill_view("image-compositing")  # 加载海报合成技能
2. skill_view("comfyui")            # 如果有复杂生图需求
3. 按技能指引逐步执行
4. 每一步验证输出再继续
```

**关键**: Hermes 有 50+ 技能。花 2 秒加载比裸写快 10 倍。

---

## 案例 4: npm 发布 2FA

**用户请求**: "帮我把这个包发到 npm"

**❌ 死循环**

```bash
npm publish → EOTP 错误
npm login --auth-type=web → 超时
npm publish → EOTP 错误
# 循环 5 次还是不行
```

**✅ 正确流程**

```
1. 用 PTY 模式启动 npm login
2. 从 log 里抓取授权链接 (https://www.npmjs.com/auth/cli/<uuid>)
3. 用 cmd.exe //c start "" "URL" 在桌面浏览器打开
4. 用户在 Chrome 认证器完成 2FA
5. 等流程结束，再 npm publish
```

**关键**: npm 2FA 链接被日志打码成 `***`，要用 PTY 模式实时抓。

---

## 案例 5: 游戏开发 — Godot 项目迭代

**用户请求**: "给我的 FPS 卡牌游戏加一个抽卡动画"

**❌ 过度设计**

```
1. 分析现有代码 → 花了 200 行写动画系统
2. 引入 tween 库、状态机、事件总线
3. 改了 15 个文件
4. 测试时游戏崩溃
```

**✅ 外科手术式**

```
1. 搜索现有动画相关代码
   → verify: 找到现有动画模式

2. 在现有 Godot AnimationPlayer 上加新动画
   → verify: 只改了 1 个 .tscn + 1 个 .gd

3. 绑定触发逻辑
   → verify: 手动跑一遍场景

4. 不改任何无关代码
```

**关键**: Godot 项目里，改动限制在场景文件和对应脚本。

---

## 案例 6: 中英混合协作

**用户是中国人，但某些输出需要英文**

**❌ 混乱**

```
用户: "帮我写个 README"
Hermes: 写了英文 README
用户: "我要中文的啊"
Hermes: 全改中文
用户: "但是代码示例还是英文好..."
# 来回拉扯
```

**✅ 明确指令**

```
默认写中文，但:
- 代码、API 名、命令保持原样
- npm 包名、GitHub 项目名不翻译
- 如果面向国际发布，另写 README.en.md
```

**关键**: AGENTS.md 里写明语言偏好，不需要每次纠正。

---

## 反模式速查

| 场景 | 反模式 | 正确做法 |
|------|--------|---------|
| 文件操作 | MSYS 路径 | Windows 原生路径 |
| Python | `python3` / `pip` | `python` / `uv` |
| 生图/设计 | 裸调工具 | 先加载技能 |
| npm 发布 | 死循环重试 | PTY + 桌面浏览器 |
| 游戏开发 | 大范围改动 | 场景文件级修改 |
| 代码审查 | 顺带重构 | 只动目标代码 |
| 工具选择 | 凭记忆 | 查记忆 + 查技能 |
