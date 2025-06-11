# demo_docs
This is demo documentation repo using MkDocs
Absolutely! Here's the **entire `README.md` file content** you can directly copy and paste into your project — no markdown wrappers around it this time, just the **raw content** of the file:

---

# 📘 Servv WordPress Plugin Documentation

This repository contains the open-source documentation for the **Servv Event Booking WordPress Plugin**, built using [MkDocs](https://www.mkdocs.org/) and the [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme.

---

## 🚀 Features

* 📄 Markdown-based documentation
* 🎨 Modern UI with Material theme
* 🌐 Hosted via GitHub Pages
* 🤖 Supports GitHub Copilot-assisted authoring
* 🔍 Search, navigation, and responsive layout out of the box

---

## 📦 Project Structure

```
servv-wordpress-docs/
├── docs/
│   ├── index.md               # Home page
│   ├── getting-started.md     # Installation & Setup
│   ├── events-widget.md       # Embedding widget
│   └── api-reference.md       # Developer API reference
├── mkdocs.yml                 # MkDocs config file
└── README.md                  # This file
```

---

## ✅ Setup Instructions

### 1. Install Requirements

Ensure you have Python ≥ 3.7 and `pip` installed.

```bash
pip install mkdocs mkdocs-material
```

---

### 2. Clone the Repo

```bash
git clone https://github.com/YOUR_USERNAME/servv-wordpress-docs.git
cd servv-wordpress-docs
```

---

### 3. Run Locally

To preview the documentation site on your machine:

```bash
mkdocs serve
```

Then visit: [http://127.0.0.1:8000](http://127.0.0.1:8000)

---

### 4. Edit Content (with GitHub Copilot)

Use GitHub Copilot in VS Code to quickly draft markdown content by prompting it.

Example markdown file prompt:

```markdown
# Getting Started
<!-- Prompt Copilot -->
Write installation steps for the Servv WordPress plugin with screenshots.
```

---

### 5. Customize Theme

This project uses the **Material for MkDocs** theme. You can configure it via `mkdocs.yml`:

```yaml
site_name: Servv WordPress Docs
theme:
  name: material
  palette:
    primary: blue
    accent: cyan
nav:
  - Home: index.md
  - Getting Started: getting-started.md
  - Events Widget: events-widget.md
  - API Reference: api-reference.md
```

To switch to another theme like `readthedocs`, update:

```yaml
theme:
  name: readthedocs
```

---

## 🌍 Deploy to GitHub Pages

Deploy the site to GitHub Pages (creates `gh-pages` branch):

```bash
mkdocs gh-deploy
```

You can now access your documentation at:

```
https://YOUR_USERNAME.github.io/servv-wordpress-docs/
```

---

## 🌐 Optional: Custom Domain

1. Add a file at `docs/CNAME` with your custom domain:

   ```
   docs.servv.ai
   ```

2. Update DNS records:

   * Add a **CNAME** record pointing to `YOUR_USERNAME.github.io`

3. GitHub will auto-configure HTTPS and serve your site at the custom domain.

---

## 🤝 Contributing

We welcome improvements to the docs!
Please submit pull requests for:

* Typos, grammar
* Better examples or screenshots
* New sections like troubleshooting or FAQ

---





