# LaTeX Build Action

**A comprehensive GitHub Action for building LaTeX documents with automatic package management, intelligent caching, and release automation.**

## 🚀 Key Features

- **Zero-configuration setup** - Works out of the box with any LaTeX project
- **Smart package detection** - Automatically installs biblatex, minted, glossaries, and TikZ packages
- **Multi-engine support** - Compatible with pdflatex, xelatex, and lualatex
- **Intelligent caching** - Dramatically speeds up builds by caching TeX Live installation
- **Automatic releases** - Creates GitHub releases with tagged PDF artifacts
- **Flexible configuration** - 14+ input parameters for customization
- **Production ready** - Used in production with comprehensive error handling

## 📖 Quick Start

Add this to your `.github/workflows/latex.yml`:

```yaml
name: Build LaTeX
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v5.0.0
      - uses: AlaCodon/latexonfly@main
        with:
          entry_tex: "main.tex"
```

## 🎯 Perfect For

- Academic papers and theses
- Technical documentation
- Presentations and slides
- Books and reports
- Any LaTeX project requiring automated builds

## 🔧 Advanced Configuration

```yaml
- uses: AlaCodon/latexonfly@main
  with:
    entry_tex: "thesis.tex"
    engine: "xelatex"
    texlive_scheme: "full"
    create_release: true
    release_tag_prefix: "v"
    keep_build_artifacts: true
```

## 📊 Outputs

The action provides outputs you can use in subsequent steps:
- `pdf_files` - List of generated PDF files
- `release_tag` - Created release tag
- `release_url` - URL of the created release

---

**Made with ❤️ for the LaTeX community**
