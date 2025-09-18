# Example: Using as GitHub Action

This example shows how to use the LaTeX Build Action in your repository.

> **Note**: For release automation and additional features, see the [full-featured workflow example](full-featured-workflow.yml).

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

  build-slides:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5.0.0

      - name: Build slides
        uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "slides.tex"
          working_directory: "slides"
          engine: "pdflatex"
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

      - name: Notify success
        if: success()
        run: |
          echo "LaTeX compilation successful!"
          echo "PDFs: ${{ steps.latex.outputs.pdf_files }}"
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
          keep_build_deps: "true"  # For debugging
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
          timeout_minutes: "45"
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

    # Build configuration
    keep_build_deps: "false"                # Upload TeX package list for debugging
```
