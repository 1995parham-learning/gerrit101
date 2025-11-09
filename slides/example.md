---
marp: true
theme: custom
paginate: true
header: 'My Presentation'
footer: 'Created with Marp'
---

<!-- _class: invert -->

# Welcome to Marp

Your Markdown Presentation Ecosystem

---

## What is Marp?

- **Markdown-based** presentation tool
- Create beautiful slides with simple syntax
- Export to HTML, PDF, and PowerPoint
- Perfect for GitHub Pages deployment

---

## Key Features

1. Easy to write and maintain
2. Version control friendly
3. Customizable themes
4. Code syntax highlighting
5. Math expressions support

---

## Code Example

Here's some code with syntax highlighting:

```python
def hello_world():
    print("Hello from Marp!")
    return True
```

```javascript
const greet = () => {
  console.log("Marp is awesome!");
};
```

---

## Lists and Formatting

- **Bold text** for emphasis
- *Italic text* for subtle emphasis
- `Inline code` for technical terms
- [Links](https://marp.app) work too!

> Blockquotes are great for highlighting important information

---

## Two-Column Layout

<div style="display: flex; gap: 2rem;">
<div style="flex: 1;">

### Left Column
- Point 1
- Point 2
- Point 3

</div>
<div style="flex: 1;">

### Right Column
- Feature A
- Feature B
- Feature C

</div>
</div>

---

## Images

You can add images easily:

![width:400px](https://via.placeholder.com/400x300?text=Your+Image+Here)

Place your images in the `assets/images/` folder!

---

## Math Expressions

Marp supports LaTeX math expressions:

Inline math: $E = mc^2$

Block math:
$$
\frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$

---

<!-- _class: invert -->

## Getting Started

1. Edit markdown files in the `slides/` directory
2. Run `npm run build` to generate HTML
3. Push to GitHub and enable GitHub Pages
4. Your presentation is live!

---

## Customization

- Create custom themes in `themes/` directory
- Add your own CSS styles
- Use HTML for advanced layouts
- Configure in the front matter

```yaml
---
marp: true
theme: your-custom-theme
---
```

---

<!-- _class: invert -->

# Thank You!

Questions?

---

## Additional Resources

- [Marp Official Site](https://marp.app)
- [Marp CLI Documentation](https://github.com/marp-team/marp-cli)
- [Markdown Guide](https://www.markdownguide.org)

**Happy Presenting!** 🎉
