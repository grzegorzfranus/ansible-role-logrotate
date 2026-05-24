# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.2](https://github.com/grzegorzfranus/ansible-role-logrotate/compare/v1.1.1...v1.1.2) (2026-05-24)


### Documentation

* add Role Properties and Role Output sections to README.md ([#6](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/6)) ([63dac99](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/63dac994476b8f69b435c63b20756584f3284393))

## [1.1.1](https://github.com/grzegorzfranus/ansible-role-logrotate/compare/v1.1.0...v1.1.1) (2026-05-22)


### CI/CD

* implement enterprise CI/CD hardening and fix molecule syntax check ([#4](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/4)) ([11156a5](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/11156a5aa7a3aad48a0541bb7fffe9e22f0c8506))

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
