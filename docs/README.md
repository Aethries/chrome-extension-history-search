# Chrome Navigator: Documentation

Welcome to the Chrome Navigator documentation repository.

## Documentation Structure

- [Technical Specifications Suite (`docs/specs/`)](specs/README.md): The authoritative, modular technical specifications for Chrome Navigator. Contains 15 domain-specific documents covering system architecture, invocation, query parser, search engine, ranking math, deduplication, keyboard interaction, navigation semantics, pins, commands, storage tiering, privacy, configuration, performance, and integrations.
  - [Master Index and Requirement Traceability Matrix](specs/README.md): Maps all 300 functional requirements to specific technical documents and sections.
  - [00. Overview and Architecture](specs/00-overview-and-architecture.md)
  - [01. Invocation, Overlay and Web Compatibility](specs/01-invocation-and-overlay.md)
  - [02. Query Syntax, Lexer and AST Parser](specs/02-query-syntax-and-parser.md)
  - [03. Search Engine and Multi-Tier Matching Pipeline](specs/03-search-engine-and-matching.md)
  - [04. Ranking, Relevance and Local Learning Engine](specs/04-ranking-and-relevance.md)
  - [05. Unified Results, Canonical URLs and Deduplication](specs/05-unified-results-and-deduplication.md)
  - [06. Keyboard Navigation, Interaction and Action Menu](specs/06-keyboard-and-user-interaction.md)
  - [07. Navigation Semantics, Tab and Window Management](specs/07-navigation-and-tab-management.md)
  - [08. Pins, Favorites, Saved Context and Workspaces](specs/08-pins-favorites-and-context.md)
  - [09. Commands, Utilities, Tools and Omnibox Integration](specs/09-commands-tools-and-integrations.md)
  - [10. Persistence, Tiered Storage and Schema Migrations](specs/10-storage-sync-and-migrations.md)
  - [11. Privacy Guarantees, Security Boundaries and Permissions](specs/11-privacy-security-and-safety.md)
  - [12. Configuration, Appearance, Theming and Rules](specs/12-configuration-and-appearance.md)
  - [13. Performance, High-Volume Scalability and Concurrency](specs/13-performance-and-scalability.md)
  - [14. Integrations, Remote Providers and Extensibility](specs/14-integrations-and-extensibility.md)
