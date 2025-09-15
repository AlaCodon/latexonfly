# Example: Using as GitHub Action

This example shows how to use the LaTeX Build Action in your repository.

## Basic Usage

Create `.github/workflows/latex.yml` in your repository:

```yaml
name: Build LaTeX Documents

on:
  push:
    branches: [main, release]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v5.0.0

      - name: Build LaTeX documents
        uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "main.tex"
          engine: "pdflatex"
          create_release: true
          release_tag_prefix: "v"
```

## Advanced Usage with Multiple Documents

```yaml
name: Build Multiple LaTeX Documents

on:
  push:
    branches: [main]

jobs:
  build-thesis:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v5.0.0

      - name: Build thesis
        uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "thesis.tex"
          working_directory: "thesis"
          engine: "xelatex"
          texlive_scheme: "full"
          artifact_name: "thesis-pdf"
          release_tag_prefix: "thesis"

  build-slides:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v5.0.0

      - name: Build slides
        uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "slides.tex"
          working_directory: "slides"
          engine: "pdflatex"
          create_release: false  # Only release thesis
          artifact_name: "slides-pdf"
```

## Using Outputs

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v5.0.0

      - name: Build LaTeX
        id: latex
        uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "document.tex"

      - name: Process outputs
        run: |
          echo "Generated PDFs: ${{ steps.latex.outputs.pdf_files }}"
          echo "Release tag: ${{ steps.latex.outputs.release_tag }}"
          echo "Release URL: ${{ steps.latex.outputs.release_url }}"

      - name: Notify success
        if: success()
        run: |
          echo "LaTeX compilation successful!"
          echo "PDFs available at: ${{ steps.latex.outputs.release_url }}"
```

## Development Build (No Releases)

```yaml
jobs:
  dev-build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5.0.0

      - name: Build for development
        uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "main.tex"
          create_release: false
          push_to_releases_branch: false
          keep_build_artifacts: true  # For debugging
          cache_key_suffix: "-dev"
```

## Complex Document with Bibliography

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v5.0.0

      - name: Build with bibliography
        uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "paper.tex"
          engine: "pdflatex"
          texlive_scheme: "basic"  # biblatex detected automatically
          timeout_minutes: 45
```

## All Available Inputs

```yaml
- name: Build LaTeX with all options
  uses: AlaCodon/latexonfly@main
  with:
    # Document configuration
    entry_tex: "main.tex"                    # Main LaTeX file
    engine: "pdflatex"                       # pdflatex, xelatex, lualatex
    working_directory: "."                   # Directory with LaTeX files

    # TeX Live configuration
    texlive_scheme: "basic"                  # basic, minimal, full
    texlive_mirror: "https://mirror.ctan.org/systems/texlive/tlnet"
    cache_key_suffix: ""                     # For custom cache keys
    timeout_minutes: "30"                    # Job timeout

    # Release configuration
    create_release: "true"                   # Create GitHub releases
    release_tag_prefix: "rel"               # Prefix for release tags
    push_to_releases_branch: "true"         # Push to releases branch
    releases_branch_name: "releases"        # Name of releases branch

    # Artifact configuration
    artifact_name: "latex-pdfs"            # Name for PDF artifacts
    keep_build_deps: "false"                # Upload TeX package list
    keep_build_artifacts: "false"           # Upload build logs
```
