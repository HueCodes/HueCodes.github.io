# Homepage Layout Options

I've created three different homepage layouts for you to choose from. Each has a different visual approach and content organization.

## Option 1: Hero with Stats
**File:** `index-option1.md`

### Key Features:
- **Large hero section** with profile photo, name, tagline
- **Quick stats** showing project count, OSS contributions, blog posts
- **Featured projects** in a grid layout with tags
- **Collapsible sections** for "more projects" and "more contributions"
- **Recent blog posts** preview section
- **Combined about/currently** section (less redundant)

### Best For:
- Making a strong first impression
- Highlighting your most impressive work
- Showing activity at a glance with stats

### Layout Structure:
```
Hero (photo + stats)
  ↓
About (combined)
  ↓
Featured Projects (3 cards with tags)
  ↓
More projects (collapsible)
  ↓
Recent Blog Posts
  ↓
OSS Contributions (top 3 + collapsible)
```

---

## Option 2: Minimal Two-Column
**File:** `index-option2.md`

### Key Features:
- **Clean, professional** minimal design
- **Two-column layout** - main content + sidebar
- **Detailed project descriptions** with full tech stacks
- **Sidebar** with "currently", "interests", recent posts, personal info
- **Expandable sections** to reveal all projects
- **Focus on depth** over breadth

### Best For:
- Professional, resume-like presentation
- Providing detailed context for each project
- Easy scanning of tech stacks and skills

### Layout Structure:
```
Main Content (Left)         |  Sidebar (Right)
----------------------------|------------------
Work Section:               |  Currently
  - Project 1 (detailed)    |  Interests
  - Project 2 (detailed)    |  Recent Posts
  - Project 3 (detailed)    |  Outside Work
  + expand more             |
                           |
OSS Section:                |
  - Contribution 1          |
  - Contribution 2          |
  + expand more             |
```

---

## Option 3: Visual Card Grid
**File:** `index-option3.md`

### Key Features:
- **Modern card-based** design
- **Icons/emojis** for visual interest
- **Metrics highlighted** in each card (13K QPS, <1ms latency, etc.)
- **Tech badges** for quick scanning (Go, Rust, C++)
- **Compact header** with avatar and quick links
- **All 7 projects** shown (including new Huffman-Cpp)
- **OSS contribution badges** in compact grid

### Best For:
- Visual appeal and modern aesthetic
- Showcasing metrics and performance numbers
- Making it easy to scan all projects quickly
- Portfolio/showcase feel

### Layout Structure:
```
Compact Header (avatar + links)
  ↓
Bio (one paragraph)
  ↓
Project Cards Grid (3 columns)
[Card] [Card] [Card]
[Card] [Card] [Card]
[Card]
  ↓
OSS Badges (compact grid)
[Badge] [Badge] [Badge]
[Badge] [Badge] [Badge]
```

---

## Comparison Table

| Feature | Option 1 (Hero) | Option 2 (Two-Column) | Option 3 (Cards) |
|---------|----------------|----------------------|------------------|
| **Visual Impact** | High (stats, large hero) | Medium (clean, professional) | High (cards, icons) |
| **Info Density** | Medium (collapsible) | High (detailed descriptions) | Medium (features list) |
| **Scanability** | Good (tags) | Excellent (tech stacks) | Excellent (metrics, badges) |
| **Modern Feel** | Modern | Professional/Traditional | Very Modern |
| **Mobile Friendly** | Good | Fair (two-column) | Good (cards stack) |
| **First Impression** | "Active developer with stats" | "Detailed, serious engineer" | "Visual portfolio" |

---

## My Recommendation

**Option 3 (Visual Card Grid)** if you want:
- Modern, portfolio-style aesthetic
- Metrics and performance numbers front and center
- Easy visual scanning
- Include all projects including Huffman-Cpp

**Option 1 (Hero with Stats)** if you want:
- Strong first impression with stats
- Featured projects approach (highlight best 3)
- Recent activity emphasis

**Option 2 (Minimal Two-Column)** if you want:
- Professional, resume-like presentation
- Detailed technical context for each project
- Traditional, serious tone

---

## Next Steps

1. **Pick your favorite** or tell me elements you like from each
2. I can create a **hybrid** combining elements you like
3. I'll help you **add the necessary CSS** to make it look polished
4. We can **preview it** before replacing your current homepage

Which direction appeals to you most?
