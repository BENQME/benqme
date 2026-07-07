# Contributing

> How to contribute to this project — setup, workflow, standards, and expectations.

---

## Table of Contents

1. [Getting Started](#getting-started)
2. [Ways to Contribute](#ways-to-contribute)
3. [Development Workflow](#development-workflow)
4. [Commit Conventions](#commit-conventions)
5. [Pull Request Process](#pull-request-process)
6. [Code Standards](#code-standards)
7. [Testing Requirements](#testing-requirements)
8. [Documentation](#documentation)
9. [Issue Reporting](#issue-reporting)
10. [Code of Conduct](#code-of-conduct)

---

## Getting Started

1. **Fork** the repository on GitHub
2. **Clone** your fork locally:
   ```bash
   git clone https://github.com/YOUR_USERNAME/benqme.git
   cd benqme
   ```
3. **Add upstream remote**:
   ```bash
   git remote add upstream https://github.com/BENQME/benqme.git
   ```
4. **Follow the setup guide** in [`development.md`](./development.md)
5. **Verify everything works**:
   ```bash
   pnpm install
   pnpm dev
   pnpm test
   ```

---

## Ways to Contribute

| Type | Description |
|------|-------------|
| 🐛 Bug reports | Found something broken? Open an issue |
| ✨ Feature requests | Have an idea? Open a discussion or issue |
| 📝 Documentation | Improve docs, fix typos, add examples |
| 🧪 Tests | Improve test coverage |
| ♿ Accessibility | Find and fix a11y issues |
| ⚡ Performance | Identify and fix bottlenecks |
| 🔒 Security | Report vulnerabilities privately |
| 💡 Code | Bug fixes, features, refactors |

---

## Development Workflow

### 1. Sync with upstream
```bash
git fetch upstream
git checkout develop
git merge upstream/develop
```

### 2. Create a branch
```bash
# Features
git checkout -b feature/your-feature-name

# Bug fixes
git checkout -b fix/bug-description

# Documentation
git checkout -b docs/what-you-changed
```

### 3. Make your changes
- Write code following the [Code Standards](#code-standards)
- Write tests for your changes
- Update documentation if needed
- Commit early and often with clear messages

### 4. Keep your branch up to date
```bash
git fetch upstream
git rebase upstream/develop
```

### 5. Run the full check suite before pushing
```bash
pnpm lint:fix    # Fix linting issues
pnpm typecheck   # Check TypeScript
pnpm test        # Run all tests
pnpm build       # Verify build passes
```

### 6. Push and open a PR
```bash
git push origin feature/your-feature-name
```
Then open a PR against the `develop` branch.

---

## Commit Conventions

This project follows [Conventional Commits](https://www.conventionalcommits.org/).

```
<type>(<scope>): <short description>

[optional body]

[optional footer(s)]
```

### Types
| Type | When to use |
|------|-------------|
| `feat` | New user-facing feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, whitespace (no logic change) |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `perf` | Performance improvement |
| `test` | Adding or updating tests |
| `chore` | Build process, dependencies, tooling |
| `ci` | CI/CD configuration |

### Examples
```
feat(auth): add GitHub OAuth login
fix(button): correct disabled state cursor
docs(accessibility): add keyboard navigation examples
test(useCounter): add edge case for negative values
chore(deps): update Next.js to 14.2.0
```

---

## Pull Request Process

### Before submitting
- [ ] Branch is up to date with `develop`
- [ ] All CI checks pass locally
- [ ] Tests added for new code
- [ ] Documentation updated
- [ ] PR description explains the change and why

### PR Description Template
```markdown
## Summary
[What does this PR do? Why?]

## Changes
- [Change 1]
- [Change 2]

## Testing
[How did you test this?]

## Screenshots (if UI changes)
[Before / After]

## Checklist
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] Accessibility checked
- [ ] Mobile layout checked
```

### Review Process
1. At least **1 approval** required to merge
2. Address all review comments before merging
3. Resolve all conversations before merging
4. Squash and merge to keep history clean

---

## Code Standards

- **Language**: TypeScript (strict mode)
- **Formatting**: Prettier (runs automatically via lint-staged)
- **Linting**: ESLint (must pass with no errors)
- **Imports**: Use `@/` alias for absolute imports
- **Exports**: Named exports (no default exports except for pages)
- **Types**: Explicit types; no `any`
- **Comments**: Only for non-obvious logic; prefer self-documenting code

See [`instructions.md`](./instructions.md) for the full coding standards reference.

---

## Testing Requirements

| Change type | Test requirement |
|------------|-----------------|
| New utility function | Unit tests with ≥ 80% coverage |
| New React component | Render test + interaction tests |
| New API route | Request/response tests covering success + error cases |
| Bug fix | Regression test that fails without the fix |
| Refactor | All existing tests must still pass |

See [`testing.md`](./testing.md) for testing patterns and examples.

---

## Documentation

Update documentation alongside code changes:

- New component → add to `components.md`
- New environment variable → add to `development.md`
- New API endpoint → add to `api.md`
- Architectural decision → add to `decisions.md`
- Breaking change → update `CHANGELOG.md`

---

## Issue Reporting

### Bug Reports
Include:
1. **Description** of the bug
2. **Steps to reproduce**
3. **Expected behavior**
4. **Actual behavior**
5. **Environment** (browser, OS, Node version)
6. **Screenshots or recordings** (if UI related)

### Feature Requests
Include:
1. **Problem statement** — what problem does this solve?
2. **Proposed solution** — how should it work?
3. **Alternatives considered**
4. **Additional context**

### Security Vulnerabilities
**Do not** open a public issue for security vulnerabilities. Use GitHub's private security advisory feature instead.

---

## Code of Conduct

This project follows the [Contributor Covenant Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/).

**In short**: Be respectful, inclusive, and constructive. Harassment and discrimination will not be tolerated.

To report violations, contact the maintainer directly via GitHub.
