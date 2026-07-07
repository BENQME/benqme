# Versioning

> Versioning strategy, release process, and SemVer conventions.

---

## Table of Contents

1. [Versioning Strategy](#versioning-strategy)
2. [Semantic Versioning](#semantic-versioning)
3. [Release Process](#release-process)
4. [Changelog](#changelog)
5. [Tagging](#tagging)
6. [Branch Strategy](#branch-strategy)
7. [Deprecation Policy](#deprecation-policy)

---

## Versioning Strategy

This project follows [Semantic Versioning (SemVer)](https://semver.org/): `MAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes that require user action
- **MINOR**: New features, fully backward compatible
- **PATCH**: Bug fixes and minor improvements

Current version: see `package.json`.

---

## Semantic Versioning

```
1.4.2
│ │ └── PATCH — Bug fix, no new features
│ └──── MINOR — New feature, backward compatible
└────── MAJOR — Breaking change
```

### What constitutes a breaking change?
- Removing a public API, endpoint, or prop
- Changing the behavior of an existing feature in a non-additive way
- Removing or renaming a route
- Changing a database schema in a way that breaks existing data

### Pre-release versions
```
1.0.0-alpha.1    — Early development, may break
1.0.0-beta.1     — Feature-complete, fixing bugs
1.0.0-rc.1       — Release candidate, near-final
```

---

## Release Process

### 1. Prepare the release
```bash
# Ensure you're on develop with all changes merged
git checkout develop
git pull origin develop

# Run the full test suite
pnpm test
pnpm build
```

### 2. Update version
```bash
# Patch release
npm version patch

# Minor release
npm version minor

# Major release
npm version major

# Pre-release
npm version prerelease --preid=beta
```

This automatically:
- Updates `package.json`
- Creates a git commit
- Creates a git tag (`v1.2.3`)

### 3. Update CHANGELOG.md
- Move items from `[Unreleased]` to the new version section
- Add release date
- Ensure all notable changes are documented

### 4. Create release PR
```bash
git push origin develop
# Open PR: develop → main
```

### 5. Merge and tag
After PR is merged:
```bash
git checkout main
git pull origin main
git push origin --tags
```

### 6. Create GitHub Release
```bash
gh release create v1.2.3 \
  --title "v1.2.3" \
  --notes-from-tag \
  --generate-notes
```

---

## Changelog

The `CHANGELOG.md` file follows [Keep a Changelog](https://keepachangelog.com/) format.

```markdown
# Changelog

All notable changes to this project will be documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased]

### Added
- New feature description

### Changed
- Changed behavior description

### Fixed
- Bug fix description

## [1.2.0] - 2024-03-15

### Added
- Dark mode support
- Command palette (⌘K)

### Changed
- Improved navigation animation performance

### Fixed
- Fixed focus trap in mobile menu

## [1.1.0] - 2024-02-01

...

[Unreleased]: https://github.com/BENQME/benqme/compare/v1.2.0...HEAD
[1.2.0]: https://github.com/BENQME/benqme/compare/v1.1.0...v1.2.0
```

### Change Categories
| Category | Description |
|----------|-------------|
| `Added` | New features |
| `Changed` | Changes in existing functionality |
| `Deprecated` | Features to be removed in a future release |
| `Removed` | Removed features |
| `Fixed` | Bug fixes |
| `Security` | Vulnerability fixes |

---

## Tagging

```bash
# List existing tags
git tag -l

# Create annotated tag
git tag -a v1.2.3 -m "Release v1.2.3"

# Push tag
git push origin v1.2.3

# Push all tags
git push origin --tags

# Delete a tag (if released prematurely)
git tag -d v1.2.3
git push origin --delete v1.2.3
```

Tag format: `v{MAJOR}.{MINOR}.{PATCH}` (always with `v` prefix).

---

## Branch Strategy

```
main          → production (versioned, tagged)
develop       → integration (pre-release)
feature/*     → feature work (merges into develop)
fix/*         → bug fixes (merges into develop)
release/*     → release preparation (merges into main + develop)
hotfix/*      → urgent production fixes (merges into main + develop)
```

### Hotfix Process
```bash
# Branch from main
git checkout main
git checkout -b hotfix/critical-auth-bug

# Fix the bug and commit
git commit -m "fix(auth): resolve session expiry edge case"

# Bump patch version
npm version patch

# Merge into main
git checkout main
git merge hotfix/critical-auth-bug --no-ff
git push origin main --tags

# Merge back into develop
git checkout develop
git merge hotfix/critical-auth-bug --no-ff
git push origin develop
```

---

## Deprecation Policy

When deprecating a feature:

1. Mark as deprecated in code with a comment:
   ```typescript
   /**
    * @deprecated Use `newFunction` instead. Will be removed in v2.0.0.
    */
   export function oldFunction() { ... }
   ```

2. Add to `CHANGELOG.md` under `### Deprecated`

3. Keep the deprecated feature functional for at least **one minor version** before removal

4. Remove in the next major version with a migration note in the changelog

5. For APIs: return a deprecation warning header:
   ```http
   Deprecation: version="v1.2"; rel="successor-version"
   Link: </api/v2/resource>; rel="successor-version"
   ```
