# examples/README.md
# Example Workflows

This directory contains example workflows demonstrating different use cases for the reusable LaTeX CI workflow.

## Available Examples

### 1. Multi-Document Project (`multi-document-project.yml`)
**Use case**: Projects with multiple documents (thesis, slides, poster)
- Builds multiple documents in parallel
- Different engines and configurations per document
- Optional combined release with all PDFs
- Separate release branches for different document types

### 2. Development vs Production (`development-vs-production.yml`)
**Use case**: Different behavior for development and production builds
- Development: Fast builds, debugging artifacts, no releases
- Production: Full TeX Live, official releases, optimized builds
- Branch-based conditional logic

### 3. Complex Academic Document (`complex-document-with-bibliography.yml`)
**Use case**: Academic papers with bibliography, supplementary materials
- Full TeX Live installation for complex documents
- Separate supplementary materials build
- Submission package creation with combined artifacts
- Journal submission workflow

## How to Use These Examples

1. **Copy the example** that matches your use case
2. **Rename** the file to something appropriate (e.g., `latex-ci.yml`)
3. **Place it** in your repository's `.github/workflows/` directory
4. **Customize** the inputs to match your project structure
5. **Test** with a commit to verify it works as expected

## Common Customizations

### File and Directory Structure
```yaml
with:
  entry_tex: "your-main-file.tex"      # Change this to your main file
  working_directory: "docs"            # If LaTeX files are in a subdirectory
```

### Engine Selection
```yaml
with:
  engine: "pdflatex"    # Most common, fast
  engine: "xelatex"     # For Unicode and system fonts
  engine: "lualatex"    # For advanced typography
```

### TeX Live Configuration
```yaml
with:
  texlive_scheme: "basic"    # Fast install, most packages
  texlive_scheme: "minimal"  # Minimal install, manual packages
  texlive_scheme: "full"     # Complete install, all packages
```

### Release Configuration
```yaml
with:
  create_release: true               # Enable/disable releases
  release_tag_prefix: "v"            # Tag prefix (v1.0, paper-v1.0, etc.)
  push_to_releases_branch: true      # Enable/disable releases branch
  releases_branch_name: "releases"   # Custom branch name
```

## Combining Examples

You can combine patterns from different examples. For instance:

```yaml
# Combined: Multi-document + Development/Production
name: Advanced LaTeX CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  # Development builds (fast, no releases)
  dev-thesis:
    if: github.ref != 'refs/heads/main'
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "thesis.tex"
      working_directory: "thesis"
      texlive_scheme: "basic"
      create_release: false
      keep_build_artifacts: true
    permissions:
      contents: write

  # Production builds (full features)
  prod-thesis:
    if: github.ref == 'refs/heads/main'
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "thesis.tex"
      working_directory: "thesis"
      texlive_scheme: "full"
      engine: "xelatex"
      create_release: true
    permissions:
      contents: write
```

## Need Help?

- Check the main [README.md](../README.md) for complete documentation
- Look at the [MIGRATION.md](../MIGRATION.md) for migration from old workflows
- Review the [troubleshooting section](../README.md#troubleshooting) for common issues
