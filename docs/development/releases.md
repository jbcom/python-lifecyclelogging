# Release Process

This document describes the automated release process for lifecyclelogging.

## Overview

Releases are fully automated using [Python Semantic Release](https://python-semantic-release.readthedocs.io/) and GitHub Actions. The process follows [Semantic Versioning (SemVer)](https://semver.org/).

## How It Works

### 1. Commit Format

The release process analyzes commit messages following [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types that trigger releases:**
- `feat`: New feature → **MINOR** version bump (e.g., 1.0.0 → 1.1.0)
- `fix`: Bug fix → **PATCH** version bump (e.g., 1.0.0 → 1.0.1)
- `perf`: Performance improvement → **PATCH** version bump

**Types that don't trigger releases:**
- `docs`: Documentation changes
- `style`: Code style changes
- `refactor`: Code refactoring
- `test`: Test changes
- `chore`: Maintenance tasks
- `ci`: CI/CD changes

**Breaking changes:**
Add `!` after type or include `BREAKING CHANGE:` in footer → **MAJOR** version bump (e.g., 1.0.0 → 2.0.0)

### 2. Automated Release Flow

When commits are pushed to `main`:

1. **CI Runs**: Build, test, and lint checks must pass
2. **Release Check**: Semantic-release analyzes commits since last release
3. **Version Bump**: If release needed:
   - Updates version in `pyproject.toml` and `__init__.py`
   - Creates git commit with `[skip ci]` to avoid loop
   - Creates git tag (e.g., `v1.1.0`)
4. **Publish**: 
   - Pushes version commit and tag to GitHub (uses `CI_GITHUB_TOKEN` to bypass branch protection)
   - Builds Python package
   - Publishes to PyPI using Trusted Publishing
   - Creates GitHub Release with changelog
5. **Documentation**: Automatically deploys to GitHub Pages

## Branch Protection

The release workflow uses `CI_GITHUB_TOKEN` secret to bypass branch protection rules when pushing version commits and tags. This allows automated releases while keeping main branch protected.

### Required Secrets

- `CI_GITHUB_TOKEN`: GitHub Personal Access Token with repo permissions and bypass branch protection
- PyPI Trusted Publishing: Configured via GitHub OIDC (no token needed)

## Manual Release (Emergency)

If automation fails, you can manually release:

```bash
# 1. Ensure you're on main and up to date
git checkout main
git pull origin main

# 2. Install semantic-release
uv tool install python-semantic-release

# 3. Run release (dry-run first)
semantic-release version --noop
semantic-release version

# 4. Build and publish
uv build
uv tool install twine
uv tool run twine upload dist/*

# 5. Create GitHub release
gh release create v<version> dist/* --generate-notes
```

## Examples

### Feature Release (Minor Version)

```bash
git commit -m "feat(logging): add new logging handler for structured data"
# On merge to main: 1.0.0 → 1.1.0
```

### Bug Fix (Patch Version)

```bash
git commit -m "fix(handlers): correct file handler encoding issue"
# On merge to main: 1.1.0 → 1.1.1
```

### Breaking Change (Major Version)

```bash
git commit -m "feat(api)!: redesign logging configuration API

BREAKING CHANGE: Configuration API has been redesigned.
The old `setup()` method is removed. Use `Logging()` constructor instead."
# On merge to main: 1.1.1 → 2.0.0
```

### Non-Release Commit

```bash
git commit -m "docs: update README with new examples"
# No release triggered
```

## Skipping CI

Add `[skip ci]` to commit message to prevent CI from running:

```bash
git commit -m "chore: update dependencies [skip ci]"
```

The automated release process always adds `[skip ci]` to version bump commits to prevent infinite loops.

## Troubleshooting

### Release didn't trigger

- Check that commits follow conventional format
- Verify at least one `feat` or `fix` commit since last release
- Check CI workflow logs: https://github.com/extended-data-library/logging/actions

### PyPI publish failed

- Verify PyPI Trusted Publishing is configured: https://pypi.org/manage/account/publishing/
- Check that `environment: pypi` matches GitHub environment name
- Ensure `id-token: write` permission is set in workflow

### Git push failed (branch protection)

- Verify `CI_GITHUB_TOKEN` secret exists
- Ensure token has repo scope and can bypass branch protection
- Check that token is used in workflow: `token: ${{ secrets.CI_GITHUB_TOKEN }}`

## Release Checklist

Before triggering a release:

- [ ] All tests pass locally
- [ ] CHANGELOG updated (if manual entry needed)
- [ ] Breaking changes documented
- [ ] Version bump follows SemVer
- [ ] Commit message follows conventional format

## Monitoring

- **GitHub Actions**: https://github.com/extended-data-library/logging/actions
- **PyPI Releases**: https://pypi.org/project/lifecyclelogging/#history
- **GitHub Releases**: https://github.com/extended-data-library/logging/releases

---

For more details, see:
- [Python Semantic Release Documentation](https://python-semantic-release.readthedocs.io/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Semantic Versioning](https://semver.org/)
