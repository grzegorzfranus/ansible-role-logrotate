# Ansible Role: logrotate

| Source | Version | CI | License |
|--------|---------|----|---------|
| [![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-logrotate) | [![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-logrotate)](https://github.com/grzegorzfranus/ansible-role-logrotate/releases) | [![CI](https://github.com/grzegorzfranus/ansible-role-logrotate/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-logrotate/actions/workflows/ci.yml) | [![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE) |

This Ansible role installs and configures logrotate, managing the main `/etc/logrotate.conf` and application-specific rules under `/etc/logrotate.d/`. It provides safe defaults, validations, and Molecule coverage for Debian-based systems.

## ✨ Features

- 🔄 **Log Rotation Management**: Install and ensure `logrotate` is present.
- 🔧 **Main Configuration**: Manage `/etc/logrotate.conf` via Jinja2 with validation.
- 📁 **Drop-in Rules**: Manage application-specific rules under `/etc/logrotate.d/*`.
- 🧪 **Container Testing**: Full Molecule test suite for CI/CD integration.
- 🛡️ **Idempotent & Secure**: Safe defaults, validations, and clean lint execution.

## 🎯 Architecture

The role provides a complete log rotation management layout:

- **Main Configuration**: Renders global options to `/etc/logrotate.conf` with validation.
- **Drop-in Rules**: Renders per-application configurations to `/etc/logrotate.d/` directory.

```
Config Vars → main.yml → install logrotate → template /etc/logrotate.conf → template /etc/logrotate.d/*
```

## 📋 Requirements

- **Ansible**: 2.14 or higher
- **Python**: 3.8 or higher on target hosts
- **Privileges**: sudo/root access on target hosts

### Supported operating systems
List of officially supported operating systems for this role:

| OS Family | Version | Status |
|-----------|---------|---------|
| Ubuntu | 24.04 (Noble) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |
| Debian | 12 (Bookworm) | ![✓](https://img.shields.io/badge/✓-brightgreen.svg) |

### Setup module

The role uses facts gathered by Ansible on the remote host. If you disable the Setup module in your playbook, ensure equivalent facts are provided.

### Root access

Root privileges are required for managing files under `/etc` and installing packages.

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

### Default Configuration

The role comes with production-ready defaults:

```yaml
logrotate_frequency: "weekly"
logrotate_rotate: 4
logrotate_compress: false
logrotate_missingok: true
logrotate_notifempty: true
logrotate_dateext: false
logrotate_dateformat: "-%Y%m%d"
logrotate_create: ""
logrotate_su_user: "root"
logrotate_su_group: "adm"
logrotate_status_file: "/var/lib/logrotate/status"
logrotate_manage_main_conf: true
logrotate_olddir_create_enabled: true
logrotate_olddir_owner: "root"
logrotate_olddir_group: "root"
logrotate_olddir_mode: "0755"
logrotate_rules: []
```

### Advanced Configuration

Customize rules for specific applications (e.g. Nginx):

```yaml
logrotate_rules:
  - name: nginx
    paths:
      - /var/log/nginx/*.log
    options:
      rotate: 7
      daily: true
      compress: true
      missingok: true
      notifempty: true
      create: "0640 www-data adm"
    state: present
```

## 📊 Variables

### General Options

| Variable | Description | Default |
|----------|-------------|---------|
| `logrotate_status_file` | Path to logrotate state file used for validation and runs | `/var/lib/logrotate/status` |
| `logrotate_manage_main_conf` | Whether to manage `/etc/logrotate.conf` | `true` |
| `logrotate_frequency` | Global rotation frequency (options: `daily`, `weekly`, `monthly`) | `"weekly"` |
| `logrotate_rotate` | Number of archives to keep (global) | `4` |
| `logrotate_compress` | Compress rotated logs (gzip) | `false` |
| `logrotate_missingok` | Ignore missing log files | `true` |
| `logrotate_notifempty` | Do not rotate empty logs | `true` |
| `logrotate_dateext` | Use date-based suffixes for rotated logs | `false` |
| `logrotate_dateformat` | Date suffix format (effective when `logrotate_dateext` is `true`) | `"-%Y%m%d"` |
| `logrotate_create` | Create string for new log file after rotation (`<mode> <owner> <group>`) | `""` |
| `logrotate_su_user` | User for rotation (via `su`) | `"root"` |
| `logrotate_su_group` | Group for rotation (via `su`) | `"adm"` |
| `logrotate_rules` | List of per-rule items rendered to `/etc/logrotate.d` | `[]` |
| `logrotate_olddir_create_enabled` | Auto-create `olddir` when defined in a rule | `true` |
| `logrotate_olddir_owner` | Owner for auto-created `olddir` | `"root"` |
| `logrotate_olddir_group` | Group for auto-created `olddir` | `"root"` |
| `logrotate_olddir_mode` | Mode for auto-created `olddir` | `"0755"` |

### Per-rule Options (`logrotate_rules[].options`)

| Option | Description | Default |
|--------|-------------|---------|
| `rotate` | Number of archives to keep for this rule | `-` |
| `daily` / `weekly` / `monthly` | Frequency flags (choose one) | `-` |
| `compress` | Compress rotated logs | `-` |
| `delaycompress` | Delay compression until next rotation | `-` |
| `missingok` | Do not fail if log file is missing | `-` |
| `notifempty` | Skip rotation if log is empty | `-` |
| `dateext` | Add date suffix to rotated logs | `-` |
| `dateformat` | Date suffix format (requires `dateext`) | `-` |
| `create` | Mode and ownership for new log file (e.g., `0640 user group`) | `-` |
| `copytruncate` | Copy and truncate instead of move | `-` |
| `olddir` | Directory to move rotated logs into (absolute path) | `-` |
| `sharedscripts` | Group prerotate/postrotate script execution | `-` |
| `prerotate` | Multiline shell script to run before rotation | `-` |
| `postrotate` | Multiline shell script to run after rotation | `-` |
| `size` | Rotate when log grows beyond the specified size | `-` |
| `su` | `user group` used during rotation | `-` |

### Per-rule Item Fields (`logrotate_rules[]`)

| Field | Description | Default |
|-------|-------------|---------|
| `name` | Rule file name under `/etc/logrotate.d/` | required |
| `paths` | List of log file paths/globs | required |
| `options` | Options dictionary (see above) | optional |
| `state` | Rule state (`present` or `absent`) | `present` |

### Role Control

| Variable | Description | Default |
|----------|-------------|---------|
| `logrotate_role_action` | Which parts of the role to run (`all`, `install`, `configure`, `logrotate`) | `"all"` |

### Paths and Package (constants)

| Variable | Description | Default |
|----------|-------------|---------|
| `logrotate_package_name` | Package name for logrotate | `"logrotate"` |
| `logrotate_d_directory_path` | Directory for drop-in rules | `/etc/logrotate.d` |
| `logrotate_main_config_path` | Path to main configuration file | `/etc/logrotate.conf` |

## 📌 Role Properties

| Property | Value | Description |
|----------|-------|-------------|
| **Idempotent** | ✅ Yes | Running the role multiple times with the same parameters produces the same result. |
| **Atomic** | ❌ No | The role can be partially applied. A failure mid-execution may leave the system in an intermediate state. |
| **Check Mode** | ✅ Supported | All tasks support check mode. Package installation and configuration writes are simulated. |
| **Diff Mode** | ✅ Supported | Template tasks support diff mode for change preview. |

## 📤 Role Output

This role does not set any public output facts. All internal facts use the `__logrotate_` prefix.

## 🔍 Verification

After deployment, verify that logrotate is configured correctly:

```bash
# Run logrotate in debug mode to check configuration
sudo logrotate -d /etc/logrotate.conf
```

## 🛡️ Security Features

- ✅ **Secure Default Configuration**: Minimal attack surface and secure default file permissions.
- ✅ **Configuration Validation**: Automatic syntax verification is performed using `logrotate -d` before finalizing changes.

### Uninstall

To remove logrotate from a host, run standard package uninstall commands or override variables to remove:

```yaml
# Standard package removal configuration if needed
```

### Roll-back Capabilities

Configuration files are backed up automatically using Ansible's `backup: true` directive. If you need to revert to a previous state:

1. Restore the configuration files from the `.bak` timestamps created in the `/etc/` directory.
2. Verify the configuration again in debug mode.

## 🔒 Security considerations

- Ensure logrotate configuration files under `/etc/logrotate.d/` have secure permissions (managed automatically by this role).
- Avoid managing log files in user-writable directories without setting the `su` option.

## 🧪 Check mode behavior

- Validation and dry-run syntax checks run normally in Check Mode.
- Mutating package installations and configuration writes are safely simulated.

## 🏷️ Tags usage

- Use `--tags` to run selective parts of the role: `always`, `setup`, `init`, `validate`, `requirements`, `install`, `configure`, `config`, `logrotate`, `upgrade`, `test`, `verify`.

## 🧰 Repository management

- This role relies on default OS package repositories to install logrotate. It does not configure custom upstream repository sources directly.

## 🔧 Troubleshooting

Validate configuration via debug:

```bash
sudo logrotate -d -s /var/lib/logrotate/status /etc/logrotate.conf
```

## 📁 File Structure

```
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
├── defaults/
│   └── main.yml              # Default variables
├── handlers/
│   └── main.yml              # Service handlers
├── meta/
│   └── main.yml              # Role metadata
├── molecule/
│   └── default/
│       ├── molecule.yml      # Test configuration
│       ├── converge.yml      # Role execution playbook
│       ├── prepare.yml       # Test preparation tasks
│       └── verify.yml        # Verification tests
├── tasks/
│   ├── main.yml              # Main orchestration and flow control
│   ├── assert.yml            # Variable validation
│   ├── install.yml           # Package installation
│   ├── configure.yml         # Main configuration management
│   ├── rules.yml             # Drop-in rules management
│   └── verify.yml            # Configuration verification (dry-run)
├── templates/
│   ├── logrotate.conf.j2     # Main logrotate configuration template
│   └── logrotate_rule.j2     # Per-rule template for /etc/logrotate.d
└── vars/
    ├── main.yml              # Common variables/constants
    ├── debian.yml            # Debian-specific variables
    └── redhat.yml            # RedHat-specific variables
```

## 🏷️ Tags

All tags are prefixed with `logrotate_` where possible to avoid collisions.

| Tag | Description |
|-----|-------------|
| `always` | Tasks that always run (variable loading, validation) |
| `setup` | OS variables, installation, and configuration setup |
| `init` | Initial setup tasks |
| `validate` | Variable validation tasks |
| `requirements` | System requirements and pre-checks |
| `install` | Package installation tasks |
| `configure` | Main configuration tasks |
| `config` | Configuration-related tasks |
| `logrotate` | Management of `/etc/logrotate.d` rules |
| `upgrade` | Upgrade tasks (if implemented) |
| `test` | Testing tasks |
| `verify` | Verification tasks (dry-run) |

## Example Playbooks

```yaml
---
- hosts: all
  become: true
  roles:
    - role: grzegorzfranus.logrotate
      vars:
        logrotate_frequency: "daily"
        logrotate_rotate: 7
```

## 🤝 Contributing

Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`
- Use [Conventional Commits](https://www.conventionalcommits.org/) for commit messages
- Ensure your code passes all CI checks (YAML lint, Ansible lint, Molecule tests)
- Submit a pull request describing your changes (a template is available under `.github/PULL_REQUEST_TEMPLATE/pull_request_template.md` to help structure your PR description)
- For major changes, please open an issue first to discuss what you would like to change (issue templates for bug reports, feature requests, and tasks are available under `.github/ISSUE_TEMPLATE/`)

## 📝 License

This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information

This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).
