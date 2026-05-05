# Changelog

All notable changes to this project will be documented in this file.

Format based on [Keep a Changelog](https://keepachangelog.com/).

## [1.2.0] - 2026-05-05

### Added
- Report templates (daily/weekly/monthly) now available in all 4 languages: EN, zh-CN, zh-TW, JA
- Feedback.json envelope structure (version, updated, items) documented in ARCHITECTURE.md section 5.1

### Fixed
- Japanese translation: 频度 → 頻度 (line 297 of ARCHITECTURE.ja.md)
- Footer version format unified: "v1.1" → "1.1.0" (matches semver in frontmatter)

## [1.1.0] - 2026-05-05

### Added
- YAML frontmatter (name, description, version, updated) to all ARCHITECTURE.md files
- Four-language support: zh-CN, zh-TW, ja translations for ARCHITECTURE.md

### Changed
- Architecture aligned with actual implementation: merged audit + feedback into single workflow
- Honest README approach: README only lists content that actually exists
- Removed empty docs/ files that were never populated

## [1.0.0] - 2026-05-05

### Added
- Initial release of the Memory Audit workflow
- ARCHITECTURE.md: Complete design documentation
- README.md: Quick start and usage guide
- Templates: config.json, feedback.json, daily/weekly/monthly report skeletons
