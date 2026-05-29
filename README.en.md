# format

A Claude Code skill that formats and beautifies your code — automatically detects existing tools in your project, and falls back to Claude's built-in formatting when none are found.

## Features

- Detects and runs existing formatters (prettier, black, gofmt, etc.) automatically
- No tools installed? Claude formats the code directly — no setup required
- Formats an entire project directory or a single file
- Reports a clean summary of what was changed

## Supported Languages

| Language | Preferred Tool | Fallback |
|----------|----------------|----------|
| JavaScript / TypeScript | prettier → eslint --fix | Claude |
| Python | black → ruff → autopep8 | Claude |
| Go | gofmt → goimports | Claude |
| Rust | rustfmt (cargo fmt) | Claude |
| CSS / HTML / JSON / Markdown | prettier | Claude |
| Java / Kotlin | spotless (mvn/gradle) | Claude |
| C / C++ | clang-format | Claude |
| Shell / Bash | shfmt | Claude |

## Usage

Just say it naturally in Claude Code:

```
/format
format my code
clean up this file
the indentation is a mess, fix it
unify the code style across the project
```

## Formatting Rules (when no tool is available)

- **Indentation**: 2 spaces for JS/TS/CSS/HTML, 4 spaces for Python, tabs for Go
- **Quotes**: single quotes for JS/TS, double quotes for Python
- **Blank lines**: 1–2 blank lines between functions/classes, 1 newline at end of file
- **Operators**: spaces on both sides (`a + b`, not `a+b`)
- **Import order**: Python groups stdlib → third-party → local; JS/TS orders node_modules before relative paths
- **Line length**: lines over 120 characters are wrapped

## Installation

Drag `format.skill` into Claude Code to install.

## File Structure

```
format/
├── SKILL.md      # skill instructions
├── README.md     # Chinese documentation
└── README.en.md  # this file
```
