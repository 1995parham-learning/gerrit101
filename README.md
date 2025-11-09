<div align="center">
  <h1>Gerrit Presentations</h1>

  <img alt="GitHub Actions Workflow Status" src="https://img.shields.io/github/actions/workflow/status/1995parham-learning/gerrit101/deploy.yml?style=for-the-badge">
</div>

## About Marp

[Marp](https://marp.app) (Markdown Presentation Ecosystem) is a tool that allows you to create presentation slides using Markdown syntax. This repository provides a complete setup for creating and deploying Marp presentations to GitHub Pages.

## Project Structure

```text
.
├── slides/              # Your presentation markdown files
│   └── example.md       # Example presentation
├── themes/              # Custom Marp themes
│   └── custom.css       # Custom theme example
├── assets/              # Static assets
│   └── images/          # Images for your presentations
├── dist/                # Built HTML files (generated)
├── .github/
│   └── workflows/
│       └── deploy.yml   # GitHub Actions deployment workflow
└── package.json         # NPM dependencies and scripts
```

## Resources

- [Marp Official Documentation](https://marpit.marp.app/)
- [Marp CLI](https://github.com/marp-team/marp-cli)
- [Markdown Guide](https://www.markdownguide.org/)
- [Marp Themes](https://github.com/marp-team/marp-core/tree/main/themes)
