# Laravel Full-Stack Skills

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
![Laravel](https://img.shields.io/badge/-Laravel-FF2D20?logo=laravel&logoColor=white)
![React](https://img.shields.io/badge/-React-61DAFB?logo=react&logoColor=black)
![Inertia.js](https://img.shields.io/badge/-Inertia.js-9553E9?logo=inertia&logoColor=white)

**Language:** English | [中文](README.zh-CN.md)

A comprehensive skill pack for **Laravel + React (Inertia.js)** development with **Test-Driven Development (TDD)** workflows and **Multi-Model Code Review** automation.

## ✨ Features

- 🎯 **Laravel-focused skills** - Patterns, API design, Eloquent, Boost integration
- 🔄 **TDD Workflow** - Strict RED-GREEN-REFACTOR cycle with 80%+ coverage
- 🤖 **Multi-Model Review** - Parallel code review with Gemini/Codex/Zhipu
- 🔧 **Auto-Fix Loop** - Automatic issue fixing with retry limits
- 📊 **Project Blueprint** - Track progress and quality metrics

---

## 📁 Project Structure

```
laravel-full-stack-skills/
├── skills/                  # AI Skills (SKILL.md files)
│   ├── laravel-boost/       # Laravel Boost MCP integration
│   ├── laravel-patterns/    # Architecture patterns
│   ├── laravel-tdd/         # TDD best practices
│   ├── laravel-api/         # API Resource, Sanctum
│   ├── inertia-react/       # Inertia.js + React patterns
│   ├── multi-model-review/  # Multi-model review config
│   ├── auto-fix-loop/       # Auto-fix loop control
│   ├── frontend-patterns/   # Frontend best practices
│   ├── postgres-patterns/   # PostgreSQL patterns
│   └── security-review/     # Security review checklist
├── workflows/               # Development Workflows
│   ├── blueprint.md         # Project monitoring
│   ├── laravel-tdd-dev.md   # TDD development
│   ├── code-review.md       # Multi-model review
│   └── dev-cycle.md         # Combined TDD + Review
├── commands/                # Slash Commands
│   ├── blueprint.md         # /blueprint
│   ├── tdd-dev.md           # /tdd-dev
│   ├── code-review-loop.md  # /code-review
│   └── dev-cycle.md         # /dev-cycle
├── agents/                  # Agent Configurations
│   ├── tdd-guide.md         # TDD specialist
│   ├── code-reviewer.md     # Code reviewer
│   ├── security-reviewer.md # Security specialist
│   └── database-reviewer.md # Database specialist
└── rules/                   # Code Standards
```

---

## 🚀 Quick Start

### Installation

1. Clone this repository into your project:

```bash
git clone https://github.com/whalestore/laravel-full-stack-skills.git .ai/skills
```

2. Or add as a Git submodule:

```bash
git submodule add https://github.com/whalestore/laravel-full-stack-skills.git .ai/skills
```

### Using with Antigravity

1. Open Antigravity editor
2. The skills will be automatically loaded from `.ai/skills/` directory
3. Use slash commands to trigger workflows

---

## 📋 Workflows

### 1. Project Blueprint (`/blueprint`)

Generate and maintain a project blueprint document tracking development progress.

```
/blueprint
```

**What it does:**
- Scans project structure (app/, routes/, resources/)
- Identifies feature modules from routes
- Analyzes test coverage
- Evaluates code quality
- Generates `blueprint.md` with progress tracking

**Output Example:**
```markdown
# Project Blueprint: MyApp

| Metric | Value |
|--------|-------|
| Tech Stack | Laravel 12.x + React |
| Test Coverage | 85% |
| Code Quality | A |

## Features
| Feature | Status | Progress | Coverage |
|---------|--------|----------|----------|
| User Auth | 🟢 Done | 100% | 90% |
| Orders | 🟡 WIP | 60% | 45% |
```

---

### 2. TDD Development (`/tdd-dev`)

Strict Test-Driven Development workflow with RED-GREEN-REFACTOR cycle.

```
/tdd-dev <feature description>
```

**Workflow Steps:**

| Step | Phase | Description |
|------|-------|-------------|
| 1 | Analyze | Parse requirements |
| 2 | RED | Write failing test |
| 3 | GREEN | Implement minimal code |
| 4 | REFACTOR | Optimize code |
| 5 | Coverage | Ensure 80%+ coverage |
| 6 | Browser Test | Optional: Antigravity browser test |
| 7 | Mobile Test | Optional: Maestro MCP test |
| 8 | Commit | Auto commit with message |

**Example:**
```bash
/tdd-dev Implement product search with filters
```

**TDD Cycle:**
```
┌─────────┐     ┌─────────┐     ┌──────────┐
│   RED   │ ──→ │  GREEN  │ ──→ │ REFACTOR │
│Write Test│     │ Minimal │     │ Optimize │
│ (Fails) │     │  Code   │     │   Code   │
└─────────┘     └─────────┘     └──────────┘
      ↑                               │
      └───────────────────────────────┘
              Next Test Case
```

---

### 3. Code Review (`/code-review`)

Multi-model parallel code review with automatic fix loop.

```
/code-review [--range=<ref>] [--max-retries=<n>]
```

**Review Process:**

```
                 ┌──────────────────────┐
                 │   Collect Changes    │
                 └──────────┬───────────┘
                            │
         ┌──────────────────┼──────────────────┐
         ▼                  ▼                  ▼
   ┌───────────┐      ┌───────────┐      ┌───────────┐
   │  Gemini   │      │   Codex   │      │   Zhipu   │
   │Architecture│      │ Security  │      │Code Style │
   └─────┬─────┘      └─────┬─────┘      └─────┬─────┘
         │                  │                  │
         └──────────────────┼──────────────────┘
                            ▼
                 ┌──────────────────────┐
                 │   Aggregate Issues   │
                 │   Priority: C/H/M/L  │
                 └──────────┬───────────┘
                            │
            ┌───────────────┼───────────────┐
            ▼               ▼               ▼
         No Issues      Fixable       Critical
            │               │               │
            ▼               ▼               ▼
          Pass ✅      Auto-Fix 🔄    Human 🛑
                            │
                     (max 3 retries)
```

**Priority Levels:**

| Level | Description | Action |
|-------|-------------|--------|
| 🔴 Critical | Security vulnerabilities | Human intervention |
| 🟠 High | Performance, logic errors | Auto-fix |
| 🟡 Medium | Code quality | Auto-fix |
| 🟢 Low | Suggestions | Optional |

---

### 4. Complete Dev Cycle (`/dev-cycle`)

Combined TDD + Code Review workflow.

```
/dev-cycle <feature description>
```

**Complete Flow:**

```
┌─────────────────────────────────────────────────┐
│            Phase 1: TDD Development             │
│         RED → GREEN → REFACTOR → Test           │
└────────────────────────┬────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────┐
│            Phase 2: Code Review                 │
│       Multi-Model Review → Auto-Fix Loop        │
└────────────────────────┬────────────────────────┘
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
       Pass ✅      Need Fix 🔄    Need Human 🛑
          │              │              │
          │         Back to Phase 1     │
          │         (fix issues)    Stop & Report
          ▼
┌─────────────────────────────────────────────────┐
│            Phase 3: Update Blueprint            │
│           Record progress & metrics             │
└─────────────────────────────────────────────────┘
```

---

## 🛠 Skills Reference

### Laravel Skills

| Skill | Description |
|-------|-------------|
| `laravel-boost` | MCP Server integration, AI guidelines |
| `laravel-patterns` | Controllers, Services, Repository |
| `laravel-tdd` | PHPUnit/Pest, TDD workflow |
| `laravel-api` | API Resource, Sanctum auth |
| `inertia-react` | Page components, forms, routing |

### Review Skills

| Skill | Description |
|-------|-------------|
| `multi-model-review` | Configure multiple review models |
| `auto-fix-loop` | Automatic fix loop control |
| `security-review` | Security checklist |

---

## 🔧 Using with Antigravity

### Setup

1. **Clone skills to your project:**
   ```bash
   cd your-laravel-project
   git clone https://github.com/whalestore/laravel-full-stack-skills.git .ai/skills
   ```

2. **Open with Antigravity:**
   - Launch Antigravity editor
   - Open your Laravel project
   - Skills are automatically detected

### Commands

Type these commands in Antigravity chat:

| Command | Description |
|---------|-------------|
| `/blueprint` | Generate project blueprint |
| `/tdd-dev <feature>` | Start TDD development |
| `/code-review` | Run code review |
| `/dev-cycle <feature>` | Complete dev cycle |

### Browser Testing

The TDD workflow supports Antigravity's built-in browser tools:

1. When developing web pages, browser tests run automatically
2. Screenshots and recordings are saved for validation
3. UI flows are verified against expected behavior

### MCP Integration

Configure Laravel Boost MCP in your project:

```json
{
  "mcpServers": {
    "laravel-boost": {
      "command": "php",
      "args": ["artisan", "boost:mcp"]
    }
  }
}
```

---

## 📄 License

MIT License - see [LICENSE](LICENSE) file.

---

## 🙏 Credits

Based on [everything-claude-code](https://github.com/affaan-m/everything-claude-code), adapted for Laravel full-stack development.
