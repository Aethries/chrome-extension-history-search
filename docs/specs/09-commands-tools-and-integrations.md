# 09. Commands, Utilities, Tools & Omnibox Integration

> **Document ID**: SPEC-09  
> **Status**: Normative  
> **Covers Requirements**: #90, #91, #94, #95, #96, #97, #98, #99, #100, #101, #102, #113, #117, #127, #205, #206, #207, #222, #223, #224, #225, #226, #227, #228, #229, #230, #241, #242, #243  

---

## 1. Browser Commands Mode (`>` or `/cmd`)

Chrome Navigator includes a dedicated **Command Palette Mode** for controlling the browser and extension. Typing `>` or `/cmd` switches the palette from search mode to command mode:

```text
┌────────────────────────────────────────────────────────────────────────┐
│  > close                                                               │
├────────────────────────────────────────────────────────────────────────┤
│  ⚡ Close Other Tabs                                               CMD  │
│  ⚡ Close Duplicate Tabs                                           CMD  │
│  ⚡ Close Tabs to the Right                                        CMD  │
│  ⚡ Close Current Window                                           CMD  │
└────────────────────────────────────────────────────────────────────────┘
```

### 1.1 Standard Browser Action Catalog

| Command ID | Display Label | Execution API / Behavior |
| :--- | :--- | :--- |
| `cmd:new-tab` | New Tab | `chrome.tabs.create({})` |
| `cmd:new-window` | New Window | `chrome.windows.create({})` |
| `cmd:new-incognito` | New Incognito Window | `chrome.windows.create({ incognito: true })` |
| `cmd:close-other-tabs` | Close Other Tabs | Closes all tabs in window except active |
| `cmd:close-duplicate-tabs`| Close Duplicate Tabs | Prunes duplicate open tabs |
| `cmd:reopen-closed-tab`| Reopen Closed Tab | `chrome.sessions.restore()` |
| `cmd:clear-browsing-data`| Clear Browsing Data | `chrome.browsingData.remove()` |
| `cmd:open-settings` | Chrome Settings | Opens `chrome://settings` |
| `cmd:open-extensions` | Chrome Extensions | Opens `chrome://extensions` |
| `cmd:open-downloads` | Chrome Downloads | Opens `chrome://downloads` |
| `cmd:open-history` | Chrome History Page | Opens `chrome://history` |
| `cmd:open-bookmarks` | Bookmark Manager | Opens `chrome://bookmarks` |

---

## 2. Omnibox Integration (Keyword `nav`)

Navigator registers the keyword `nav` with Chrome's Omnibox API (`chrome.omnibox`):
1. In the Chrome address bar, user types `nav` followed by `Space` or `Tab`.
2. Chrome hands keystrokes to the Navigator background service worker.
3. Suggestion list displays live search results from tabs, bookmarks, and history directly inside the browser address bar.
4. Selecting an item executes standard Navigator tab-reuse or navigation.

---

## 3. URL Launcher & Direct Navigation

When a user pastes or types a valid URL or hostname into Navigator:
- Recognizes valid URL schemas (`http://`, `https://`, `localhost:8080`, `file:///`).
- Recognizes domain syntax (`example.com`, `sub.domain.org/path`).
- **Direct Navigation Option**: Top result surfaces `🚀 Navigate to https://...`, allowing instantaneous jumping without querying search sources.

---

## 4. Quick Math Parser & Inline Calculator

Typing a mathematical expression prefixed with `=` or consisting purely of numerical math expressions:
- **Input**: `= 1920 * 1080 / (1024 * 1024)` or `149.99 * 1.08`
- **Result Row**: Displays computed answer: `161.9892`
- **Action**: Pressing `Enter` copies the result to the clipboard and dismisses the overlay.
- **Safety**: Evaluated using a sandboxed AST math evaluator (strictly prohibiting `eval()` or `Function()`).

---

## 5. Web Search Engine Fallback & URL Templates

When local results are insufficient, Navigator falls back to web search engines:

```typescript
export interface SearchEngineTemplate {
  id: string;
  name: string;                   // e.g. "DuckDuckGo"
  prefix: string;                 // Trigger prefix (e.g. "ddg")
  searchUrlTemplate: string;      // "https://duckduckgo.com/?q={q}"
}
```

### 5.1 Parametric URL Templates

Custom templates can interpolate multiple variables:
- **GitHub Issue**: `https://github.com/{repo}/issues/{id}`
- **NPM Package**: `https://www.npmjs.com/package/{q}`
- **MDN Web Docs**: `https://developer.mozilla.org/en-US/search?q={q}`

---

## 6. Context Menu & Page Quick Actions

Navigator injects right-click context menu options (`chrome.contextMenus`):
1. **Pin This Page**: Instantly adds the active tab to Navigator pins.
2. **Search Selected Text in Navigator**: Opens Navigator seeded with the highlighted text.
3. **Copy Markdown Link**: Copies active page as `[Title](URL)`.

---

## 7. Local Scratchpad & Notes Utility

Typing `/notes` or `/pad` reveals an inline markdown scratchpad:
- Persisted locally in `chrome.storage.local`.
- Ideal for temporarily pasting issue numbers, code snippets, or draft URLs without leaving the keyboard flow.

---

## 8. In-Palette Settings & Alias Search

Users can configure extension preferences directly from the palette:
- `/settings <query>`: Instantly jumps to and toggles a specific setting (e.g. `/settings dark mode`).
- `/aliases <query>`: Search, edit, or create custom slash aliases without opening a separate browser options tab.
