# Release-Please Configurations

Production-ready [release-please](https://github.com/googleapis/release-please) configuration files for automated release management with semantic versioning.

## Installation

### Option 1: Direct Download

Download the configuration file for your project type and place it in your project root:

```bash
# Node.js project
curl -o release-please-config.json https://raw.githubusercontent.com/gfmio/config-release-please/main/release-please-config.node.json

# Python project
curl -o release-please-config.json https://raw.githubusercontent.com/gfmio/config-release-please/main/release-please-config.python.json

# Go project
curl -o release-please-config.json https://raw.githubusercontent.com/gfmio/config-release-please/main/release-please-config.go.json

# Rust project
curl -o release-please-config.json https://raw.githubusercontent.com/gfmio/config-release-please/main/release-please-config.rust.json

# Base template (language-agnostic)
curl -o release-please-config.json https://raw.githubusercontent.com/gfmio/config-release-please/main/release-please-config.base.json
```

Then create an empty manifest file:

```bash
echo '{}' > .release-please-manifest.json
```

### Option 2: Git Clone

```bash
# Clone the repository
git clone https://github.com/gfmio/config-release-please.git

# Copy to your project
cp config-release-please/release-please-config.node.json /path/to/your/project/release-please-config.json
echo '{}' > /path/to/your/project/.release-please-manifest.json
```

### Option 3: Git Submodule

```bash
# Add as a submodule
git submodule add https://github.com/gfmio/config-release-please.git .release-please-configs

# Create a symlink (Unix/macOS/Linux)
ln -s .release-please-configs/release-please-config.node.json release-please-config.json

# Or on Windows (requires admin)
mklink release-please-config.json .release-please-configs\release-please-config.node.json

# Create manifest
echo '{}' > .release-please-manifest.json
```

## What is Release-Please?

Release-please is an automated release management tool from Google that:

- **Automates semantic versioning** based on Conventional Commits
- **Maintains release PRs** that aggregate all pending changes
- **Generates changelogs** automatically from commit history
- **Creates GitHub releases and tags** when release PRs merge
- **Supports 19+ language ecosystems** (Node.js, Python, Go, Rust, Java, Ruby, PHP, etc.)
- **Handles monorepos** with multiple packages and independent versioning

**Benefits:**

- No manual changelog maintenance
- Consistent versioning across projects
- Clear release history
- Automated release notes
- Zero-config for common use cases

## What's Included

This repository provides production-ready configurations for different project types:

### Configuration Files

**[release-please-config.base.json](release-please-config.base.json)** - Base template

- Language-agnostic starting point
- Comprehensive changelog sections
- Pre-1.0 versioning support
- Customize `package-name` and add `release-type`

**[release-please-config.node.json](release-please-config.node.json)** - Node.js/JavaScript

- Optimized for `package.json` projects
- npm/yarn/pnpm compatible
- Works with TypeScript, React, Vue, Angular, etc.
- Monorepo support with node-workspace plugin

**[release-please-config.python.json](release-please-config.python.json)** - Python

- Supports `setup.py`, `pyproject.toml`, `setup.cfg`
- Compatible with Poetry, setuptools, flit
- Follows Python packaging conventions
- PEP 440 versioning

**[release-please-config.go.json](release-please-config.go.json)** - Go

- Semantic import versioning (v2, v3, etc.)
- Tag-based versioning (no version files)
- Go modules compatible
- Multi-module repository support

**[release-please-config.rust.json](release-please-config.rust.json)** - Rust

- Updates `Cargo.toml` version field
- Cargo workspace support
- Lockfile handling
- crates.io publishing workflow

### Changelog Sections

All configurations include well-organized changelog sections:

| Commit Type | Section | Visible | Notes |
|-------------|---------|---------|-------|
| `feat:` | Features | Yes | Minor version bump (0.x.0) |
| `fix:` | Bug Fixes | Yes | Patch version bump (0.0.x) |
| `perf:` | Performance Improvements | Yes | Patch version bump |
| `revert:` | Reverts | Yes | Depends on reverted commit |
| `refactor:` | Code Refactoring | Yes | No version bump (unless breaking) |
| `docs:` | Documentation | No | No version bump |
| `build:` | Build System | No | No version bump |
| `style:` | Styles | No | No version bump |
| `test:` | Tests | No | No version bump |
| `ci:` | Continuous Integration | No | No version bump |
| `chore:` | Miscellaneous Chores | No | No version bump |

**Note:** Commits with `BREAKING CHANGE:` or `!` suffix trigger major version bumps.

### Pre-1.0 Versioning

All configurations include pre-1.0 version handling:

```json
{
  "bump-minor-pre-major": true,
  "bump-patch-for-minor-pre-major": true
}
```

This means:

- `feat!:` or `BREAKING CHANGE:` → 0.x.0 (not 1.0.0)
- `feat:` → 0.0.x (not 0.x.0)
- `fix:` → 0.0.x

Remove these settings after releasing v1.0.0.

## Usage

### Basic Setup

1. **Add configuration to your repository:**

```bash
# Choose your language-specific config
curl -o release-please-config.json https://raw.githubusercontent.com/gfmio/config-release-please/main/release-please-config.node.json

# Create empty manifest
echo '{}' > .release-please-manifest.json
```

2. **Update the package name:**

Edit [release-please-config.json](release-please-config.json):

```json
{
  "packages": {
    ".": {
      "package-name": "your-package-name"
    }
  }
}
```

3. **Commit the configuration:**

```bash
git add release-please-config.json .release-please-manifest.json
git commit -m "chore: add release-please configuration"
git push
```

4. **Set up the GitHub Action:**

Create [.github/workflows/release-please.yml](.github/workflows/release-please.yml):

```yaml
name: Release Please

on:
  push:
    branches:
      - main

permissions:
  contents: write
  pull-requests: write

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
```

5. **Use Conventional Commits:**

```bash
git commit -m "feat: add new feature"
git commit -m "fix: resolve bug"
git commit -m "feat!: breaking change"
```

6. **Release-please creates a PR** with:
   - Version bump in package files
   - Updated CHANGELOG.md
   - All commits since last release

7. **Merge the release PR** to:
   - Create a GitHub release
   - Create a git tag
   - Publish release notes

### Conventional Commit Examples

```bash
# Patch version bump (0.0.x)
git commit -m "fix: correct calculation error"
git commit -m "fix(api): handle null response"

# Minor version bump (0.x.0)
git commit -m "feat: add user authentication"
git commit -m "feat(ui): add dark mode toggle"

# Major version bump (x.0.0)
git commit -m "feat!: redesign API structure"
git commit -m "fix!: remove deprecated method"

# Breaking change with body
git commit -m "feat: update authentication

BREAKING CHANGE: auth tokens now expire after 1 hour"

# No version bump
git commit -m "docs: update README"
git commit -m "test: add unit tests"
git commit -m "chore: update dependencies"

# Custom version
git commit -m "fix: critical bug

Release-As: 1.0.0"
```

### Monorepo Configuration

For projects with multiple packages:

```json
{
  "$schema": "https://raw.githubusercontent.com/googleapis/release-please/main/schemas/config.json",
  "release-type": "node",
  "bump-minor-pre-major": true,
  "bump-patch-for-minor-pre-major": true,
  "changelog-sections": [
    {"type": "feat", "section": "Features"},
    {"type": "fix", "section": "Bug Fixes"}
  ],
  "packages": {
    "packages/api": {
      "package-name": "@myorg/api"
    },
    "packages/cli": {
      "package-name": "@myorg/cli"
    },
    "packages/shared": {
      "package-name": "@myorg/shared"
    }
  },
  "plugins": [
    {
      "type": "node-workspace",
      "merge": false
    }
  ]
}
```

With initial versions in [.release-please-manifest.json](.release-please-manifest.json):

```json
{
  "packages/api": "0.1.0",
  "packages/cli": "0.1.0",
  "packages/shared": "0.1.0"
}
```

## Customization

### Custom Changelog Sections

Show or hide specific commit types:

```json
{
  "changelog-sections": [
    {"type": "feat", "section": "Features", "hidden": false},
    {"type": "fix", "section": "Bug Fixes", "hidden": false},
    {"type": "deps", "section": "Dependencies", "hidden": false},
    {"type": "docs", "section": "Documentation", "hidden": true}
  ]
}
```

### Draft Releases

Create draft releases for review before publishing:

```json
{
  "packages": {
    ".": {
      "draft": true
    }
  }
}
```

### Prerelease Versions

For beta/alpha releases:

```json
{
  "packages": {
    ".": {
      "prerelease": true
    }
  }
}
```

Produces versions like `1.0.0-beta.1`.

### Custom PR Titles

```json
{
  "packages": {
    ".": {
      "pull-request-title-pattern": "chore${scope}: release${component} ${version}"
    }
  }
}
```

### Bootstrap from Specific Commit

Skip old history when setting up release-please:

```json
{
  "packages": {
    ".": {
      "bootstrap-sha": "abc123def456"
    }
  }
}
```

After the first release PR is created, you can remove `bootstrap-sha`.

### Update Custom Files

Update version in non-standard files:

```json
{
  "packages": {
    ".": {
      "extra-files": [
        "src/version.ts",
        {
          "type": "json",
          "path": "manifest.json",
          "jsonpath": "$.version"
        }
      ]
    }
  }
}
```

### Exclude Paths

Ignore specific directories from triggering releases:

```json
{
  "packages": {
    ".": {
      "exclude-paths": ["docs/**/*", "examples/**/*"]
    }
  }
}
```

## GitHub Action Configuration

### Basic Action

```yaml
name: Release Please

on:
  push:
    branches:
      - main

permissions:
  contents: write
  pull-requests: write

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
```

### With Publishing

Automatically publish packages when releases are created:

**Node.js/npm:**

```yaml
name: Release Please

on:
  push:
    branches:
      - main

permissions:
  contents: write
  pull-requests: write

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        id: release
        with:
          token: ${{ secrets.GITHUB_TOKEN }}

      - uses: actions/checkout@v4
        if: ${{ steps.release.outputs.release_created }}

      - uses: actions/setup-node@v4
        if: ${{ steps.release.outputs.release_created }}
        with:
          node-version: 20
          registry-url: 'https://registry.npmjs.org'

      - run: npm ci
        if: ${{ steps.release.outputs.release_created }}

      - run: npm publish
        if: ${{ steps.release.outputs.release_created }}
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

**Python/PyPI:**

```yaml
- uses: actions/setup-python@v5
  if: ${{ steps.release.outputs.release_created }}
  with:
    python-version: '3.x'

- run: |
    python -m pip install --upgrade pip
    pip install build twine
  if: ${{ steps.release.outputs.release_created }}

- run: python -m build
  if: ${{ steps.release.outputs.release_created }}

- run: twine upload dist/*
  if: ${{ steps.release.outputs.release_created }}
  env:
    TWINE_USERNAME: __token__
    TWINE_PASSWORD: ${{ secrets.PYPI_TOKEN }}
```

**Rust/crates.io:**

```yaml
- uses: actions/checkout@v4
  if: ${{ steps.release.outputs.release_created }}

- uses: dtolnay/rust-toolchain@stable
  if: ${{ steps.release.outputs.release_created }}

- run: cargo publish
  if: ${{ steps.release.outputs.release_created }}
  env:
    CARGO_REGISTRY_TOKEN: ${{ secrets.CARGO_TOKEN }}
```

### Multiple Branches

Support releases from different branches:

```yaml
name: Release Please

on:
  push:
    branches:
      - main
      - next
      - v1.x

permissions:
  contents: write
  pull-requests: write

jobs:
  release-please:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          target-branch: ${{ github.ref_name }}
```

## Language-Specific Details

### Node.js Projects

**Supports:**

- `package.json` version updates
- `package-lock.json` updates
- Scoped packages (`@org/package`)
- Monorepos with workspaces
- Private packages

**Plugin:** `node-workspace`

**Extra features:**

- Updates workspace dependencies
- Handles linked packages
- Supports npm, yarn, pnpm

### Python Projects

**Supports:**

- `setup.py` version updates
- `pyproject.toml` updates
- `setup.cfg` updates
- `__version__` in Python files

**Requirements:**

- Must set `package-name` (PyPI name)
- Follows PEP 440 versioning

**Example `pyproject.toml`:**

```toml
[project]
name = "my-package"
version = "0.1.0"
```

### Go Projects

**Supports:**

- Tag-based versioning (`v1.0.0`)
- Semantic import versioning (`/v2`, `/v3`)
- Multi-module repositories
- `go.mod` updates

**No version files needed** - Go uses git tags.

**Monorepo example:**

```json
{
  "packages": {
    "module1": {},
    "module2": {}
  }
}
```

### Rust Projects

**Supports:**

- `Cargo.toml` version updates
- Cargo workspaces
- `Cargo.lock` updates

**Plugin:** `cargo-workspace`

**Workspace example:**

```json
{
  "packages": {
    "crates/api": {},
    "crates/cli": {}
  },
  "plugins": [
    {"type": "cargo-workspace"}
  ]
}
```

## Best Practices

### 1. Use Conventional Commits

Enforce with commitlint:

```bash
npm install --save-dev @commitlint/cli @commitlint/config-conventional
```

[.commitlintrc.json](.commitlintrc.json):

```json
{
  "extends": ["@commitlint/config-conventional"]
}
```

### 2. Squash Merge Pull Requests

Configure GitHub to squash merge:

- Settings → General → Pull Requests
- Enable "Allow squash merging"
- Set "Default to squash title and commit details"

This keeps one commit per PR, making changelogs cleaner.

### 3. Write Descriptive Commit Messages

**Good:**

```
feat(auth): add OAuth2 support for Google and GitHub

Implements OAuth2 flow with PKCE for enhanced security.
Users can now sign in with their Google or GitHub accounts.
```

**Bad:**

```
feat: stuff
```

### 4. Use Scopes

Group related changes:

```bash
git commit -m "feat(api): add user endpoints"
git commit -m "fix(ui): resolve button alignment"
git commit -m "docs(readme): update installation steps"
```

### 5. Bootstrap Wisely

For existing projects with history:

1. Use `bootstrap-sha` to start from a specific commit
2. Set initial version in `.release-please-manifest.json`
3. After first release, remove `bootstrap-sha`

### 6. Test Changes

Review the release PR before merging:

- Check version bump is correct
- Review changelog entries
- Verify package files updated correctly

### 7. Protected Branches

Configure branch protection:

- Require PR reviews
- Require status checks
- Enable "Include administrators"

This prevents accidental direct pushes that bypass release-please.

### 8. Semantic Versioning

Understand version bumps:

- **Major (x.0.0):** Breaking changes - `feat!:` or `BREAKING CHANGE:`
- **Minor (0.x.0):** New features - `feat:`
- **Patch (0.0.x):** Bug fixes - `fix:`, `perf:`

Before v1.0.0, use pre-major settings to keep major at 0.

## Troubleshooting

### No Release PR Created

**Cause:** No releasable commits since last release.

**Solution:** Ensure commits use conventional format:

```bash
git log --oneline
# Should show: feat: ..., fix: ..., etc.
```

### Release PR Not Updating

**Cause:** Manual commits to release PR branch.

**Solution:** Don't commit directly to release PR branches. Close the PR and push new commits to main. Release-please will update automatically.

### Wrong Version Bump

**Cause:** Incorrect commit type.

**Examples:**

- Used `fix:` instead of `feat!:` for breaking change
- Used `chore:` instead of `fix:` for bug fix

**Solution:** Use correct commit types. For existing commits, can override with:

```
Release-As: 2.0.0
```

### Manifest Conflicts

**Cause:** Multiple people merged changes simultaneously.

**Solution:** The `.release-please-manifest.json` tracks versions. Resolve conflicts by keeping the higher version.

### Bootstrap Not Working

**Cause:** Incorrect `bootstrap-sha` or empty commit history.

**Solution:**

1. Verify SHA: `git log --oneline | grep <sha>`
2. Ensure commits after SHA use conventional format
3. Check GitHub Action logs for errors

### Changes Not in Changelog

**Cause:** Commit type is hidden in changelog sections.

**Solution:** Update `changelog-sections` to show the type:

```json
{
  "type": "docs",
  "section": "Documentation",
  "hidden": false
}
```

## FAQ

**Q: Do I need to manually update version numbers?**

A: No. Release-please updates versions automatically based on commits. Never manually edit version numbers.

**Q: Can I customize the changelog format?**

A: Yes, via `changelog-sections`. You can show/hide sections and rename them.

**Q: What if I need a specific version?**

A: Add `Release-As: x.y.z` to the commit message footer:

```
fix: critical bug

Release-As: 1.0.0
```

**Q: How do I skip a release?**

A: Don't merge the release PR. Continue making commits; release-please will accumulate changes.

**Q: Can I use this with private repositories?**

A: Yes. Release-please works with private repos. Just configure the GitHub Action.

**Q: Does this work with GitHub Enterprise?**

A: Yes. Use the `github-api-url` parameter in the action.

**Q: What about breaking changes before v1.0.0?**

A: Use `bump-minor-pre-major: true` to bump minor (0.x.0) instead of major (1.0.0) for breaking changes.

**Q: Can I have multiple release branches?**

A: Yes. Configure the action to run on multiple branches with `target-branch`.

**Q: Does this replace semantic-release?**

A: They serve similar purposes. Release-please is simpler and more GitHub-focused. Semantic-release has more plugins but is more complex.

**Q: What about monorepos?**

A: Fully supported. Configure multiple packages with plugins like `node-workspace`, `cargo-workspace`, etc.

## Resources

- [Release-Please Repository](https://github.com/googleapis/release-please)
- [Release-Please Documentation](https://github.com/googleapis/release-please/tree/main/docs)
- [Conventional Commits Specification](https://www.conventionalcommits.org/)
- [Semantic Versioning](https://semver.org/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Manifest Releaser Docs](https://github.com/googleapis/release-please/blob/main/docs/manifest-releaser.md)
- [Customizing Docs](https://github.com/googleapis/release-please/blob/main/docs/customizing.md)

## License

[MIT](LICENSE)

## Author

Frédérique Mittelstaedt

- GitHub: [@gfmio](https://github.com/gfmio)

## Acknowledgments

- The Google release-please team for creating this essential automation tool
- The Conventional Commits community for standardizing commit messages
- Contributors who help improve these configurations
