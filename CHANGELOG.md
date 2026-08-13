# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.2.1](https://github.com/grzegorzfranus/ansible-role-logrotate/compare/v2.2.0...v2.2.1) (2026-08-13)


### CI/CD

* track reusable workflows on main branch instead of pinned tag ([#31](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/31)) ([bc8d0e0](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/bc8d0e0d95abb9e063eed5384e9d7bfa41c9834c))

## [2.2.0](https://github.com/grzegorzfranus/ansible-role-logrotate/compare/v2.1.0...v2.2.0) (2026-08-13)


### Features

* move full-tree validation to verify stage and purge superseded distro drop-ins ([#28](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/28)) ([915aae4](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/915aae42c0905c7be3c18e99547df3350da1e4c7))

## [2.1.0](https://github.com/grzegorzfranus/ansible-role-logrotate/compare/v2.0.0...v2.1.0) (2026-08-08)


### Features

* **24:** add Debian 13 and Ubuntu 26.04 support ([#25](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/25)) ([370b6b7](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/370b6b7fb85e4f27897eebeabc7b1c6671c3b424))

## [2.0.0](https://github.com/grzegorzfranus/ansible-role-logrotate/compare/v1.1.5...v2.0.0) (2026-08-08)


### ⚠ BREAKING CHANGES

* **21:** drop logrotate_role_action, empty logrotate_rules, add lifecycle state ([#22](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/22))

### Code Refactoring

* **21:** drop logrotate_role_action, empty logrotate_rules, add lifecycle state ([#22](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/22)) ([697064e](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/697064e4fd97a1b071594c25dc893c780afa0a56))

## [1.1.5](https://github.com/grzegorzfranus/ansible-role-logrotate/compare/v1.1.4...v1.1.5) (2026-08-07)


### Code Refactoring

* align role with Ansible standards and reference configuration ([#19](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/19)) ([0793811](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/0793811333877a5fbf501ee8cbdc1a3c0d4a0d38))

## [1.1.4](https://github.com/grzegorzfranus/ansible-role-logrotate/compare/v1.1.3...v1.1.4) (2026-07-21)


### Bug Fixes

* **14:** correct release workflow reusable workflow invocation ([#15](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/15)) ([23fcfe3](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/23fcfe3975dd30556a05039885d37315a61e7f61))


### CI/CD

* **11:** upgrade github workflows to v3.0.1 and update documentation ([#12](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/12)) ([55029c4](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/55029c4d3257ee994c34a01cc1da7b2cc2538b78))

## [1.1.3](https://github.com/grzegorzfranus/ansible-role-logrotate/compare/v1.1.2...v1.1.3) (2026-06-30)


### Miscellaneous

* migrate workflows to github-workflows and align configuration ([#9](https://github.com/grzegorzfranus/ansible-role-logrotate/issues/9)) ([849b085](https://github.com/grzegorzfranus/ansible-role-logrotate/commit/849b08553bfa685adc81d4df311e09696fe043f5))

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
