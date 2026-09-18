<div align="center">

# ⚡ Chrome Navigator

**A blazing fast, keyboard-first universal command palette and fuzzy navigator for Chromium browsers.**

[![Manifest V3](https://img.shields.io/badge/Manifest-V3-success?logo=googlechrome&logoColor=white&style=for-the-badge)](https://developer.chrome.com/docs/extensions/mv3/intro/)
[![Chromium](https://img.shields.io/badge/Platform-Chromium%20%7C%20Brave%20%7C%20Edge%20%7C%20Arc-blue?logo=googlechrome&logoColor=white&style=for-the-badge)](https://www.chromium.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-blue?logo=typescript&logoColor=white&style=for-the-badge)](https://www.typescriptlang.org)
[![Framework](https://img.shields.io/badge/Framework-WXT%20%2B%20React-61dafb?logo=react&logoColor=black&style=for-the-badge)](https://wxt.dev)
[![Specs](https://img.shields.io/badge/Specs-15%20Docs%20%7C%20300%20Reqs-indigo?style=for-the-badge)](docs/specs/README.md)
[![Privacy](https://img.shields.io/badge/Privacy-100%25%20On--Device%20%7C%20Zero%20Telemetry-green?style=for-the-badge)](docs/specs/11-privacy-security-and-safety.md)
[![License](https://img.shields.io/badge/License-MIT%20%2F%20Apache--2.0-purple?style=for-the-badge)](LICENSE)

<p align="center">
  <a href="#overview">Overview</a> •
  <a href="#key-features">Key Features</a> •
  <a href="#command-palette-preview">Palette Preview</a> •
  <a href="#query-syntax--cheat-sheet">Query Syntax</a> •
  <a href="#keybinding-reference">Keybindings</a> •
  <a href="#system-architecture">Architecture</a> •
  <a href="#technical-specifications--roadmap">Specs & Roadmap</a> •
  <a href="#installation--development">Quickstart</a>
</p>

</div>

---

## Overview

Modern browsing is fragmented across dozens of open windows, hundreds of tabs, thousands of bookmarks, and deep browsing histories. **Chrome Navigator** unifies them into a single, instant command palette injected directly into any web page.

Summon with a keystroke, type a fragment, and navigate directly to your target without touching the mouse or waiting on slow browser menus.

```text
Keystroke (Shift+O) ──> Fuzzy Search ──> Instant Tab Reuse / Navigation
```

---

## Key Features

| Capability | Implementation | Benefit |
| :--- | :--- | :--- |
| **Zero Duplicate Tabs** | Smart Canonical Matching | Switches to existing tabs across all Chrome windows instead of spawning clones. |
| **Sub-Millisecond Search** | In-Memory Tri-Gram Cache | Keystroke-to-render cycle runs in single-frame budget (<16ms). |
| **Data-Driven Scope Registry** | Extensible Parser AST | Flexible `/tab`, `/bm`, `/history`, `/audio`, and custom prefix triggers. |
| **Audio Tab Hunter** | Background Media Detection | Instantly identify noisy tabs and mute/unmute with a single keystroke (`M`). |
| **Isolated Closed Shadow DOM** | Encapsulated Content Root | Guaranteed 0% style bleeding or DOM conflicts with host applications. |
| **On-Device Adaptive Learning** | Local Decay & Frequency Math | Remembers frequent selections locally without cloud AI or external telemetry. |
| **Query Draft Recovery** | 10-Second Session Cache | Accidental dismissals or outside clicks immediately restore your active query. |
| **Noise-Filtered History** | Smart URL Sanitization | Filters ephemeral OAuth tokens, auth redirects, and tracking clutter. |
| **100% Local Privacy** | Strict Manifest V3 Sandbox | No accounts, no external network requests, zero telemetry. |

---

## Command Palette Preview

```text
┌──────────────────────────────────────────────────────────────────────────────┐
│  > Search tabs, bookmarks, history, and commands...                          │
├──────────────────────────────────────────────────────────────────────────────┤
│  ⚡ GitHub / chrome-extension-history-search                     TAB [Win 1]  │
│    https://github.com/Aethries/chrome-extension-history-search               │
│                                                                              │
│  ★  GitHub: Where the world builds software                     BM           │
│    https://github.com                                           /Work/Dev    │
│                                                                              │
│  ⏱  Pull Request #42: Add Fuzzy Matching Pipeline               HISTORY      │
│    https://github.com/company/my-project/pull/42                             │
│                                                                              │
│  🔊 YouTube Music - Playing                                     TAB [Audio]  │
│    https://music.youtube.com                                                 │
├──────────────────────────────────────────────────────────────────────────────┤
│  Enter Open  │  Alt+Enter New Tab  │  Shift+Enter Background  │  Ctrl+K Menu │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Query Syntax & Cheat Sheet

Navigator utilizes a non-rigid, modular query lexer and AST parser:

```text
[modifiers]  [scope]  [alias]  [filters]  [query text]
```

### Common Patterns

* **Universal Search**: `react state` — Search tabs, bookmarks, and history simultaneously.
* **Scope Filtering**:
  * `/tab jira` — Filter only active browser tabs.
  * `/bm rust` — Filter Chrome bookmarks exclusively.
  * `/history pr` — Search deep browsing history entries.
  * `/audio` — List all tabs currently playing audio or muted.
* **Aliases & Domain Scopes**:
  * `/jira bug login` — Search specifically within Jira domains (`*.atlassian.net`).
  * `GH react` — Custom head-anchored prefix matching GitHub repositories.
* **Depth Modifiers**:
  * `@domain /jira login` — Strip path/parameters and jump straight to the root domain.
  * `@query /history pull/42` — Preserve deep parameters and query paths.
* **Exact Matching & Exclusion**:
  * `"pull request" -draft` — Match exact phrase while excluding items containing `draft`.
* **Inline Calculator**:
  * `= 1920 * 1080 / 2` — Built-in sandboxed arithmetic calculation.

---

## Keybinding Reference

### Summon & Dismiss

| Shortcut | Context | Action |
| :--- | :--- | :--- |
| `Shift + O` | In-Page | Summon or dismiss the Navigator command palette |
| `Cmd+Shift+O` / `Ctrl+Shift+O` | Browser Global | Global browser hotkey to activate overlay |
| `Escape` | Palette | First press clears active query; second press dismisses |

### Navigation & Actions

| Shortcut | Action |
| :--- | :--- |
| `Down` / `Ctrl + N` | Move highlight down |
| `Up` / `Ctrl + P` | Move highlight up |
| `Home` / `PageUp` | Jump to top result |
| `End` / `PageDown` | Jump to bottom result |
| `Enter` | Activate selected item (switch tab or navigate) |
| `Alt + Enter` | Force open in new foreground tab (bypasses tab reuse) |
| `Shift + Enter` | Open in background tab |
| `Ctrl + Shift + Enter` | Open in new browser window |
| `Ctrl + K` / `Right` | Open contextual Actions Menu |
| `M` / `Space` | Instant mute/unmute toggle on highlighted audio tab |
| `Ctrl + C` | Copy canonical URL to clipboard |
| `Ctrl + Shift + C` | Copy Markdown link `[Title](URL)` |
| `Alt + P` | Toggle pinned state for item |
| `Alt + 1` .. `Alt + 9` | Instant jump to Quick Slot / Pin 1 through 9 |
| `Shift + Down` / `Tab` | Toggle multi-select checkbox for batch operations |

---

## System Architecture

Chrome Navigator runs under Google Chrome's **Manifest V3** specification with complete decoupling between web page DOM contexts and background search orchestration:

```text
[ Host Web Page (Any Origin) ]
               │
      (Shift+O Keystroke)
               ▼
  [ Content Script (Isolated) ]
               │
  [ Closed Shadow Root UI ] ◄───────────────┐
               │                            │
     (Chrome Runtime IPC)                   │
               ▼                            │
[ Service Worker Coordinator ]              │
               │                            │
      ┌────────┴────────┐                   │
      ▼                 ▼                   │
[ In-Memory Index ]  [ Chrome APIs ]        │
  • Tri-gram Cache     • chrome.tabs        │
  • Pins & Aliases     • chrome.bookmarks   │
                       • chrome.history     │
      │                 │                   │
      └────────┬────────┘                   │
               ▼                            │
     [ Multi-Tier Ranker ]                  │
     • Exact / Prefix / Fuzzy               │
     • Recency & Decay                      │
               │                            │
               └────(Stream Ranked Results)─┘
```

### Mathematical Ranking Formula

Candidate ranking computes relevance, source tier, window locality, and adaptive weights:

$$\text{Score} = (S_{\text{match}} \times W_{\text{source}}) + B_{\text{window}} - P_{\text{active}} + B_{\text{recency}} + B_{\text{frequency}} + B_{\text{learned}} + B_{\text{context}}$$

* **Source Weights ($W_{\text{source}}$)**: Pinned ($1.4$) > Tabs ($1.2$) > Bookmarks ($1.0$) > History ($0.7$).
* **Window Locality ($B_{\text{window}}$)**: $+0.15$ boost for tabs in the active focused window.
* **Active Tab Demotion ($P_{\text{active}}$)**: Current tab penalized by $-0.40$ to favor navigation targets.
* **Recency Decay ($B_{\text{recency}}$)**: Half-life exponential decay over 24 hours.
* **Adaptive Learning ($B_{\text{learned}}$)**: On-device reinforcement for frequent query-selection pairs.

---

## Technical Specifications & Roadmap

Chrome Navigator is developed against a formal, 15-document architectural specification suite covering **300 explicit requirements**:

| Specification | Document | Focus |
| :--- | :--- | :--- |
| **00. Architecture** | [00-overview-and-architecture.md](docs/specs/00-overview-and-architecture.md) | MV3 lifecycle, memory budgeting, IPC contracts |
| **01. Overlay** | [01-invocation-and-overlay.md](docs/specs/01-invocation-and-overlay.md) | Closed Shadow Root isolation, CSS scoping, z-index |
| **02. Query Parser** | [02-query-syntax-and-parser.md](docs/specs/02-query-syntax-and-parser.md) | EBNF grammar, token lexer, AST generation, scopes |
| **03. Search Engine** | [03-search-engine-and-matching.md](docs/specs/03-search-engine-and-matching.md) | Tri-gram indexing, Unicode folding, token matches |
| **04. Relevance** | [04-ranking-and-relevance.md](docs/specs/04-ranking-and-relevance.md) | Scoring mathematics, recency decay, local learning |
| **05. Deduplication** | [05-unified-results-and-deduplication.md](docs/specs/05-unified-results-and-deduplication.md) | Canonical URL deduplication, parameter stripping |
| **06. Keyboard** | [06-keyboard-and-user-interaction.md](docs/specs/06-keyboard-and-user-interaction.md) | Selection stability, action menus, ARIA compliance |
| **07. Tab Operations** | [07-navigation-and-tab-management.md](docs/specs/07-navigation-and-tab-management.md) | Cross-window focus, tab equality, discard actions |
| **08. Pins & Workspaces**| [08-pins-favorites-and-context.md](docs/specs/08-pins-favorites-and-context.md) | Polymorphic pinned slots, tags, zero-state dashboard |
| **09. Tools & Omnibox** | [09-commands-tools-and-integrations.md](docs/specs/09-commands-tools-and-integrations.md) | Omnibox keyword `nav`, calculator, command palette |
| **10. Storage** | [10-storage-sync-and-migrations.md](docs/specs/10-storage-sync-and-migrations.md) | Three-tier storage (`sync`, `local`, `IndexedDB`) |
| **11. Privacy & Safety** | [11-privacy-security-and-safety.md](docs/specs/11-privacy-security-and-safety.md) | Zero telemetry, protocol whitelist, permission boundaries |
| **12. UI & Themes** | [12-configuration-and-appearance.md](docs/specs/12-configuration-and-appearance.md) | Settings dashboard (`options.html`), theme tokens |
| **13. Scalability** | [13-performance-and-scalability.md](docs/specs/13-performance-and-scalability.md) | Concurrency control, benchmark profiles, memory limits |
| **14. Extensibility** | [14-integrations-and-extensibility.md](docs/specs/14-integrations-and-extensibility.md) | Plugin contracts and custom provider integration |

> Complete implementation plan with 11 milestones, 53 GitHub issues, and dependency ordering is available in **[PROJECT_PLAN.md](docs/PROJECT_PLAN.md)** and the **[Project Roadmap Board](https://github.com/orgs/Aethries/projects/7)**.

---

## Installation & Development

### Prerequisites

* Google Chrome, Brave, Arc, Edge, or any Chromium-based browser (v108+)
* Node.js (v18+) and `npm` or `pnpm`

### Build from Source

```bash
# Clone the repository
git clone https://github.com/Aethries/chrome-extension-history-search.git
cd chrome-extension-history-search

# Install dependencies and build extension bundle
npm install
npm run build
```

### Load Unpacked Extension

1. Open `chrome://extensions` in your Chromium browser.
2. Toggle **Developer mode** in the top-right corner.
3. Click **Load unpacked**.
4. Select the generated `dist/` directory.
5. Open any website and press `Shift + O` to activate Navigator.

---

## Privacy Contract

Chrome Navigator is engineered around strict local-first principles:
* **No Analytics or Telemetry**: Zero tracking scripts, metric trackers, or external beacon pings.
* **No Cloud Dependency**: Bookmarks, history indexes, and search tokens remain entirely on your physical machine.
* **No Account Required**: Ready to use immediately without signup or authentication.

---

## License

Dual-licensed under the **Apache 2.0** and **MIT** licenses. See [LICENSE](LICENSE) for full details.
