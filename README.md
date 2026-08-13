# Ansible Role: Logrotate

|Source|Version|CI|License|
|---|---|---|---|
| [![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-logrotate) | [![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-logrotate)](https://github.com/grzegorzfranus/ansible-role-logrotate/releases) | [![CI](https://github.com/grzegorzfranus/ansible-role-logrotate/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-logrotate/actions/workflows/ci.yml) | [![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE) |

This Ansible role installs and configures `logrotate`, managing the main `/etc/logrotate.conf` configuration file and application-specific drop-in rules under `/etc/logrotate.d/`. It provides declarative input specifications, runtime assertions, safe defaults, and containerized Molecule verification for Debian and Ubuntu systems.

## ✨ Features

- 🔄 **Log Rotation Management**: Installs and manages the native `logrotate` package.
- 🔧 **Main Configuration**: Renders global `/etc/logrotate.conf` with validation and backup capabilities.
- 📁 **Drop-in Rules**: Renders and manages per-application rotation policies under `/etc/logrotate.d/*`.
- 🧪 **Container Testing**: Full Molecule integration test suite on Docker across Ubuntu 26.04/24.04 and Debian 13/12.
- 🛡️ **Declarative Validation**: Dual-layer input validation with `meta/argument_specs.yml` and `tasks/assert.yml`.

## 🎯 Architecture

The role provides a complete log rotation management layout:

```text
include_vars → assert → [present: install → configure → rules → verify | absent: remove]
```

### Delivery Method Decision: Native OS Package

- **Architecture Rationale:**
  - **Native OS Integration**: Logrotate is tightly coupled with system logging services, cron/systemd timers, and system file permissions.
  - **Low Footprint**: Uses the platform package manager (`apt`) without container runtime overhead.
  - **Standardized Paths**: Manages standard OS configuration directories `/etc/logrotate.conf` and `/etc/logrotate.d/`.

## 📋 Requirements

- **Ansible**: 2.15.0 or higher
- **Python**: 3.9 or higher on target hosts
- **Privileges**: Elevated root privileges (`become: true`) for package installation and file management under `/etc`

### Supported operating systems

Officially supported operating systems for this role:

| OS Family | Version | Status |
|---|---|---|
| Ubuntu | 26.04 (Resolute) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 13 (Trixie) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

> [!NOTE]
> The role validates the target distribution before making any changes and aborts on unsupported systems. To run it on an untested Debian derivative, set `logrotate_os_support_check_enabled: false` in your playbook or inventory. Pass it as a real boolean — `-e logrotate_os_support_check_enabled=false` supplies a string and is rejected by input validation; use `-e '{"logrotate_os_support_check_enabled": false}'` if you need a command-line override.

### Setup module

The role relies on standard Ansible facts gathered by `ansible.builtin.setup` (`ansible_facts['distribution']`, `ansible_facts['os_family']`, `ansible_facts['distribution_major_version']`). If fact gathering is disabled in your playbook, ensure equivalent facts are supplied.

### Root access

Root access (`become: true`) is required to manage configuration files under `/etc/` and install system packages.

## 🚀 Quick Start

### 1. Basic Setup

```yaml
---
- hosts: all
  become: true
  roles:
    - role: grzegorzfranus.logrotate
```

### 2. Run the playbook

```bash
ansible-playbook -i inventory playbook.yml
```

## ⚙️ Configuration

### ⬆️ Upgrading from 1.x

Version 2.0.0 introduces two breaking changes:

1. **`logrotate_role_action` removed**: Execution scope is now controlled exclusively via Ansible tags (`logrotate_setup`, `logrotate_install`, `logrotate_configure`, `logrotate_rules`, `logrotate_verify`, `logrotate_remove`).
2. **`logrotate_rules` defaults to `[]`**: The role no longer manages any drop-in rules out of the box. To retain the six system rules (`rsyslog`, `wtmp`, `btmp`, `apt`, `unattended-upgrades`, `dpkg`) previously enabled by default, copy their definitions from [Reproducing Debian/Ubuntu System Log Rules](#reproducing-debianubuntu-system-log-rules) into your playbook or inventory. Existing files under `/etc/logrotate.d` are left untouched unless explicitly managed.

### Default Configuration

The role comes pre-configured with safe, production-ready defaults:

```yaml
logrotate_status_file: "/var/lib/logrotate/status"
logrotate_manage_main_conf: true
logrotate_os_support_check_enabled: true
logrotate_frequency: "weekly"
logrotate_rotate: 4
logrotate_compress: false
logrotate_su_user: "root"
logrotate_su_group: "adm"
logrotate_missingok: true
logrotate_notifempty: true
logrotate_dateext: false
logrotate_dateformat: "-%Y%m%d"
logrotate_create: ""
logrotate_olddir_create_enabled: true
logrotate_olddir_owner: "root"
logrotate_olddir_group: "root"
logrotate_olddir_mode: "0755"
logrotate_purge_distro_configs: []
logrotate_rules: []
logrotate_state: "present"
logrotate_remove_package: false
logrotate_fail_on_verify_error: true
```

## 📊 Variables

### 1. General Settings

| Variable | Description | Default |
|---|---|---|
| `logrotate_status_file` | Path to logrotate status file used for validation and execution runs | `/var/lib/logrotate/status` |
| `logrotate_manage_main_conf` | Whether this role should manage the main `/etc/logrotate.conf` file | `true` |
| `logrotate_os_support_check_enabled` | Whether the role validates that the target host runs a supported distribution | `true` |

### 2. Main Logrotate Configuration

| Variable | Description | Default |
|---|---|---|
| `logrotate_frequency` | Global rotation frequency (`daily`, `weekly`, `monthly`) | `"weekly"` |
| `logrotate_rotate` | Number of rotated log archives to keep | `4` |
| `logrotate_compress` | Whether to compress rotated log files using gzip | `false` |
| `logrotate_su_user` | System user executing rotation scripts via `su` directive | `"root"` |
| `logrotate_su_group` | System group executing rotation scripts via `su` directive | `"adm"` |
| `logrotate_missingok` | Ignore missing log files without issuing errors | `true` |
| `logrotate_notifempty` | Do not rotate empty log files | `true` |
| `logrotate_dateext` | Use date format suffix for rotated log archives | `false` |
| `logrotate_dateformat` | Date suffix format appended to rotated logs when `logrotate_dateext` is `true` | `"-%Y%m%d"` |
| `logrotate_create` | Creation mode, owner, and group for new log files created after rotation | `""` |
| `logrotate_olddir_create_enabled` | Automatically create `olddir` directories when specified in rules | `true` |
| `logrotate_olddir_owner` | Owner user for auto-created `olddir` directories | `"root"` |
| `logrotate_olddir_group` | Owner group for auto-created `olddir` directories | `"root"` |
| `logrotate_olddir_mode` | Permissions mode octal string for auto-created `olddir` directories | `"0755"` |

### 3. Per-Rule Management

| Variable | Description | Default |
|---|---|---|
| `logrotate_purge_distro_configs` | Names of distribution-provided drop-ins under `/etc/logrotate.d` to remove before applying fleet rules | `[]` |
| `logrotate_rules` | List of application-specific rule dictionaries rendered under `/etc/logrotate.d/<name>`. Empty by default: the role manages no drop-in rules unless explicitly configured | `[]` |

#### Per-rule Item Fields (`logrotate_rules[]`)

| Field | Description | Required | Default |
|---|---|---|---|
| `name` | Rule file name under `/etc/logrotate.d/` | Yes | - |
| `paths` | List of target log file paths or glob expressions (not required for entries whose `state` is `absent` or when `logrotate_state` is `absent`) | Only when rendered | - |
| `state` | Desired rule file state (`present` or `absent`) | No | `"present"` |
| `options` | Dictionary of per-rule options (see suboptions below) | No | `{}` |

#### Per-rule Options (`logrotate_rules[].options`)

| Option | Description | Type |
|---|---|---|
| `rotate` | Number of log file archives to keep for this rule | `int` |
| `daily` / `weekly` / `monthly` | Rotation frequency flags | `bool` |
| `compress` | Compress rotated log files | `bool` |
| `delaycompress` | Postpone compression until next rotation cycle | `bool` |
| `missingok` | Do not report error if target log file is missing | `bool` |
| `notifempty` | Do not rotate log file if it is empty | `bool` |
| `dateext` | Append date format suffix to rotated archives | `bool` |
| `dateformat` | Date suffix format string | `str` |
| `create` | Mode, owner, and group string for new log file (e.g., `0640 www-data adm`) | `str` |
| `copytruncate` | Truncate original file in place after creating a copy | `bool` |
| `olddir` | Directory path to move log archives into | `str` |
| `sharedscripts` | Run prerotate/postrotate scripts only once for all matched logs | `bool` |
| `prerotate` | Shell command script executed before rotation | `str` |
| `postrotate` | Shell command script executed after rotation | `str` |
| `size` | Log size threshold triggering rotation (e.g. `10M`) | `str` |
| `su` | User and group string used to run rotation scripts (e.g. `root syslog`) | `str` |

### 4. Role Lifecycle

| Variable | Description | Default |
|---|---|---|
| `logrotate_state` | Desired lifecycle state of the logrotate configuration managed by this role (`present`, `absent`) | `"present"` |
| `logrotate_remove_package` | Whether the `absent` state also uninstalls the logrotate package | `false` |
| `logrotate_fail_on_verify_error` | Whether a failed logrotate dry-run verification aborts the role run | `true` |

> [!WARNING]
> Enabling `logrotate_remove_package: true` on Debian/Ubuntu systems may remove the base `logrotate` system package and pull in or remove unrelated system dependencies. Use with caution.
> Note: The `absent` state removes managed drop-in rule files under `/etc/logrotate.d/`, but intentionally leaves the distribution-owned `/etc/logrotate.conf` untouched.

### 5. Internal Constants (`vars/*.yml`)

| Variable | Description | Value |
|---|---|---|
| `logrotate_package_name` | Package name installed via apt | `"logrotate"` |
| `logrotate_d_directory_path` | Directory containing drop-in rules | `"/etc/logrotate.d"` |
| `logrotate_main_config_path` | Path to main logrotate configuration file | `"/etc/logrotate.conf"` |
| `logrotate_supported_platforms` | Supported distribution and major version combinations validated by the OS support gate | `["Debian-12", "Debian-13", "Ubuntu-24", "Ubuntu-26"]` |

## 📌 Role Properties

| Property | Value | Description |
|---|---|---|
| **Idempotent** | Yes | Running the role multiple times with identical inputs produces no further changes. |
| **Atomic** | No | Tasks execute sequentially; failure mid-run leaves already applied tasks in place. |
| **Check Mode** | Supported | Tasks run safely without mutating state when check mode (`--check`) is enabled. |
| **Diff Mode** | Supported | Template changes show inline diffs when diff mode (`--diff`) is enabled. |

## 📤 Role Output

This role does not set any public output facts. Task-level registered variables use the `__logrotate_` prefix; role constants are defined in `vars/`.

## 🔍 Verification

### Check logrotate Configuration

Verify that the main configuration syntax is valid by executing a dry-run:

```bash
sudo logrotate -d /etc/logrotate.conf
```

> [!NOTE]
> The dry-run verification task (`logrotate -d`) validates all configuration files under `/etc/logrotate.d/`, including pre-existing third-party rules not managed by this role. If target hosts contain pre-existing unmanaged rules with syntax errors or unresolvable user/group definitions, set `logrotate_fail_on_verify_error: false` to log dry-run verification failures as non-fatal warnings without aborting role execution.

### Check Managed Rule Files

Inspect drop-in configuration rules rendered under `/etc/logrotate.d/`:

```bash
ls -la /etc/logrotate.d/
cat /etc/logrotate.d/rsyslog
```

## 🛡️ Security Features

- **Secure Permissions**: Configuration files under `/etc/logrotate.d/` are created with strict permissions (`0644`, owner `root:root`).
- **Configuration Validation**: Uses automatic pre-flight template verification (`logrotate -d -s ... %s`) before writing configuration files to disk.
- **Privilege Separation**: Supports per-rule `su` directives to ensure log rotation runs under appropriate system user and group contexts.

### Uninstall

To completely remove `logrotate` and all managed configuration files:

```bash
sudo apt-get purge -y logrotate
sudo rm -rf /etc/logrotate.conf /etc/logrotate.d /var/lib/logrotate
```

### Roll-back Capabilities

All template deployment tasks set `backup: true`. Previous versions of managed configuration files are automatically saved with timestamp suffixes (e.g. `/etc/logrotate.conf.12345.2026-08-07~`) prior to modification. To roll back, restore the backup file over `/etc/logrotate.conf` or `/etc/logrotate.d/<rule>`.

## 🧪 Check mode behavior

- Declarative argument specifications and runtime assertion tasks run normally in Check Mode (`--check`).
- Package installation (`ansible.builtin.package`) and file modification tasks (`ansible.builtin.template`, `ansible.builtin.file`) simulate changes without writing to disk.

## 🌐 Network resilience

This role relies on system package repositories accessible via `apt`. Package installation tasks include automatic retry logic (`retries: 3`, `delay: 5`) to withstand transient network failures during repository updates and package retrieval.

## 🧰 Repository management

The role installs standard distribution packages from configured OS repositories and does not add third-party PPA or external package sources.

## 🔧 Troubleshooting

### Validate Configuration Syntax

Run a dry-run parse check against the status file:

```bash
sudo logrotate -d -s /var/lib/logrotate/status /etc/logrotate.conf
```

### Inspect Rotation State

View the current state and last rotation timestamp for all managed log files:

```bash
cat /var/lib/logrotate/status
```

### Handle "failed to validate" or "duplicate log entry" Errors

If logrotate dry-run verification fails with duplicate log entry errors (or previously failed during template rendering with "Module failed: failed to validate"):

- **Cause**: The main `/etc/logrotate.conf` configuration contains an `include /etc/logrotate.d` directive, causing `logrotate -d` dry-run validation to parse the full drop-in tree. Default distribution packages (such as `postgresql-common` or `dpkg`) may install drop-in files under `/etc/logrotate.d/` that duplicate log file path declarations managed by fleet rules.
- **Diagnostic Command**: Run logrotate dry-run and filter errors:
  ```bash
  sudo logrotate -d -s /var/lib/logrotate/status /etc/logrotate.conf 2>&1 | grep '^error:'
  ```
- **Resolution**: Specify the names of the conflicting distribution drop-in files in `logrotate_purge_distro_configs` in your inventory or group variables:
  ```yaml
  logrotate_purge_distro_configs:
    - "postgresql-common"
  ```

## 📁 File Structure

```text
ansible-role-logrotate/
├── .github/
│   ├── ISSUE_TEMPLATE/                # Issue templates for bug, feature, task
│   │   ├── bug_report.yml
│   │   ├── config.yml
│   │   ├── feature_request.yml
│   │   └── task.yml
│   ├── PULL_REQUEST_TEMPLATE/         # Pull request description template
│   │   └── pull_request_template.md
│   ├── workflows/
│   │   ├── ci.yml                     # CI pipeline
│   │   └── release.yml                # Release Please + Galaxy publish
│   └── dependabot.yml                 # Dependabot configuration for GitHub Actions
├── .ansible-lint                      # Ansible-lint configuration
├── .gitignore                         # Git ignore patterns
├── .release-please-manifest.json      # Release Please manifest tracking release version
├── .yamllint                          # Yamllint configuration
├── CHANGELOG.md                       # Automatically generated release changelog
├── LICENSE                            # License file (Apache-2.0)
├── README.md                          # Role documentation
├── defaults/
│   └── main.yml                       # Default variables
├── handlers/
│   └── main.yml                       # Service handlers
├── meta/
│   ├── argument_specs.yml             # Declarative argument specifications
│   └── main.yml                       # Role Galaxy metadata
├── molecule/
│   ├── default/
│   │   ├── converge.yml               # Role execution playbook
│   │   ├── molecule.yml               # Test configuration
│   │   ├── prepare.yml                # Test environment preparation
│   │   └── verify.yml                 # Verification assertions
│   ├── purge-conflict/
│   │   ├── converge.yml               # Role execution expecting verify stage failure
│   │   ├── molecule.yml               # Test configuration
│   │   ├── prepare.yml                # Test environment preparation with conflicting drop-in
│   │   └── verify.yml                 # Verification of failure task origin and error formatting
│   └── uninstall/
│       ├── converge.yml               # Role removal execution playbook
│       ├── molecule.yml               # Test configuration
│       ├── prepare.yml                # Test environment preparation
│       └── verify.yml                 # Uninstall verification assertions
├── release-please-config.json         # Release Please release configuration
├── tasks/
│   ├── assert.yml                     # Runtime variable assertions
│   ├── configure.yml                  # Main configuration management
│   ├── install.yml                    # Package installation
│   ├── main.yml                       # Main orchestration and flow control
│   ├── purge_distro.yml               # Purge superseded distribution drop-in files
│   ├── remove.yml                     # Role removal tasks
│   ├── rules.yml                      # Drop-in rules management
│   └── verify.yml                     # Configuration verification (dry-run)
├── templates/
│   ├── logrotate.conf.j2              # Main logrotate configuration template
│   └── logrotate_rule.j2              # Per-rule template for /etc/logrotate.d
└── vars/
    └── main.yml                       # Common variables/constants
```

> [!NOTE]
> The `molecule/default/converge.yml` playbook connects as an unprivileged user (`remote_user: molecule`, `become: false`) to ensure all role tasks requiring root privileges explicitly elevate via `become: true`. This serves as a continuous regression guard in CI against unprivileged task execution.

## 🏷️ Tags

All role-specific tags are prefixed with `logrotate_` to prevent collisions across playbooks.

| Tag | Description |
|---|---|
| `always` | Tasks that always run (OS variable loading and configuration assertions) |
| `logrotate_setup` | High-level setup meta tag covering installation and configuration |
| `logrotate_init` | Initial setup and OS variable loading |
| `logrotate_validate` | Variable validation and configuration assertions |
| `logrotate_install` | Package installation tasks |
| `logrotate_configure` | Main `/etc/logrotate.conf` configuration tasks |
| `logrotate_purge` | Purge superseded distribution-provided drop-in files under `/etc/logrotate.d` |
| `logrotate_rules` | Drop-in rule management under `/etc/logrotate.d` |
| `logrotate_verify` | Dry-run configuration verification tasks |
| `logrotate_remove` | Tasks executing role removal and cleanup when `logrotate_state` is `absent` |

## CI/CD Pipeline

This repository uses centralized reusable workflows from `grzegorzfranus/github-workflows` (branch `@main`) for quality assurance, linting, security scanning, and release publishing.

### CI Pipeline (`ansible-ci.yml`)

Executes automatically on every pull request targeting `main`:

1. **Branch Name Validation** (`branch-name-lint`) — ensures branch names follow required prefix patterns.
2. **PR Title Validation** (`pr-title-lint`) — verifies Conventional Commits formatting on PR titles.
3. **YAML Syntax & Layout** (`yamllint`) — validates syntax rules against `.yamllint`.
4. **Ansible Best Practices** (`ansible-lint`) — checks role standards and CoP rules against `.ansible-lint`.
5. **Galaxy Metadata Validation** — verifies structure of `meta/main.yml`.
6. **Security Scanning** — scans for hardcoded secrets and IaC vulnerabilities.
7. **Molecule Matrix Integration** — runs Molecule tests across Ubuntu 26.04, Ubuntu 24.04, Debian 13, and Debian 12 containers.
8. **Merge Check Gate** (`merge-check`) — aggregates status across all jobs as a single required check.

### Release & Publish Pipeline (`ansible-publish.yml`)

Automated release workflow driven by Release Please:

1. Merge PR to `main` → Release Please opens or updates a release pull request with changelog updates.
2. Merging Release PR → creates Git tag, GitHub Release, and publishes the updated role to Ansible Galaxy.

## Example Playbooks

### Application Log Rotation with Custom Rules

```yaml
---
- hosts: all
  become: true
  roles:
    - role: grzegorzfranus.logrotate
      vars:
        logrotate_frequency: "daily"
        logrotate_rotate: 7
        logrotate_compress: true
        logrotate_rules:
          - name: nginx
            paths:
              - /var/log/nginx/*.log
            options:
              daily: true
              rotate: 14
              compress: true
              delaycompress: true
              missingok: true
              notifempty: true
              create: "0640 www-data adm"
              sharedscripts: true
              postrotate: |
                /usr/bin/systemctl reload nginx || true
            state: present
```

### Removing Managed Drop-in Rules

When removing rules you only need to declare each rule's `name` — `paths` are not required because nothing is rendered.

```yaml
---
- hosts: all
  become: true
  roles:
    - role: grzegorzfranus.logrotate
      vars:
        logrotate_state: "absent"
        logrotate_rules:
          - name: nginx
        logrotate_remove_package: false
```

### Reproducing Debian/Ubuntu System Log Rules

Prior to version 2.0.0, this role included six pre-configured drop-in rules by default. To preserve or opt into this behavior, define `logrotate_rules` in your playbook or inventory:

```yaml
---
- hosts: all
  become: true
  roles:
    - role: grzegorzfranus.logrotate
      vars:
        logrotate_rules:
          # Core system logs (rsyslog-managed)
          - name: rsyslog
            paths:
              - /var/log/syslog
              - /var/log/mail.log
              - /var/log/kern.log
              - /var/log/auth.log
              - /var/log/user.log
              - /var/log/cron.log
            options:
              weekly: true
              rotate: 4
              compress: true
              delaycompress: false
              missingok: true
              notifempty: true
              su: "root syslog"
              sharedscripts: true
              postrotate: |
                /usr/lib/rsyslog/rsyslog-rotate || true
            state: present

          # Login accounting (wtmp)
          - name: wtmp
            paths:
              - /var/log/wtmp
            options:
              monthly: true
              rotate: 1
              create: "0664 root utmp"
              missingok: true
              notifempty: true
              compress: false
            state: present

          # Failed login accounting (btmp)
          - name: btmp
            paths:
              - /var/log/btmp
            options:
              monthly: true
              rotate: 1
              create: "0600 root utmp"
              missingok: true
              notifempty: true
              compress: false
            state: present

          # APT logs
          - name: apt
            paths:
              - /var/log/apt/term.log
              - /var/log/apt/history.log
            options:
              monthly: true
              rotate: 12
              compress: false
              delaycompress: false
              missingok: true
              notifempty: true
              create: "0640 root adm"
            state: present

          # Unattended upgrades logs
          - name: unattended-upgrades
            paths:
              - /var/log/unattended-upgrades/unattended-upgrades.log
              - /var/log/unattended-upgrades/unattended-upgrades-shutdown.log
            options:
              monthly: true
              rotate: 6
              compress: false
              delaycompress: false
              missingok: true
              notifempty: true
              create: "0640 root adm"
            state: present

          # DPKG related logs
          - name: dpkg
            paths:
              - /var/log/dpkg.log
              - /var/log/alternatives.log
            options:
              monthly: true
              rotate: 12
              compress: false
              delaycompress: false
              missingok: true
              notifempty: true
              create: "0640 root adm"
            state: present
```

## 🤝 Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`
- Use [Conventional Commits](https://www.conventionalcommits.org/) for commit messages:
  - `feat:` — new features
  - `fix:` — bug fixes
  - `refactor:` — code refactoring
  - `docs:` — documentation changes
  - `ci:` — CI/CD pipeline updates
  - `build:` — dependency and build configuration updates
  - `chore:` — maintenance tasks
  - `test:` — test additions or corrections
  - `perf:` — performance improvements
  - `revert:` — code reverts
  - `style:` — code formatting and style
- Use branch naming convention: `feature/`, `bugfix/`, `fix/`, `hotfix/`, `release/`, `chore/`, `docs/`, `refactor/`, `test/`, `build/`, `ci/`, `perf/`, `revert/`
- Ensure your code passes all CI checks (YAML lint, Ansible lint, Molecule tests)
- Centralized workflows from [github-workflows](https://github.com/grzegorzfranus/github-workflows) are used to run CI/CD pipelines
- Submit a pull request describing your changes (a template is available under `.github/PULL_REQUEST_TEMPLATE/pull_request_template.md` to help structure your PR description)
- For major changes, please open an issue first to discuss what you would like to change (issue templates for bug reports, feature requests, and tasks are available under `.github/ISSUE_TEMPLATE/`)

## 📝 License

This project is licensed under the Apache-2.0 License - see the [LICENSE](LICENSE) file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).
