# 08. Pins, Favorites, Saved Context & Workspaces

> **Document ID**: SPEC-08  
> **Status**: Normative  
> **Covers Requirements**: #78, #79, #80, #81, #82, #83, #84, #85, #86, #87, #88, #92, #93, #212, #213, #214, #215, #216, #217, #218, #219, #220, #221, #281, #282  

---

## 1. First-Class Pinning Subsystem

In Chrome Navigator, **Pins** are first-class persistent entities distinct from bookmarks. They provide rapid, high-priority access to essential daily workflows.

```typescript
export type PinType = 'url' | 'domain' | 'search' | 'command';

export interface PinnedResource {
  id: string;                      // UUID
  type: PinType;
  title: string;                   // Custom display label
  target: string;                  // URL, domain, search string, or command ID
  customIcon?: string;
  order: number;                   // Manual display order
  createdAt: number;
  tags?: string[];
}
```

### 1.1 Pinning Mechanics & Hotkeys

- Striking `Alt+P` on any highlighted search result toggles its pinned status.
- Pins can be manually reordered via drag-and-drop in settings or via `Ctrl+Shift+Up` / `Ctrl+Shift+Down` in the palette.

---

## 2. Polymorphic Pin Semantics

Pins support four distinct operational models:

| Pin Type | Target Example | Execution Behavior |
| :--- | :--- | :--- |
| **URL Pin** | `https://github.com/company/project` | Direct navigation / tab switch to exact URL |
| **Domain Pin** | `github.com` | Truncates navigation to domain root (`https://github.com/`) |
| **Search Pin** | `/jira assigned to me` | Re-executes the saved query string into search input |
| **Command Pin** | `cmd:close-duplicate-tabs` | Instantly executes the specified browser command |

---

## 3. Pin Metadata Customization & Broken Pin Detection

1. **Custom Display Names**: Users can rename pinned items to short memorable names (e.g. renaming `https://atlassian.net/jira/board/412` to `Sprint Board`).
2. **Broken Pin Remediation**:
   - If a pinned bookmark or tab no longer resolves (e.g. underlying bookmark deleted), Navigator displays an alert icon: `⚠️ Broken Link`.
   - Offers actions: `Update Target URL`, `Relink`, or `Remove Pin`.

---

## 4. Default Empty Query Viewport (Zero-State View)

When Navigator is summoned and the search input is empty, rather than displaying an uninformative blank container, it presents a curated **Dashboard View**:

```text
┌────────────────────────────────────────────────────────────────────────┐
│  🔍 Type a command or search...                                        │
├────────────────────────────────────────────────────────────────────────┤
│  ★ PINNED ITEMS                                                        │
│  ★ GitHub — my-project                         ⌥1             URL      │
│  ★ Jira Cloud Dashboard                        ⌥2             DOMAIN   │
│  ★ Pinned Search: /gh issues                   ⌥3             SEARCH   │
│                                                                        │
│  🕒 RECENTLY ACCESSED                                                  │
│  ⚡ Pull Request #42: Fuzzy Pipeline                           TAB      │
│  ★  Figma Design System                                        BM       │
│                                                                        │
│  📦 RECENTLY CLOSED TABS                                               │
│  ↩ Reopen: Stack Overflow: CSS Shadow DOM Z-Index             CLOSED   │
│                                                                        │
│  🔥 FREQUENT DESTINATIONS                                              │
│  🌐 Google Drive                                               HIST     │
└────────────────────────────────────────────────────────────────────────┘
```

### 4.1 Empty View Section Ordering

1. **Pinned Items**: Always rendered first, ordered by user-assigned index ($1 \dots N$).
2. **Recent Navigator Selections**: Last 3–5 items selected directly through Navigator.
3. **Recently Closed Tabs**: Surfaces the 3 most recently closed browser tabs for one-click reopening.
4. **Frequent Destinations**: Top domains and URLs calculated from local usage frequency.
5. **Current Context Tabs**: Other tabs currently open in the active window.

---

## 5. Saved Searches Subsystem

Users can persist complex, recurring queries:
- **Save Query**: From the `Ctrl+K` menu of an active search, select `Save Search Query`.
- **Re-Execution**: Invoking a saved search populates the input field and executes the query pipeline immediately.

---

## 6. Multi-Tag Taxonomic Index & Bookmark Tagging

Chrome's native bookmark system does not support arbitrary tags. Chrome Navigator provides a **Non-Destructive Local Tagging Layer**:
- Users can attach tags (e.g. `#dev`, `#infra`, `#docs`) to any URL, bookmark, or pin.
- **Tag Search Engine**: Typing `#dev` or `tag:dev` constrains results to items carrying that tag.
- Tags are stored in `chrome.storage.sync` and do not alter the underlying Chrome bookmark tree.

---

## 7. Custom Collections

Users can organize related URLs into named collections (e.g. `"Sprint Review"`, `"Production Deployment"`, `"Design Audit"`):
- A collection can be opened in a single action: `Open All in New Window` or `Open All in Tab Group`.

---

## 8. Multi-Window Workspaces & Session Restoration

Chrome Navigator allows snapshotting and restoring complete window environments:

```typescript
export interface WorkspaceSnapshot {
  id: string;
  name: string;                   // e.g. "Frontend Development"
  createdAt: number;
  windows: {
    tabUrls: string[];
    windowState: 'normal' | 'maximized';
  }[];
}
```

- **Restore Session**: Select `/session restore <name>` to reconstitute the saved window and tab state.
- **Session Search**: Search across saved sessions and snapshots from past work sessions.
