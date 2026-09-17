# Chrome Navigator

> Fast, keyboard-first universal command palette and navigator for Google Chrome.

Modern browsing is fragmented across dozens of windows, hundreds of tabs, thousands of bookmarks, and deep browsing histories. Chrome Navigator unifies them into a single, instant command palette injected directly into web pages.

Summon with a keystroke, type a fragment, and jump to the exact resource you need without touching the mouse.

```
Open -> Type -> Navigate
```

```
+----------------------------------------------------------------------+
| > Search tabs, bookmarks, history, and commands...                   |
+----------------------------------------------------------------------+
| GitHub / my-project                                      TAB [Win 1] |
| github.com/company/my-project                                        |
|                                                                      |
| GitHub: Where the world builds software                  BM          |
| github.com                                               /Work/Dev   |
|                                                                      |
| Pull Request #42: Add Fuzzy Matching Pipeline            HISTORY     |
| github.com/company/my-project/pull/42                                |
+----------------------------------------------------------------------+
| Enter Open   Alt+Enter New Tab   Shift+Enter Background   Ctrl+K Actions |
+----------------------------------------------------------------------+
```

## Why Chrome Navigator?

Chrome Navigator is built for developers and power users who live in their browser and refuse to waste time hunting through tabs, folders, or history pages.

- No Duplicate Tabs: If a destination URL is already open in any Chrome window, Navigator switches to it instantly instead of spawning clones.
- True Sub-Millisecond Speed: In-memory tri-gram indexing and virtualized DOM rendering guarantee single-frame (under 16ms) keystroke response.
- Data-Driven Scope Registry: Built-in scopes (/tab, /bm, /history, /audio) are fully customizable rather than hardcoded. Rebind /tab to /o or /bm to /b as you prefer.
- Audio Tab Hunting: Quickly locate and silence noisy background tabs with the /audio scope or instant Mute toggle.
- Query Draft Recovery: Accidental clicks outside or Escape dismissals within 10 seconds automatically restore your in-progress search query.
- Smart History Noise Filter: Automatically de-prioritizes ephemeral OAuth callbacks, auth redirects, and tracking clutter.
- URL Depth Control: Use @query to keep full path and parameters, or @domain to jump directly to the root domain.
- Dedicated Graphical Settings: Full options dashboard (options.html) to configure keybindings, custom aliases, domain rules, and themes.
- Local Adaptive Ranking: A private, on-device feedback loop boosts frequently and recently selected items for specific queries without cloud AI or external telemetry.
- Isolated Shadow DOM: Injected via a closed Shadow Root, guaranteeing zero stylesheet conflicts with host web applications.
- Zero-Telemetry Privacy: Everything runs locally. No tracking, no analytics, no external servers, full offline capability.

## Query Syntax

Navigator features a flexible, non-rigid query parser supporting scopes, modifiers, custom triggers, exact phrases, and exclusions.

```
[modifiers] [scope] [alias] [filters] [text]
```

### Examples

- `react`: Universal search across open tabs, bookmarks, and history.
- `/tab jira`: Filter only open browser tabs matching `jira`.
- `/audio`: Locate all background tabs currently producing sound or muted.
- `/bm rust`: Search exclusively within Chrome bookmarks.
- `/history pr`: Search recent browsing history entries.
- `/jira bug login`: Scope search to company Jira domains (`*.atlassian.net`, `jira.company.com`).
- `GH react`: Head-anchored custom prefix matching GitHub repositories.
- `@domain /jira login`: Strip path and parameters, navigating directly to the Jira root domain.
- `@query /history pull/42`: Preserve complete query parameters and deep paths from history.
- `"pull request" -draft`: Match exact phrase `"pull request"` while excluding items containing `draft`.
- `= 1920 * 1080 / 2`: Built-in sandboxed calculator mode.

## Keybinding Reference

| Shortcut | Context | Behavior |
| :--- | :--- | :--- |
| `Shift+O` | In-Page | Summon or dismiss the Navigator command palette |
| `Command+Shift+O` / `Ctrl+Shift+O` | Browser Global | Global browser command to toggle Navigator |
| `Down` / `Ctrl+N` | Palette | Move selection down |
| `Up` / `Ctrl+P` | Palette | Move selection up |
| `Home` / `PageUp` | Palette | Jump to top result |
| `End` / `PageDown` | Palette | Jump to bottom result |
| `Enter` | Palette | Activate item (switch tab or navigate) |
| `Alt+Enter` | Palette | Force open in new tab (bypasses tab reuse) |
| `Shift+Enter` | Palette | Open in background tab |
| `Ctrl+Shift+Enter` | Palette | Open in new browser window |
| `Ctrl+K` / `Right` | Palette | Open contextual Actions Menu |
| `M` / `Space` | Palette | Instant mute or unmute toggle on highlighted tab |
| `Ctrl+C` | Palette | Copy canonical URL to clipboard |
| `Ctrl+Shift+C` | Palette | Copy Markdown anchor `[Title](URL)` |
| `Alt+P` | Palette | Toggle pinned status for selected item |
| `Alt+1` ... `Alt+9` | Palette | Instant jump to Quick Slot / Pin 1 through 9 |
| `Shift+Down` / `Tab` | Palette | Toggle multi-select checkbox for batch actions |
| `Escape` | Palette | First press clears query; second press dismisses |

## System Architecture

Chrome Navigator runs under Google Chrome's Manifest V3 architecture with strict separation between host DOM execution and background indexing:

```
[ Host Web Page (Any Origin) ]
               |
    (Shift+O Keystroke Filter)
               v
  [ Content Script (Isolated) ]
               |
  [ Closed Shadow Root UI ] <---------------+
               |                             |
     (Chrome Runtime IPC)                    |
               v                             |
[ Service Worker Coordinator ]               |
               |                             |
      +--------+--------+                    |
      |                 |                    |
[ In-Memory Index ]  [ Browser APIs ]        |
  (Tri-gram Cache)    - chrome.tabs          |
  (Pins & Aliases)    - chrome.bookmarks     |
                      - chrome.history       |
      |                 |                    |
      +--------+--------+                    |
               |                             |
    [ Multi-Tier Ranker ]                    |
    (Exact / Prefix / Fuzzy)                 |
    (Recency & Frequency)                    |
               |                             |
               +---(Stream Ranked Results)---+
```

### Multi-Tier Search Pipeline

1. Tier 1: Exact Match (Title, domain, or canonical URL).
2. Tier 2: Prefix Match (Token and word-boundary starts).
3. Tier 3: Word Boundary Substring Match.
4. Tier 4: Unordered Token Match (Tokens matched in any position).
5. Tier 5: Fuzzy Alignment (Smith-Waterman distance with typo tolerance).

### Scoring Mathematical Model

Candidate ranking balances relevance, source priority, window locality, and usage learning:

$$\text{Score} = (S_{\text{match}} \times W_{\text{source}}) + B_{\text{window}} - P_{\text{active}} + B_{\text{recency}} + B_{\text{frequency}} + B_{\text{learned}} + B_{\text{context}}$$

- Source Weights: Pinned ($1.4$) > Tabs ($1.2$) > Bookmarks ($1.0$) > History ($0.7$).
- Window Locality: $+0.15$ boost for tabs located in the current focused window.
- Active Tab Demotion: Current tab penalized by $-0.40$ or hidden to prioritize navigation targets.
- Recency Decay: Exponential decay with a 24-hour half-life.
- Adaptive Learning: Locally remembers query-to-selection pairs without sending data off-device.

## Technical Specifications Suite & Project Roadmap

The project follows a comprehensive, modular specification standard. Full technical specifications and implementation plans are available:

- [Project Plan, Milestones and GitHub Issues](docs/PROJECT_PLAN.md): Complete engineering plan with 11 milestones, 53 issue specifications, and label taxonomy.
- [Master Index and Traceability Matrix](docs/specs/README.md): Requirement mapping for all 300 specifications.
- [00. Overview and System Architecture](docs/specs/00-overview-and-architecture.md): Vision, MV3 component model, and lifecycles.
- [01. Invocation, Overlay and Web Compatibility](docs/specs/01-invocation-and-overlay.md): Shortcuts, closed Shadow Root isolation, z-index, and accessibility.
- [02. Query Syntax, Lexer and AST Parser](docs/specs/02-query-syntax-and-parser.md): Formal EBNF grammar, data-driven scope registry, modifiers, and alias triggers.
- [03. Search Engine and Multi-Tier Matching](docs/specs/03-search-engine-and-matching.md): In-memory tri-gram index, token matching, and Unicode normalization.
- [04. Ranking, Relevance and Local Learning](docs/specs/04-ranking-and-relevance.md): Scoring math, boosts, recency decay, and on-device feedback loops.
- [05. Unified Results and Deduplication](docs/specs/05-unified-results-and-deduplication.md): Canonical URLs, parameter stripping, and result typography.
- [06. Keyboard Navigation and Interaction](docs/specs/06-keyboard-and-user-interaction.md): Focus management, selection stability, and action menus.
- [07. Navigation Semantics and Tab Operations](docs/specs/07-navigation-and-tab-management.md): Tab equality strategies, window focusing, and duplicate cleanup.
- [08. Pins, Favorites and Workspaces](docs/specs/08-pins-favorites-and-context.md): Polymorphic pins, zero-state dashboard, tagging, and workspaces.
- [09. Commands, Utilities and Omnibox](docs/specs/09-commands-tools-and-integrations.md): Command palette mode, calculator, URL launcher, and Omnibox keyword `nav`.
- [10. Persistence, Storage and Migrations](docs/specs/10-storage-sync-and-migrations.md): Three-tiered storage (`sync`, `local`, `IndexedDB`) and schema migrations.
- [11. Privacy Guarantees and Safety Boundaries](docs/specs/11-privacy-security-and-safety.md): Zero telemetry, protocol whitelisting, and optional permissions.
- [12. Configuration, Appearance and Themes](docs/specs/12-configuration-and-appearance.md): Settings schemas, dedicated Options Page (`options.html`), density modes, and domain rules.
- [13. Performance, Scalability and Concurrency](docs/specs/13-performance-and-scalability.md): Latency budgets, cancellation tokens, and stress profiles.
- [14. Integrations and Extensibility](docs/specs/14-integrations-and-extensibility.md): Remote provider contracts and plugin architecture.

## Installation & Development

### Prerequisites

- Google Chrome (or any Chromium-based browser: Brave, Edge, Arc) version 108 or newer.
- Node.js version 18 or newer and npm / pnpm.

### Setup

```bash
git clone https://github.com/Aethries/chrome-extension-history-search.git
cd chrome-extension-history-search
npm install
npm run build
```

### Load in Chrome

1. Navigate to `chrome://extensions` in your Chrome address bar.
2. Enable "Developer mode" using the toggle in the top-right corner.
3. Click "Load unpacked".
4. Select the `dist/` directory generated by the build process.
5. Open any website and press `Shift+O` to launch Chrome Navigator.

## Privacy Contract

Chrome Navigator has no tracking, no analytics, no external servers, and no user accounts. Your bookmarks, open tabs, search queries, and browsing history stay completely on your machine.

## License

Dual-licensed under the Apache 2.0 and MIT licenses. See [LICENSE](LICENSE) for details.
