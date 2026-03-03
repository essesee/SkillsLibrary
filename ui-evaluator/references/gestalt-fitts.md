# Gestalt Principles & Fitts's Law — Evaluation Reference

## Gestalt Principles

### Proximity
Elements placed close together are perceived as a group. Related items should be clustered; unrelated items should be separated.

**Check for:**
- Form labels visually closer to their input than to the next field
- Related actions grouped (edit/delete near each other)
- Section spacing creates clear visual groups
- Card content elements properly grouped within the card

**Common violations:** Labels equidistant between fields, related buttons scattered, no visual grouping of related content.

### Similarity
Elements that share visual properties (color, shape, size, texture) are perceived as related.

**Check for:**
- All primary actions share the same visual treatment
- All secondary actions share a different but consistent treatment
- Status indicators use consistent color coding throughout
- Similar content types have similar visual containers

**Common violations:** Primary buttons styled differently across pages, inconsistent use of color for status, mixed icon styles.

### Continuity
The eye follows smooth paths. Elements arranged along a line or curve are perceived as related.

**Check for:**
- Reading flow follows a logical path (F-pattern for text, Z-pattern for landing pages)
- Visual alignment creates clear scan lines
- Step indicators and progress bars guide the eye
- Consistent left-alignment of content

**Common violations:** Broken alignment, elements that interrupt natural scan patterns, misaligned columns.

### Closure
The mind completes incomplete shapes. Used in icons and implied containers.

**Check for:**
- Icons recognizable even if simplified
- Card boundaries clear even without full borders
- Implied grouping through whitespace works as intended

### Figure-Ground
Users should instantly distinguish foreground content from background.

**Check for:**
- Clear visual hierarchy between content and chrome
- Modals/overlays properly separated from background (overlay, shadow)
- Interactive elements distinguishable from static content
- Focus states create clear figure-ground separation

## Fitts's Law

**Core principle:** The time to reach a target is a function of the distance to the target and the size of the target. Larger targets closer to the cursor are faster to reach.

### Target Size
- **Minimum touch target:** 44×44px (WCAG) / 48×48dp (Material)
- **Desktop click targets:** Minimum 24×24px, prefer 32×32px+
- **High-frequency actions** deserve larger targets
- **Destructive actions** can be smaller (intentional friction)

### Target Placement
- **Primary actions** in easy-to-reach zones (bottom-right for mouse, thumb zones for mobile)
- **Frequently paired actions** placed close together (save/cancel)
- **Opposing actions** (save vs. delete) separated to prevent misclicks
- **Edge and corner placement** is advantageous — edges are effectively infinite-width targets

### Distance
- **Related actions** should minimize cursor travel between them
- **Sequential steps** should flow in one direction (left-to-right or top-to-bottom)
- **Common workflows** should minimize total cursor distance across the full task

### Mobile Thumb Zones
- **Easy zone:** Bottom center of screen
- **OK zone:** Middle of screen
- **Hard zone:** Top corners
- **Place primary actions in easy zone**, especially for one-handed use
