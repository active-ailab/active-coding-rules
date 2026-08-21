# active-coding-rules

`active-coding-rules` 是一个语言无关的 Codex Skill，用于约束编码代理在编写、审查、调试和重构代码时的工作方式。它不提供某种语言或框架的语法规范，而是让代理先理解目标与现有代码，再做满足需求的最小改动，并在完成前建立验证闭环。

## 核心原则

1. **先思考再编码（Think Before Coding）**：明确目标、约束和预期行为；当歧义会改变实现时显式说明假设、比较合理方案，必要时向用户澄清。
2. **简单优先（Simplicity First）**：只实现当前需求，不为单次使用提前抽象，也不为假想未来添加配置、灵活性或额外错误处理。
3. **外科手术式修改（Surgical Changes）**：沿用仓库已有的命名、辅助函数、风格和目录组织，只修改任务要求的内容；不顺手重构、格式化或清理相邻代码，并把未提交改动视为用户所有。
4. **目标驱动执行（Goal-Driven Execution）**：把任务转换为可观察的成功标准。修复缺陷时先复现失败或定义失败行为，重构时验证行为保持不变，多步骤任务为每一步配套检查。

默认工作循环是：明确目标 → 检查本地代码 → 暴露关键假设 → 实施最小改动 → 运行最相关的测试、构建、lint 或手工检查 → 汇报改动、验证结果与剩余风险。代码审查模式要求先按严重程度列出具体发现，给出文件和行号并解释影响，摘要放在次要位置。

## 适用范围

该 Skill 可用于 bug 修复、行为保持型重构、复杂问题调试、代码审查、小到中等功能开发以及强调验证质量的任务。它适用于 Python、TypeScript、JavaScript、Go、Rust、Java、C 和 C++ 等语言，但不能替代语言、框架或平台自己的构建、静态分析、安全及发布规范；例如 C/C++ 项目仍应使用项目既有构建系统、编译器告警、测试、sanitizer 和格式化工具。

## 安装

仓库提供 `install.sh`，通过软链接把 `active-coding-rules/` 安装到指定的 Codex skills **父目录**：

```bash
chmod +x install.sh
./install.sh -path "$HOME/.codex/skills"
```

脚本会创建 `$HOME/.codex/skills/active-coding-rules`。不要把最终 Skill 名追加到 `-path` 参数。若链接已正确指向当前仓库，脚本会提示已安装并成功退出；若目标指向别处，或目标是普通文件/目录，则会报错而不覆盖。由于使用软链接，修改仓库内 Skill 文件后通常无需重新安装。

验证链接：

```bash
ls -l "$HOME/.codex/skills/active-coding-rules"
```

卸载只需删除链接，不会删除仓库：

```bash
rm -f "$HOME/.codex/skills/active-coding-rules"
```

## 使用示例

在支持显式 Skill 调用的环境中，可以在任务前引用：

```text
$active-coding-rules fix the failing test in src/parser.cpp and verify with the narrowest command.
```

```text
$active-coding-rules review this patch and list concrete findings ordered by severity.
```

```text
$active-coding-rules debug this crash, reproduce it first, make the smallest fix, and avoid unrelated cleanup.
```

## 仓库结构

```text
.
├── README.md
├── README.zh-CN.md
├── MATT_SKILLS_INTEGRATION_REPORT.md
├── install.sh
└── active-coding-rules/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── references/
        ├── core-rules.md
        ├── karpathy-examples.md
        └── matt-pocock-skills-lessons.md
```

- `SKILL.md`：触发说明、六步运行循环、四项核心原则和审查模式。
- `core-rules.md`：非平凡或多文件任务使用的扩展检查清单，包括编辑前检查、验证策略和最终报告要求。
- `karpathy-examples.md`：以导出用户、搜索优化、折扣计算、空邮箱修复、日志添加、认证与限流等正反例校准四项原则。
- `matt-pocock-skills-lessons.md`：用于演进本 Skill 或处理复杂诊断、TDD、架构、原型、规划和长期任务说明时参考的渐进式披露与工作流经验；它明确建议保持主 Skill 简短，而不是把所有流程塞入主文件。
- `agents/openai.yaml`：声明显示名称、简短说明和默认调用提示。

如本地 Codex 安装包含 Skill 校验脚本，可按实际路径运行 `quick_validate.py active-coding-rules`。
