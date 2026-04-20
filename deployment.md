# Deployment Guide

This guide covers how to build and deploy the AISee documentation using MkDocs and GitHub Pages.

## 💻 Local Development

To run the documentation locally:

1.  **Install dependencies**:
    ```bash
    pip install mkdocs-material pymdown-extensions
    ```

2.  **Start the server**:
    ```bash
    cd aisee-docs
    mkdocs serve
    ```

3.  **View the docs**: Open `http://127.0.0.1:8000` in your browser.

## 🚀 Automated Deployment

The documentation is automatically deployed to GitHub Pages via GitHub Actions whenever changes are pushed to the `main` branch.

### Workflow Configuration
The deployment logic is defined in `.github/workflows/deploy-docs.yml`.

### Deployment Steps
1.  **Trigger**: Push to `main` branch or manual trigger.
2.  **Build**: MkDocs builds the static site into the `site/` directory.
3.  **Publish**: The `gh-deploy` command pushes the static files to the `gh-pages` branch.

## ⚙️ Configuration (`mkdocs.yml`)

The `mkdocs.yml` file manages:
- **Theme**: Uses the Material theme with light/dark mode support.
- **Extensions**: Enabled support for code highlighting, task lists, and complex markdown.
- **Navigation**: Structured menu for easy access to API references and the user manual.
