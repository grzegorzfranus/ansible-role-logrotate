# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0](https://github.com/grzegorzfranus/ansible-role-logrotate/compare/v1.0.1...v1.1.0) (2026-05-21)


### Features

* migrate to centralized CI, Release Please, Galaxy publish, and … ([#2](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/2)) ([4f3e6e5](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/4f3e6e55c4a364c01d763524f5afdb21abe206b8))

## [1.0.1] - 2026-05-18

### Fixed
- Upgraded `actions/checkout` from v4 to v6 (Node.js 24 compatible)
- Upgraded `actions/setup-python` from v5 to v6 (Node.js 24 compatible)

### Changed
- Standardized workflow and job naming to enterprise convention (Numbered Title Case)

## [1.0.0] - 2025-09-09
### Added ✅
- Initial role to configure logrotate on Ubuntu 24.04 and Debian 12
- Main logrotate.conf templating with dateext and dateformat
- Per-rule templating for /etc/logrotate.d
- Variable validation and tags-aligned tasks
- Molecule tests for Ubuntu 24.04 and Debian 12
- Linting configs and GitHub Actions workflow
- README documentation and Galaxy metadata placeholders
