# Ansible Role: logrotate

| Source | Version | CI | License |
|-------|---------|----|---------|
|[![Source Code](https://img.shields.io/badge/source-github-blue.svg)](https://github.com/grzegorzfranus/ansible-role-logrotate)|[![Version](https://img.shields.io/github/v/release/grzegorzfranus/ansible-role-logrotate)](https://github.com/grzegorzfranus/ansible-role-logrotate/releases)|[![tests](https://github.com/grzegorzfranus/ansible-role-logrotate/actions/workflows/ci.yml/badge.svg)](https://github.com/grzegorzfranus/ansible-role-logrotate/actions)|[![Repository License](https://img.shields.io/badge/license-apache2.0-brightgreen.svg)](LICENSE)|

This Ansible role installs and configures logrotate, managing the main `/etc/logrotate.conf` and application-specific rules under `/etc/logrotate.d/`. It provides safe defaults, validations, and Molecule coverage for Debian-based systems.

## ✨ Features
- Install and ensure `logrotate` is present
- Manage main configuration via Jinja2 with validation
- Manage drop-in rules with templating (`/etc/logrotate.d/*`)
- Idempotent, lint-clean, Molecule tested

## 🎯 Architecture
- Tasks: include_vars → assert → install → configure → manage rules → verify
- Templates: `logrotate.conf.j2`, `logrotate_rule.j2`

## 📋 Requirements
### Supported operating systems
| OS Family | Version | Status |
|-----------|---------|--------|
| Ubuntu | 24.04 (Noble) | ✓ |
| Debian | 12 (Bookworm) | ✓ |

### Ansible version
Ansible >= 2.14

### Python version
Python >= 3.8

### Setup module
This role relies on gathered facts. If the Setup module is disabled, ensure equivalent facts are provided.

### Root access
Root privileges are required for managing files under `/etc` and installing packages.

## 🚀 Quick Start
Minimal playbook:
```yaml
- hosts: all
  become: true
  roles:
    - role: grzegorzfranus.logrotate
```
Run:
```bash
ansible-playbook -i inventory playbook.yml
```

## ⚙️ Configuration
### Default Configuration
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

## 🔍 Verification
- The role runs `logrotate -d -s <status> /etc/logrotate.conf` to validate configuration.

## 🛡️ Security Features
- Secure defaults; no secrets handled by the role

## 🔧 Troubleshooting
- Validate configuration:
```bash
logrotate -d -s /var/lib/logrotate/status /etc/logrotate.conf
```

## 📁 File Structure
```
ansible-role-logrotate/
├── CHANGELOG.md              # Version history and changes
├── LICENSE                   # Apache-2.0 license
├── README.md                 # Role documentation
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
- `always` - Tasks that always run (variable loading, validation)
- `setup` - OS variables, installation, and configuration setup
- `init` - Initial setup tasks
- `validate` - Variable validation tasks
- `requirements` - System requirements and pre-checks
- `install` - Package installation tasks
- `configure` - Main configuration tasks
- `config` - Configuration-related tasks
- `logrotate` - Management of `/etc/logrotate.d` rules
- `upgrade` - Upgrade tasks (if implemented)
- `test` - Testing tasks
- `verify` - Verification tasks (dry-run)

## Example Playbook
```yaml
- hosts: all
  become: true
  roles:
    - role: grzegorzfranus.logrotate
```

## 🔧 CI/CD Integration
- GitHub Actions workflow can run `yamllint`, `ansible-lint`, and `molecule`.

## 🤝 Contributing
Contributions, bug reports, and feature requests are welcome!

- Fork the repository and create your branch from `main`.
- Make your changes with clear, descriptive commit messages.
- Ensure your code passes all Molecule and lint tests.
- Submit a pull request describing your changes and the motivation.

## 📝 License
This project is licensed under the Apache-2.0 License - see the LICENSE file for details.

## 👥 Author Information
This role was created by [Grzegorz Franus](https://github.com/grzegorzfranus).
