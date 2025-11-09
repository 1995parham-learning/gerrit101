# Marp Presentations

Create beautiful presentations using Markdown and deploy them to GitHub Pages automatically!

## About Marp

[Marp](https://marp.app) (Markdown Presentation Ecosystem) is a tool that allows you to create presentation slides using Markdown syntax. This repository provides a complete setup for creating and deploying Marp presentations to GitHub Pages.

## Project Structure

```
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

## Getting Started

### Prerequisites

- Node.js (v16 or higher)
- npm or yarn

### Installation

1. Clone this repository
2. Install dependencies:

```bash
npm install
```

### Creating Presentations

1. Create a new Markdown file in the `slides/` directory
2. Add the Marp front matter at the top:

```markdown
---
marp: true
theme: default
paginate: true
---

# Your First Slide

Content goes here...

---

# Second Slide

More content...
```

### Available Commands

- `npm run build` - Build HTML files from Markdown slides
- `npm run build:pdf` - Build PDF files from Markdown slides
- `npm run watch` - Watch for changes and rebuild automatically
- `npm run serve` - Start a local server to preview your slides

## Marp Syntax Guide

### Basic Slide Structure

Slides are separated by `---`:

```markdown
# Slide 1

---

# Slide 2
```

### Front Matter

Configure your presentation at the top of each file:

```yaml
---
marp: true
theme: default
paginate: true
header: 'My Presentation'
footer: 'Author Name'
---
```

### Special Classes

- `<!-- _class: invert -->` - Invert colors for a single slide
- `<!-- _class: lead -->` - Center content (title slide)

### Code Blocks

```python
def hello():
    print("Hello, Marp!")
```

### Math

Inline: $E = mc^2$

Block:
$$
\frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$

### Images

```markdown
![width:400px](assets/images/your-image.png)
```

## Custom Themes

Create custom themes in the `themes/` directory:

1. Create a CSS file (e.g., `themes/mytheme.css`)
2. Start with: `/* @theme mytheme */`
3. Import base theme: `@import 'default';`
4. Add your custom styles
5. Use in your slides:

```yaml
---
marp: true
theme: mytheme
---
```

## GitHub Pages Deployment

This repository is configured to automatically deploy to GitHub Pages using GitHub Actions.

### Setup

1. Go to your repository Settings
2. Navigate to Pages (in the left sidebar)
3. Under "Build and deployment":
   - Source: Select "GitHub Actions"
4. Push to the main/master branch to trigger deployment

The workflow will:
- Install dependencies
- Build your slides to HTML
- Generate an index page listing all presentations
- Deploy to GitHub Pages

Your presentations will be available at: `https://[username].github.io/[repository-name]/`

### Adding New Presentations

1. Create a new `.md` file in the `slides/` directory
2. After building, update the index.html (or modify the workflow to auto-generate it)
3. Commit and push

## Local Development

### Preview Locally

```bash
# Start development server
npm run serve
```

Visit `http://localhost:8080` to see your presentations.

### Build Locally

```bash
# Build HTML
npm run build

# Build PDF
npm run build:pdf
```

Built files will be in the `dist/` directory.

## Tips and Tricks

### Two-Column Layout

```html
<div style="display: flex; gap: 2rem;">
<div style="flex: 1;">

Column 1 content

</div>
<div style="flex: 1;">

Column 2 content

</div>
</div>
```

### Background Images

```markdown
![bg](assets/images/background.jpg)
```

### Scoped Styles

```html
<style scoped>
section {
  background: linear-gradient(to bottom, #667eea 0%, #764ba2 100%);
}
</style>
```

## Resources

- [Marp Official Documentation](https://marpit.marp.app/)
- [Marp CLI](https://github.com/marp-team/marp-cli)
- [Markdown Guide](https://www.markdownguide.org/)
- [Marp Themes](https://github.com/marp-team/marp-core/tree/main/themes)

## License

MIT

## Contributing

Feel free to submit issues or pull requests to improve this template!

---

Happy presenting! 🎉
