# Reusable LaTeX CI Workflow

This repository provides a reusable GitHub Actions workflow for building LaTeX documents with automatic package management, caching, and release automation.

## Features

- 🚀 **Automatic TeX Live installation** with configurable schemes (basic, minimal, full)
- 📦 **Smart package detection** and on-demand installation for biber, glossaries, minted, and tikz externalization
- ⚡ **Intelligent caching** of TeX Live installation and packages for faster builds
- 🎯 **Multi-engine support** (pdflatex, xelatex, lualatex)
- 📋 **GitHub Releases** with automatic tagging and PDF artifacts
- 🌿 **Releases branch** for easy PDF access and history
- 🔧 **Highly configurable** inputs for different project needs
- 🛠️ **Build artifact support** for debugging

## Quick Start

To use this reusable workflow in your LaTeX project, create a workflow file in your repository at `.github/workflows/latex-ci.yml`:

```yaml
name: Build LaTeX Documents

on:
  push:
    branches: [main, release]
  pull_request:
    branches: [main]

jobs:
  build:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    permissions:
      contents: write
    with:
      entry_tex: "main.tex"
      engine: "pdflatex"
```

## Configuration Options

### Basic Configuration

| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `entry_tex` | Main LaTeX file to compile | `main.tex` | No |
| `engine` | LaTeX engine (`pdflatex`, `xelatex`, `lualatex`) | `pdflatex` | No |
| `working_directory` | Directory containing LaTeX files | `.` | No |

### TeX Live Configuration

| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `texlive_scheme` | TeX Live scheme (`basic`, `minimal`, `full`) | `basic` | No |
| `texlive_mirror` | TeX Live mirror URL | `https://mirror.ctan.org/systems/texlive/tlnet` | No |
| `cache_key_suffix` | Additional suffix for cache key | `""` | No |

### Release Configuration

| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `create_release` | Whether to create GitHub releases | `true` | No |
| `release_tag_prefix` | Prefix for release tags | `rel` | No |
| `push_to_releases_branch` | Push PDFs to releases branch | `true` | No |
| `releases_branch_name` | Name of the releases branch | `releases` | No |

### Build Configuration

| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `timeout_minutes` | Job timeout in minutes | `30` | No |
| `artifact_name` | Name for PDF artifacts | `latex-pdfs` | No |
| `keep_build_artifacts` | Upload build logs for debugging | `false` | No |

## Outputs

The workflow provides these outputs that you can use in subsequent jobs:

| Output | Description |
|--------|-------------|
| `pdf_files` | Space-separated list of generated PDF files |
| `release_tag` | Created release tag (if `create_release` is true) |
| `release_url` | URL of the created release |

### Using Outputs Example

```yaml
jobs:
  build:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "main.tex"
    permissions:
      contents: write

  notify:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Print release info
        run: |
          echo "Generated PDFs: ${{ needs.build.outputs.pdf_files }}"
          echo "Release tag: ${{ needs.build.outputs.release_tag }}"
          echo "Release URL: ${{ needs.build.outputs.release_url }}"
```

## Advanced Examples

### Multi-Document Project

```yaml
name: Build All Documents

on:
  push:
    branches: [main]

jobs:
  build-thesis:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "thesis.tex"
      working_directory: "thesis"
      artifact_name: "thesis-pdf"
      release_tag_prefix: "thesis"
    permissions:
      contents: write

  build-slides:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "slides.tex"
      working_directory: "slides"
      artifact_name: "slides-pdf"
      create_release: false  # Only release thesis
    permissions:
      contents: write
```

### XeLaTeX with Full TeX Live

```yaml
jobs:
  build:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "document.tex"
      engine: "xelatex"
      texlive_scheme: "full"  # For projects with many packages
      timeout_minutes: 60     # Longer timeout for full install
    permissions:
      contents: write
```

### Development Build with Debugging

```yaml
jobs:
  build:
    uses: AlaCodon/latexonfly/.github/workflows/latex-ci.yml@main
    with:
      entry_tex: "main.tex"
      create_release: false          # No releases for dev builds
      push_to_releases_branch: false
      keep_build_artifacts: true     # Keep logs for debugging
      cache_key_suffix: "-dev"       # Separate cache for dev
    permissions:
      contents: write
```

## Automatic Package Detection

The workflow automatically detects and installs packages for:

- **biblatex**: Detects `\usepackage{biblatex}` and installs biber
- **glossaries**: Detects glossaries packages and installs xindy, makeindex
- **minted**: Detects minted package and installs Pygments
- **tikz externalization**: Detects tikz externalization and enables shell-escape

## Caching Strategy

The workflow uses intelligent caching to speed up builds:

- TeX Live installation is cached based on the scheme and source file hashes
- Cache keys include the working directory and file patterns
- Use `cache_key_suffix` to create separate caches for different configurations

## Permissions

The workflow requires `contents: write` permission to:
- Create GitHub releases
- Push to the releases branch
- Upload artifacts

Make sure to include this in your workflow:

```yaml
permissions:
  contents: write
```

## Troubleshooting

### Build Fails with Missing Packages

1. Set `keep_build_artifacts: true` to see the build logs
2. Consider using `texlive_scheme: "full"` for complex documents
3. Check if your document uses packages not detected automatically

### Cache Issues

1. Use a unique `cache_key_suffix` to invalidate the cache
2. Check that your file patterns in `working_directory` are correct
3. Cache size is limited; consider using `texlive_scheme: "basic"` for most projects

### Timeout Issues

1. Increase `timeout_minutes` for complex documents or full TeX Live installs
2. Use caching effectively to reduce build times
3. Consider splitting large projects into multiple workflows

## Contributing

To improve this reusable workflow:

1. Fork this repository
2. Create a feature branch
3. Test your changes with the example workflows
4. Submit a pull request

## License

This workflow is provided under the same license as the repository. See the repository's license file for details.
