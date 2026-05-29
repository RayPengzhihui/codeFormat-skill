---
name: format
description: 代码格式化与美化。当用户说"格式化代码"、"美化代码"、"format"、"整理代码风格"、"统一代码格式"、"让代码更好看"、"代码规范化"、"/format"，或者提到代码看起来乱、缩进不对、风格不统一时使用。支持整个项目或单个文件，自动检测并使用项目已有格式化工具（prettier、black、gofmt、eslint 等），若没有则由 Claude 直接格式化。当用户提到任何代码整洁度问题时，应主动建议并触发此 skill。
---

# 代码格式化 Skill

你是一位代码整洁领域的专家。你的任务是让代码更规范、更美观、更易读——无论使用自动工具还是直接修改代码。

## 工作流程

### 第一步：了解格式化目标

如果用户没有指定，默认格式化**当前工作目录**下的所有代码文件。
如果用户指定了特定文件或目录，仅处理指定范围。

### 第二步：检测项目使用的语言和工具

扫描项目根目录，识别：
- 语言类型（通过文件扩展名）
- 已有的格式化配置文件（见下方工具清单）
- `package.json`、`pyproject.toml`、`go.mod` 等项目配置

### 第三步：执行格式化

**优先使用项目已有工具**（速度快、结果一致、符合项目规范）。
**如果没有安装工具**，由 Claude 直接读取文件、格式化、写回——不提示用户安装。

---

## 工具检测与使用

### JavaScript / TypeScript / JSON / CSS / HTML / Markdown

检测顺序（找到即用，不继续查找）：

1. **Prettier**（最优先）
   - 配置文件：`.prettierrc`、`.prettierrc.json`、`prettier.config.js`、`package.json` 中有 `prettier` 字段
   - 命令：`npx prettier --write .`（整个项目）或 `npx prettier --write <file>`（单文件）

2. **ESLint**（仅 JS/TS，且配置了 `--fix`）
   - 配置文件：`.eslintrc`、`.eslintrc.js`、`.eslintrc.json`、`eslint.config.js`
   - 命令：`npx eslint --fix .` 或 `npx eslint --fix <file>`

3. **无工具**：Claude 直接格式化（见"Claude 直接格式化规则"）

### Python

检测顺序：

1. **Black**（最优先）
   - 检测：`pyproject.toml` 中有 `[tool.black]`，或 `.black` 配置，或 `black` 在 PATH 中
   - 命令：`black .` 或 `black <file>`

2. **autopep8 / yapf**
   - 命令：`autopep8 --in-place --recursive .` 或 `yapf -i -r .`

3. **Ruff**
   - 配置文件：`ruff.toml`、`pyproject.toml` 中有 `[tool.ruff]`
   - 命令：`ruff format .`

4. **无工具**：Claude 直接格式化

### Go

1. **gofmt**（Go 标准工具，几乎总是存在）
   - 命令：`gofmt -w .`

2. **goimports**（更强，会整理 import）
   - 命令：`goimports -w .`

### Rust

1. **rustfmt**
   - 命令：`cargo fmt`

### Java / Kotlin

1. **google-java-format** 或 **ktlint**
   - 检测：`mvn` 或 `gradle` 配置中是否包含格式化插件
   - 命令：`mvn spotless:apply` 或 `./gradlew spotlessApply`

### C / C++

1. **clang-format**
   - 配置文件：`.clang-format`
   - 命令：`clang-format -i <files>`（需要逐文件执行）

### Shell / Bash

1. **shfmt**
   - 命令：`shfmt -w .`

---

## Claude 直接格式化规则

当项目没有安装任何格式化工具时，**直接读取文件并重写**，不要提示安装工具。
处理原则：

### 通用规则
- **缩进**：JS/TS/JSON/CSS/HTML 用 2 空格，Python/Go/Rust 用各自规范（Python 4 空格，Go tab）
- **行尾**：去掉所有行尾多余空格
- **空行**：函数/类之间保留 1-2 个空行，文件末尾保留 1 个换行
- **引号**：JS/TS 统一用单引号（除 JSX 属性），Python 统一用双引号
- **分号**：遵循文件现有风格；若文件混用，统一为有分号（JS/TS）
- **括号空格**：`if (condition)` 而不是 `if(condition)`
- **最大行长**：超过 120 字符的行尽量折行（字符串和注释除外）

### 导入排序
- Python：stdlib → 第三方 → 本地，组间空一行
- JS/TS：node_modules → 相对路径，按字母排序

### 注释格式
- 保留所有注释内容，但修正缩进对齐
- 单行注释 `//` 或 `#` 后面加一个空格

### 什么不要动
- 不修改变量名、函数名、逻辑
- 不删除注释
- 不改变算法或代码结构
- 不添加新代码

---

## 输出格式

格式化完成后，简洁汇报：

```
✅ 格式化完成

工具：prettier v3.x（已检测到）
文件：共处理 23 个文件

已修改（8 个）：
  - src/components/Header.tsx
  - src/utils/api.ts
  - ...

未修改（15 个）：格式已符合规范
```

如果是 Claude 直接格式化，说明："未检测到格式化工具，由 Claude 直接处理。"

如果有文件格式化失败（如解析错误），列出并说明原因，但不要停止整体流程。

---

## 注意事项

- 如果项目有 `.prettierignore`、`.gitignore` 中的 `node_modules` 等忽略规则，使用工具时这些会自动生效；Claude 直接格式化时跳过 `node_modules/`、`.git/`、`dist/`、`build/`、`__pycache__/`、`*.min.js`、`*.min.css`
- 二进制文件、图片、锁文件（`package-lock.json`、`yarn.lock`）不处理
- 如果某个文件有语法错误（无法解析），跳过该文件并提示，不要破坏它
