# LintandPods Official Branding & Logos

## Official Tools & Technologies

This knowledge base covers the following official tools and technologies with consistent branding throughout:

### Container & Orchestration
| Tool | Logo | Purpose |
|------|------|---------|
| **Docker** | 🐳 | Container runtime and image management |
| **Kubernetes** | ☸️ | Container orchestration and clustering |

### Databases
| Database | Logo | Purpose |
|----------|------|---------|
| **MySQL** | 🐬 | Relational database management system |
| **MongoDB** | 🍃 | Document-oriented NoSQL database |
| **PostgreSQL** | 🐘 | Advanced relational database system |

### Operating Systems
| OS | Logo | Purpose |
|----|------|---------|
| **Linux** | 🐧 | Open-source operating system |
| **RHEL** | 🎩 | Red Hat Enterprise Linux |

---

## Branding Locations

### 1. **Navigation Menu** (`mkdocs.yml`)
- **Main Categories**: Clean tool names without emoji (Docker, Kubernetes, Databases)
- **Subcategories**: Official icons for each database (🐬 MySQL, 🍃 MongoDB, 🐘 PostgreSQL)
- **Header Section**: 🏠 Home, 🐧 Linux, ❓ Q&A, 🏷️ Tags, 🔧 Misc

### 2. **Home Page** (`docs/index.md`)

#### Quick Navigation Grid
- Contains icon + tool name cards with hover effects
- Each tool links to its main documentation section
- Consistent styling with primary and accent colors

#### Official Tools & Technologies Section (NEW)
- **Logo Grid**: Interactive clickable logos for all major tools
- **Layout**: Responsive 6-column grid (auto-fit)
- **Styling**: 
  - Cards with white/dark background
  - Hover effects: scale up, colored border, shadow
  - Clean, modern design with purple/amber accent
  - Proper spacing and typography

### 3. **CSS Styling** (`docs/styles/extra.css`)

#### Tool Logo Classes
- `.logo-docker` → 🐳
- `.logo-kubernetes` → ☸️
- `.logo-mysql` → 🐬
- `.logo-mongodb` → 🍃
- `.logo-postgres` → 🐘
- `.logo-linux` → 🐧

#### Logo Grid Styling
- `.logo-grid`: Main container with gradient background
- `.logo-item`: Individual tool card with transitions
- `.logo-item-icon`: Large icon display (2.5rem)
- `.logo-item-name`: Tool name with proper typography
- `.tool-card`: Hover animations and scaling effects
- `.official-badge`: Badge styling for official tools

#### Features
- **Responsive Design**: Grid auto-fits from 1 to 6 columns
- **Dark Mode**: Full slate/dark mode support with rgba transparency
- **Hover Effects**: Scale transformation, color transitions
- **Accessibility**: Proper contrast ratios and readable text
- **Smooth Animations**: 0.2-0.3s transitions for all hover states

---

## Color Scheme

### Primary Colors
| Color | Value | Usage |
|-------|-------|-------|
| Deep Purple | `#512da8` | Main brand color |
| Amber | `#ffb300` | Accent and highlights |

### Supporting Colors
| Color | Usage |
|-------|-------|
| White | Card backgrounds (light mode) |
| Dark Slate | Card backgrounds (dark mode) |
| Semi-transparent overlay | Hover states and inactive elements |

---

## Typography

### Font Stack
- **Text Font**: Roboto (sans-serif)
- **Code Font**: Roboto Mono (monospace)

### Logo Sizing
- **Main Grid Icons**: 2.5rem (40px)
- **Navigation Icons**: 1.5rem (24px)
- **Card Headers**: 3rem (48px)

---

## Implementation Details

### Logo Grid Markup
```markdown
<div class="logo-grid" markdown>

[<span class="logo-item">
  <span class="logo-item-icon">🐳</span>
  <span class="logo-item-name">Docker</span>
</span>](docker/index.md)

[<span class="logo-item">
  <span class="logo-item-icon">☸️</span>
  <span class="logo-item-name">Kubernetes</span>
</span>](kubernetes/index.md)

</div>
```

### CSS Classes Applied
- `.logo-grid`: Grid container with responsive layout
- `.logo-item`: Styled link with flex layout
- `.logo-item-icon`: Icon display with margin
- `.logo-item-name`: Name text styling

---

## Branding Assets Used

### Official Tool Icons
- **Docker**: Official whale logo (🐳)
- **Kubernetes**: Official helm/steering wheel (☸️)
- **MySQL**: Official dolphin (🐬)
- **MongoDB**: Official leaf (🍃)
- **PostgreSQL**: Official elephant (🐘)
- **Linux**: Tux penguin (🐧)

### Icons are Consistent
✅ Across all documentation sections  
✅ In navigation menus  
✅ In quick reference tables  
✅ In home page hero sections  
✅ In tag and category listings  

---

## Brand Guidelines

### Usage Rules
1. Always use the official icon/emoji for consistency
2. Include tool name next to icon in main navigation
3. Use clean, uncluttered navigation without excess emoji
4. Apply consistent color scheme (purple + amber)
5. Maintain proper spacing and typography

### Best Practices
- Icons should have consistent sizing (1.5rem, 2.5rem, or 3rem)
- Always pair icons with readable text labels
- Use hover effects to indicate interactivity
- Ensure dark mode compatibility
- Maintain WCAG AA accessibility standards

### Color Usage
- **Primary Action**: Use deep purple (#512da8)
- **Highlights**: Use amber (#ffb300) for accents
- **Hover States**: Brighten or add shadow for depth
- **Inactive States**: Use 50-70% opacity

---

## Navigation Structure

### Main Menu Hierarchy
```
🏠 Home
├─ 🐧 Linux
│  ├─ Networking
│  ├─ Disk Operations
│  ├─ Recovery & Rescue
│  └─ RHEL
├─ Docker
│  ├─ Basics
│  ├─ Images
│  ├─ Containers
│  ├─ Networking
│  ├─ Storage & Volumes
│  └─ Docker Compose
├─ Kubernetes
│  ├─ Core Concepts
│  ├─ Deployments
│  ├─ Services
│  └─ Networking
├─ Databases
│  ├─ 🐬 MySQL
│  ├─ 🍃 MongoDB
│  └─ 🐘 PostgreSQL
├─ ❓ Q&A
│  ├─ Best Practices
│  ├─ Common Issues
│  └─ Tips & Tricks
└─ 🔧 Miscellaneous
   └─ Cheatsheets
```

---

## Future Enhancements

### Planned Improvements
- [ ] Add SVG logos for official tool branding
- [ ] Create custom icon set for databases
- [ ] Implement animated logo transitions
- [ ] Add tool-specific color palettes in cards
- [ ] Create downloadable branding assets (SVG, PNG)

### Optional Features
- [ ] Add official tool links in footer
- [ ] Create "Tools & Technologies" page
- [ ] Generate tool download badges
- [ ] Implement version badges for each tool

---

**Updated**: 2026  
**Branding Version**: 1.0  
**Theme**: Material Design with Custom Purple/Amber Palette  
**Accessibility**: WCAG AA+ Compliant
