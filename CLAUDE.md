# Founderito OS — Agent Handoff Context

**Last updated:** 2026-03-16
**Project owner:** Henry Cho (henry.cho@immutable.com)
**Workspace folder:** `~/Documents/12. Claude Code/5. Product-Builder-Scale/`

---

## What this project is

The **Founderito Operating System** is a framework for product-building founders that maps the skills needed to build with AI agents. It is structured across three phases:

- **01 · Understand** — Know your context deeply before directing agents
- **02 · Envision** — Make clear decisions that agents can execute against
- **03 · Deliver, Run + Learn** — Direct and quality-gate execution

Each phase has 5–6 dimensions (skills/capabilities). Each dimension has a current skill level (L1–L5), a minimum baseline target, and some have stretch targets.

---

## Files

| File | Purpose |
|------|---------|
| `founderito_os_dimensions.html` | **Main OS page** — long-scrolling overview of all 3 phases, their dimensions, radar charts, and skill tags. Primary file being developed. |
| `founderito-understand.html` | **Level rubric page** for the Understand phase — L1–L5 definitions per dimension, baseline/target markers. |
| `founderito-envision.html` | Level rubric page for Envision — **not yet built** (linked from nav but doesn't exist yet) |
| `founderito-deliver.html` | Level rubric page for Deliver — **not yet built** (linked from nav but doesn't exist yet) |
| `founderito-context.md` | This file |

All files open locally via `file://` protocol. The understand page URL used in code is:
`file:///Users/henry.cho/Documents/12.%20Claude%20Code/founderito-understand.html`

---

## founderito_os_dimensions.html — Architecture

Single HTML file. All CSS, JS, and data are inline. No build step.

### Design tokens (CSS variables)
```css
--void:    #ECF2F6   /* page background */
--panel:   #FFFFFF
--mid:     #2C4A60
--text:    #4E6E82
--muted:   #849DB0
--border:  rgba(0,136,168,.18)

--c-understand: #1880C0
--c-envision:   #6848B8
--c-deliver:    #1A9060

--page-max: 1280px
--page-pad: 40px
```

### Layout
- Sticky black header (`height: 56px`)
- Intro section
- Arc steps (3-chevron Understand → Envision → Deliver flow indicator)
- Insight bar (3-column — one key shift per phase)
- Phase sections × 3, each containing:
  - Phase header band (phase color background)
  - Body: **radar chart** (460×460px canvas, right column) + **dim rows** (left column, flex:1)

### Data structure
```javascript
const phases = [
  {
    id: 'understand',
    number: '01',
    name: 'Understand',
    color: '#1880C0',
    colorRgb: '24,128,192',
    shift: '...',   // shown in phase header and insight bar
    dims: [
      {
        name: 'Customer Insight',
        desc: '...',
        level: 4,        // current assessed level (1–5)
        baseline: 3,     // minimum required level (Understand dims only so far)
        target: null,    // stretch target (optional)
        link: 'customer-insight',  // anchor on founderito-understand.html
        agent: false,    // true = agent-native dim (gets colored dot + dashed radar axis)
        skills: ['Customer discovery', 'Persona development', 'Demand unfolding']
      },
      // ...
    ]
  },
  // envision, deliver...
];
```

**Note:** `baseline` and `link` are currently only set on Understand dims. Envision and Deliver dims do not yet have these fields.

### Level rubric constants
```javascript
const levelNames = ['', 'Absent', 'Emerging', 'Developing', 'Strong', 'Exceptional'];
const understandUrl = 'file:///Users/henry.cho/Documents/12.%20Claude%20Code/founderito-understand.html';
```

### Pip indicator logic
Each dim has a 5-pip strip (5 × 9px squares) showing skill level.

```javascript
const pips = [1,2,3,4,5].map(n => {
  const filled = n <= (dim.baseline || dim.level);
  const cls = 'pip' + (filled ? ' filled' : '');
  const style = filled ? `background:${phase.color}` : '';
  return `<div class="${cls}" style="${style}"></div>`;
}).join('');
```

- Filled pips = baseline level (or current level if no baseline set)
- Pips are small colored squares, filled with the phase color

### Dim-level element (pip column + tooltip)
For Understand dims (those with `dim.link`), the pip column is rendered as an `<a>` anchor that links to the relevant section of `founderito-understand.html`. For other dims it's a `<div>`.

Hovering shows a tooltip with current level name + baseline label.

```javascript
const tooltipBaseline = dim.baseline
  ? `<span class="tooltip-baseline">Baseline · L${dim.baseline} ${levelNames[dim.baseline]}</span>` : '';

const levelTag  = dim.link ? 'a' : 'div';
const levelHref = dim.link ? ` href="${understandUrl}#${dim.link}" target="_blank"` : '';

// rendered as:
<a class="dim-level" href="...#customer-insight" target="_blank">
  <div class="level-pips">{pips}</div>
  <div class="level-label">L4 / 5</div>
  <div class="level-tooltip">
    <span class="tooltip-level">L4 · Strong</span>
    <span class="tooltip-baseline">Baseline · L3 Developing</span>
  </div>
</a>
```

### Radar chart
- Canvas 2D, 460×460px, dpr-scaled
- N-sided polygon (N = dims.length per phase)
- 5 rings at L1–L5 fractions (0.2, 0.4, 0.6, 0.8, 1.0 of maxR=152)
- L3 ring is slightly more prominent (accent color at 0.28 opacity vs 0.13)
- L1–L5 labels along top axis
- Agent-native dims get dashed axis lines + hollow data point circles
- Data plotted at `dim.level / 5` fraction of maxR
- Full dimension names used as axis labels (not abbreviations)

---

## Understand dim data (current)

| Dimension | level | baseline | target | link |
|-----------|-------|----------|--------|------|
| Customer Insight | 4 | 3 | null | `customer-insight` |
| Strategic Landscape | 3 | 2 | null | `strategic-landscape` |
| Domain Dynamics | 3 | 2 | 3 | `domain-dynamics` |
| Emergent Trends | 2 | 2 | 4 | `emergent-trends` |
| Context Architecture | 2 | 3 | 4 | `context-architecture` |

Context Architecture is **agent-native** (`agent: true`).

## Envision dim data (current — no baseline/link yet)

| Dimension | level | agent |
|-----------|-------|-------|
| Opportunity | 3 | false |
| North Star Experience | 4 | false |
| Business Model | 3 | false |
| Delivery Plan | 3 | false |
| Operating Model | 2 | true |

## Deliver dim data (current — no baseline/link yet)

| Dimension | level | agent |
|-----------|-------|-------|
| Orchestration Fluency | 2 | true |
| Operational Clarity | 3 | false |
| Team Activation | 4 | false |
| Learning Velocity | 3 | false |
| Quality Stewardship | 3 | true |
| Founder State | 4 | false |

---

## founderito-understand.html — Architecture

Separate file. Each dimension has a section with:
- L1–L5 rubric descriptions (what each level looks like for that dimension)
- Visual baseline/target markers
- Anchor IDs for deep linking from the OS page

**Anchor IDs on dimension divs:**
- `id="customer-insight"`
- `id="strategic-landscape"`
- `id="domain-dynamics"`
- `id="emergent-trends"`
- `id="context-architecture"`

The page has a **sticky phase navigation bar** at the top:
```html
<nav class="phase-nav">
  <a class="phase-nav-brand" href="#">Founderito OS</a>
  <div class="phase-nav-links">
    <a class="phase-nav-link active" href="founderito-understand.html">01 · Understand</a>
    <a class="phase-nav-link" href="founderito-envision.html">02 · Envision</a>
    <a class="phase-nav-link" href="founderito-deliver.html">03 · Deliver</a>
  </div>
</nav>
```

Level color palette (CSS vars):
```css
--l1: #d4d2ce   --l1-bg: #f5f4f2
--l2: #a8c4e0   --l2-bg: #eef4fa
--l3: #7eb8a4   --l3-bg: #edf5f1
--l4: #4a9d7a   --l4-bg: #e6f2ec
--l5: #2d6a4f   --l5-bg: #ddeee6
```

---

## Pending work

### High priority
1. **Build `founderito-envision.html`** — L1–L5 rubric page for the Envision phase, matching the style and structure of the understand page. Nav already links to it.
2. **Build `founderito-deliver.html`** — Same for the Deliver phase.
3. **Add baseline/link data to Envision dims** — Once the Envision rubric page exists, add `baseline`, `target`, and `link` fields to Envision dims in `founderito_os_dimensions.html`, mirroring what was done for Understand.
4. **Add baseline/link data to Deliver dims** — Same for Deliver.

### Lower priority / nice to have
- Verify pip fill behaviour looks correct across all dims (especially Context Architecture where level=2 is below baseline=3)
- Consider whether the radar should plot baseline rather than (or in addition to) current level
- Mobile/responsive pass on the OS dimensions page

---

## Standing rules / preferences

- **No copy cropping** — after every update, ensure no text is truncated or cut off
- **Jargon-free copy** — phase shift text and insight bar should be plain, direct English
- **Single HTML files** — all CSS and JS inline, no external dependencies except Google Fonts and system fonts
- Files open locally via `file://`, not served — keep paths and links relative where possible
- Henry's local path: `~/Documents/12. Claude Code/5. Product-Builder-Scale/`
