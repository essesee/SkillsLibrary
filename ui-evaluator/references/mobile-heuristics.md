# Mobile-Specific Heuristics — Evaluation Reference

## Thumb Zone Optimization
- Primary actions within comfortable one-handed reach (bottom 40% of screen)
- Navigation controls accessible without hand repositioning
- Avoid placing critical actions in top corners (requires hand shift)
- Bottom navigation bars preferred over top hamburger menus for frequent actions

## Touch Targets
- Minimum 44×44px touch targets (48×48dp for Material Design)
- At least 8px spacing between adjacent targets to prevent mis-taps
- Destructive actions separated from constructive by distance or confirmation
- Touch targets include padding, not just the visible element

## Scroll Behavior
- Scroll depth reasonable — critical content above the fold
- Infinite scroll has clear loading indicators and end state
- Pull-to-refresh where contextually appropriate
- Scroll position preserved on back-navigation
- No horizontal scroll on content pages (unless intentional carousel)

## Touch Gestures
- Standard gestures used for standard actions (swipe to delete, pull to refresh)
- Custom gestures have visible affordances or tutorials
- Gestures have visible alternatives (buttons) for discoverability
- No gesture conflicts with system gestures (edge swipe = back)

## Bottom Sheet Patterns
- Use bottom sheets for contextual actions (instead of full-page modals on mobile)
- Dismissible via swipe-down or tap-outside
- Snap points at useful heights (peek, half, full)
- Content scrollable within the sheet

## Mobile Keyboard
- Correct keyboard type per input (email → email keyboard, phone → numeric, URL → url)
- Keyboard doesn't obscure the active input field
- Form navigation (next field) works via keyboard toolbar
- Auto-capitalization and auto-correct appropriate per field

## Performance & Loading
- Skeleton screens preferred over spinners for content loading
- Images lazy-loaded below the fold
- Interactions feel responsive (<100ms feedback)
- Offline states handled gracefully with clear messaging

## Orientation & Responsive
- Works in both portrait and landscape
- Layout adapts meaningfully (not just scales)
- Critical functionality accessible in both orientations
- No content loss on orientation change

## Mobile Navigation Patterns
- Bottom tab bar for 3-5 primary destinations
- Hamburger menu only for secondary navigation
- Back button behavior predictable and consistent
- Deep links return to correct position in hierarchy
