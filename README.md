# Hermes Agent 指南

为 Hermes Agent 优化的项目级指令，让 AI 助手更懂你的工作流。

**适用场景**：Windows 开发、Godot 游戏引擎、ComfyUI AI 生图、中文用户、非程序员技术创作者。

---

## 这不是什么

这不是 Hermes 的官方文档。这是一份经过实战验证的**项目级 AGENTS.md 模板**，解决 Hermes 在日常使用中的高频痛点：

- MSYS 路径混乱
- npm/uv 工具链选择
- 图片/媒体处理管线
- 中英文混合沟通
- 记忆和技能系统的最佳实践

---

## 四原则回顾

Hermes 系统提示词里已经内置了 Karpathy 四原则。这份指南帮你把原则落地到具体场景：

| 原则 | Hermes 特有问题 | 本指南解法 |
|------|---------------|-----------|
| **先亮假设再写代码** | MSYS vs Windows 路径混用 | 明确路径规则 |
| **简洁优先** | 过度工程化简单任务 | 项目级工具链声明 |
| **外科手术式修改** | 顺带重构、格式化漂移 | 项目风格锁 |
| **完成工作** | 只给方案不执行 | 验证循环模板 |

---

## 快速开始

```bash
# 下载到你的项目根目录
curl -o AGENTS.md https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md
```

或者追加到已有 AGENTS.md：

```bash
curl https://raw.githubusercontent.com/yushizehuohuo-gif/hermes-agent-guide/main/AGENTS.md >> AGENTS.md
```

---

## 目录结构

```
hermes-agent-guide/
├── README.md          # 本文件（中文）
├── README.en.md       # 英文版
├── AGENTS.md          # 项目级指令（核心文件）
├── EXAMPLES.md        # 实战案例
└── LICENSE            # MIT
```

## 实战案例

见 [EXAMPLES.md](./EXAMPLES.md)

## 许可证

MIT
