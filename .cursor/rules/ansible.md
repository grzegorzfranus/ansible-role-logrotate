# LLM Rule: Ansible Role Best Practices
*(Authored with the mindset of a Senior DevOps Engineer)*

As a senior DevOps engineer, you must design **Ansible roles** that are production‑grade, reusable, idempotent, and aligned with both community best practices and enterprise requirements. Every role must be **modular**, **lint‑clean**, **testable with Molecule**, and **fully documented**.

---

## 🔎 Scope
- These rules apply ONLY to Ansible roles under `ansible/roles/*` in this repository.
- Out of scope: `ansible/playbooks/*`, `ansible/inventory/*`, `terraform/*`, and any external repositories.
- All content (task names, comments, messages, docs, variables, tags) MUST be English-only.

---

## 🎯 Intent & Principles
- Favor **idempotency**, **clarity**, and **safety** over cleverness.
- MUST use FQCN for built-ins: `ansible.builtin.*` (never bare module names).
- Avoid `command`/`shell` unless strictly required and no suitable module exists.
- Keep roles **composable** (single responsibility) and **overridable** via `defaults`.
- Enforce **inputs** with `tasks/assert.yml`; fail fast with actionable errors.
- Treat **security** as a first‑class concern (Vault, `no_log`, least privilege).

---

## 📁 Structure & Naming
```
roles/role_name/
├── .github/workflows/           # CI/CD workflows
│   ├── test-and-validation.yml  # Testing pipeline
│   └── publish-to-galaxy.yml    # Galaxy publishing
├── .ansible-lint                # Linting configuration
├── .gitignore                   # Git ignore rules
├── .yamllint                    # YAML linting config
├── CHANGELOG.md                 # Version history
├── LICENSE                      # License file
├── README.md                    # Role documentation
├── defaults/main.yml            # Default variables
├── handlers/main.yml            # Service handlers
├── meta/main.yml                # Role metadata
├── molecule/default/            # Testing scenarios
│   ├── converge.yml             # Test playbook
│   ├── molecule.yml             # Test configuration
│   ├── prepare.yml              # Test preparation
│   └── verify.yml               # Test verification
├── tasks/                       # Task files
│   ├── main.yml                 # Main orchestration (REQUIRED)
│   ├── assert.yml               # Variable validation (REQUIRED)
│   ├── prerequisites.yml        # System prerequisites (optional)
│   ├── configure.yml            # Service configuration (optional)
├── templates/                   # Jinja2 templates (optional)
│   └── config/                  # Configuration templates
└── vars/                        # Variable files
    ├── main.yml                 # Common variables (optional)
    ├── debian.yml               # Debian-specific vars (as needed)
    └── redhat.yml               # RedHat-specific vars (as needed)
```

**Structure Notes**
- Create sub‑task files (`install.yml`, `configure.yml`, etc.) **only** if complexity warrants it.
- OS‑specific vars (`debian.yml`, `redhat.yml`) are present **only** if multiple distributions are supported.
- Minimal roles may consist of `tasks/main.yml` and `tasks/assert.yml`.

**Naming Conventions**
- **Variables:** `rolename_category_setting` (e.g., `nginx_service_enabled`, `mysql_package_name`)
- **Tasks:** `"Rolename | filename/action | description"` (e.g., `"Nginx | install | Install package"`)
- **Tags (allowed only):** `always`, `setup`, `init`, `validate`, `requirements`, `install`, `configure`, `config`, `logrotate`, `upgrade`, `test`, `verify`
- **Files:** `lowercase_with_underscores` (e.g., `install.yml`, `configure.yml`)

**Comment Standards**
- MUST follow `.cursor/examples/ansible-role/defaults/main.yml` commenting style for variables.
- MUST follow `.cursor/examples/ansible-role/tasks/main.yml` commenting style for tasks.

---

## 🔒 Security & Validation
- **Ansible Vault:** All sensitive variables MUST be vaulted.
- **no_log: true:** Mandatory for tasks handling passwords/secrets/tokens.
- **become:** Use only when required; never default to global privilege escalation.
- **Variable validation:** Always include `tasks/assert.yml` to enforce requirements and types.
- **OS‑specific vars:** Load with a `first_found` pattern:

```yaml
- name: Rolename | include_vars | Gather OS specific variables
  ansible.builtin.include_vars: "{{ lookup('first_found', params) }}"
  vars:
    params:
      files:
        - "{{ ansible_facts['distribution'] | lower }}.yml"
        - "{{ ansible_facts['os_family'] | lower }}.yml"
        - "main.yml"
      paths:
        - "{{ role_path }}/vars"
  tags: [always, setup, init]
```

**Additional Security Rules**
- Never echo secrets to logs; prefer modules with secure params over shell commands.
- Use `validate` commands on templates where appropriate (e.g., `nginx -t -c {{ path }}`) before applying changes.

---

## ⚙️ Variables & Files Policy
- **defaults/main.yml:** User‑overridable variables with safe, sensible defaults.
- **vars/main.yml & OS var files:** Constants or platform‑specific values **not intended** for user override; keep to a minimum.
- **templates/**: Jinja2 (`.j2`) templates; avoid hard‑coded values—prefer variables with defaults.
- **files/**: Static artifacts; keep binary files out of Git where possible (fetch during `prepare.yml` in Molecule if needed).

---

## 🔁 Handlers
- Handlers must be **named clearly** and triggered only when state changes.
- Use `notify` from tasks; do **not** restart services unconditionally.
- Group related notifications where possible to prevent excessive restarts.

---

## 🧪 Quality, Linting & Testing
- **Idempotency:** Must pass `ansible-playbook --check` without unexpected changes.
- **Molecule:** Provide at least one scenario (`molecule/default`) with `converge`, `verify`, and proper `prepare` steps.
- **Error Handling:** Use meaningful `failed_when` with clear messaging for operators.
- **Linting:** MUST run `ansible-lint` and `yamllint` on all YAML files; no warnings permitted.
- **Supported Modules:** MUST use `ansible.builtin.*` names for core modules.
- **CI:** Include `.github/workflows/test-and-validation.yml` to run lint + molecule on PRs.

**Minimal .ansible-lint (example)**
```yaml
skip_list: []
warn_list: []
parseable: true
strict: true
```

**Minimal .yamllint (example)**
```yaml
extends: default
rules:
  line-length: {max: 120}
  indentation:
    spaces: 2
    indent-sequences: consistent
```

---

## 📝 Documentation Requirements
- **README.md:** MUST follow `.cursor/examples/ansible-role/README.md` template.
  - Purpose and scope
  - Supported platforms
  - Default variables with descriptions
  - Usage examples
  - Notes on idempotency and caveats
- **CHANGELOG.md:** Update for every version bump using Keep a Changelog style (dates and changes).

### 🔐 Repository-specific hard requirements (example role as canonical reference)
- **README.md sections and order MUST match `.cursor/examples/ansible-role/README.md`:**
  1) Title and badges table (Source | Version | CI | License)
  2) Short role summary paragraph
  3) ✨ Features (bullet list)
  4) 🎯 Architecture (with optional ASCII diagram)
  5) 📋 Requirements
     - Supported operating systems table
     - Ansible version
     - Python version
     - Setup module note
     - Root access note
  6) 🚀 Quick Start (minimal playbooks + run command)
  7) ⚙️ Configuration
     - Default Configuration (YAML block)
     - Advanced Configuration (YAML block)
  8) 📊 Variables (tables grouped by sections)
  9) 🔍 Verification (commands)
  10) 🛡️ Security Features (bullets + optional config example)
  11) 🔧 Troubleshooting (common issues with commands)
  12) 📁 File Structure (tree)
  13) 🏷️ Tags (list)
  14) Example Playbook (full examples)
  15) 🧪 Testing (how to run molecule; test matrix)
  16) 🔧 CI/CD Integration (workflows overview)
  17) 🤝 Contributing
  18) 📝 License
  19) 👥 Author Information

- **CHANGELOG.md MUST match `.cursor/examples/ansible-role/CHANGELOG.md` style:**
  - Title `# Changelog`, intro noting Keep a Changelog v1.0.0 and SemVer.
  - Releases as `## [x.y.z] - YYYY-MM-DD`.
  - Section headings with emojis exactly: `### Added ✅`, `### Changed 🔄`, `### Fixed 🔧`, plus optional thematic blocks like `Quality Improvements 📈`, `Workflow Improvements 🚀`, etc.
  - Bullet entries are concise, professional, and English-only.

- **Tasks/comments MUST mirror `.cursor/examples/ansible-role/tasks/assert.yml` style:**
  - File header banner with role name and purpose delineated by `# =============================================================================`.
  - Section dividers with `# -----------------------------------------------------------------------------` and numbered sections.
  - Task names use emoji prefixes per role standard and the pattern: `Rolename | action | description` (e.g., `🧪 Chrony | assert | Validate ...`).
  - Assert tasks use `ansible.builtin.assert` with:
    - `that:` checks including type and value validation
    - Clear `fail_msg` with ❌ and contextual `| default('undefined')`
    - Clear `success_msg` with ✅ and the actual value
  - Conditional assertions (`when:`) used where appropriate (e.g., optional features enabled).

- **Variable validation MUST follow `.cursor/examples/ansible-role/tasks/assert.yml` patterns:**
  - Validate: definition, type (string/number/boolean/mapping), allowed values/ranges, and format using `is search('regex')` where applicable.
  - Group assertions into logical sections (General, Logrotate, Service/Package, Paths/Permissions, Advanced tuning, etc.).
  - Use absolute path checks with `is search('^/')` for filesystem paths.
  - For lists/mappings, validate structure and presence of required keys before use.
  - Prefer multiple focused assert tasks over one mega-assert for clarity.
  - All validation and messages must be in English and use emojis for clarity.

- **Conditional statements (`when`) MUST use folded style exactly as in `.cursor/examples/ansible-role/tasks/main.yml`:**
  - Use `when: >` with the condition indented on the next line.
  - Keep a two-space indent for the folded content.
  - Do not use inline single-line `when: expr` or list-form `when:` conditions.
  - Do not wrap conditions in `{{ }}`; write raw Jinja expressions.
  - Example:
    
    ```yaml
    when: >
      chrony_role_action in ['all', 'prerequisites']
    ```
  - Multi-condition example:
    
    ```yaml
    when: >
      chrony_role_action in ['all', 'configure'] and
      chrony_configure_logrotate and
      not ansible_check_mode
    ```

---

## 📜 Task Flow (tasks/main.yml)
- The main orchestration MUST follow this order (use appropriate tags):
  1) `📂 include_vars` → tags: `always`, `setup`, `init`
  2) `🧪 assert` (variable validation) → tags: `always`, `validate`
  3) `🔍 requirements/prerequisites` (optional) → tags: `requirements`, `setup`
  4) `📦 install` → tags: `install`
  5) `⚙️ configure` → tags: `configure`, `config`
  6) `📝 logrotate` (if applicable) → tags: `logrotate`
  7) `🔄 upgrade` (if applicable, usually `never`) → tags: `upgrade`
  8) `🧪 test/verify` → tags: `test`, `verify`

---

## 🔐 Safety & Module Usage
- MUST NOT use `state: latest`; use `state: present` or pinned versions.
- Use `command`/`shell` only if no module exists; then provide `changed_when`, `failed_when`, and `creates`/`removes` where applicable for idempotency.
- Templates MUST use `validate` when supported before applying.
- Support `--check`; use `check_mode: no` only if unavoidable and explain why in a preceding comment.

---

## ✅ Consistency Checklist for New/Changed Variables
- Add to `defaults/main.yml` with clear documentation comments.
- Add assertions in `tasks/assert.yml` (definition, type, range/enum, format).
- Document in README Variables section (table or equivalent) with defaults.
- Wire into templates/handlers/tasks as needed.

---

## 🚀 Development Workflow
1. **Scaffold:** Start from `.cursor/examples/ansible-role` template.
2. **Implement:** Add tasks with strict naming, tagging, and validation.
3. **Test:** Run `molecule test` across targeted platforms.
4. **Lint:** Ensure `ansible-lint` and `yamllint` pass with zero warnings.
5. **Document:** Update README and CHANGELOG accordingly.
6. **Review:** Confirm production‑readiness; ensure role is self‑contained and composable.
7. **Publish (optional):** Use `.github/workflows/publish-to-galaxy.yml` to release.

**Quality Checklist**  
✅ Lint clean • ✅ Variables validated • ✅ Molecule tests pass • ✅ Idempotent in `--check` • ✅ Docs & Changelog updated

---

## ❌ Critical Requirements
- NEVER commit secrets without Vault.
- ALWAYS set `no_log: true` for sensitive tasks.
- MUST validate all required variables via `tasks/assert.yml`.
- MUST test idempotency (`--check`) and address diffs.
- MUST resolve **all** lint warnings before merge.
