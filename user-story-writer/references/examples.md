# Story Examples

Real examples from the Platform team's best completed stories. One per type variant. Use for calibration — these represent the quality bar.

---

## Feature Example (derived from TST-539)

### User story

* As a travel agent, when manually booking a cruise, I want a unified "Cruise Booking Details" and "Cruise Itinerary" form so I can complete the entire booking in one pass with minimal errors.

### Context

Tricia, an experienced travel agent, enters cruise details and needs booking-level fields plus a day-by-day itinerary in one session with no backend delays.

### Design

* **Figma:** [Agent-Admin — Cruise Details](link)
* **Prototype:** [Cruise Prototype](link)

### Acceptance criteria

- [ ] Cruise Booking Details section contains all required fields: Cruise Line, Ship, Confirmation Number, Tour Number, Destination, Departure/Return dates, Category, Cabin Number, Dining Preference
- [ ] All required fields show validation errors when empty on submit
- [ ] Date pickers enforce Departure <= Return constraint
- [ ] Cruise Itinerary section allows adding day-by-day port entries
- [ ] Form saves complete booking in a single submit action

### Other information

* **Environment**: Preview + CDEV only. Do NOT release to Prod.

**What makes this good:** Specific persona with name, clear situation trigger, concrete ACs with validation rules, design references included, scope constraint clearly stated.

---

## Tech Debt Example (derived from TST-4022)

### User story

* As a developer, when I am working on the server code, I want to replace synchronous file reads with async alternatives so that the event loop is not blocked during file operations.
* As a developer, when I review middleware dependencies, I want to remove redundant packages so that the codebase is cleaner and more efficient.
* As a developer, when I analyze log verbosity, I want to reduce unnecessary logging so that production logs are more manageable.

### Context

This issue focuses on improving server code quality and addressing technical debt. Key tasks include replacing synchronous file reads, removing redundant dependencies, reducing log verbosity, and scoping middleware to appropriate routes. These changes aim to enhance performance and maintainability.

### Acceptance criteria

- [ ] `readFileSync` in `src/server/routes/checkout.tsx` is replaced with `fs.promises.readFile()` or startup-time caching
- [ ] The MSW test page still loads correctly after the change
- [ ] `body-parser` import is removed from `src/server/main.ts`
- [ ] `bodyParser.json()` is replaced with `express.json()`
- [ ] `body-parser` and `@types/body-parser` are removed from `package.json`
- [ ] Verbose logs ("Starting licenseeDataMiddleware", "Successfully read cookie value") are at `debug` level instead of `info`

**What makes this good:** Multiple user stories for distinct concerns, ACs reference specific files and functions, existing behavior preservation included ("MSW test page still loads"), checkbox format for engineering tracking.

---

## Spike Example (derived from TST-4332 + TST-3969)

### User story

* As a developer, when planning multi-room hotel checkout, I want to investigate how the receipt panel should display 2+ hotels so that we can design the correct UI before building.

### Context

The current checkout receipt panel only supports a single hotel. We need to determine the UX for multiple hotel bookings before committing to implementation.

### Questions

1. How will multiple hotel line items appear in the receipt panel — stacked blocks with subtotals or combined view?
2. How are taxes and fees broken down — per hotel or aggregated?
3. How does "Due at Property" vs "Due Today" display when hotels have different payment terms?
4. Does trip protection show as one line or per hotel?
5. Does "Remove Hotel" need to appear for each hotel listed?

### Output

* Design recommendation from UX
* API sketch from backend for multi-hotel receipt data structure

### Timebox

2 days

### Answered Questions

* Traveler forms do NOT need to repeat for each hotel — same travelers assumed.

**What makes this good:** Specific, answerable questions (not vague). Clear output deliverables with owners. Timebox present. Pre-answered questions included to avoid re-investigation.

---

## Bug Consolidation Example (synthetic — based on team patterns)

### User story

* As a travel agent, when using SSO to access the platform, I want authentication to work reliably across all entry points so that I don't lose work or get locked out during active sessions.

### Context

Multiple bugs report authentication failures across different SSO flows — initial login, session refresh, and cross-app navigation. The root cause appears to be inconsistent token handling between the identity provider and platform session management. 8 agents reported issues in the last 30 days.

**Consolidated bugs:**
| Ticket | Summary | Severity |
|--------|---------|----------|
| TST-3001 | SSO login fails intermittently on Chrome | High |
| TST-3045 | Session expires during active booking flow | Critical |
| TST-3067 | Cross-app navigation loses auth state | Medium |

### Acceptance criteria

- [ ] Token refresh logic handles all three SSO entry points (login, session refresh, cross-app)
- [ ] Session does not expire during active user interaction (no idle timeout during form entry)
- [ ] Cross-app navigation preserves authentication state without requiring re-login
- [ ] All three original bug scenarios no longer reproduce
- [ ] No regressions in standard login/logout flow

### Other information

* **Priority score**: 8.2 (composite from bug-consolidator)
* **Related tickets**: TST-3001, TST-3045, TST-3067 (become sub-defects)
* **Root cause hypothesis**: Token refresh timer doesn't account for cross-app iframe context

**What makes this good:** User story describes the root problem, not individual symptoms. Consolidated bugs table for quick reference. ACs cover each child bug's scenario plus regression check. Root cause hypothesis included for developer context.
