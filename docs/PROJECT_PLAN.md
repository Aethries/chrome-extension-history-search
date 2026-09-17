# Chrome Navigator: Project Plan, Milestones and GitHub Setup

This document defines the complete engineering roadmap, GitHub Milestone structure, GitHub Issue catalog, and label taxonomy for Chrome Navigator.

The execution model is capability-driven rather than deadline-driven. Work progresses sequentially through functional milestones based on technical dependencies.

---

## 1. GitHub Labels Taxonomy

Use this standardized label configuration in your GitHub repository.

### Area Labels (Functional Subsystems)

| Label Name | Description | Color Code |
| :--- | :--- | :--- |
| `area:overlay` | In-page injection, closed Shadow Root, CSS isolation, z-index | `#0E8A16` |
| `area:tabs` | Chrome tabs querying, tab reuse, window focusing, tab groups | `#1D76DB` |
| `area:search` | Multi-tier matching engine, tri-gram index, token search | `#5319E7` |
| `area:ranking` | Scoring formula, decay algorithms, local learning feedback | `#B60205` |
| `area:parser` | Query lexer, EBNF parser, AST generation, scopes, aliases | `#D93F0B` |
| `area:keyboard` | Keybinding handlers, focus model, actions menu, multi-select | `#0052CC` |
| `area:storage` | Storage tiering, IndexedDB, Chrome sync, schema migrations | `#006B75` |
| `area:a11y` | Accessibility, ARIA combobox pattern, screen readers, focus traps | `#C5DEF5` |
| `area:compat` | Web app conflict resolution, iframe and fullscreen handling | `#FBCA04` |
| `area:tools` | Omnibox integration, inline calculator, URL launcher | `#E99695` |
| `area:perf` | Performance optimization, memory profiling, cancellation tokens | `#7057FF` |

### Type Labels (Issue Nature)

| Label Name | Description | Color Code |
| :--- | :--- | :--- |
| `type:feat` | New user-facing capability or system feature | `#1D76DB` |
| `type:fix` | Bug fix or behavioral correction | `#D73A4A` |
| `type:perf` | Performance improvement or latency reduction | `#A2EEEF` |
| `type:refactor` | Code restructuring without feature alteration | `#CFD3D7` |
| `type:spec` | Architectural specification update or documentation | `#0075CA` |

### Priority Labels (Relative Urgency)

| Label Name | Description | Color Code |
| :--- | :--- | :--- |
| `priority:p0` | Fundamental architecture or core MVP blocker | `#B60205` |
| `priority:p1` | High value capability required for daily power use | `#D93F0B` |
| `priority:p2` | Useful enhancement or secondary workflow | `#FBCA04` |
| `priority:p3` | Polish, experimental feature, or future integration | `#C5DEF5` |

---

## 2. GitHub Project Fields Configuration

When setting up your GitHub Project (v2), configure these custom fields:

- Status: Backlog, Ready, In Progress, Testing, Done
- Milestone: Select from GitHub Milestones (Milestone 0 to 10)
- Priority: Single Select (P0, P1, P2, P3)
- Effort: Single Select (XS, S, M, L, XL) or Story Points (1, 2, 3, 5, 8)
- Area: Single Select matching Area labels

---

## 3. Milestone Catalog & Issue Specifications

### Milestone 0: Foundation and Host Injection

- Goal: Construct Manifest V3 extension skeleton, background service worker lifecycle, and closed Shadow DOM overlay.
- Target Specs: [SPEC-00 (Overview & Architecture)](specs/00-overview-and-architecture.md), [SPEC-01 (Invocation & Overlay)](specs/01-invocation-and-overlay.md)

#### Issue 0.1: Manifest V3 Skeleton and Build Pipeline
- Title: `feat(core): manifest v3 skeleton and build pipeline`
- Labels: `area:overlay`, `type:feat`, `priority:p0`
- Effort: S
- Related Specs:
  - [SPEC-00 Section 3: Chrome Extension Architecture (Manifest V3)](specs/00-overview-and-architecture.md)
  - [SPEC-00 Section 4: Service Worker Lifecycle & State Recovery](specs/00-overview-and-architecture.md)
- Description:
  Set up the base Chrome extension structure using TypeScript and a modern bundler.
- Requirements:
  - Manifest file targeting Manifest V3 with minimal required permissions (`tabs`, `storage`, `commands`).
  - Background Service Worker registration with entrypoint `background.ts`.
  - Content script configured to inject into `<all_urls>` at `document_start`.
  - Development build script with live recompile.
- Acceptance Criteria:
  - Extension loads into Chrome without manifest errors or warnings.
  - Background service worker starts and logs initialization.
  - Content script executes on any visited web page.

#### Issue 0.2: Closed Shadow Root Injection and CSS Isolation
- Title: `feat(overlay): closed shadow root injection and css isolation`
- Labels: `area:overlay`, `type:feat`, `priority:p0`
- Effort: M
- Related Specs:
  - [SPEC-01 Section 4: Overlay Injection & DOM Isolation](specs/01-invocation-and-overlay.md)
  - [SPEC-01 Section 5: Positioning, Geometry & Responsive Layout](specs/01-invocation-and-overlay.md)
- Description:
  Inject the command palette container into web pages inside a closed Shadow DOM to guarantee total isolation from host page styles.
- Requirements:
  - Create custom host element `chrome-navigator-host`.
  - Attach closed shadow root using `Element.attachShadow({ mode: "closed" })`.
  - Apply CSS resets using CSS layers to prevent host stylesheet inheritance.
  - Set z-index to maximum integer (2147483647) with `position: fixed`.
- Acceptance Criteria:
  - Host website styles (CSS resets, fonts, colors) do not affect Navigator UI.
  - Navigator CSS rules do not bleed out into host website elements.

#### Issue 0.3: In-Page Keystroke Filtering and Suppression
- Title: `feat(input): in-page keystroke filtering and suppression`
- Labels: `area:overlay`, `type:feat`, `priority:p0`
- Effort: S
- Related Specs:
  - [SPEC-01 Section 1: Primary Invocation Shortcut & Keystroke Filtering](specs/01-invocation-and-overlay.md)
- Description:
  Capture `Shift+O` key combination on the window capturing phase while suppressing activation when the user is typing in form controls or code editors.
- Requirements:
  - Add capturing keydown listener on `window`.
  - Implement element inspection for input, textarea, select, contenteditable, and ARIA text roles.
  - Check for common code editor containers (Monaco, CodeMirror, ProseMirror, Slate).
- Acceptance Criteria:
  - Pressing `Shift+O` in regular page context summons the palette.
  - Pressing `Shift+O` inside an input field, Google Docs, or VS Code Web inserts capital letter O and does not open Navigator.

#### Issue 0.4: Browser Shortcut and Palette Toggle State Machine
- Title: `feat(input): browser shortcut and palette toggle state machine`
- Labels: `area:overlay`, `type:feat`, `priority:p0`
- Effort: S
- Related Specs:
  - [SPEC-01 Section 2: Browser-Level Global Shortcuts & Conflict Arbitration](specs/01-invocation-and-overlay.md)
  - [SPEC-01 Section 3: Toggle Lifecycle & State Transitions](specs/01-invocation-and-overlay.md)
- Description:
  Integrate Chrome Commands API for browser-level toggling and manage overlay state transitions.
- Requirements:
  - Register `CommandOrControl+Shift+O` in `manifest.json`.
  - Implement toggle finite-state machine: Closed -> Opening -> Open -> Closing -> Closed.
  - Close palette when Escape is pressed or when clicking outside on the backdrop.
- Acceptance Criteria:
  - Hitting browser shortcut toggles overlay visibility from anywhere.
  - Overlay dismisses cleanly and restores focus to previous active element.

---

### Milestone 1: Minimum Viable Palette and Tab Switcher

- Goal: Complete the core loop `Open -> Type -> Navigate` for open browser tabs across all windows.
- Target Specs: [SPEC-06 (Keyboard & User Interaction)](specs/06-keyboard-and-user-interaction.md), [SPEC-07 (Navigation & Tab Management)](specs/07-navigation-and-tab-management.md)

#### Issue 1.1: Search Input Autofocus and Virtual Result List
- Title: `feat(ui): search input autofocus and virtual result list`
- Labels: `area:overlay`, `area:keyboard`, `type:feat`, `priority:p0`
- Effort: M
- Related Specs:
  - [SPEC-01 Section 7: Search Input Mechanics & Autofocus](specs/01-invocation-and-overlay.md)
  - [SPEC-05 Section 6: Result Row Visual Structure](specs/05-unified-results-and-deduplication.md)
- Description:
  Implement palette UI containing the search input box and a high-performance result list.
- Requirements:
  - Search input automatically gains focus on mount without requiring mouse clicks.
  - Render list of results using fixed row heights for high rendering speed.
  - Position overlay horizontally centered and 18 percent from top of viewport.
- Acceptance Criteria:
  - Input is ready to accept text within 16ms of overlay opening.
  - Palette layout is visually centered and responsive.

#### Issue 1.2: Multi-Window Tab Provider and Reactive Cache
- Title: `feat(tabs): multi-window tab provider and reactive cache`
- Labels: `area:tabs`, `type:feat`, `priority:p0`
- Effort: M
- Related Specs:
  - [SPEC-07 Section 1: Primary Navigation Execution Semantics](specs/07-navigation-and-tab-management.md)
  - [SPEC-07 Section 7: Multi-Window Indexing & Scopes](specs/07-navigation-and-tab-management.md)
- Description:
  Retrieve and maintain an in-memory index of open tabs across all Chrome windows.
- Requirements:
  - Fetch open tabs via `chrome.tabs.query({})`.
  - Listen to `chrome.tabs.onCreated`, `onRemoved`, and `onUpdated` to update memory cache reactively.
  - Extract title, URL, window ID, tab index, and audio status.
- Acceptance Criteria:
  - Tabs in background windows are indexed and searchable.
  - Creating or closing a tab immediately updates the in-memory tab index.

#### Issue 1.3: Tab Reuse and Window Focusing Navigation
- Title: `feat(nav): tab reuse and window focusing navigation`
- Labels: `area:tabs`, `type:feat`, `priority:p0`
- Effort: S
- Related Specs:
  - [SPEC-07 Section 2: Existing-Tab Detection & Window Switching Protocol](specs/07-navigation-and-tab-management.md)
  - [SPEC-07 Section 3: Tab Equality & Matching Strategies](specs/07-navigation-and-tab-management.md)
- Description:
  Switch to existing open tab when selected, bringing its parent window to focus.
- Requirements:
  - On Enter, determine target tab ID and window ID.
  - Focus target window using `chrome.windows.update(windowId, { focused: true })`.
  - Activate target tab using `chrome.tabs.update(tabId, { active: true })`.
  - Close the Navigator overlay on the origin page.
- Acceptance Criteria:
  - Selecting an existing open tab switches to it in under 25ms without creating a duplicate.
  - Works across multiple monitors and separate browser windows.

#### Issue 1.4: Keyboard Navigation and Boundary Wrapping
- Title: `feat(keyboard): keyboard navigation and boundary wrapping`
- Labels: `area:keyboard`, `type:feat`, `priority:p0`
- Effort: S
- Related Specs:
  - [SPEC-06 Section 1: Primary Keyboard Navigation Controls](specs/06-keyboard-and-user-interaction.md)
  - [SPEC-06 Section 3: Cyclic Selection Boundaries (Wrap-Around)](specs/06-keyboard-and-user-interaction.md)
- Description:
  Provide responsive keyboard navigation through result rows.
- Requirements:
  - Arrow Down and `Ctrl+N` move selection down.
  - Arrow Up and `Ctrl+P` move selection up.
  - Home and End jump to first and last items.
  - Wrap selection cyclically from bottom to top and top to bottom.
- Acceptance Criteria:
  - Visual selection indicator tracks active index synchronously on keydown.
  - Virtual list auto-scrolls to keep active item in view.

---

### Milestone 2: Universal Retrieval and Deduplication

- Goal: Expand search to Bookmarks and Browsing History, with URL canonicalization and cross-source deduplication.
- Target Specs: [SPEC-03 (Search Engine & Matching)](specs/03-search-engine-and-matching.md), [SPEC-05 (Unified Results & Deduplication)](specs/05-unified-results-and-deduplication.md)

#### Issue 2.1: Bookmarks and History Data Providers
- Title: `feat(search): bookmarks and history data providers`
- Labels: `area:search`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-03 Section 1: Universal Search Architecture](specs/03-search-engine-and-matching.md)
  - [SPEC-03 Section 6: Bookmark Hierarchy Traversal](specs/03-search-engine-and-matching.md)
  - [SPEC-03 Section 7: History Search Integration](specs/03-search-engine-and-matching.md)
- Description:
  Connect Chrome Bookmarks and History APIs to the federated search engine.
- Requirements:
  - Read bookmark tree via `chrome.bookmarks.search`.
  - Query history via `chrome.history.search` with a 120ms debounce.
  - Support non-blocking asynchronous result delivery.
- Acceptance Criteria:
  - Bookmarks and history records appear in candidate search stream.
  - Fast sources (tabs, bookmarks) render before history without UI freeze.

#### Issue 2.2: Canonical URL Normalization and Tracking Sanitizer
- Title: `feat(url): canonical url normalization and tracking sanitizer`
- Labels: `area:search`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-05 Section 1: URL Normalization Engine & Canonical Identity](specs/05-unified-results-and-deduplication.md)
  - [SPEC-05 Section 2: Tracking Parameter Sanitization](specs/05-unified-results-and-deduplication.md)
- Description:
  Transform raw URLs into standardized canonical representations to enable deduplication and clean matching.
- Requirements:
  - Lowercase protocol and hostname.
  - Remove standard ports (80, 443) and normalize trailing slashes.
  - Strip tracking query parameters (`utm_source`, `utm_medium`, `fbclid`, `gclid`).
- Acceptance Criteria:
  - URLs with identical destinations but differing tracking parameters evaluate to the same canonical key.

#### Issue 2.3: Cross-Source Entity Deduplication Pipeline
- Title: `feat(search): cross-source entity deduplication pipeline`
- Labels: `area:search`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-05 Section 4: Result Type System](specs/05-unified-results-and-deduplication.md)
  - [SPEC-05 Section 5: Cross-Source Entity Deduplication](specs/05-unified-results-and-deduplication.md)
- Description:
  Merge multi-source occurrences of the same canonical URL into a single unified result item.
- Requirements:
  - If a URL is open in a tab, saved as a bookmark, and present in history, produce one result row.
  - Assign combined badges (`TAB`, `BM`, `HISTORY`).
  - Set primary navigation target according to priority: Tab > Bookmark > History.
- Acceptance Criteria:
  - No duplicate rows appear for the same web page.
  - Activating a deduplicated item activates the open tab rather than navigating.

#### Issue 2.4: Multi-Tier Matching Engine
- Title: `feat(search): multi-tier matching engine`
- Labels: `area:search`, `type:feat`, `priority:p1`
- Effort: L
- Related Specs:
  - [SPEC-03 Section 3: Multi-Tier Matching Pipeline](specs/03-search-engine-and-matching.md)
  - [SPEC-03 Section 4: Hostname & Domain Matching Rules](specs/03-search-engine-and-matching.md)
- Description:
  Implement five-tier matching pipeline for search queries.
- Requirements:
  - Tier 1: Exact match on title, host, or URL.
  - Tier 2: Prefix match on tokens and word boundaries.
  - Tier 3: Word boundary substring match.
  - Tier 4: Unordered tokenized match.
  - Tier 5: Fuzzy match with configurable strength (Smith-Waterman distance).
- Acceptance Criteria:
  - Querying `jira login bug` matches `Login issue [Bug] - Jira Cloud`.
  - Typos like `gthb` successfully match `GitHub` under balanced fuzzy setting.

#### Issue 2.5: Result Row Typography, Match Highlight and Favicons
- Title: `feat(ui): result row typography, match highlight and favicons`
- Labels: `area:overlay`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-05 Section 6: Result Row Visual Structure](specs/05-unified-results-and-deduplication.md)
  - [SPEC-05 Section 7: URL Typography & Smart Domain Names](specs/05-unified-results-and-deduplication.md)
  - [SPEC-05 Section 8: Substring & Token Match Highlighting](specs/05-unified-results-and-deduplication.md)
- Description:
  Render clean result items with visual badges, bold character highlights, and favicons.
- Requirements:
  - Highlight matched character indices in title and URL.
  - Render source badges (`TAB`, `BM`, `HIST`).
  - Load favicons via Chrome internal favicon URL with SVG domain-letter fallback.
- Acceptance Criteria:
  - Matched characters are clearly visible.
  - Missing favicons gracefully fall back to monogram SVGs without layout shifts.

---

#### Issue 2.6: Smart Ephemeral Redirect and OAuth Noise Filtering
- Title: `feat(history): smart ephemeral redirect and oauth noise filtering`
- Labels: `area:search`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-05 Section 2: Tracking Parameter Sanitization](specs/05-unified-results-and-deduplication.md)
  - [SPEC-11 Section 2: History Privacy & Excluded Domain Scrubbing](specs/11-privacy-security-and-safety.md)
- Description:
  Filter out ephemeral OAuth callbacks, auth redirects, and noisy intermediate pages from history search results.
- Requirements:
  - Maintain default pattern blacklist for ephemeral auth/redirect URLs (e.g. `accounts.google.com/o/oauth2`, `login.microsoftonline.com`, `/callback`, `/oauth/authorize`, `/saml`).
  - De-prioritize or filter out raw search engine query result pages (e.g. `google.com/search?q=...`) unless explicitly scoped.
  - Allow user to toggle or customize noise filters in settings.
- Acceptance Criteria:
  - Searching history does not get polluted with expired login redirects or one-time OAuth verification URLs.

---

### Milestone 3: Query Parser, Scopes and Custom Aliases

- Goal: Implement EBNF query grammar, built-in slash scopes, and user-defined domain aliases.
- Target Specs: [SPEC-02 (Query Syntax & Parser)](specs/02-query-syntax-and-parser.md)

#### Issue 3.1: Query Lexer and AST Generator
- Title: `feat(parser): query lexer and ast generator`
- Labels: `area:parser`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-02 Section 1: Formal Query Grammar & Lexer Architecture](specs/02-query-syntax-and-parser.md)
  - [SPEC-02 Section 2: Concrete Parsing Walkthroughs](specs/02-query-syntax-and-parser.md)
- Description:
  Build a lexer and parser that generates a typed Abstract Syntax Tree from search input.
- Requirements:
  - Tokenize modifiers, scopes, aliases, filter operators, quoted strings, and negated terms.
  - Support order independence (e.g. `@query /history jira` equals `/history @query jira`).
  - Support exact phrase search with double quotes (`"pull request"`).
  - Support negation tokens (`-draft`).
- Acceptance Criteria:
  - Valid AST produced for complex composite queries.
  - Quoted tokens are preserved intact; negated terms filter out matching candidates.

#### Issue 3.2: Built-in Scope Commands and Fallback
- Title: `feat(scope): built-in scope commands and fallback`
- Labels: `area:parser`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-02 Section 3: Data-Driven Scope Registry & Configurable Built-in Scopes](specs/02-query-syntax-and-parser.md)
- Description:
  Provide slash commands to restrict candidate search sources directly.
- Requirements:
  - Support `/tab`, `/t` for open tabs only.
  - Support `/bm`, `/bookmark` for bookmarks only.
  - Support `/history`, `/ht` for history only.
  - Fall back gracefully: unknown slash commands (e.g. `/foo bar`) are treated as literal search text.
- Acceptance Criteria:
  - Entering `/tab react` strictly limits candidates to active tabs.
  - Typing an unconfigured slash command does not throw errors or blank the UI.

#### Issue 3.3: Custom Domain Aliases and Head-Anchored Prefixes
- Title: `feat(alias): custom domain aliases and head-anchored prefixes`
- Labels: `area:parser`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-02 Section 4: Custom Domain Scopes & Multi-Domain Mapping](specs/02-query-syntax-and-parser.md)
  - [SPEC-02 Section 5: Custom Trigger Prefixes & Head-Anchoring](specs/02-query-syntax-and-parser.md)
  - [SPEC-02 Section 6: Alias Conflict Resolution & Precedence Rules](specs/02-query-syntax-and-parser.md)
- Description:
  Enable users to define custom triggers mapping to specific domains or wildcard paths.
- Requirements:
  - Support custom alias model (name, triggers, domains, URL patterns).
  - Support plain text prefixes (e.g. `GH`, `gh`, `JIRA`) case-insensitively.
  - Enforce head-anchoring: prefix only triggers if it is the first token in the query.
  - Resolve conflicts: system scopes beat custom aliases, longest match wins.
- Acceptance Criteria:
  - Typing `GH react` scopes search to `github.com`.
  - Typing `react GH hook` does not trigger the GitHub alias.

#### Issue 3.4: URL Navigation Modifiers (@query and @domain)
- Title: `feat(modifier): url navigation modifiers`
- Labels: `area:parser`, `area:tabs`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-02 Section 7: Search Modifiers Specification](specs/02-query-syntax-and-parser.md)
- Description:
  Allow users to control destination URL depth using `@query` and `@domain` modifiers.
- Requirements:
  - `@query`: Preserves full URL path and query parameters upon navigation.
  - `@domain`: Strips pathname and query parameters, collapsing navigation to root domain.
  - Modifier explicitly overrides alias or global navigation defaults.
- Acceptance Criteria:
  - Selecting item with `@domain` modifier navigates to `https://jira.com/` instead of deep issue path.
  - Selecting item with `@query` preserves all query parameters.

---

#### Issue 3.5: Data-Driven Scope Registry with Configurable Default Triggers
- Title: `refactor(parser): data-driven scope registry with configurable default triggers`
- Labels: `area:parser`, `type:refactor`, `priority:p0`
- Effort: M
- Related Specs:
  - [SPEC-02 Section 3: Data-Driven Scope Registry & Configurable Built-in Scopes](specs/02-query-syntax-and-parser.md)
  - [SPEC-12 Section 1: Comprehensive Configuration Schema](specs/12-configuration-and-appearance.md)
- Description:
  Eliminate hardcoded `/tab`, `/bm`, and `/history` string checks in query parser. Implement a data-driven ScopeRegistry that initializes with system defaults but allows full user customization.
- Requirements:
  - Define `ScopeDefinition` entity with `defaultTriggers` and `userTriggers`.
  - Seed default scopes: tabs (`/tab`, `/t`), bookmarks (`/bm`, `/b`), history (`/history`, `/ht`), pins (`/pin`), recent (`/recent`), commands (`>`).
  - Parser queries ScopeRegistry dynamically rather than checking hardcoded constants.
  - Allow users to modify triggers, add prefixes, or disable individual scopes.
- Acceptance Criteria:
  - User can change `/tab` to `/o` or `/t` to `/tab` in settings and parser respects new triggers immediately.
  - System scope providers remain immutable while their trigger keywords are completely customizable.

---

### Milestone 4: Ranking Math and Local Usage Learning

- Goal: Implement multi-factor scoring formula, exponential recency decay, and on-device learning.
- Target Specs: [SPEC-04 (Ranking & Relevance)](specs/04-ranking-and-relevance.md)

#### Issue 4.1: Multi-Factor Composite Scoring Formula
- Title: `feat(ranking): multi-factor composite scoring formula`
- Labels: `area:ranking`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-04 Section 1: Multi-Factor Scoring Mathematical Model](specs/04-ranking-and-relevance.md)
  - [SPEC-04 Section 2: Source Weighting Hierarchy](specs/04-ranking-and-relevance.md)
  - [SPEC-04 Section 3: Window Locality & Contextual Boosts](specs/04-ranking-and-relevance.md)
  - [SPEC-04 Section 4: Active Tab Suppression Policy](specs/04-ranking-and-relevance.md)
- Description:
  Combine match quality, source weights, window locality, and active tab status into a composite ranking score.
- Requirements:
  - Base source weights: Pin (1.4), Tab (1.2), Bookmark (1.0), History (0.7).
  - Current window boost: Apply +0.15 to tabs located in the active Chrome window.
  - Active tab penalty: Deduct 0.40 from the active tab or hide it completely based on user setting.
- Acceptance Criteria:
  - Open tabs in the current window rank above distant history items with similar match quality.
  - Currently focused tab does not occupy the top result slot.

#### Issue 4.2: Exponential Recency Decay Function
- Title: `feat(ranking): exponential recency decay function`
- Labels: `area:ranking`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-04 Section 5: Recency Boost & Exponential Decay Function](specs/04-ranking-and-relevance.md)
- Description:
  Apply time-decayed recency bonus to recently accessed items.
- Requirements:
  - Implement exponential decay: `B_recency = M_recency * e^(-lambda * delta_t)`.
  - Configure half-life of 24 hours.
  - Pages visited in the last hour receive near-maximum bonus.
- Acceptance Criteria:
  - Recently visited pages rank higher than pages visited days ago for identical text matches.

#### Issue 4.3: Local Query-to-Destination Feedback Loop
- Title: `feat(learning): local query-to-destination feedback loop`
- Labels: `area:ranking`, `area:storage`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-04 Section 6: Access Frequency Multiplier](specs/04-ranking-and-relevance.md)
  - [SPEC-04 Section 7: Local Frequency-Based Feedback](specs/04-ranking-and-relevance.md)
  - [SPEC-04 Section 8: Learned Weight Decay](specs/04-ranking-and-relevance.md)
- Description:
  Build an on-device feedback loop that reinforces user selections for specific search terms.
- Requirements:
  - Record selection events `(normalizedQuery, canonicalUrl, timestamp)` in `chrome.storage.local`.
  - Increment learned bonus when the same query is repeated.
  - Apply 30-day linear decay to unused learned associations.
  - Provide a one-click purge button to clear all learned rankings.
- Acceptance Criteria:
  - Selecting a specific dashboard for query `prod` promotes it to rank 1 after 2 to 3 selections.
  - No data is sent across the network.

#### Issue 4.4: Search Ranking Inspector and Debug Mode
- Title: `feat(debug): search ranking inspector and debug mode`
- Labels: `area:ranking`, `type:feat`, `priority:p2`
- Effort: S
- Related Specs:
  - [SPEC-04 Section 1: Multi-Factor Scoring Mathematical Model](specs/04-ranking-and-relevance.md)
  - [SPEC-12 Section 5: Feature Flags & Experimental Labs](specs/12-configuration-and-appearance.md)
- Description:
  Provide a diagnostic ranking view for power users and developers.
- Requirements:
  - Triggered via `/debug` command or setting toggle.
  - Display mathematical breakdown of each score component (match, source, window, recency, learned).
- Acceptance Criteria:
  - Selecting a row in debug mode shows exact contribution of each scoring factor.

---

### Milestone 5: Advanced Keyboard Interactions and Action Menu

- Goal: Complete full keyboard control, selection stability during async loads, and contextual Actions Menu.
- Target Specs: [SPEC-06 (Keyboard & User Interaction)](specs/06-keyboard-and-user-interaction.md)

#### Issue 5.1: Navigation Modifier Keybindings
- Title: `feat(nav): navigation modifier keybindings`
- Labels: `area:keyboard`, `area:tabs`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-06 Section 1: Primary Keyboard Navigation Controls](specs/06-keyboard-and-user-interaction.md)
  - [SPEC-07 Section 4: Modifier Keybindings for Tab Creation](specs/07-navigation-and-tab-management.md)
- Description:
  Support power-user chord modifiers when opening results.
- Requirements:
  - `Alt+Enter`: Force open in new tab (bypasses tab reuse).
  - `Shift+Enter`: Open in background tab without stealing active focus.
  - `Ctrl+Shift+Enter`: Open in new browser window.
- Acceptance Criteria:
  - Pressing `Alt+Enter` always creates a new tab regardless of open tabs.
  - Pressing `Shift+Enter` opens tab in background and keeps current page focused.

#### Issue 5.2: Anchor-Based Selection Stability for Asynchronous Streaming
- Title: `feat(ui): anchor-based selection stability for asynchronous streaming`
- Labels: `area:keyboard`, `area:overlay`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-06 Section 2: Selection Stability Across Asynchronous Updates](specs/06-keyboard-and-user-interaction.md)
- Description:
  Prevent active cursor jumping when delayed search results arrive from slow sources.
- Requirements:
  - Anchor selection to item deterministic UUID rather than numerical index.
  - When re-sorting list upon arrival of history results, recompute active index matching the anchored UUID.
  - If anchored item is pruned, select nearest adjacent item.
- Acceptance Criteria:
  - Cursor never jumps to an unintended item while typing and navigating rapidly.

#### Issue 5.3: Contextual Action Menu (Ctrl+K)
- Title: `feat(actions): contextual action menu`
- Labels: `area:keyboard`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-06 Section 5: Contextual Actions Menu (Ctrl+K)](specs/06-keyboard-and-user-interaction.md)
- Description:
  Provide secondary actions on highlighted items via `Ctrl+K` or `RightArrow`.
- Requirements:
  - Open actions sub-menu with breadcrumb display: `Search > Item > Actions`.
  - Actions include: Open, Open New Tab, Copy URL, Copy Markdown, Pin, Close Tab, Remove History.
  - Actions menu is searchable by typing.
  - Pressing Backspace on empty input returns to main search palette.
- Acceptance Criteria:
  - Power users can trigger secondary actions without taking hands off keyboard.

#### Issue 5.4: Multi-Format Clipboard Operations
- Title: `feat(clipboard): multi-format clipboard operations`
- Labels: `area:keyboard`, `type:feat`, `priority:p2`
- Effort: S
- Related Specs:
  - [SPEC-06 Section 4: Clipboard Operations & Multi-Format Exporters](specs/06-keyboard-and-user-interaction.md)
- Description:
  Allow instant copying of URL and title without opening the web page.
- Requirements:
  - `Ctrl+C`: Copy canonical URL to system clipboard.
  - `Ctrl+Shift+C`: Copy Markdown anchor `[Title](URL)`.
  - Show temporary confirmation badge on row.
- Acceptance Criteria:
  - Markdown anchor is copied formatted and immediately pasteable into editors.

#### Issue 5.5: Multi-Select and Batch Tab Management
- Title: `feat(batch): multi-select and batch tab management`
- Labels: `area:keyboard`, `area:tabs`, `type:feat`, `priority:p2`
- Effort: M
- Related Specs:
  - [SPEC-06 Section 6: Multi-Select & Batch Operations](specs/06-keyboard-and-user-interaction.md)
  - [SPEC-07 Section 6: Tab Manipulation Actions Catalog](specs/07-navigation-and-tab-management.md)
- Description:
  Support selecting multiple items for batch operations.
- Requirements:
  - `Shift+Down` and `Space` toggle selection checkboxes.
  - Batch actions exposed via `Ctrl+K`: Close Selected Tabs, Bookmark Selected, Copy All URLs.
- Acceptance Criteria:
  - Users can select 5 tabs and close all 5 in a single operation.

---

#### Issue 5.6: Query Draft Recovery on Accidental Dismissal
- Title: `feat(ux): query draft recovery on accidental dismissal`
- Labels: `area:overlay`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-01 Section 6: Overlay Dismissal Triggers](specs/01-invocation-and-overlay.md)
  - [SPEC-06 Section 1: Primary Keyboard Navigation Controls](specs/06-keyboard-and-user-interaction.md)
- Description:
  Prevent loss of context when a user accidentally clicks outside or presses Escape while composing a long search query.
- Requirements:
  - Store the last non-empty query string and active selection in ephemeral session storage upon dismissal.
  - If user summons Navigator again within 10 seconds, restore the draft query and selection index.
  - Add subtle visual indicator: `Restored previous draft (Press Esc to clear)`.
- Acceptance Criteria:
  - Accidental clicks outside the palette do not cause user to retype complex multi-token queries.

---

### Milestone 6: Pins, Zero-State Dashboard and Tab Hygiene

- Goal: Implement persistent polymorphic pins, the empty-state dashboard, and duplicate tab cleanup.
- Target Specs: [SPEC-07 (Navigation & Tab Management)](specs/07-navigation-and-tab-management.md), [SPEC-08 (Pins, Favorites & Context)](specs/08-pins-favorites-and-context.md)

#### Issue 6.1: Polymorphic Pinning Subsystem
- Title: `feat(pins): polymorphic pinning subsystem`
- Labels: `area:storage`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-08 Section 1: First-Class Pinning Subsystem](specs/08-pins-favorites-and-context.md)
  - [SPEC-08 Section 2: Polymorphic Pin Semantics](specs/08-pins-favorites-and-context.md)
- Description:
  Provide first-class pin support with multiple target semantics.
- Requirements:
  - Toggle pin via `Alt+P`.
  - Support URL Pin, Domain Pin, Search Pin, and Command Pin.
  - Quick launch slots: Map top 9 pins to `Alt+1` through `Alt+9`.
  - Persist pins in `chrome.storage.sync`.
- Acceptance Criteria:
  - Pressing `Alt+1` instantly navigates to Pin 1.
  - Pinned items sync across Chrome browsers signed into the same profile.

#### Issue 6.2: Default Empty Query Viewport (Zero-State Dashboard)
- Title: `feat(dashboard): default empty query viewport`
- Labels: `area:overlay`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-08 Section 4: Default Empty Query Viewport (Zero-State View)](specs/08-pins-favorites-and-context.md)
- Description:
  Render a curated command dashboard when Navigator opens with an empty query.
- Requirements:
  - Section 1: Pinned Items ordered by user index.
  - Section 2: Recently Accessed items from Navigator sessions.
  - Section 3: Recently Closed Tabs for quick restoration.
  - Section 4: Frequent Destinations based on visit statistics.
- Acceptance Criteria:
  - Opening Navigator presents immediate navigation targets without requiring typing.

#### Issue 6.3: Duplicate Tab Detection and Batch Pruning
- Title: `feat(cleanup): duplicate tab detection and batch pruning`
- Labels: `area:tabs`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-07 Section 6: Tab Manipulation Actions Catalog](specs/07-navigation-and-tab-management.md)
- Description:
  Identify open tabs sharing identical canonical URLs and provide automated cleanup.
- Requirements:
  - Scan all open tabs across windows and cluster by canonical URL.
  - Expose command `Close Duplicate Tabs` (`/clean`).
  - Retain oldest or most recently active tab in each cluster; close duplicates.
- Acceptance Criteria:
  - Command correctly closes redundant duplicate tabs and reports count closed.

#### Issue 6.4: Chrome Tab Groups Integration
- Title: `feat(groups): chrome tab groups integration`
- Labels: `area:tabs`, `type:feat`, `priority:p2`
- Effort: M
- Related Specs:
  - [SPEC-07 Section 8: Chrome Tab Groups Integration](specs/07-navigation-and-tab-management.md)
- Description:
  Search and manipulate Chrome tab groups.
- Requirements:
  - Filter tabs by group title or color via `/groups` or group name query.
  - Display group chip badge on tab results.
  - Actions: Collapse group, expand group, close group.
- Acceptance Criteria:
  - Users can search and manage tab groups directly from the command palette.

---

#### Issue 6.5: Audible Tabs Hunting and Quick Mute Toggle
- Title: `feat(audio): audible tabs hunting and quick mute toggle`
- Labels: `area:tabs`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-07 Section 6: Tab Manipulation Actions Catalog](specs/07-navigation-and-tab-management.md)
- Description:
  Solve the common user pain point of finding and silencing noisy background tabs.
- Requirements:
  - Add `/audio` built-in scope to surface only tabs currently producing sound or muted.
  - Sort currently audible tabs to top of result candidates when audio is detected.
  - Provide quick shortcut (`M` or `Space` in list) to toggle mute state immediately.
  - Add command `Mute All Other Tabs`.
- Acceptance Criteria:
  - User can type `/audio` to see all noisy tabs and mute them with a single keystroke.

---

### Milestone 7: Configuration, Persistence and Backup

- Goal: Build settings system, schema migrations, and JSON import/export.
- Target Specs: [SPEC-10 (Storage, Sync & Migrations)](specs/10-storage-sync-and-migrations.md), [SPEC-12 (Configuration & Appearance)](specs/12-configuration-and-appearance.md)

#### Issue 7.1: Tiered Storage Architecture and Sync
- Title: `feat(storage): tiered storage architecture and sync`
- Labels: `area:storage`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-10 Section 1: Tiered Storage Subsystem Architecture](specs/10-storage-sync-and-migrations.md)
  - [SPEC-10 Section 2: Cross-Device Synchronization Strategy](specs/10-storage-sync-and-migrations.md)
- Description:
  Implement three-tier storage model according to Chrome quota constraints.
- Requirements:
  - Tier 1: `chrome.storage.sync` for settings, pins, aliases, domain rules.
  - Tier 2: `chrome.storage.local` for learned ranking data, recents, scratchpad.
  - Tier 3: `IndexedDB` for tri-gram inverted search index and caches.
- Acceptance Criteria:
  - Settings and pins sync across devices without exceeding the 100KB sync quota.

#### Issue 7.2: Automated Schema Migration Pipeline
- Title: `feat(migration): automated schema migration pipeline`
- Labels: `area:storage`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-10 Section 3: Data Schema Versioning & Automated Migrations](specs/10-storage-sync-and-migrations.md)
  - [SPEC-10 Section 4: Corrupt Store Fallback & Error Recovery](specs/10-storage-sync-and-migrations.md)
- Description:
  Provide safe, versioned migrations for persistent data.
- Requirements:
  - Include monotonic `schemaVersion` integer in stored configuration.
  - Execute sequential migration functions during extension update.
  - Implement corrupted store fallback to safe defaults with rescue backup.
- Acceptance Criteria:
  - Extension upgrades never wipe user pins, aliases, or settings.

#### Issue 7.3: Appearance, Themes and Density Configuration
- Title: `feat(settings): appearance, themes and density configuration`
- Labels: `area:overlay`, `type:feat`, `priority:p2`
- Effort: S
- Related Specs:
  - [SPEC-12 Section 1: Comprehensive Configuration Schema](specs/12-configuration-and-appearance.md)
  - [SPEC-12 Section 2: Appearance Customization & Themes](specs/12-configuration-and-appearance.md)
- Description:
  Provide appearance preferences in extension settings.
- Requirements:
  - Themes: System auto-detect, Dark, Light, High Contrast.
  - Density modes: Compact (40px), Normal (52px), Comfortable (64px).
  - Palette width: Small (540px), Normal (640px), Wide (760px).
- Acceptance Criteria:
  - Theme and density switch instantly without requiring page reload.

#### Issue 7.4: JSON Configuration Export, Import and Reset
- Title: `feat(backup): json configuration export, import and reset`
- Labels: `area:storage`, `type:feat`, `priority:p2`
- Effort: S
- Related Specs:
  - [SPEC-10 Section 5: JSON Schema Export & Import Engine](specs/10-storage-sync-and-migrations.md)
  - [SPEC-10 Section 6: Backup Archiving & Factory Reset](specs/10-storage-sync-and-migrations.md)
- Description:
  Allow full backup and restoration of user configurations.
- Requirements:
  - Export complete configuration as a validated JSON file.
  - Import JSON with schema validation and conflict resolution (merge vs overwrite).
  - Selective and full factory reset options.
- Acceptance Criteria:
  - Exported configuration file restores cleanly on a fresh browser profile.

---

#### Issue 7.5: Dedicated Extension Settings Page (options.html)
- Title: `feat(options): dedicated extension settings page (options.html)`
- Labels: `area:storage`, `type:feat`, `priority:p0`
- Effort: L
- Related Specs:
  - [SPEC-12 Section 1: Comprehensive Configuration Schema](specs/12-configuration-and-appearance.md)
  - [SPEC-12 Section 6: Dedicated Extension Options Page](specs/12-configuration-and-appearance.md)
- Description:
  Build a full-featured, dedicated Web Extension Options Page (`options.html`) providing an intuitive graphical settings dashboard.
- Requirements:
  - Tab 1: General (In-page shortcut remapping, blur dismiss toggles, query memory).
  - Tab 2: Scopes and Aliases (Visual editor for modifying default scope triggers and creating custom domain aliases).
  - Tab 3: Domain and URL Rules (Query parameter whitelists, tracking stripping, domain exclusions for Shift+O).
  - Tab 4: Appearance (Theme switcher for Dark/Light/System/High-Contrast, density mode, palette width).
  - Tab 5: Privacy and Data (Learned ranking purge button, sensitive domain exclusion list, JSON backup export/import).
- Acceptance Criteria:
  - Opens via right-click extension icon -> Options, or via `/settings` command.
  - Changes save reactively to `chrome.storage.sync` and apply immediately to content scripts without page reload.

---

### Milestone 8: Robustness, Web Compatibility and Accessibility

- Goal: Guarantee flawless execution on complex web apps, full keyboard accessibility, and international input support.
- Target Specs: [SPEC-01 (Invocation & Overlay)](specs/01-invocation-and-overlay.md), [SPEC-11 (Privacy, Security & Safety)](specs/11-privacy-security-and-safety.md)

#### Issue 8.1: Web App Conflict Exclusions and Custom Keybindings
- Title: `feat(compat): web app conflict exclusions and custom keybindings`
- Labels: `area:compat`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-01 Section 1: Primary Invocation Shortcut & Keystroke Filtering](specs/01-invocation-and-overlay.md)
  - [SPEC-12 Section 3: Domain Navigation & Rewrite Rules](specs/12-configuration-and-appearance.md)
- Description:
  Prevent shortcut collisions on web applications with rich keyboard shortcuts.
- Requirements:
  - Configure domain exclusion list (e.g. `figma.com`, `docs.google.com`).
  - Disable in-page `Shift+O` on excluded domains, preserving global browser shortcut.
  - Support per-domain shortcut remapping.
- Acceptance Criteria:
  - Figma and Google Docs function without in-page Navigator shortcut interference.

#### Issue 8.2: Fullscreen and Iframe Attachment Handling
- Title: `feat(compat): fullscreen and iframe attachment handling`
- Labels: `area:compat`, `type:feat`, `priority:p2`
- Effort: S
- Related Specs:
  - [SPEC-01 Section 4: Overlay Injection & DOM Isolation](specs/01-invocation-and-overlay.md)
  - [SPEC-01 Section 5: Positioning, Geometry & Responsive Layout](specs/01-invocation-and-overlay.md)
- Description:
  Ensure overlay displays correctly during video fullscreen and inside iframes.
- Requirements:
  - Detect `document.fullscreenElement` and attach overlay host inside it.
  - Restrict overlay rendering to top frame to avoid duplicate UI in iframes.
- Acceptance Criteria:
  - Navigator opens cleanly over fullscreen YouTube videos and presentations.

#### Issue 8.3: WCAG 2.1 AA Accessibility and Focus Trap
- Title: `feat(a11y): wcag 2.1 aa accessibility and focus trap`
- Labels: `area:a11y`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-01 Section 8: Accessibility & WCAG 2.1 AA Compliance](specs/01-invocation-and-overlay.md)
- Description:
  Implement complete ARIA semantics and focus management.
- Requirements:
  - Implement WAI-ARIA Combobox 1.2 pattern (`role="combobox"`, `role="listbox"`, `aria-activedescendant`).
  - Add invisible `aria-live="polite"` region announcing result counts and active item.
  - Trap Tab focus inside palette while open.
- Acceptance Criteria:
  - Screen readers (NVDA, VoiceOver) announce items and counts correctly.

#### Issue 8.4: IME Composition and International Text Normalization
- Title: `feat(i18n): ime composition and international text normalization`
- Labels: `area:a11y`, `area:search`, `type:feat`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-01 Section 7: Search Input Mechanics & Autofocus](specs/01-invocation-and-overlay.md)
  - [SPEC-03 Section 3: Multi-Tier Matching Pipeline](specs/03-search-engine-and-matching.md)
  - [SPEC-12 Section 4: Internationalization (i18n) & Relative Timestamps](specs/12-configuration-and-appearance.md)
- Description:
  Support international keyboard input (CJK, Vietnamese) without accidental triggers.
- Requirements:
  - Listen to `compositionstart` and `compositionend`.
  - Suppress Enter navigation and list traversal while `isComposing` is true.
  - Normalize text using Unicode Canonical Decomposition (`NFKD`) to strip diacritics.
- Acceptance Criteria:
  - Striking Enter to commit an IME candidate does not navigate or close Navigator.
  - Searching `cafe` matches `café`; searching `hanoi` matches `Hà Nội`.

---

#### Issue 8.5: Interactive Keyboard Shortcut Playground and Cheat-Sheet
- Title: `feat(onboarding): interactive keyboard shortcut playground and cheat-sheet`
- Labels: `area:overlay`, `type:feat`, `priority:p2`
- Effort: S
- Related Specs:
  - [SPEC-01 Section 1: Primary Invocation Shortcut & Keystroke Filtering](specs/01-invocation-and-overlay.md)
  - [SPEC-06 Section 1: Primary Keyboard Navigation Controls](specs/06-keyboard-and-user-interaction.md)
- Description:
  Provide an interactive onboarding guide on first install to teach core keyboard shortcuts without leaving the browser page.
- Requirements:
  - Detect extension install event (`chrome.runtime.onInstalled` with reason `'install'`).
  - Open a lightweight interactive modal playground demonstrating `Shift+O`, arrow navigation, `Alt+Enter`, and `Ctrl+K`.
  - Provide a persistent help footer in palette showing context-aware shortcut hints based on current selection.
- Acceptance Criteria:
  - New users learn the 4 essential shortcuts within 30 seconds of installing the extension.

---

### Milestone 9: Utilities, Tools and Omnibox Integration

- Goal: Implement lightweight inline tools, Chrome Omnibox keyword, and URL launch commands.
- Target Specs: [SPEC-09 (Commands, Tools & Integrations)](specs/09-commands-tools-and-integrations.md)

#### Issue 9.1: Chrome Address Bar Omnibox Integration
- Title: `feat(omnibox): chrome address bar omnibox integration`
- Labels: `area:tools`, `type:feat`, `priority:p2`
- Effort: M
- Related Specs:
  - [SPEC-09 Section 2: Omnibox Integration (Keyword nav)](specs/09-commands-tools-and-integrations.md)
- Description:
  Enable searching Chrome Navigator directly from Chrome's primary address bar.
- Requirements:
  - Register keyword `nav` with `chrome.omnibox`.
  - Stream formatted suggestions into address bar dropdown.
  - Selecting an entry performs tab reuse or navigation.
- Acceptance Criteria:
  - Typing `nav` followed by Space in Chrome address bar searches tabs and bookmarks.

#### Issue 9.2: Sandboxed Inline Math Calculator
- Title: `feat(tools): sandboxed inline math calculator`
- Labels: `area:tools`, `type:feat`, `priority:p2`
- Effort: S
- Related Specs:
  - [SPEC-09 Section 4: Quick Math Parser & Inline Calculator](specs/09-commands-tools-and-integrations.md)
- Description:
  Evaluate math expressions directly within the search bar.
- Requirements:
  - Trigger when query starts with `=` or matches numerical math expressions.
  - Parse and evaluate using a sandboxed AST evaluator (never use `eval`).
  - Pressing Enter copies result to clipboard and dismisses overlay.
- Acceptance Criteria:
  - Typing `= 1920 * 1080 / 2` calculates `1036800` and copies to clipboard on Enter.

#### Issue 9.3: Direct URL Launcher and Hostname Autocomplete
- Title: `feat(tools): direct url launcher and hostname autocomplete`
- Labels: `area:tools`, `type:feat`, `priority:p2`
- Effort: S
- Related Specs:
  - [SPEC-09 Section 3: URL Launcher & Direct Navigation](specs/09-commands-tools-and-integrations.md)
- Description:
  Detect when input is a valid URL or hostname and provide direct navigation.
- Requirements:
  - Recognize URLs, localhost with ports, and standard domain formats.
  - Surface top action `Navigate to URL` bypassing search engine providers.
- Acceptance Criteria:
  - Pasting `http://localhost:3000/api` provides instant navigation option.

#### Issue 9.4: Web Search Engine Fallbacks and URL Templates
- Title: `feat(tools): web search engine fallbacks and url templates`
- Labels: `area:tools`, `type:feat`, `priority:p2`
- Effort: S
- Related Specs:
  - [SPEC-09 Section 5: Web Search Engine Fallback & URL Templates](specs/09-commands-tools-and-integrations.md)
- Description:
  Provide fallback searches when local resources produce zero results.
- Requirements:
  - Present `Search Google for "query"` option in zero-result state.
  - Support configurable URL templates (e.g. GitHub issues, NPM package lookup).
- Acceptance Criteria:
  - Selecting web fallback opens user search query in default web search engine.

---

### Milestone 10: Performance Optimization and Extensibility

- Goal: Maximum performance under stress loads (10k+ bookmarks) and architecture for external plugins.
- Target Specs: [SPEC-13 (Performance & Scalability)](specs/13-performance-and-scalability.md), [SPEC-14 (Integrations & Extensibility)](specs/14-integrations-and-extensibility.md)

#### Issue 10.1: In-Memory Tri-Gram Inverted Index
- Title: `perf(scale): in-memory tri-gram inverted index`
- Labels: `area:perf`, `area:search`, `type:perf`, `priority:p1`
- Effort: L
- Related Specs:
  - [SPEC-03 Section 8: In-Memory Tri-Gram Inverted Index](specs/03-search-engine-and-matching.md)
  - [SPEC-13 Section 2: High-Volume Scalability Benchmarks](specs/13-performance-and-scalability.md)
- Description:
  Build tri-gram inverted index for sub-millisecond lookups across 10,000+ bookmarks.
- Requirements:
  - Index document tokens into 3-character keys mapped to document ID bitsets.
  - Query candidates via fast set intersection.
  - Maintain background memory footprint under 35MB.
- Acceptance Criteria:
  - Benchmark search across 10,000 bookmarks returns in under 5ms.

#### Issue 10.2: Monotonic Request Cancellation and Concurrency Control
- Title: `perf(concurrency): monotonic request cancellation and concurrency control`
- Labels: `area:perf`, `type:perf`, `priority:p1`
- Effort: S
- Related Specs:
  - [SPEC-13 Section 3: Tiered Asynchronous Result Streaming](specs/13-performance-and-scalability.md)
  - [SPEC-13 Section 4: Search Cancellation & Stale Result Rejection](specs/13-performance-and-scalability.md)
  - [SPEC-13 Section 5: Keystroke Debounce Architecture](specs/13-performance-and-scalability.md)
- Description:
  Eliminate race conditions from rapid keystrokes using monotonic tokens.
- Requirements:
  - Tag outgoing queries with incrementing `monotonicRequestId`.
  - Abort in-flight history searches with `AbortController`.
  - Discard any received responses where `requestId < currentRequestId`.
- Acceptance Criteria:
  - Typing fast (e.g. 10 keystrokes in 1 second) never renders stale results from early keystrokes.

#### Issue 10.3: Remote Resource Provider and Plugin Interface
- Title: `feat(ext): remote resource provider and plugin interface`
- Labels: `area:compat`, `type:feat`, `priority:p3`
- Effort: M
- Related Specs:
  - [SPEC-14 Section 1: Remote Integration Architecture](specs/14-integrations-and-extensibility.md)
  - [SPEC-14 Section 2: Remote Resource Entity Contract](specs/14-integrations-and-extensibility.md)
  - [SPEC-14 Section 4: Standard Provider Extension Interface](specs/14-integrations-and-extensibility.md)
- Description:
  Establish contract for optional external integrations (GitHub, Jira, Linear APIs).
- Requirements:
  - Define `SearchProvider` and `RemoteResource` TypeScript interfaces.
  - Strict fault isolation: Remote network failures or timeouts never block local searches.
  - Bypassed entirely when offline (`navigator.onLine === false`).
- Acceptance Criteria:
  - Future plugins can register search providers without altering core navigator code.

#### Issue 10.4: Unit and Integration Testing Suite for Parser and Ranker
- Title: `test(core): unit and integration testing suite for parser and ranker`
- Labels: `area:search`, `type:feat`, `priority:p1`
- Effort: M
- Related Specs:
  - [SPEC-02 Section 1: Formal Query Grammar & Lexer Architecture](specs/02-query-syntax-and-parser.md)
  - [SPEC-03 Section 3: Multi-Tier Matching Pipeline](specs/03-search-engine-and-matching.md)
  - [SPEC-04 Section 1: Multi-Factor Scoring Mathematical Model](specs/04-ranking-and-relevance.md)
  - [SPEC-05 Section 1: URL Normalization Engine & Canonical Identity](specs/05-unified-results-and-deduplication.md)
- Description:
  Establish automated unit and integration tests using Vitest to prevent regressions in core algorithms.
- Requirements:
  - Test suite for Query Lexer and EBNF Parser (validating scopes, aliases, quotes, negation, escapes).
  - Test suite for URL Normalization and tracking parameter stripper.
  - Test suite for multi-tier matching (exact, prefix, token, fuzzy Smith-Waterman).
  - Test suite for composite ranking formula and recency decay math.
- Acceptance Criteria:
  - Test runner runs in under 3 seconds with 90%+ branch coverage across core parsing and ranking modules.

#### Issue 10.5: Chrome Web Store Packaging and Build Automation
- Title: `chore(release): chrome web store packaging and build automation`
- Labels: `area:overlay`, `type:feat`, `priority:p2`
- Effort: S
- Related Specs:
  - [SPEC-00 Section 3: Chrome Extension Architecture (Manifest V3)](specs/00-overview-and-architecture.md)
  - [SPEC-11 Section 1: Local-First Privacy Directives & Zero External Telemetry](specs/11-privacy-security-and-safety.md)
  - [SPEC-11 Section 5: URL Safety, Protocol Whitelist & Execution Defense](specs/11-privacy-security-and-safety.md)
- Description:
  Automate production asset generation, extension packaging, and Chrome Web Store zip validation.
- Requirements:
  - Generate crisp production icons in 16x16, 32x32, 48x48, and 128x128 formats.
  - Add `npm run zip` command to create clean, production-minified extension bundle.
  - Validate `manifest.json` against Chrome Web Store policies (CSP compliance, minimum permissions).
- Acceptance Criteria:
  - Running `npm run build && npm run zip` creates a ready-to-upload zip file without development artifacts.
