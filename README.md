# format

代码格式化与美化 skill，让代码更规范、更易读。

## 功能

- 自动检测项目已有格式化工具并使用（prettier、black、gofmt 等）
- 没有工具时由 Claude 直接格式化，无需安装任何依赖
- 支持格式化整个项目或单个文件
- 格式化完成后输出处理摘要

## 支持的语言

| 语言 | 优先工具 | 回退方案 |
|------|----------|----------|
| JavaScript / TypeScript | prettier → eslint --fix | Claude 直接格式化 |
| Python | black → ruff → autopep8 | Claude 直接格式化 |
| Go | gofmt → goimports | Claude 直接格式化 |
| Rust | rustfmt (cargo fmt) | Claude 直接格式化 |
| CSS / HTML / JSON / Markdown | prettier | Claude 直接格式化 |
| Java / Kotlin | spotless (mvn/gradle) | Claude 直接格式化 |
| C / C++ | clang-format | Claude 直接格式化 |
| Shell / Bash | shfmt | Claude 直接格式化 |

## 使用方式

在 Claude Code 中直接说：

```
/format
格式化代码
帮我美化一下这个文件
代码写得比较乱，整理一下
```

## 格式化规则（无工具时）

- **缩进**：JS/TS/CSS/HTML 用 2 空格，Python 用 4 空格，Go 用 Tab
- **引号**：JS/TS 用单引号，Python 用双引号
- **空行**：函数/类之间保留 1-2 个空行，文件末尾保留 1 个换行
- **运算符**：两侧加空格（`a + b`，不是 `a+b`）
- **导入排序**：Python 按 stdlib → 第三方 → 本地分组；JS/TS 按 node_modules → 相对路径
- **长行**：超过 120 字符时折行

## 安装

将 `format.skill` 文件拖入 Claude Code 即可安装。

## 文件结构

```
format/
└── SKILL.md    # skill 主体指令
```
