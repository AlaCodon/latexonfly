# LaTeX Build Action

**A comprehensive GitHub Action for building LaTeX documents with automatic package management and intelligent caching.**

## 🚀 Key Features

- **Zero-configuration setup** - Works out of the box with any LaTeX project
- **Smart package detection** - Automatically installs biblatex, minted, glossaries, and TikZ packages
- **Multi-engine support** - Compatible with pdflatex, xelatex, and lualatex
- **Intelligent caching** - Dramatically speeds up builds by caching TeX Live installation
- **Flexible configuration** - 8 input parameters for customization
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
    keep_build_deps: "true"
```

## 📊 Outputs

The action provides this output you can use in subsequent steps:
- `pdf_files` - List of generated PDF files

---

**Made with ❤️ for the LaTeX community**
