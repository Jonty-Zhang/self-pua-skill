# self-pua-skill

> 让 AI 在动手之前先"PUA 自己"：把你的一句话需求扩写成完整的任务书，想透每个细节，然后不偷懒地把活干好。

仓库里有两个 Agent Skill，可以分开安装：

| Skill | 一句话 | 调用 |
|---|---|---|
| **self-pua** | 自主模式：所有细节由 AI 自己决定，并写清理由 | `/self-pua` |
| **self-pua-a** | 询问模式：重要细节动手前先问你，琐碎的 AI 自己定 | `/self-pua-a` |

## 为什么做这个

1. **产出质量取决于提示词。** 同一个任务，提示词写得好不好，结果差别很大。而 AI 自己扩写出来的提示词，往往比我们随手写的一句话完整得多。那就让 AI 在做任何事之前，先替我们把提示词写好：想清楚真实目标、交付物、约束、边界情况、验收标准，再动手。
2. **AI 会偷懒。** 用 `...` 省略代码、留 TODO、没运行就说"应该可以了"、遇到报错删测试绕过去、把难做的需求悄悄换成好做的。所以两个 skill 都内置了一套反偷懒守则和交付前自检。

## 工作流程

```
你的一句话需求
   │
   ▼
① 先查上下文 ──── 能查到的不猜，也不问
   │
   ▼
② 扩写成任务书 ── 真实目标 / 交付物 / 约束 / 范围 / 验收标准 / 风险 / 验证方式
   │
   ├─ self-pua   ：细节全部自己定，写明理由，直接开干
   └─ self-pua-a ：重要细节集中问你（带选项和推荐），你拍板后开干
   │
   ▼
③ 按反偷懒守则执行
   │
   ▼
④ 逐条对照验收标准自检，拿出证据
   │
   ▼
⑤ 交付：做了什么、为什么、怎么验证的、还剩什么
```

## 两种模式怎么选

| | self-pua | self-pua-a |
|---|---|---|
| 细节谁来定 | AI 全权决定 | 重要的你定，琐碎的 AI 定 |
| 什么时候停下来问你 | 只在删除、对外发布、花钱这类需要授权的操作之前 | 上面这些 + 每一个重要决策点 |
| 适合 | 想省心；任务有公认的好做法；你愿意事后看决策记录 | 你有明确偏好；结果很看口味；选错了代价大 |

"重要决策"的判定标准（满足任意一条）：口味偏好、需求有多种合理解读、显著影响范围或成本、难以撤销、涉及他人或对外。

## 安装

### Claude Code

```bash
git clone https://github.com/Jonty-Zhang/self-pua-skill.git
cp -r self-pua-skill/skills/self-pua self-pua-skill/skills/self-pua-a ~/.claude/skills/
```

### 其他支持 Agent Skills 的工具（Codex 等）

把两个文件夹放进对应工具的 skills 目录，比如 `~/.agents/skills/`。

想让本地随仓库更新自动同步，可以用软链接代替复制（在 clone 下来的目录的上一级执行）：

```bash
ln -s "$(pwd)/self-pua-skill/skills/self-pua" ~/.agents/skills/self-pua
ln -s "$(pwd)/self-pua-skill/skills/self-pua-a" ~/.agents/skills/self-pua-a
```

## 使用

```
/self-pua 把 src/auth 里的登录逻辑重构一下
/self-pua-a 帮我写一份给新人的项目上手文档
```

- **点名才启用**，不会自动触发
- **启用后持续生效**：之后的每个任务都会先扩写再动手，直到你说"关掉 self-pua"或者会话结束
- 两个模式互斥，以后调用的为准
- skill 不会凌驾于系统设置和项目规则（CLAUDE.md / AGENTS.md）之上

## 仓库结构

```
self-pua-skill/
├── README.md
├── .gitignore
└── skills/
    ├── self-pua/                         # 自主模式
    │   ├── SKILL.md                      # 主文件：开关、流程、授权类例外、红线、示例
    │   └── references/
    │       ├── prompt-expansion.md       # 需求扩写清单：10 个维度 + 任务书模板
    │       ├── anti-laziness.md          # 反偷懒守则：工作原则、偷懒清单、交付前自检
    │       └── autonomous-decisions.md   # 自主决策指南：依据优先级、默认倾向、决策记录
    └── self-pua-a/                       # 询问模式
        ├── SKILL.md                      # 主文件：开关、流程、重要决策判定、提问方式、示例
        └── references/
            ├── prompt-expansion.md       # 与 self-pua 相同的副本
            ├── anti-laziness.md          # 与 self-pua 相同的副本
            └── asking-decisions.md       # 提问决策指南：分拣、好坏问题对照、模板、回答后的处理
```

### 维护注意

`prompt-expansion.md` 和 `anti-laziness.md` 在两个 skill 里各有一份**内容相同的副本**，这样每个 skill 都能单独安装。改了其中一份，记得同步另一份，然后确认两边一致：

```bash
diff skills/self-pua/references/prompt-expansion.md skills/self-pua-a/references/prompt-expansion.md && diff skills/self-pua/references/anti-laziness.md skills/self-pua-a/references/anti-laziness.md && echo 两份副本一致
```

## 致谢

反偷懒守则里的六条工作原则，改编自网上流传的 "ultrathink" 提示词（小红书 @migeai 整理的中英文版本）。
