# Nielsen's 10 Usability Heuristics — Evaluation Reference

## 1. Visibility of System Status
The system should keep users informed about what's going on through timely, appropriate feedback.

**Check for:** Loading indicators, progress bars, state changes confirmed visually, current location indicators (breadcrumbs, active nav), save confirmations, error states visible.

**Common violations:** Silent failures, no loading state, unclear whether action completed, ambiguous toggle states.

## 2. Match Between System and Real World
Use language and concepts familiar to the user. Follow real-world conventions and present information in a natural, logical order.

**Check for:** Jargon-free labels, familiar icons, logical ordering (chronological, alphabetical, priority), metaphors that match mental models.

**Common violations:** Technical jargon in UI, unfamiliar icons, arbitrary ordering, developer-centric language.

## 3. User Control and Freedom
Users need a clearly marked "emergency exit" to leave unwanted actions without an extended process.

**Check for:** Undo/redo, cancel buttons, back navigation, ability to dismiss modals, escape key support, edit/delete existing entries.

**Common violations:** No way to undo, forced multi-step flows with no exit, modal traps, destructive actions without confirmation.

## 4. Consistency and Standards
Users shouldn't have to wonder whether different words, situations, or actions mean the same thing. Follow platform and industry conventions.

**Check for:** Consistent button styles, consistent terminology, standard icon meanings, predictable placement of navigation, consistent interaction patterns.

**Common violations:** Same action labeled differently in different places, inconsistent button hierarchy, non-standard icons, placement varies between pages.

## 5. Error Prevention
Design to prevent problems from occurring in the first place. Eliminate error-prone conditions or present users with a confirmation before they commit.

**Check for:** Input validation before submission, confirmation dialogs for destructive actions, smart defaults, constraints that prevent invalid states, helpful placeholder text.

**Common violations:** Free-text where structured input works, no validation until submit, destructive actions with single click, easy to enter invalid data.

## 6. Recognition Rather Than Recall
Minimize memory load by making elements, actions, and options visible. Users shouldn't have to remember information between steps.

**Check for:** Visible options (not hidden menus), recently used items, search with suggestions, labels on icons, context preserved across steps.

**Common violations:** Hidden navigation, icon-only toolbars without labels, requiring users to remember codes or values from previous screens, no search.

## 7. Flexibility and Efficiency of Use
Accelerators — unseen by novice users — speed up interaction for expert users. Allow users to tailor frequent actions.

**Check for:** Keyboard shortcuts, bulk actions, customizable defaults, recent/favorites, power-user paths that don't complicate the basic flow.

**Common violations:** No keyboard shortcuts, forced step-by-step for repetitive tasks, no way to batch actions, one-size-fits-all flow.

## 8. Aesthetic and Minimalist Design
Every extra unit of information competes with relevant information and diminishes its relative visibility. Keep interfaces clean and focused.

**Check for:** Content hierarchy clear, whitespace used well, no decorative clutter, progressive disclosure for secondary info, focused calls to action.

**Common violations:** Information overload, competing CTAs, decorative elements that distract, everything at the same visual weight, unnecessary chrome.

## 9. Help Users Recognize, Diagnose, and Recover from Errors
Error messages should be in plain language, precisely indicate the problem, and constructively suggest a solution.

**Check for:** Error messages near the problem field, plain language (not error codes), specific about what went wrong, suggest how to fix, don't destroy user input.

**Common violations:** Generic "something went wrong," error codes shown to users, error messages far from the problem, form clears on error, no recovery path.

## 10. Help and Documentation
Best if the system is usable without docs, but provide help when needed. Help should be searchable, task-focused, and concise.

**Check for:** Contextual help (tooltips, inline hints), searchable help/FAQ, onboarding for new features, empty states that guide action.

**Common violations:** No help at all, help docs that are separate and hard to find, no onboarding, empty states with no guidance.

## Severity Rating Scale
- **0: Not a usability problem** — Cosmetic only
- **1: Cosmetic** — Fix if time allows
- **2: Minor** — Low priority fix
- **3: Major** — Important to fix, high priority
- **4: Catastrophic** — Must fix before release
