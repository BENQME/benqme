# Claude AI Guidelines

> Instructions and best practices for working with Claude as an AI coding assistant in this project.

---

## Table of Contents

1. [Purpose](#purpose)
2. [How to Use Claude Effectively](#how-to-use-claude-effectively)
3. [Context to Always Provide](#context-to-always-provide)
4. [Prompt Patterns](#prompt-patterns)
5. [Code Review Workflow](#code-review-workflow)
6. [Dos and Don'ts](#dos-and-donts)
7. [Memory & Context Windows](#memory--context-windows)
8. [Output Verification](#output-verification)

---

## Purpose

Claude is used in this project as a coding assistant for:
- Code generation and scaffolding
- Refactoring and code review
- Documentation drafting
- Debugging and problem solving
- Architecture exploration

---

## How to Use Claude Effectively

### 1. Be Specific About the Codebase
Always tell Claude the tech stack, file structure, and relevant conventions before asking it to generate code.

### 2. Provide File Context
Paste the relevant file contents or describe the interfaces Claude needs to respect.

### 3. Request Incremental Changes
Ask for one focused change at a time. Avoid asking Claude to "rewrite the whole feature."

### 4. Ask for Reasoning
When unsure about a suggestion, ask: *"Why did you choose this approach?"*

### 5. Iterate With Examples
If the first output misses the mark, show a before/after example of what you expect.

---

## Context to Always Provide

When starting a new session, include:

```
Project: [project name]
Stack: [e.g., Next.js 14, TypeScript, Tailwind CSS, Prisma]
Package manager: [npm/pnpm/yarn]
Conventions: See docs/instructions.md
Task: [specific task description]
```

For component work:

```
Component: [name]
Location: src/components/[name]/
Props interface: [paste the TypeScript interface]
Design spec: [describe or link to design]
Existing patterns: [paste a similar component as reference]
```

---

## Prompt Patterns

### Generate a Component
```
Create a [ComponentName] React component in TypeScript.
- Props: [list props with types]
- Behavior: [describe interactions]
- Style: Use Tailwind CSS utility classes following the project design tokens
- Accessibility: Include ARIA attributes and keyboard navigation
- Don't add default exports; use named exports
```

### Refactor Code
```
Refactor the following code to [goal].
Preserve all existing behavior.
Do not change the public API.
Explain each change you make.

[paste code]
```

### Fix a Bug
```
The following code has a bug: [describe the bug / actual vs expected behavior].
Here is the code:

[paste code]

Identify the root cause and suggest a minimal fix.
```

### Write Tests
```
Write unit tests for the following function using [Vitest/Jest].
Cover: happy path, edge cases, and error cases.
Use the existing test file style: [paste example test]

[paste function]
```

### Document Code
```
Write JSDoc comments for all exported functions and types in this file.
Match the existing documentation style.
Do not modify any code — only add comments.

[paste file]
```

---

## Code Review Workflow

1. Paste your diff or the changed file
2. Ask: *"Review this change for correctness, performance, and security."*
3. Ask follow-up: *"Are there any edge cases I haven't handled?"*
4. Ask: *"Does this follow the project conventions in docs/instructions.md?"*

---

## Dos and Don'ts

### ✅ Do
- Verify all generated code before committing
- Run linters and tests on Claude-generated code
- Ask Claude to explain non-obvious logic
- Use Claude for first drafts, then refine manually
- Ask for multiple approaches and pick the best one

### ❌ Don't
- Blindly commit Claude-generated code without review
- Ask Claude to modify production secrets or credentials
- Use Claude output as the final source of truth for security decisions
- Paste sensitive data (API keys, PII) into prompts

---

## Memory & Context Windows

- Claude does not retain memory between sessions — re-establish context at the start of each conversation
- For long files, paste only the relevant section plus the surrounding 20–30 lines
- If hitting context limits, break the task into smaller steps
- Use the `docs/` folder as your persistent memory — keep it updated so Claude can be pointed to it

---

## Output Verification

Before using any Claude-generated code:

- [ ] Code compiles and passes type checking
- [ ] All tests pass
- [ ] Linter reports no new errors
- [ ] Logic matches described behavior
- [ ] No security vulnerabilities introduced
- [ ] No secrets or hardcoded credentials
- [ ] Accessibility requirements met
- [ ] Performance implications considered
