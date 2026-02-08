# Network Knowledge Base Template

A ready-to-use MkDocs template for building a searchable, keyword-friendly knowledge base for networking commands, configurations, and troubleshooting guides.

## Features

- ✅ **Full-text search** with advanced indexing
- ✅ **Tag-based organization** for easy discovery
- ✅ **Beautiful Material Design** theme with dark mode
- ✅ **Code syntax highlighting** with copy buttons
- ✅ **Mobile responsive** design
- ✅ **GitHub Pages ready** with auto-deployment
- ✅ **Question-Answer format** optimized for commands and outputs
- ✅ **Example content** to get you started

## Quick Start

### Prerequisites

```bash
# RHEL/CentOS/Fedora
sudo dnf install python3 python3-pip git

# Ubuntu/Debian
sudo apt install python3 python3-pip git
```

### Installation

```bash
# 1. Install MkDocs and Material theme
pip3 install mkdocs-material

# 2. Clone or download this template
git clone https://github.com/yourusername/network-kb.git
cd network-kb

# 3. Test locally
mkdocs serve

# 4. Open in browser
# Visit: http://127.0.0.1:8000
```

### First Time Setup

1. **Edit `mkdocs.yml`:**
   ```yaml
   site_name: Your KB Name
   site_author: Your Name
   site_url: https://yourusername.github.io/network-kb/
   repo_url: https://github.com/yourusername/network-kb
   ```

2. **Customize home page:**
   Edit `docs/index.md`

3. **Add your content:**
   - Follow the examples in `docs/networking/commands/nmcli.md`
   - Use the Q&A format for commands
   - Add tags to all pages

## Project Structure

```
network-kb/
├── docs/                           # All documentation
│   ├── index.md                    # Home page
│   ├── tags.md                     # Auto-generated tag index
│   ├── networking/                 # Networking section
│   │   ├── index.md
│   │   ├── commands/               # Command references
│   │   │   ├── nmcli.md
│   │   │   ├── ip-command.md
│   │   │   └── firewall-cmd.md
│   │   ├── troubleshooting/        # Troubleshooting guides
│   │   │   ├── connectivity.md
│   │   │   └── dns-issues.md
│   │   └── basics/                 # Fundamentals
│   │       ├── osi-model.md
│   │       └── ip-addressing.md
│   ├── rhel/                       # RHEL specific
│   │   ├── configuration/
│   │   └── services/
│   ├── qa/                         # Q&A section
│   │   ├── common-issues.md
│   │   └── tips-and-tricks.md
│   └── cheatsheets/                # Quick references
│       ├── nmcli-cheatsheet.md
│       └── firewall-cheatsheet.md
├── mkdocs.yml                      # Main configuration
├── .github/
│   └── workflows/
│       └── deploy-docs.yml         # Auto-deploy to GitHub Pages
└── README.md                       # This file
```

## Creating Content

### Page Template

Create new markdown files with this structure:

```markdown
---
tags:
  - networking
  - commands
  - your-tag
---

# Page Title

Brief introduction.

---

## Question: Your Question Here?

**Context:** When you need to...

**Command:**
```bash
your command here
```

**Expected Output:**
```
sample output
```

**Explanation:** Why this works...

**Tags:** #tag1 #tag2
```

### Using Tags

Add tags to page frontmatter:

```yaml
---
tags:
  - networking      # Category
  - configuration   # Type
  - nmcli          # Tool
  - static-ip      # Specific topic
---
```

Users can click tags to find related content.

### Code Blocks

Use triple backticks with language:

````markdown
```bash
nmcli connection show
```
````

### Admonitions (Callouts)

```markdown
!!! tip "Pro Tip"
    This is a tip box

!!! warning "Important"
    This is a warning

!!! danger "Critical"
    This is critical information

!!! note
    This is a note
```

### Collapsible Sections

```markdown
??? question "How do I do X?"
    Hidden answer here
    
    ```bash
    command here
    ```
```

## Building & Deploying

### Local Development

```bash
# Start development server (auto-reload on changes)
mkdocs serve

# Build static site
mkdocs build
# Output in site/ directory
```

### Deploy to GitHub Pages

#### Method 1: Manual

```bash
# Build and deploy
mkdocs gh-deploy
```

This creates/updates the `gh-pages` branch and pushes it.

#### Method 2: Automatic (Recommended)

The template includes GitHub Actions workflow (`.github/workflows/deploy-docs.yml`).

**Setup:**

1. Push to GitHub:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git remote add origin https://github.com/yourusername/network-kb.git
   git push -u origin main
   ```

2. Enable GitHub Pages:
   - Go to repository Settings → Pages
   - Source: Deploy from a branch
   - Branch: `gh-pages` / `root`
   - Save

3. Every push to `main` will auto-deploy!

Your site will be at: `https://yourusername.github.io/network-kb/`

## Customization

### Change Theme Colors

Edit `mkdocs.yml`:

```yaml
theme:
  palette:
    primary: blue       # blue, indigo, teal, green, etc.
    accent: amber       # accent color
```

### Add Navigation Items

Edit `mkdocs.yml`:

```yaml
nav:
  - Home: index.md
  - Your Section:
      - Page 1: section/page1.md
      - Page 2: section/page2.md
```

### Enable Features

Edit `mkdocs.yml` theme features:

```yaml
features:
  - navigation.tabs        # Top-level tabs
  - navigation.sections    # Collapsible sections
  - navigation.expand      # Expand all by default
  - navigation.top         # Back to top button
  - search.suggest         # Search suggestions
  - search.highlight       # Highlight search terms
  - content.code.copy      # Copy button for code
```

## Writing Best Practices

### For Commands

1. **Show actual output** - Include real command output
2. **Explain each option** - Don't just list commands
3. **Include verification** - Show how to verify the change worked
4. **Add context** - When would you use this?
5. **Link related topics** - Cross-reference other pages

### For Troubleshooting

1. **Start with symptoms** - What the user sees
2. **Provide diagnosis steps** - How to identify the issue
3. **Show multiple solutions** - Different approaches
4. **Include verification** - How to confirm it's fixed
5. **Add prevention tips** - How to avoid this issue

### For Q&A

1. **Use clear questions** - Match how users would ask
2. **Provide context** - When/why this is needed
3. **Show complete examples** - Working, tested code
4. **Include common variations** - Related scenarios
5. **Tag appropriately** - Help users find content

## Maintenance

### Adding New Content

```bash
# 1. Create markdown file in appropriate section
vim docs/networking/commands/new-command.md

# 2. Add frontmatter with tags
# 3. Write content following Q&A format
# 4. Add to navigation in mkdocs.yml (optional, auto-discovered)
# 5. Test locally
mkdocs serve

# 6. Commit and push
git add docs/networking/commands/new-command.md
git commit -m "Add new command reference"
git push
```

### Regular Updates

- Review and update content monthly
- Check for broken links
- Update command outputs if RHEL version changes
- Add new tags as needed
- Remove outdated information

## Search Tips for Users

The search supports:

- **Exact phrases:** "static ip address"
- **Commands:** nmcli, ip addr, firewall-cmd
- **Error messages:** "connection refused"
- **Tags:** Click any tag to see related content
- **Partial matches:** Types "net conf" finds "network configuration"

## Advanced Features

### Mermaid Diagrams

```markdown
```mermaid
graph LR
    A[Start] --> B[Step 1]
    B --> C[Step 2]
    C --> D[End]
```
````

### Math Equations

Requires enabling in `mkdocs.yml`:

```markdown
$$
E = mc^2
$$
```

### Tabbed Content

```markdown
=== "Tab 1"
    Content for tab 1

=== "Tab 2"
    Content for tab 2
```

## Troubleshooting

### Build fails

```bash
# Check Python version (need 3.8+)
python3 --version

# Reinstall dependencies
pip3 install --upgrade mkdocs-material

# Clear cache
rm -rf site/
mkdocs build
```

### Search not working

- Ensure `search` plugin is enabled in `mkdocs.yml`
- Build site and test in browser (not on file:// protocol)

### GitHub Pages not updating

- Check Actions tab in GitHub for errors
- Ensure `gh-pages` branch exists
- Verify Pages settings in repository

## Resources

- [MkDocs Documentation](https://www.mkdocs.org/)
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
- [Markdown Guide](https://www.markdownguide.org/)
- [GitHub Pages Docs](https://docs.github.com/en/pages)

## Support

Found an issue or have a suggestion?

- Open an issue in the repository
- Submit a pull request
- Check existing documentation

## License

This template is free to use and modify for your personal or commercial projects.

---

**Happy Documenting! 📚🚀**
