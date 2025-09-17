# LLM Rule: Ansible Inventory Rules

## 📋 MANDATORY WORKFLOW
**Before ANY inventory changes:**
1. Plan inventory structure
2. Ask clarifying questions about target environment  
3. Get explicit user approval
4. NEVER modify without approval

---

## 🏗️ STRUCTURE STANDARDS

### File Organization
```
inventory/
├── 01-hosts           # Host definitions
├── 02-groups          # Group relationships  
├── group_vars/all/    # Global variables
└── host_vars/         # Host-specific vars
```

### Host Format (01-hosts)
```ini
### Environment: Production | Last Updated: 2024-MM-DD

[webservers]
web-01.prod.example.com ansible_host=192.168.1.10
web-02.prod.example.com ansible_host=192.168.1.11
```

### Group Format (02-groups)
```ini
[production:children]
webservers
databases
```

---

## 🔐 SECURITY RULES

### ✅ REQUIRED:
- Use `ansible-vault` for all secrets
- SSH key authentication only
- Separate environments (no mixing prod/dev)
- Document with comments and dates

### ❌ FORBIDDEN:
- Plain text passwords in inventory
- Mixed case hostnames (`Web_Server_01`)
- Special characters in hostnames
- More than 3 levels of group nesting

---

## 🎨 NAMING CONVENTIONS

**Hostnames:** `[env]-[role]-[number].[domain]`
- ✅ `prod-web-01.company.com`
- ❌ `WebServer_Production_01`

**Groups:** lowercase with underscores
- ✅ `webservers`, `ubuntu_servers` 
- ❌ `WebServers`, `DB_Servers`

**Variables:** snake_case
- ✅ `nginx_worker_processes`
- ❌ `nginxWorkerProcesses`

---

## 🔍 VALIDATION COMMANDS

```bash
# Always validate before deployment
ansible-inventory --list --yaml
ansible-inventory --graph
```

---

## ⚠️ COMMON MISTAKES
- **❌ Mixed case hostnames**
- **❌ Inline secrets** 
- **❌ Deep nesting (>3 levels)**
- **❌ Missing environment comments**
- **❌ Environment mixing in groups**

---

## 🎯 REMEMBER: Plan → Approve → Execute → Document
