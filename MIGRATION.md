# Migration Guide: From Repository-Specific to Reusable Workflow

This guide helps you migrate from the old repository-specific LaTeX CI workflow to the new reusable workflow.

## What Changed

The LaTeX CI workflow has been converted from a repository-specific workflow to a **reusable GitHub Actions workflow**. This means:

- ✅ **You can now use this workflow from any repository**
- ✅ **No need to copy/paste workflow code**
- ✅ **Centralized updates and improvements**
- ✅ **Highly configurable for different projects**

## Migration Steps

### Step 1: Update Your Workflow File

**Old workflow** (repository-specific):
```yaml
# .github/workflows/main.yml
name: LaTeX CI (pure TeX Live, minimal, cached, on-demand)

on:
  push:
    branches:
      - release

permissions:
  contents: write

# ... 250+ lines of workflow code
```

**New workflow** (reusable):
```yaml
# .github/workflows/latex-ci.yml
name: Build LaTeX Documents

on:
  push:
    branches:
      - main
      - release
  pull_request:
    branches:
      - main

jobs:
  build:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    permissions:
      contents: write
    with:
      entry_tex: "main.tex"
      engine: "pdflatex"
      create_release: true
      push_to_releases_branch: true
```

### Step 2: Configure for Your Project

Customize the `with:` section based on your project needs:

```yaml
with:
  # Basic configuration
  entry_tex: "your-main-file.tex"        # Your main LaTeX file
  engine: "pdflatex"                      # or "xelatex", "lualatex"
  working_directory: "docs"               # if LaTeX files are in a subdirectory

  # Release settings
  create_release: true                    # Set to false for draft projects
  release_tag_prefix: "v"                # Change prefix if needed
  push_to_releases_branch: true          # Keep PDF history
  releases_branch_name: "releases"       # Custom branch name

  # Advanced settings
  texlive_scheme: "basic"                # or "minimal", "full"
  timeout_minutes: 30                    # Increase for complex documents
  artifact_name: "my-project-pdfs"       # Custom artifact name
```

### Step 3: Update Triggers (Optional)

The old workflow only triggered on `release` branch. You might want to update triggers:

```yaml
on:
  push:
    branches:
      - main          # Build on main branch
      - release       # Keep existing release branch
      - develop       # Add other branches as needed
  pull_request:
    branches:
      - main          # Build PRs to validate changes
```

## Feature Mapping

| Old Workflow Feature | New Workflow Input | Notes |
|---------------------|-------------------|-------|
| `ENTRY_TEX: main.tex` | `entry_tex: "main.tex"` | Now configurable per call |
| `ENGINE: pdflatex` | `engine: "pdflatex"` | Supports pdflatex, xelatex, lualatex |
| Branch: `release` | Configure in `on:` section | Now flexible |
| TeX Live scheme-basic | `texlive_scheme: "basic"` | Now supports basic, minimal, full |
| Release creation | `create_release: true` | Can be disabled |
| Releases branch | `push_to_releases_branch: true` | Can be disabled or renamed |

## Advanced Migration Examples

### Multiple Documents

**Old**: Required separate workflows or manual modification
**New**: Easy parallel builds

```yaml
jobs:
  build-thesis:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "thesis.tex"
      working_directory: "thesis"
      release_tag_prefix: "thesis"
    permissions:
      contents: write

  build-slides:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "slides.tex"
      working_directory: "slides"
      create_release: false  # Only release thesis
    permissions:
      contents: write
```

### Complex Document with Full TeX Live

**Old**: Required manual modification of scheme
**New**: Simple configuration

```yaml
jobs:
  build:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "complex-document.tex"
      texlive_scheme: "full"      # Full TeX Live installation
      timeout_minutes: 60         # Longer timeout for full install
      engine: "xelatex"          # Different engine
    permissions:
      contents: write
```

### Development vs. Production

**Old**: Required branch-based logic in single workflow
**New**: Separate workflows with different configurations

```yaml
# .github/workflows/dev-build.yml
name: Development Build
on:
  push:
    branches: [develop]
  pull_request:

jobs:
  build:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "main.tex"
      create_release: false          # No releases for dev
      push_to_releases_branch: false
      keep_build_artifacts: true     # Debug info
    permissions:
      contents: write

---
# .github/workflows/production-build.yml
name: Production Build
on:
  push:
    branches: [main, release]

jobs:
  build:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "main.tex"
      create_release: true           # Create releases
      release_tag_prefix: "v"        # Version tags
    permissions:
      contents: write
```

## Benefits of Migration

1. **Reduced Maintenance**: Updates to the workflow happen centrally
2. **Better Configuration**: 15+ inputs vs. hardcoded values
3. **Multi-Project Support**: Use the same workflow across repositories
4. **Enhanced Features**: Better caching, artifacts, debugging support
5. **Future-Proof**: New features added automatically

## Troubleshooting Migration

### Workflow Not Found
```
Error: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main not found
```
**Solution**: Ensure you're using the correct repository path and the workflow file exists.

### Permission Denied
```
Error: Resource not accessible by integration
```
**Solution**: Add `permissions: contents: write` to your job.

### Different Behavior
If the new workflow behaves differently:
1. Check your input configuration
2. Enable `keep_build_artifacts: true` for debugging
3. Compare with the example configurations
4. Check the CHANGELOG.md for breaking changes

## Need Help?

- Check the [README.md](README.md) for comprehensive documentation
- Look at [example workflows](.github/workflows/example-usage.yml)
- Review the [troubleshooting section](README.md#troubleshooting) in the README
- Open an issue in this repository for bugs or feature requests
