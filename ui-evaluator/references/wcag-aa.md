# WCAG 2.1 AA Compliance — Evaluation Checklist

AA is the baseline. These are must-pass criteria.

## Perceivable

### Text Alternatives (1.1)
- All non-text content has text alternatives (alt text, aria-label)
- Decorative images marked with empty alt or role="presentation"
- Complex images have extended descriptions

### Color & Contrast (1.4)
- **Text contrast:** 4.5:1 minimum for normal text, 3:1 for large text (18pt+ or 14pt bold+)
- **Non-text contrast:** 3:1 for UI components and graphical objects
- Color is not the only means of conveying information (links, errors, status)

### Adaptable (1.3)
- Content structure conveyed through proper HTML semantics (headings, lists, tables)
- Reading order makes sense when CSS is disabled
- Orientation not locked (works in portrait and landscape)

### Distinguishable (1.4 continued)
- Text resizable to 200% without loss of content
- No horizontal scrolling at 320px viewport width (reflow)
- Text spacing adjustable without breaking layout

## Operable

### Keyboard Accessible (2.1)
- All functionality available via keyboard
- No keyboard traps (can tab in and out of every component)
- Focus visible on all interactive elements (minimum 2px solid outline or equivalent)

### Timing (2.2)
- Time limits can be extended or turned off
- Moving/auto-updating content can be paused, stopped, or hidden

### Seizures (2.3)
- No content flashes more than 3 times per second

### Navigation (2.4)
- Skip-to-main-content link present
- Page titles descriptive and unique
- Focus order follows logical reading sequence
- Link purpose clear from link text (no "click here")
- Multiple ways to reach any page (nav, search, sitemap)
- Headings and labels descriptive

## Understandable

### Readable (3.1)
- Page language declared in HTML lang attribute
- Language changes marked on inline elements

### Predictable (3.2)
- Focus doesn't trigger unexpected context changes
- Consistent navigation across pages
- Consistent identification of repeated components

### Input Assistance (3.3)
- Error identification: errors described in text, not just color
- Labels or instructions provided for user input
- Error suggestions offered when known
- Error prevention: submissions reversible, data checked, confirmation provided

## Robust

### Compatible (4.1)
- Valid HTML (no duplicate IDs, proper nesting)
- Name, role, value: all UI components have accessible names and roles
- Status messages conveyed to assistive tech without focus change
