# 12. Configuration, Appearance, Theming & Rules

> **Document ID**: SPEC-12  
> **Status**: Normative  
> **Covers Requirements**: #120, #121, #137, #138, #139, #140, #141, #142, #143, #144, #145, #146, #147, #150, #151, #247, #248, #251, #252, #268, #269  

---

## 1. Comprehensive Configuration Schema

All user preferences are encapsulated within a strongly-typed, versioned configuration document:

```typescript
export interface UserSettings {
  schemaVersion: number;

  // General Preferences
  general: {
    inPageShortcut: string;         // Default: "Shift+O"
    browserShortcut: string;        // Default: "CommandOrControl+Shift+O"
    closeOnWindowBlur: boolean;     // Default: true
    preservePreviousQuery: boolean; // Default: false (#268)
    escapeDismissBehavior: 'clear_then_dismiss' | 'instant_dismiss'; // (#269)
  };

  // Search Engine Preferences
  search: {
    fuzzyStrength: 'off' | 'low' | 'balanced' | 'aggressive';
    typoTolerance: boolean;
    maxResultCount: number;         // 5, 10, 20, 50 (Default: 10)
    enableHistorySearch: boolean;   // Default: true
    historyDebounceMs: number;      // Default: 120
    hashPolicy: 'ignore' | 'preserve';
  };

  // Ranking & Scoring Weights
  ranking: {
    sourceWeights: {
      pin: number;                  // Default: 1.4
      tab: number;                  // Default: 1.2
      bookmark: number;             // Default: 1.0
      history: number;              // Default: 0.7
    };
    currentWindowBoost: number;     // Default: 0.15
    activeTabPenalty: number;       // Default: 0.40
    hideCurrentTab: boolean;        // Default: false
    enableLearnedRanking: boolean;  // Default: true
    decayHalfLifeHours: number;     // Default: 24
  };

  // Navigation Semantics
  navigation: {
    defaultNavigationMode: 'exact' | 'query' | 'domain';
    tabMatchStrategy: 'exact' | 'ignore_query' | 'same_path' | 'same_origin' | 'same_domain';
    keepOpenAfterAction: boolean;   // Default: false (#266)
  };

  // Visual Styling & Appearance
  appearance: {
    theme: 'system' | 'dark' | 'light' | 'high_contrast';
    density: 'compact' | 'normal' | 'comfortable';
    width: 'small' | 'normal' | 'wide'; // 540px | 640px | 760px
    showResultPreview: boolean;     // Default: false
    showSourceBadges: boolean;      // Default: true
    showFavicons: boolean;          // Default: true
    enableAnimations: boolean;      // Default: true
  };

  // Experimental Flags
  experiments: {
    enableTabGroupSearch: boolean;
    enableCalculator: boolean;
    enableRemoteIntegrations: boolean;
    enableSearchDebugger: boolean;
  };
}
```

---

## 2. Appearance Customization & Themes

### 2.1 Theme Engine

Navigator provides a self-contained CSS variable theme engine within its closed Shadow Root:
- **System (Default)**: Automatically tracks host OS color scheme via `@media (prefers-color-scheme: dark)`.
- **Dark Theme**: High-contrast dark charcoal background (`#18181b`) with light gray text (`#f4f4f5`).
- **Light Theme**: Clean crisp white background (`#ffffff`) with dark typography (`#09090b`).
- **High Contrast**: WCAG AAA compliant contrast ratio ($> 7:1$) with enhanced active borders.

### 2.2 UI Density Modes

| Density Mode | Row Height | Font Size | Best Suited For |
| :--- | :---: | :---: | :--- |
| **Compact** | $40\text{px}$ | $13\text{px}$ | Power users, high information density |
| **Normal (Default)** | $52\text{px}$ | $14\text{px}$ | Balanced desktop readability |
| **Comfortable** | $64\text{px}$ | $16\text{px}$ | Touch displays or accessibility requirements |

### 2.3 Motion & Animation Policy

All transitions (modal fade, selection movement) execute within $< 100\text{ms}$. When the user has enabled OS-level reduced motion (`prefers-reduced-motion: reduce`), all animations are disabled to prevent motion sickness.

---

## 3. Domain Navigation & Rewrite Rules

Advanced users can define deterministic URL modification rules applied whenever a domain is visited or selected:

```typescript
export interface DomainRuleDefinition {
  domainPattern: string;          // e.g. "jira.company.com"
  defaultMode: 'query' | 'domain' | 'exact';
  stripParameters: string[];      // ["session_id", "filter_tmp"]
  keepParameters: string[];       // ["selectedIssue"]
  stripHash: boolean;
  rewritePathRegex?: {
    find: string;
    replace: string;
  };
}
```

---

## 4. Internationalization (i18n) & Relative Timestamps

1. **Chrome `i18n` Architecture**: UI strings are stored in `_locales/{locale}/messages.json` supporting international translations.
2. **Locale-Aware Timestamps**: Timestamps for history entries and recents format dynamically:
   - Within 60 seconds: `"Just now"`
   - Within 60 minutes: `"12m ago"`
   - Within 24 hours: `"3h ago"`
   - Yesterday: `"Yesterday"`
   - Older: Localized short date string (`"Sep 17"`).

---

## 5. Feature Flags & Experimental Labs

The `experiments` configuration tree allows modular shipping of cutting-edge experimental features (e.g. remote Jira provider, AI summaries) without destabilizing core production navigation.
