# Changelog

All notable changes to this reusable LaTeX CI workflow will be documented in this file.

## [2.0.0] - 2024-12-19

### Added
- **Reusable workflow**: Converted the original workflow into a reusable GitHub Actions workflow that can be called from other repositories
- **Comprehensive input configuration**: 15+ configurable inputs for customizing build behavior
- **Output support**: Workflow outputs for PDF files, release tags, and release URLs
- **Multi-scheme TeX Live support**: Configurable TeX Live schemes (basic, minimal, full)
- **Enhanced caching**: Improved caching strategy with configurable cache keys
- **Artifact management**: Configurable artifact names and optional build artifact retention
- **Working directory support**: Ability to specify different working directories for LaTeX files
- **Comprehensive documentation**: Detailed README with examples and troubleshooting guide

### Changed
- **Breaking**: Original workflow moved to `main.yml.deprecated`
- **Improved**: Better error handling and robustness
- **Enhanced**: More flexible release and branch management

### Migration Guide

**Before** (original workflow):
```yaml
# Repository-specific workflow
name: LaTeX CI
on:
  push:
    branches: [release]
# ... rest of workflow
```

**After** (reusable workflow):
```yaml
# Can be used from any repository
name: Build LaTeX Documents
on:
  push:
    branches: [main, release]
jobs:
  build:
    uses: AlaCodon/tex-quantum_memory/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "main.tex"
      engine: "pdflatex"
    permissions:
      contents: write
```

## [1.0.0] - 2024-12-19

### Added
- Initial LaTeX CI workflow with TeX Live installation
- Automatic package detection for biber, glossaries, minted, and tikz
- PDF generation and GitHub releases
- Releases branch for PDF history
- TeX Live caching for performance
