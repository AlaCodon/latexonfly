# LaTeX Build Action

This repository provides a GitHub Action for building LaTeX documents with automatic package management and caching.

## 📦 Using as a GitHub Action

You can use this as a GitHub Action in your workflow for basic LaTeX compilation:

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
    steps:
      - name: Checkout repository
        uses: actions/checkout@v5.0.0

      - name: Build LaTeX documents
        uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "main.tex"
          engine: "pdflatex"
```

## Features

- 🚀 **Automatic TeX Live installation** with configurable schemes (basic, minimal, full)
- 📦 **Smart package detection** and on-demand installation for biber, glossaries, minted, and tikz externalization
- ⚡ **Intelligent caching** of TeX Live installation and packages for faster builds
- 🎯 **Multi-engine support** (pdflatex, xelatex, lualatex)
- 🔧 **Configurable** inputs for different project needs
- 🛠️ **Package debugging** support

> **Note**: For advanced features like GitHub releases and custom artifact management, see the [full-featured example](examples/full-featured-workflow.yml).

## Advanced Examples

### Using as GitHub Action with Outputs

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5.0.0

      - name: Build LaTeX
        id: latex
        uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "main.tex"
          engine: "pdflatex"

      - name: Use outputs
        run: |
          echo "Generated PDFs: ${{ steps.latex.outputs.pdf_files }}"
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

### Build Configuration

| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `timeout_minutes` | Job timeout in minutes | `30` | No |
| `cache_key_suffix` | Additional suffix for cache key | `""` | No |
| `keep_build_deps` | Upload build deps for debugging | `false` | No |

## Outputs

The action provides this output that you can use in subsequent steps:

| Output | Description |
|--------|-------------|
| `pdf_files` | Space-separated list of generated PDF files |

### Using Outputs Example

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5.0.0

      - name: Build LaTeX
        id: latex
        uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "main.tex"

      - name: Use outputs
        run: |
          echo "Generated PDFs: ${{ steps.latex.outputs.pdf_files }}"
```
```

## Advanced Examples

## Advanced Examples

For more complex use cases, see the [examples directory](examples/):

- **[Full-Featured Workflow](examples/full-featured-workflow.yml)**: Complete example with release automation
- **[Multi-Document Project](examples/multi-document-project.yml)**: Building multiple documents in parallel
- **[Development vs Production](examples/development-vs-production.yml)**: Different configs for different environments
- **[Complex Academic Document](examples/complex-document-with-bibliography.yml)**: Academic papers with submission packages

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

1. Set `keep_build_deps: "true"` to see the installed TeX packages list
2. Consider using `texlive_scheme: "full"` for complex documents
3. Check if your document uses packages not detected automatically
4. For release automation and additional features, see the [full-featured workflow example](examples/full-featured-workflow.yml)

### Cache Issues

1. Use a unique `cache_key_suffix` to invalidate the cache
2. Check that your file patterns in `working_directory` are correct
3. Cache size is limited; consider using `texlive_scheme: "basic"` for most projects

### Timeout Issues

1. Increase `timeout_minutes` for complex documents or full TeX Live installs
2. Use caching effectively to reduce build times
3. Consider splitting large projects into multiple workflows

## Contributing

To improve this action and reusable workflow:

1. Fork this repository
2. Create a feature branch
3. Test your changes with the example workflows
4. Submit a pull request

## GitHub Actions Marketplace

This action is available on the GitHub Actions Marketplace as `AlaCodon/latexonfly`. You can use it in your workflows by referencing:

```yaml
- uses: AlaCodon/latexonfly@main
```

For production use, we recommend pinning to a specific version:

```yaml
- uses: AlaCodon/latexonfly@v1.0.0
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
